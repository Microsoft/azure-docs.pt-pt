---
title: Executar previsões de lotes em grandes dados
titleSuffix: Azure Machine Learning
description: Aprenda a obter inferências assíncronamente em grandes quantidades de dados usando ParallelRunStep em Azure Machine Learning. ParallelRunStep fornece capacidades de processamento paralelas fora da caixa e otimiza para inferência de alta produção, fogo-e-esquecimento para casos de uso de grandes dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: jmartens, larryfr
ms.author: tracych
author: tracychms
ms.date: 08/14/2020
ms.custom: Build2020, devx-track-python
ms.openlocfilehash: 8e8d0a13bc01e95311345154648ecb00b624c4bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905614"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Executar inferência de lote em grandes quantidades de dados utilizando Azure Machine Learning


Este artigo mostra como executar o modelo do Azure Machine Learning em paralelo, o que avalia rapidamente grandes quantidades de dados. 

A inferição de grandes conjuntos de dados ou com modelos complicados pode ser demorado. A `ParallelRunStep` classe permite-lhe realizar o processamento em paralelo, potencialmente obtenindo resultados globais mais rapidamente. Mesmo que executar uma única avaliação seja bastante rápido, muitos cenários (deteção de objetos, processamento de vídeo, processamento de linguagem natural, etc.) envolvem executar muitas avaliações. 

Com `ParallelRunStep` , é simples escalar inferências de lote para grandes aglomerados de máquinas. Estes clusters podem lidar com terabytes de dados estruturados ou não estruturados com uma produtividade melhorada e custos otimizados.

Neste artigo, aprende-se as seguintes tarefas:

