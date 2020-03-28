---
title: Executar previsões de lote em big data
titleSuffix: Azure Machine Learning
description: Aprenda a obter inferências assincronicamente sobre grandes quantidades de dados utilizando ParallelRunStep em Azure Machine Learning. ParalelamenteRunStep fornece capacidades de processamento paralelas fora da caixa e otimiza para inferência de alta-adesão, fogo e esquecimento para casos de uso de big-data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: vaidyas
author: vaidya-s
ms.date: 01/15/2020
ms.custom: Ignite2019
ms.openlocfilehash: 3d283d1094336b928869aa281b4a640d7a62dd94
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79477192"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Executar inferência do lote sobre grandes quantidades de dados usando Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a processar grandes quantidades de dados sincronicamente e em paralelo utilizando o Azure Machine Learning. A capacidade ParallelRunStep descrita aqui está na pré-visualização pública. É uma forma de alto desempenho e alta supor para gerar inferências e dados de processamento. Fornece capacidades assíncronas fora da caixa.

Com o ParallelRunStep, é simples escalar inferências offline a grandes aglomerados de máquinas em terabytes de dados de produção, resultando numa melhor produtividade e custo otimizado.

Neste artigo, aprende-se as seguintes tarefas:

> * Crie um recurso de computação remota.
> * Escreva um roteiro de inferência personalizada.
> * Crie um pipeline de [aprendizagem automática](concept-ml-pipelines.md) para registar um modelo de classificação de imagem pré-treinado com base no conjunto de dados [MNIST.](https://publicdataset.azurewebsites.net/dataDetail/mnist/) 
> * Utilize o modelo para executar inferência de lote em imagens de amostra disponíveis na sua conta de armazenamento Azure Blob. 

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

* Para um arranque rápido guiado, complete o tutorial de [configuração](tutorial-1st-experiment-sdk-setup.md) se ainda não tiver um espaço de trabalho Azure Machine Learning ou uma máquina virtual de caderno. 

* Para gerir o seu próprio ambiente e dependências, consulte o [guia como configurar](how-to-configure-environment.md) o seu próprio ambiente. Corra `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps` no seu ambiente para descarregar as dependências necessárias.

## <a name="set-up-machine-learning-resources"></a>Configurar recursos de aprendizagem automática

As seguintes ações configuram os recursos necessários para executar um gasoduto de inferência de lote:

- Crie uma loja de dados que aponte para um recipiente de blob que tenha imagens para inferência.
- Configurar referências de dados como inputs e saídas para o passo do gasoduto de inferência do lote.
- Criar um cluster de cálculo para executar o passo de inferência do lote.

### <a name="create-a-datastore-with-sample-images"></a>Criar uma loja de dados com imagens de amostra

Obtenha o conjunto de avaliação MNIST do `sampledata` `pipelinedata`recipiente de blob público numa conta chamada . Crie uma loja `mnist_datastore`de dados com o nome , que aponta para este recipiente. Na chamada seguinte, `register_azure_blob_container`definindo a `overwrite` bandeira para `True` substituir qualquer datastore que tenha sido criada anteriormente com esse nome. 

Pode alterar este passo para apontar para o seu recipiente `datastore_name` `container_name`de `account_name`bolhas, fornecendo os seus próprios valores para , e .

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

Quando cria o seu espaço de trabalho, [os Ficheiros](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) Azure e o [armazenamento](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) Blob estão ligados ao espaço de trabalho por defeito. O Azure Files é a loja de dados padrão para um espaço de trabalho, mas também pode utilizar o armazenamento Blob como uma loja de dados. Para mais informações, consulte as opções de [armazenamento do Azure.](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-inputs-and-outputs"></a>Configurar as inputs e saídas de dados

Agora precisa configurar as inputs e saídas de dados, incluindo:

- O diretório que contém as imagens de entrada.
- O diretório onde o modelo pré-treinado é armazenado.
- O diretório que contém os rótulos.
- O diretório para a produção.

[`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)é uma classe para explorar, transformar e gerir dados em Azure Machine Learning. Esta classe tem [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) dois [`FileDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py)tipos: e . Neste exemplo, utilizará `FileDataset` como entrada sintetizadora para o passo do gasoduto de inferência do lote. 

> [!NOTE] 
> `FileDataset`o suporte na inferência do lote está restrito ao armazenamento de Azure Blob por enquanto. 

Também pode fazer referência a outros conjuntos de dados no seu script de inferência personalizada. Por exemplo, pode usá-lo para aceder a etiquetas `Dataset.register` no `Dataset.get_by_name`seu script para rotular imagens utilizando e .

Para obter mais informações sobre os conjuntos de dados do Azure Machine Learning, consulte Criar e aceder a conjuntos de [dados (pré-visualização)](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets).

[`PipelineData`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)os objetos são utilizados para a transferência de dados intermédios entre os passos do gasoduto. Neste exemplo, usa-o para saídas de inferência.

```python
from azureml.core.dataset import Dataset

mnist_ds_name = 'mnist_sample_data'

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
registered_mnist_ds = input_mnist_ds.register(ws, mnist_ds_name, create_new_version=True)
named_mnist_ds = registered_mnist_ds.as_named_input(mnist_ds_name)

output_dir = PipelineData(name="inferences", 
                          datastore=def_data_store, 
                          output_path_on_compute="mnist/results")
```

### <a name="set-up-a-compute-target"></a>Configurar um alvo de cálculo

Em Azure Machine Learning, *a computação* (ou *o alvo da computação)* refere-se às máquinas ou clusters que realizam os passos computacionais no seu pipeline de aprendizagem automática. Executar o seguinte código para criar um alvo [amlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) baseado em CPU.

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
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

Em seguida, registe o modelo com o seu espaço de trabalho para que esteja disponível para o seu recurso de computação remota.

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
-  `run(mini_batch)`: A função `mini_batch` será executada para cada instância.
    -  `mini_batch`: Passo de execução paralelo invocará o método de execução e passará uma lista ou Pandas DataFrame como argumento para o método. Cada entrada em min_batch será - um caminho de ficheiro se a entrada for um Conjunto de Dados de Ficheiros, um DataFrame pandas se a entrada for um TabularDataset.
    -  `response`: executar() método deve devolver um DataFrame pandas ou uma matriz. Para append_row output_action, estes elementos devolvidos estão anexados ao ficheiro de saída comum. Para summary_only, o conteúdo dos elementos é ignorado. Para todas as ações de saída, cada elemento de saída devolvido indica uma execução bem sucedida do elemento de entrada no mini-lote de entrada. Deve certificar-se de que os dados suficientes estão incluídos no resultado do mapa para mapear a entrada para executar o resultado. A saída de execução será escrita no ficheiro de saída e não garantidamente por ordem, deve utilizar alguma chave na saída para mapeá-la para a entrada.

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

### <a name="how-to-access-other-files-in-source-directory-in-entry_script"></a>Como aceder a outros ficheiros em diretório de origem em entry_script

Se tiver outro ficheiro ou pasta no mesmo diretório que o seu script de entrada, pode referenciar-o encontrando o atual diretório de trabalho.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>Construir e executar o oleoduto contendo ParallelRunStep

Agora tem tudo o que precisa para construir o oleoduto.

### <a name="prepare-the-run-environment"></a>Preparar o ambiente de corrida

Primeiro, especifique as dependências do seu guião. Usa este objeto mais tarde quando cria o passo do gasoduto.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.13.1", "pillow"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
batch_env.spark.precache_packages = False
```

### <a name="specify-the-parameters-for-your-batch-inference-pipeline-step"></a>Especifique os parâmetros para o seu passo de gasoduto de inferência do lote

`ParallelRunConfig`é a configuração principal para a `ParallelRunStep` nova introdução da instância de inferência do lote dentro do gasoduto De aprendizagem automática Azure. Usa-o para embrulhar o script e configurar os parâmetros necessários, incluindo todos os seguintes parâmetros:
- `entry_script`: Um script de utilizador como um caminho de arquivo local que será executado em paralelo em vários nódosos. Se `source_directory` estiver presente, utilize um caminho relativo. Caso contrário, utilize qualquer caminho acessível na máquina.
- `mini_batch_size`: O tamanho do mini-lote `run()` passou para uma única chamada. (opcional; o valor `10` predefinido são `1MB` ficheiros para FileDataset e para TabularDataset.)
    - Pois, `FileDataset`é o número de ficheiros `1`com um valor mínimo de . Pode combinar vários ficheiros num mini-lote.
    - Pois, `TabularDataset`é o tamanho dos dados. Os valores `1024KB` `10MB`exemplo `1GB`são, `1024`e . O valor `1MB`recomendado é . O mini-lote `TabularDataset` de nunca cruzará os limites dos ficheiros. Por exemplo, se tiver ficheiros .csv com vários tamanhos, o ficheiro mais pequeno é de 100 KB e o maior é de 10 MB. Se definir, `mini_batch_size = 1MB`os ficheiros com um tamanho inferior a 1 MB serão tratados como um mini-lote. Os ficheiros com um tamanho maior do que 1 MB serão divididos em vários mini-lotes.
- `error_threshold`: O número de `TabularDataset` falhas recorde para `FileDataset` e falhas de ficheiros para isso deve ser ignorado durante o processamento. Se a contagem de erros para toda a entrada for superior a este valor, o trabalho será abortado. O limiar de erro é para toda a entrada e `run()` não para mini-lotes individuais enviados para o método. O alcance `[-1, int.max]`é. A `-1` peça indica ignorar todas as falhas durante o processamento.
- `output_action`: Um dos seguintes valores indica como a saída será organizada:
    - `summary_only`: O script do utilizador armazenará a saída. `ParallelRunStep`utilizará a saída apenas para o cálculo do limiar de erro.
    - `append_row`: Para todos os ficheiros de entrada, apenas um ficheiro será criado na pasta de saída para anexar todas as saídas separadas por linha. O nome do `parallel_run_step.txt`ficheiro será.
- `source_directory`: Caminhos para pastas que contenham todos os ficheiros a executar no alvo do cálculo (opcional).
- `compute_target`: `AmlCompute` Só é suportado.
- `node_count`: O número de nós de cálculo a utilizar para executar o script do utilizador.
- `process_count_per_node`: O número de processos por nó.
- `environment`: A definição de ambiente Python. Pode configurá-lo para usar um ambiente Python existente ou para criar um ambiente temporário para a experiência. A definição é também responsável pela definição das dependências de aplicação necessárias (opcional).
- `logging_level`: Verbosidade de log. Os valores na verbosidade crescente são: `WARNING`, `INFO`e `DEBUG`. (opcional; o valor `INFO`predefinido é)
- `run_invocation_timeout`: `run()` O tempo de intenção de invocação do método em segundos. (opcional; valor `60`por defeito é)

```python
from azureml.contrib.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory=scripts_folder,
    entry_script="digit_identification.py",
    mini_batch_size="5",
    error_threshold=10,
    output_action="append_row",
    environment=batch_env,
    compute_target=compute_target,
    node_count=4)
```

### <a name="create-the-pipeline-step"></a>Criar o passo do oleoduto

Crie o passo do gasoduto utilizando o script, a configuração do ambiente e os parâmetros. Especifique o alvo computacional que já anexou ao seu espaço de trabalho como alvo de execução para o script. Utilizar `ParallelRunStep` para criar o passo do gasoduto de inferência do lote, que leva todos os seguintes parâmetros:
- `name`: O nome do passo, com as seguintes restrições de nomeação:\[caracteres\]únicos, 3-32 e regex ^ a-z ([-a-z0-9]*[a-z0-9]??$.?$.
- `models`: Zero ou mais nomes de modelos já registados no registo do modelo de Aprendizagem Automática Azure.
- `parallel_run_config`: `ParallelRunConfig` Um objeto, tal como definido anteriormente.
- `inputs`: Um ou mais conjuntos de dados de aprendizagem automática azure de letra única.
- `output`: `PipelineData` Um objeto que corresponda ao diretório de saída.
- `arguments`: Uma lista de argumentos passados para o script do utilizador (opcional).
- `allow_reuse`: Se o passo deve reutilizar os resultados anteriores quando executado com as mesmas definições/entradas. Se este parâmetro `False`for , será sempre gerada uma nova execução para este passo durante a execução do gasoduto. (opcional; o valor `True`predefinido é .)

```python
from azureml.contrib.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="batch-mnist",
    models=[model],
    parallel_run_config=parallel_run_config,
    inputs=[named_mnist_ds],
    output=output_dir,
    arguments=[],
    allow_reuse=True
)
```

>[!Note]
> O passo acima `azureml-contrib-pipeline-steps`depende, como descrito nos [pré-requisitos.](#prerequisites) 

### <a name="submit-the-pipeline"></a>Submeter o gasoduto

Agora, corre o oleoduto. Primeiro, crie um [`Pipeline`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) objeto utilizando a referência do espaço de trabalho e o passo do gasoduto que criou. O `steps` parâmetro é uma variedade de passos. Neste caso, só há um passo para marcar lotes. Para construir oleodutos com vários passos, coloque os passos em ordem nesta matriz.

Em seguida, `Experiment.submit()` utilize a função para submeter o gasoduto para execução.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
pipeline_run = Experiment(ws, 'digit_identification').submit(pipeline)
```

## <a name="monitor-the-parallel-run-job"></a>Monitorize o trabalho paralelo de execução

Um trabalho de inferência do lote pode levar muito tempo para terminar. Este exemplo monitoriza o progresso utilizando um widget Jupyter. Também pode gerir o progresso do trabalho usando:

* Estúdio de Aprendizagem automática Azure. 
* Saída da [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py) consola a partir do objeto.

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="next-steps"></a>Passos seguintes

Para ver este processo a funcionar de ponta a ponta, experimente o caderno de [inferência](https://aka.ms/batch-inference-notebooks)do lote . 

Para depurar e resolver problemas para ParallelRunStep, consulte o [guia como orientar](how-to-debug-parallel-run-step.md).

Para depurar e resolver problemas para os oleodutos, consulte o guia como [orientar](how-to-debug-pipelines.md).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

