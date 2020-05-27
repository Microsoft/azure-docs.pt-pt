---
title: Executar previsões de lote em big data
titleSuffix: Azure Machine Learning
description: Aprenda a obter inferências assincronicamente sobre grandes quantidades de dados utilizando ParallelRunStep em Azure Machine Learning. ParalelamenteRunStep fornece capacidades de processamento paralelas fora da caixa e otimiza para inferência de alta-adesão, fogo e esquecimento para casos de uso de big-data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye, jmartens, larryfr
ms.author: tracych
author: tracychms
ms.date: 04/15/2020
ms.custom: Build2020
ms.openlocfilehash: 058cdaa77a38dcb45164e01a54e73218b469940b
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860958"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Executar inferência do lote sobre grandes quantidades de dados usando Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a executar inferência de lote sobre grandes quantidades de dados assincronicamente e em paralelo utilizando o Azure Machine Learning. O ParallelRunStep fornece capacidades de paralelismo fora da caixa.

Com o ParallelRunStep, é simples escalar inferências offline a grandes aglomerados de máquinas em terabytes de dados estruturados ou não estruturados com melhor produtividade e custo otimizado.

Neste artigo, aprende-se as seguintes tarefas:

> * Criar recursos de aprendizagem automática.
> * Configure as inferências de dados do lote e a saída.
> * Prepare o modelo de classificação de imagem pré-treinado com base no conjunto de dados [MNIST.](https://publicdataset.azurewebsites.net/dataDetail/mnist/) 
> * Escreve o teu guião de inferência.
> * Crie um gasoduto de [aprendizagem automática](concept-ml-pipelines.md) contendo ParallelRunStep e execute a inferência do lote em imagens de teste MNIST. 
> * Reenvie uma inferência do lote com novas informações e parâmetros. 

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

* Para um arranque rápido guiado, complete o tutorial de [configuração](tutorial-1st-experiment-sdk-setup.md) se ainda não tiver um espaço de trabalho Azure Machine Learning. 

* Para gerir o seu próprio ambiente e dependências, consulte o [guia como configurar](how-to-configure-environment.md) o seu próprio ambiente local.

## <a name="set-up-machine-learning-resources"></a>Configurar recursos de aprendizagem automática

As seguintes ações configuram os recursos de aprendizagem automática que você precisa para executar um pipeline de inferência de lote:

- Ligue-se a um espaço de trabalho.
- Crie ou anexe o recurso computacional existente.

### <a name="configure-workspace"></a>Configurar a área de trabalho

Crie um objeto de área de trabalho a partir da área de trabalho existente. `Workspace.from_config()`lê o ficheiro config.json e coloca os detalhes num objeto chamado WS.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

> [!IMPORTANT]
> Este código de corte espera que a configuração do espaço de trabalho seja guardada no diretório atual ou no seu progenitor. Para obter mais informações sobre a criação de um espaço de trabalho, consulte Criar e gerir espaços de [trabalho azure machine learning.](how-to-manage-workspace.md) Para obter mais informações sobre como guardar a configuração para arquivar, consulte Criar um ficheiro de [configuração do espaço de trabalho](how-to-configure-environment.md#workspace).

### <a name="create-a-compute-target"></a>Criar um alvo de computação

Em Azure Machine Learning, *a computação* (ou *o alvo da computação)* refere-se às máquinas ou clusters que realizam os passos computacionais no seu pipeline de aprendizagem automática. Executar o seguinte código para criar um alvo [amlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) baseado em CPU.

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

## <a name="configure-inputs-and-output"></a>Configurar as inputs e a saída

### <a name="create-a-datastore-with-sample-images"></a>Criar uma loja de dados com imagens de amostra

Obtenha o conjunto de avaliação MNIST do recipiente de blob público `sampledata` numa conta chamada `pipelinedata` . Crie uma loja de dados com o nome `mnist_datastore` , que aponta para este recipiente. Na chamada seguinte, `register_azure_blob_container` definindo a `overwrite` bandeira para substituir `True` qualquer datastore que tenha sido criada anteriormente com esse nome. 

Pode alterar este passo para apontar para o seu recipiente de bolhas, fornecendo os seus próprios valores para `datastore_name` `container_name` , e `account_name` .

```python
from azureml.core import Datastore
from azureml.core import Workspace

# Load workspace authorization details from config.json
ws = Workspace.from_config()

mnist_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="mnist_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata",
                      overwrite=True)
```

Em seguida, especifique a loja de dados padrão do espaço de trabalho como a loja de dados de saída. Vaiusá-lo para a saída de inferência.

Quando cria o seu espaço de trabalho, [os Ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)   e o armazenamento [Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)   estão ligados ao espaço de trabalho por defeito. O Azure Files é a loja de dados padrão para um espaço de trabalho, mas também pode utilizar o armazenamento Blob como uma loja de dados. Para mais informações, consulte as opções de [armazenamento do Azure.](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)

```python
def_data_store = ws.get_default_datastore()
```

### <a name="create-the-data-inputs"></a>Criar os inputs de dados

As inputs para inferência do lote são os dados que pretende dividir para processamento paralelo. Um gasoduto de inferência de lote aceita inputs de dados através [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) de .

`Dataset`é para explorar, transformar e gerir dados em Azure Machine Learning. Há dois tipos: [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) e [`FileDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py) . Neste exemplo, você usará `FileDataset` como as inputs. `FileDataset`fornece-lhe a capacidade de descarregar ou montar os ficheiros para a sua computação. Ao criar um conjunto de dados, cria uma referência à localização da fonte de dados. Se aplicar quaisquer transformações de subposição ao conjunto de dados, serão armazenadas também no conjunto de dados. Os dados permanecem na sua localização existente, pelo que não é incorrido nenhum custo extra de armazenamento.

Para obter mais informações sobre os conjuntos de dados do Azure Machine Learning, consulte Criar e aceder a conjuntos de [dados (pré-visualização)](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets).

```python
from azureml.core.dataset import Dataset

mnist_ds_name = 'mnist_sample_data'

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
```

Para utilizar inputs dinâmicos de dados ao executar o gasoduto de inferência do lote, pode definir as inputs `Dataset` como [`PipelineParameter`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) a . Pode especificar o conjunto de dados de inputs cada vez que reenviar uma execução de gasoduto de inferência de lote.

```python
from azureml.data.dataset_consumption_config import DatasetConsumptionConfig
from azureml.pipeline.core import PipelineParameter

pipeline_param = PipelineParameter(name="mnist_param", default_value=input_mnist_ds)
input_mnist_ds_consumption = DatasetConsumptionConfig("minist_param_config", pipeline_param).as_mount()
```

### <a name="create-the-output"></a>Criar a saída

[`PipelineData`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)os objetos são utilizados para a transferência de dados intermédios entre os passos do gasoduto. Neste exemplo, usa-o para a saída de inferência.

```python
from azureml.pipeline.core import Pipeline, PipelineData

output_dir = PipelineData(name="inferences", 
                          datastore=def_data_store, 
                          output_path_on_compute="mnist/results")
```

## <a name="prepare-the-model"></a>Preparar o modelo

[Descarregue o modelo de classificação de imagem pré-treinado](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz)e, em seguida, extrai-lo para o `models` diretório.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

Em seguida, registe o modelo com o seu espaço de trabalho para que esteja disponível para o seu recurso computacional.

```python
from azureml.core.model import Model

# Register the downloaded model 
model = Model.register(model_path="models/",
                       model_name="mnist",
                       tags={'pretrained': "mnist"},
                       description="Mnist trained tensorflow model",
                       workspace=ws)
```

## <a name="write-your-inference-script"></a>Escreva o seu roteiro de inferência

>[!Warning]
>O código seguinte é apenas uma amostra que o [caderno de amostras](https://aka.ms/batch-inference-notebooks) utiliza. Tens de criar o teu próprio guião para o teu cenário.

O script *deve conter* duas funções:
- `init()`: Utilize esta função para qualquer preparação dispendiosa ou comum para inferência posterior. Por exemplo, use-o para carregar o modelo num objeto global. Esta função será chamada apenas uma vez no início do processo.
-  `run(mini_batch)`: A função será executada para cada `mini_batch` instância.
    -  `mini_batch`: ParalelamenteRunStep invocará o método de execução e passará uma lista ou Pandas DataFrame como argumento para o método. Cada entrada em mini_batch será - um caminho de ficheiro se a entrada for um Conjunto de Dados de Ficheiros, um DataFrame pandas se a entrada for um TabularDataset.
    -  `response`: executar() método deve devolver um DataFrame pandas ou uma matriz. Para append_row output_action, estes elementos devolvidos estão anexados ao ficheiro de saída comum. Para summary_only, o conteúdo dos elementos é ignorado. Para todas as ações de saída, cada elemento de saída devolvido indica uma execução bem sucedida do elemento de entrada no mini-lote de entrada. Deve certificar-se de que os dados suficientes estão incluídos no resultado do execução para mapear a entrada para executar o resultado da saída. A saída de execução será escrita no ficheiro de saída e não garantidamente por ordem, deve utilizar alguma chave na saída para mapeá-la para a entrada.

```python
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://aka.ms/batch-inference-notebooks)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

Se tiver outro ficheiro ou pasta no mesmo diretório que o seu script de inferência, pode referenciar encontrando o atual diretório de trabalho.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>Construir e executar o oleoduto contendo ParallelRunStep

Agora tem tudo o que precisa: as inputs de dados, o modelo, a saída e o seu script de inferência. Vamos construir o gasoduto de inferência do lote contendo ParallelRunStep.

### <a name="prepare-the-environment"></a>Preparar o ambiente

Primeiro, especifique as dependências do seu guião. Isto permite instalar pacotes de pip, bem como configurar o ambiente. Por favor, inclua sempre pacotes de **azureml-core** e **azureml-dataprep[pandas, fusíveis].**

Se utilizar uma imagem personalizada de estivador (user_managed_dependencies=Verdade), também deverá ter conda instalada.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.13.1", "pillow",
                                                          "azureml-core", "azureml-dataprep[pandas, fuse]"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="specify-the-parameters-using-parallelrunconfig"></a>Especifique os parâmetros usando ParallelRunConfig

`ParallelRunConfig`é a configuração principal, `ParallelRunStep` por exemplo, dentro do oleoduto Azure Machine Learning. Usa-o para embrulhar o guião e configurar os parâmetros necessários, incluindo todos os seguintes:
- `entry_script`: Um script de utilizador como um caminho de arquivo local que será executado em paralelo em vários nódosos. Se `source_directory` estiver presente, utilize um caminho relativo. Caso contrário, utilize qualquer caminho acessível na máquina.
- `mini_batch_size`: O tamanho do mini-lote passou para uma única `run()` chamada. (opcional; o valor predefinido são `10` ficheiros para FileDataset e `1MB` para TabularDataset.)
    - Pois, `FileDataset` é o número de ficheiros com um valor mínimo de `1` . Pode combinar vários ficheiros num mini-lote.
    - Pois, `TabularDataset` é o tamanho dos dados. Os valores exemplo `1024` `1024KB` são, `10MB` e `1GB` . O valor recomendado é `1MB` . O mini-lote de `TabularDataset` nunca cruzará os limites dos ficheiros. Por exemplo, se tiver ficheiros .csv com vários tamanhos, o ficheiro mais pequeno é de 100 KB e o maior é de 10 MB. Se `mini_batch_size = 1MB` definir, os ficheiros com um tamanho inferior a 1 MB serão tratados como um mini-lote. Os ficheiros com um tamanho maior do que 1 MB serão divididos em vários mini-lotes.
- `error_threshold`: O número de falhas recorde para `TabularDataset` e falhas de ficheiros para isso deve `FileDataset` ser ignorado durante o processamento. Se a contagem de erros para toda a entrada for superior a este valor, o trabalho será abortado. O limiar de erro é para toda a entrada e não para mini-lote individual enviado para o `run()` método. O alcance `[-1, int.max]` é. A peça indica ignorar todas as falhas durante o `-1` processamento.
- `output_action`: Um dos seguintes valores indica como a saída será organizada:
    - `summary_only`: O script do utilizador armazenará a saída. `ParallelRunStep`utilizará a saída apenas para o cálculo do limiar de erro.
    - `append_row`: Para todas as entradas, apenas um ficheiro será criado na pasta de saída para anexar todas as saídas separadas por linha.
- `append_row_file_name`: Personalizar o nome do ficheiro de saída para append_row output_action (opcional; valor por `parallel_run_step.txt` defeito).
- `source_directory`: Caminhos para pastas que contenham todos os ficheiros a executar no alvo do cálculo (opcional).
- `compute_target`: Só `AmlCompute` é suportado.
- `node_count`: O número de nós de cálculo a utilizar para executar o script do utilizador.
- `process_count_per_node`: O número de processos por nó. A melhor prática é definir o número de GPU ou CPU que um nó tem (opcional; valor por `1` defeito é).
- `environment`: A definição de ambiente Python. Pode configurá-lo para usar um ambiente Python existente ou para criar um ambiente temporário. A definição é também responsável pela definição das dependências de aplicação necessárias (opcional).
- `logging_level`: Verbosidade de log. Os valores na verbosidade crescente são: `WARNING` `INFO` , e `DEBUG` . (opcional; o valor predefinido `INFO` é)
- `run_invocation_timeout`: O tempo de `run()` intenção de invocação do método em segundos. (opcional; valor por defeito `60` é)
- `run_max_try`: Contagem máxima de tentativa `run()` para um minilote. A `run()` é falhado se uma exceção for lançada, ou nada for devolvido quando for alcançado `run_invocation_timeout` (opcional; valor por `3` defeito é). 

Pode `mini_batch_size` `node_count` especificar, `process_count_per_node` `logging_level` e `run_invocation_timeout` `run_max_try` `PipelineParameter` como, para que, quando voltar a enviar um gasoduto, possa afinar os valores do parâmetro. Neste exemplo, utiliza o PipelineParameter para e altera estes valores quando voltar a `mini_batch_size` enviar uma corrida `Process_count_per_node` posteriormente. 

```python
from azureml.pipeline.core import PipelineParameter
from azureml.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory=scripts_folder,
    entry_script="digit_identification.py",
    mini_batch_size=PipelineParameter(name="batch_size_param", default_value="5"),
    error_threshold=10,
    output_action="append_row",
    append_row_file_name="mnist_outputs.txt",
    environment=batch_env,
    compute_target=compute_target,
    process_count_per_node=PipelineParameter(name="process_count_param", default_value=2),
    node_count=2)
```

### <a name="create-the-parallelrunstep"></a>Criar o ParallelRunStep

Crie o ParallelRunStep utilizando o script, configuração do ambiente e parâmetros. Especifique o alvo computacional que já anexou ao seu espaço de trabalho como alvo de execução para o seu script de inferência. Utilizar para criar o passo do gasoduto de `ParallelRunStep` inferência do lote, que leva todos os seguintes parâmetros:
- `name`: O nome do passo, com as seguintes restrições de nomeação: caracteres únicos, 3-32 e regex ^ \[ a-z \] ([-a-z0-9]*[a-z0-9]??$.?$.
- `parallel_run_config`: Um `ParallelRunConfig` objeto, tal como definido anteriormente.
- `inputs`: Um ou mais conjuntos de dados de aprendizagem automática azure de letra única a serem divididos para tratamento paralelo.
- `side_inputs`: Um ou mais dados de referência ou conjuntos de dados utilizados como inputs laterais sem necessidade de serem divididos.
- `output`: Um `PipelineData` objeto que corresponda ao diretório de saída.
- `arguments`: Uma lista de argumentos passados para o script do utilizador. Utilize unknown_args para recuperá-las no seu script de entrada (opcional).
- `allow_reuse`: Se o passo deve reutilizar os resultados anteriores quando executado com as mesmas definições/entradas. Se este parâmetro for `False` , será sempre gerada uma nova execução para este passo durante a execução do gasoduto. (opcional; o valor predefinido é `True` .)

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```
### <a name="create-and-run-the-pipeline"></a>criar e executar o oleoduto

Agora, corre o oleoduto. Primeiro, crie um objeto utilizando a referência do espaço de [`Pipeline`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) trabalho e o passo do gasoduto que criou. O `steps` parâmetro é uma variedade de passos. Neste caso, só há um passo para a inferência do lote. Para construir oleodutos com vários passos, coloque os passos em ordem nesta matriz.

Em seguida, utilize a `Experiment.submit()` função para submeter o gasoduto para execução.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
experiment = Experiment(ws, 'digit_identification')
pipeline_run = experiment.submit(pipeline)
```

## <a name="monitor-the-batch-inference-job"></a>Monitorize o trabalho de inferência do lote

Um trabalho de inferência do lote pode levar muito tempo para terminar. Este exemplo monitoriza o progresso utilizando um widget Jupyter. Também pode monitorizar o progresso do trabalho utilizando:

* Estúdio de Aprendizagem automática Azure. 
* Saída da consola a partir do [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py) objeto.

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="resubmit-a-run-with-new-data-inputs-and-parameters"></a>Reenvie uma corrida com novas inputs e parâmetros de dados

Uma vez que fez as inputs e várias configurações `PipelineParameter` como, pode reenviar uma inferência de lote com uma entrada diferente de conjunto de dados e afinar os parâmetros sem ter que criar um pipeline inteiramente novo. Utilizará a mesma loja de dados, mas utilizará apenas uma única imagem como inputs de dados.

```python
path_on_datastore = mnist_data.path('mnist/0.png')
single_image_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
single_image_ds._ensure_saved(ws)

pipeline_run_2 = experiment.submit(pipeline, 
                                   pipeline_parameters={"mnist_param": single_image_ds, 
                                                        "batch_size_param": "1",
                                                        "process_count_param": 1}
)

pipeline_run_2.wait_for_completion(show_output=True)
```

## <a name="next-steps"></a>Passos seguintes

Para ver este processo a funcionar de ponta a ponta, experimente o caderno de [inferência](https://aka.ms/batch-inference-notebooks)do lote . 

Para depurar e resolver problemas para ParallelRunStep, consulte o [guia como orientar](how-to-debug-parallel-run-step.md).

Para depurar e resolver problemas para os oleodutos, consulte o guia como [orientar](how-to-debug-pipelines.md).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