> 1. Crie recursos de aprendizagem automática.
> 1. Configure entradas e saídas de dados de inferência do lote.
> 1. Prepare o modelo de classificação de imagem pré-treinado com base no conjunto de dados [do MNIST.](https://publicdataset.azurewebsites.net/dataDetail/mnist/) 
> 1.  Escreve o teu guião de inferência.
> 1. Crie um [pipeline de aprendizagem automática](concept-ml-pipelines.md) contendo ParallelRunStep e execute inferência de lote em imagens de teste MNIST. 
> 1. Reenviar uma inferência de lote executada com nova entrada de dados e parâmetros. 
> 1. Veja os resultados.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Para um arranque rápido guiado, complete o [tutorial de configuração](tutorial-1st-experiment-sdk-setup.md) se ainda não tiver um espaço de trabalho de Aprendizagem automática Azure. 

* Para gerir o seu próprio ambiente e dependências, consulte o [guia de como](how-to-configure-environment.md) configurar o seu próprio ambiente local.

## <a name="set-up-machine-learning-resources"></a>Criar recursos de aprendizagem automática

As seguintes ações configuram os recursos de aprendizagem automática que você precisa para executar um pipeline de inferência de lote:

- Ligue-se a um espaço de trabalho.
- Criar ou anexar o recurso de computação existente.

### <a name="configure-workspace"></a>Configurar a área de trabalho

Crie um objeto de área de trabalho a partir da área de trabalho existente. `Workspace.from_config()` lê o config.jsno ficheiro e carrega os detalhes num objeto chamado WS.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

> [!IMPORTANT]
> Este código de corte espera que a configuração do espaço de trabalho seja guardada no diretório atual ou no seu progenitor. Para obter mais informações sobre a criação de um espaço de trabalho, consulte [Criar e gerir espaços de trabalho de Aprendizagem automática Azure.](how-to-manage-workspace.md) Para obter mais informações sobre a poupança da configuração para arquivar, consulte [Criar um ficheiro de configuração do espaço de trabalho](how-to-configure-environment.md#workspace).

### <a name="create-a-compute-target"></a>Criar um alvo de computação

No Azure Machine Learning, *o cálculo* (ou o alvo *do cálculo)* refere-se às máquinas ou clusters que realizam os passos computacionais no seu pipeline de aprendizagem automática. Executar o seguinte código para criar um alvo [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py&preserve-view=true) baseado em CPU.

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

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

## <a name="configure-inputs-and-output"></a>Configurar entradas e saídas

### <a name="create-a-datastore-with-sample-images"></a>Criar uma loja de dados com imagens de amostra

Obtenha a avaliação do MNIST definida a partir do recipiente de bolhas públicas `sampledata` numa conta chamada `pipelinedata` . Crie uma loja de dados com o `mnist_datastore` nome, que aponta para este recipiente. Na chamada `register_azure_blob_container` seguinte, colocando a `overwrite` bandeira para substituir qualquer `True` datastore que tenha sido criada anteriormente com esse nome. 

Pode alterar este passo para apontar para o seu recipiente de bolhas, fornecendo os seus próprios valores para `datastore_name` `container_name` , e `account_name` .

```python
from azureml.core import Datastore
from azureml.core import Workspace

mnist_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="mnist_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata",
                      overwrite=True)
```

Em seguida, especifique a loja de dados predefinido do espaço de trabalho como a datas de saída. Vai usá-lo para inferência.

Quando cria o seu espaço de trabalho, [os Ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)   e o armazenamento [blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)   são ligados ao espaço de trabalho por padrão. O Azure Files é a loja de dados predefinida para um espaço de trabalho, mas também pode utilizar o armazenamento blob como uma loja de dados. Para mais informações, consulte [as opções de armazenamento Azure.](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)

```python
def_data_store = ws.get_default_datastore()
```

### <a name="create-the-data-inputs"></a>Criar as entradas de dados

As entradas para inferência do lote são os dados que pretende dividir para processamento paralelo. Um pipeline de inferência de lote aceita entradas de dados através [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py&preserve-view=true) de .

`Dataset` é para explorar, transformar e gerir dados em Azure Machine Learning. Existem dois tipos: [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true) e [`FileDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py&preserve-view=true) . Neste exemplo, usará `FileDataset` como entradas. `FileDataset` fornece-lhe a capacidade de descarregar ou montar os ficheiros para o seu cálculo. Ao criar um conjunto de dados, cria uma referência à localização da fonte de dados. Se aplicar algumas transformações de subscímia ao conjunto de dados, elas também serão armazenadas no conjunto de dados. Os dados permanecem na sua localização existente, pelo que não é incorrido qualquer custo extra de armazenamento.

Para obter mais informações sobre conjuntos de dados de aprendizagem automática Azure, consulte [Criar e aceder conjuntos de dados (pré-visualização)](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets).

```python
from azureml.core.dataset import Dataset

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
```

Para utilizar entradas dinâmicas de dados ao executar o pipeline de inferência do lote, pode definir as entradas `Dataset` como um [`PipelineParameter`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py&preserve-view=true) . Pode especificar o conjunto de dados de entradas sempre que voltar aubscrever um pipeline de inferência de lote.

```python
from azureml.data.dataset_consumption_config import DatasetConsumptionConfig
from azureml.pipeline.core import PipelineParameter

pipeline_param = PipelineParameter(name="mnist_param", default_value=input_mnist_ds)
input_mnist_ds_consumption = DatasetConsumptionConfig("minist_param_config", pipeline_param).as_mount()
```

### <a name="create-the-output"></a>Criar a saída

[`PipelineData`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true) os objetos são utilizados para transferir dados intermédios entre etapas de gasoduto. Neste exemplo, usa-se para inferência.

```python
from azureml.pipeline.core import Pipeline, PipelineData

output_dir = PipelineData(name="inferences", datastore=def_data_store)
```

## <a name="prepare-the-model"></a>Preparar o modelo

[Descarregue o modelo de classificação de imagem pré-treinado](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz)e, em seguida, extraia-o para o `models` diretório.

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

Em seguida, registe o modelo com o seu espaço de trabalho para que esteja disponível para o seu recurso compute.

```python
from azureml.core.model import Model

# Register the downloaded model 
model = Model.register(model_path="models/",
                       model_name="mnist",
                       tags={'pretrained': "mnist"},
                       description="Mnist trained tensorflow model",
                       workspace=ws)
```

## <a name="write-your-inference-script"></a>Escreva o seu script de inferência

>[!Warning]
>O seguinte código é apenas uma amostra que o [caderno de amostras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run) utiliza. Terás de criar o teu próprio guião para o teu cenário.

O script *deve conter* duas funções:
- `init()`: Utilize esta função para qualquer preparação dispendiosa ou comum para posterior inferência. Por exemplo, use-o para carregar o modelo num objeto global. Esta função será chamada apenas uma vez no início do processo.
-  `run(mini_batch)`: A função funcionará para cada `mini_batch` instância.
    -  `mini_batch`: `ParallelRunStep` invocará o método de execução e passará uma lista ou pandas `DataFrame` como argumento para o método. Cada entrada em mini_batch será um caminho de arquivo se a entrada for um `FileDataset` ou um pandas `DataFrame` se a entrada for um `TabularDataset` .
    -  `response`: método de execução() deve devolver um pandas `DataFrame` ou uma matriz. Para append_row output_action, estes elementos devolvidos são anexados ao ficheiro de saída comum. Para summary_only, o conteúdo dos elementos é ignorado. Para todas as ações de saída, cada elemento de saída devolvido indica uma execução bem sucedida do elemento de entrada no mini-lote de entrada. Certifique-se de que os dados suficientes são incluídos no resultado de execução para mapear a entrada para executar o resultado da saída. A saída de execução será escrita no ficheiro de saída e não garantida para estar em ordem, deve utilizar alguma chave na saída para mapear a entrada.

```python
%%writefile digit_identification.py
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)
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

Se tiver outro ficheiro ou pasta no mesmo diretório que o seu script de inferência, pode fazê-lo referenciar encontrando o diretório de trabalho atual.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>Construa e corram o gasoduto contendo ParallelRunStep

Agora tem tudo o que precisa: as entradas de dados, o modelo, a saída e o seu script de inferência. Vamos construir o pipeline de inferência de lote contendo ParallelRunStep.

### <a name="prepare-the-environment"></a>Preparar o ambiente

Primeiro, especifique as dependências do seu script. Ao fazê-lo, permite-lhe instalar pacotes de pips, bem como configurar o ambiente.

Inclua sempre **o azureml-core** e **o tempo de execução de dados azureml[pandas, fusíveis]** na lista de pacotes de pip. Se utilizar uma imagem personalizada do estivador (user_managed_dependencies=Verdade), também deverá ter a Conda instalada.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.15.2", "pillow", 
                                                          "azureml-core", "azureml-dataset-runtime[pandas, fuse]"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="specify-the-parameters-using-parallelrunconfig"></a>Especificar os parâmetros usando ParallelRunConfig

`ParallelRunConfig` é a principal `ParallelRunStep` configuração, por exemplo, dentro do pipeline Azure Machine Learning. Usa-o para embrulhar o seu script e configurar os parâmetros necessários, incluindo todas as seguintes entradas:
- `entry_script`: Um script do utilizador como um caminho de arquivo local que será executado em paralelo em múltiplos nós. Se `source_directory` estiver presente, use um caminho relativo. Caso contrário, utilize qualquer caminho acessível na máquina.
- `mini_batch_size`: O tamanho do mini-lote passou para uma única `run()` chamada. (opcional; o valor predefinido são `10` ficheiros para `FileDataset` e para `1MB` `TabularDataset` .)
    - Para `FileDataset` , é o número de ficheiros com um valor mínimo de `1` . Pode combinar vários ficheiros num mini-lote.
    - Para, `TabularDataset` é o tamanho dos dados. Os valores de exemplo `1024` `1024KB` `10MB` são, `1GB` e. O valor recomendado é `1MB` . O mini-lote `TabularDataset` de nunca cruzará os limites do ficheiro. Por exemplo, se tiver ficheiros .csv com vários tamanhos, o ficheiro mais pequeno é de 100 KB e o maior é de 10 MB. Se `mini_batch_size = 1MB` definir, os ficheiros com um tamanho inferior a 1 MB serão tratados como um mini-lote. Os ficheiros com um tamanho superior a 1 MB serão divididos em vários mini-lotes.
- `error_threshold`: O número de falhas de registo `TabularDataset` e falhas de ficheiros para tal deve `FileDataset` ser ignorado durante o processamento. Se a contagem de erros para toda a entrada for superior a este valor, o trabalho será abortado. O limiar de erro é para toda a entrada e não para mini-lote individual enviado para o `run()` método. O alcance `[-1, int.max]` é. A `-1` peça indica ignorar todas as falhas durante o processamento.
- `output_action`: Um dos seguintes valores indica como a saída será organizada:
    - `summary_only`: O script do utilizador armazena a saída. `ParallelRunStep` utilizará a saída apenas para o cálculo do limiar de erro.
    - `append_row`: Para todas as entradas, apenas um ficheiro será criado na pasta de saída para anexar todas as saídas separadas por linha.
- `append_row_file_name`: Para personalizar o nome do ficheiro de saída para append_row output_action (opcional; valor predefinido `parallel_run_step.txt` é ).
- `source_directory`: Caminhos para pastas que contenham todos os ficheiros a executar no alvo do cálculo (opcional).
- `compute_target`: Só `AmlCompute` é suportado.
- `node_count`: O número de nós computacional a utilizar para executar o script do utilizador.
- `process_count_per_node`: O número de processos por nó. A melhor prática é definir o número de GPU ou CPU que um nó tem (opcional; valor padrão é `1` ).
- `environment`: A definição do ambiente Python. Você pode configugá-lo para usar um ambiente Python existente ou para configurar um ambiente temporário. A definição é também responsável pela definição das dependências de aplicação necessárias (opcional).
- `logging_level`: Log verbosity. Os valores em cada vez maior verbosidade são: `WARNING` `INFO` e `DEBUG` . (opcional; o valor predefinido `INFO` é)
- `run_invocation_timeout`: O `run()` tempo de tempo de invocação do método em segundos. (opcional; valor predefinido `60` é)
- `run_max_try`: Contagem máxima de tentativa `run()` para um mini-lote. A `run()` é falhado se uma exceção for lançada, ou nada for devolvido quando for alcançado `run_invocation_timeout` (opcional; o valor padrão `3` é). 

Pode especificar `mini_batch_size` , , e como , para `node_count` `process_count_per_node` `logging_level` `run_invocation_timeout` `run_max_try` `PipelineParameter` que, quando reenviar uma corrida de gasoduto, possa afinar os valores dos parâmetros. Neste exemplo, `PipelineParameter` `mini_batch_size` usa-se e `Process_count_per_node` muda estes valores quando voltar a apresentar uma execução mais tarde. 

Este exemplo pressupõe que está a usar o `digit_identification.py` guião que foi discutido anteriormente. Se utilizar o seu próprio script, altere os `source_directory` parâmetros e `entry_script` os parâmetros em conformidade.

```python
from azureml.pipeline.core import PipelineParameter
from azureml.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory='.',
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

Crie o ParallelRunStep utilizando o script, a configuração do ambiente e os parâmetros. Especifique o alvo do cálculo que já anexou ao seu espaço de trabalho como alvo de execução para o seu script de inferência. Utilize `ParallelRunStep` para criar o passo do gasoduto de inferência do lote, que toma todos os seguintes parâmetros:
- `name`: O nome do passo, com as seguintes restrições de nomeação: caracteres únicos, 3-32, e regex ^ \[ a-z \] ([-a-z0-9]*[a-z0-9])?$.
- `parallel_run_config`: Um `ParallelRunConfig` objeto, tal como definido anteriormente.
- `inputs`: Um ou mais conjuntos de dados de aprendizagem de máquinas Azure de tipo único a serem divididos para processamento paralelo.
- `side_inputs`: Um ou mais dados de referência ou conjuntos de dados utilizados como entradas laterais sem necessidade de serem divididos.
- `output`: Um `PipelineData` objeto que corresponda ao diretório de saída.
- `arguments`: Uma lista de argumentos passados para o script do utilizador. Utilize unknown_args para os recuperar no seu script de entrada (opcional).
- `allow_reuse`: Se o passo deve reutilizar os resultados anteriores quando executado com as mesmas definições/entradas. Se este parâmetro `False` for, será sempre gerado um novo ensaio para este passo durante a execução do gasoduto. (opcional; o valor predefinido é `True` .)

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
### <a name="create-and-run-the-pipeline"></a>Criar e executar o oleoduto

Agora, corre o oleoduto. Em primeiro lugar, crie um [`Pipeline`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py&preserve-view=true) objeto utilizando a referência do seu espaço de trabalho e o passo de pipeline que criou. O `steps` parâmetro é uma variedade de passos. Neste caso, só há um passo para a inferência do lote. Para construir oleodutos que tenham múltiplos passos, coloque os passos em ordem nesta matriz.

Em seguida, utilize a `Experiment.submit()` função para submeter o gasoduto para execução.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
experiment = Experiment(ws, 'digit_identification')
pipeline_run = experiment.submit(pipeline)
```

## <a name="monitor-the-batch-inference-job"></a>Monitorize o trabalho de inferência do lote

Um trabalho de inferência de lote pode levar muito tempo para terminar. Este exemplo monitoriza o progresso utilizando um widget Jupyter. Também pode monitorizar o progresso do trabalho utilizando:

* Estúdio de Aprendizagem automática Azure. 
* Saída da consola a partir do [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py&preserve-view=true) objeto.

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="resubmit-a-run-with-new-data-inputs-and-parameters"></a>Reenviar uma execução com novas entradas de dados e parâmetros

Uma vez que fez as entradas e várias configurações como `PipelineParameter` , pode reenviar uma inferência de lote executada com uma entrada de conjunto de dados diferente e afinar os parâmetros sem ter que criar um pipeline inteiramente novo. Utilizará a mesma loja de dados, mas utilizará apenas uma única imagem como entradas de dados.

```python
path_on_datastore = mnist_blob.path('mnist/0.png')
single_image_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)

pipeline_run_2 = experiment.submit(pipeline, 
                                   pipeline_parameters={"mnist_param": single_image_ds, 
                                                        "batch_size_param": "1",
                                                        "process_count_param": 1}
)

pipeline_run_2.wait_for_completion(show_output=True)
```
## <a name="view-the-results"></a>Ver os resultados

Os resultados acima são escritos para o `DataStore` especificado no objeto como `PipelineData` dados de saída, que neste caso são *chamados inferências*. Os resultados são armazenados no recipiente blob predefinido, pode navegar para a sua conta de armazenamento e visualizar através do Storage Explorer, o caminho do ficheiro é azureml-blobstore-*GUID*/azureml/*RunId* / *output_dir*.

Também pode fazer o download destes dados para ver os resultados. Abaixo está o código de amostra para ver as primeiras 10 linhas.

```python
import pandas as pd
import tempfile

batch_run = pipeline_run.find_step_run(parallelrun_step.name)[0]
batch_output = batch_run.get_output_data(output_dir.name)

target_dir = tempfile.mkdtemp()
batch_output.download(local_path=target_dir)
result_file = os.path.join(target_dir, batch_output.path_on_datastore, parallel_run_config.append_row_file_name)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10) 
```

## <a name="next-steps"></a>Passos seguintes

Para ver este processo de trabalho de ponta a ponta, experimente o [caderno de inferência](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines/parallel-run)do lote . 

Para depurar e resolver problemas para ParallelRunStep, consulte o [guia de como fazer.](how-to-debug-parallel-run-step.md)

Para depurar e resolver problemas de orientação para os gasodutos, consulte o [guia de como fazer.](how-to-debug-pipelines.md)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

