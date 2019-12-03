---
title: Executar previsões de lote em Big Data
titleSuffix: Azure Machine Learning
description: Saiba como obter inferências de forma assíncrona em grandes quantidades de dados usando a inferência de lote no Azure Machine Learning. A inferência de lote fornece recursos de processamento paralelo prontos para uso e otimiza a inferência de alta taxa de transferência, acionamento e esquecer para casos de utilização de Big Data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: vaidyas
author: vaidya-s
ms.date: 11/04/2019
ms.custom: Ignite2019
ms.openlocfilehash: 62a2c3324df70c7ccdbbac273d314ff94cbb7b9a
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671570"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Executar a inferência de lote em grandes quantidades de dados usando Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste "como", você aprende a obter inferências sobre grandes quantidades de dados de forma assíncrona e em paralelo usando Azure Machine Learning. A funcionalidade de inferência de lote descrita aqui está em visualização pública. É uma maneira de alto desempenho e alta taxa de transferência para gerar inferências e processar dados. Ele fornece recursos assíncronos prontos para uso.

Com a inferência de lote, é simples dimensionar as inferências offline para grandes clusters de computadores em terabytes de dados de produção, resultando em produtividade aprimorada e custo otimizado.

Neste "como", você aprende as seguintes tarefas:

> * Crie um recurso de computação remota.
> * Escreva um script de inferência personalizado.
> * Crie um [pipeline de Machine Learning](concept-ml-pipelines.md) para registrar um modelo de classificação de imagem pré-treinado com base no conjunto de [MNIST](https://publicdataset.azurewebsites.net/dataDetail/mnist/) . 
> * Use o modelo para executar a inferência de lote em imagens de exemplo disponíveis em sua conta de armazenamento de BLOBs do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Para um guia de início rápido orientado, conclua o [tutorial de instalação](tutorial-1st-experiment-sdk-setup.md) se você ainda não tiver um espaço de trabalho Azure Machine Learning ou uma máquina virtual do notebook. 

* Para gerenciar seu próprio ambiente e suas dependências, consulte o [Guia de instruções sobre como](how-to-configure-environment.md) configurar seu próprio ambiente. Execute `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps` em seu ambiente para baixar as dependências necessárias.

## <a name="set-up-machine-learning-resources"></a>Configurar recursos de aprendizado de máquina

As ações a seguir configuram os recursos necessários para executar um pipeline de inferência de lote:

- Crie um repositório de armazenamento que aponte para um contêiner de BLOB que tenha imagens para inferência.
- Configure referências de dados como entradas e saídas para a etapa pipeline de inferência de lote.
- Configure um cluster de cálculo para executar a etapa de inferência de lote.

### <a name="create-a-datastore-with-sample-images"></a>Criar um repositório de armazenamento com imagens de exemplo

Obtenha o conjunto de avaliação MNIST do contêiner de blob público `sampledata` em uma conta chamada `pipelinedata`. Crie um repositório de armazenamento com o nome `mnist_datastore`, que aponta para esse contêiner. Na chamada a seguir para `register_azure_blob_container`, definir o sinalizador `overwrite` como `True` substitui qualquer repositório de armazenamento que foi criado anteriormente com esse nome. 

Você pode alterar essa etapa para apontar para o contêiner de BLOBs fornecendo seus próprios valores para `datastore_name`, `container_name`e `account_name`.

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

Em seguida, especifique o armazenamento de datastore padrão do espaço de trabalho como o repositório de armazenamento de saída. Você o usará para saída de inferência.

Quando você cria seu espaço de trabalho, os [arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) e de [armazenamento de BLOBs](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) são anexados ao espaço de trabalho por padrão. O arquivos do Azure é o repositório de armazenamento padrão para um espaço de trabalho, mas você também pode usar o armazenamento de BLOBs como um armazenamento de repositório. Para obter mais informações, consulte [Opções de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-inputs-and-outputs"></a>Configurar entradas e saídas de dados

Agora você precisa configurar entradas e saídas de dados, incluindo:

- O diretório que contém as imagens de entrada.
- O diretório em que o modelo pré-treinado está armazenado.
- O diretório que contém os rótulos.
- O diretório para saída.

`Dataset` é uma classe para explorar, transformar e gerenciar dados no Azure Machine Learning. Essa classe tem dois tipos: `TabularDataset` e `FileDataset`. Neste exemplo, você usará `FileDataset` como as entradas para a etapa pipeline de inferência de lote. 

> [!NOTE] 
> o suporte a `FileDataset` na inferência de lote é restrito ao armazenamento de BLOBs do Azure por enquanto. 

Você também pode fazer referência a outros conjuntos de valores em seu script de inferência personalizada. Por exemplo, você pode usá-lo para acessar rótulos em seu script para rotular imagens usando `Dataset.register` e `Dataset.get_by_name`.

Para obter mais informações sobre conjuntos de dados Azure Machine Learning, consulte [criar e acessar conjuntos de dados (versão prévia)](https://docs.microsoft.com/azure/machine-learning/service/how-to-create-register-datasets).

`PipelineData` objetos são usados para transferir dados intermediários entre etapas de pipeline. Neste exemplo, você o utiliza para saídas de inferência.

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

### <a name="set-up-a-compute-target"></a>Configurar um destino de computação

No Azure Machine Learning, *computação* (ou *destino de computação*) refere-se aos computadores ou clusters que executam as etapas computacionais no pipeline do Machine Learning. Execute o código a seguir para criar um destino [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) baseado em CPU.

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

[Baixe o modelo de classificação de imagem pré-treinado](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz)e, em seguida, extraia-o para o diretório `models`.

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

Em seguida, registre o modelo com seu espaço de trabalho para que ele esteja disponível para o recurso de computação remota.

```python
from azureml.core.model import Model

# Register the downloaded model 
model = Model.register(model_path="models/",
                       model_name="mnist",
                       tags={'pretrained': "mnist"},
                       description="Mnist trained tensorflow model",
                       workspace=ws)
```

## <a name="write-your-inference-script"></a>Escreva seu script de inferência

>[!Warning]
>O código a seguir é apenas uma amostra usada pelo [bloco de anotações de exemplo](https://aka.ms/batch-inference-notebooks) . Você precisará criar seu próprio script para seu cenário.

O script *deve conter* duas funções:
- `init()`: Use essa função para qualquer preparação dispendiosa ou comum para a inferência posterior. Por exemplo, use-o para carregar o modelo em um objeto global.
-  `run(mini_batch)`: a função será executada para cada instância de `mini_batch`.
    -  `mini_batch`: a inferência de lote invocará o método Run e passará uma List ou pandas dataframe como um argumento para o método. Cada entrada em min_batch será um FilePath se a entrada for um filedataset, um dataframe do pandas se a entrada for um TabularDataset.
    -  `response`: o método Run () deve retornar um dataframe do pandas ou uma matriz. Para append_row output_action, esses elementos retornados são acrescentados ao arquivo de saída comum. Por summary_only, o conteúdo dos elementos é ignorado. Para todas as ações de saída, cada elemento de saída retornado indica uma inferência bem-sucedida do elemento input no mini-lote de entrada. O usuário deve garantir que dados suficientes sejam incluídos no resultado da inferência para mapear a entrada para a inferência. A saída de inferência será gravada no arquivo de saída e não haverá garantia de que esteja em ordem, o usuário deverá usar alguma chave na saída para mapeá-la para entrada.

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

## <a name="build-and-run-the-batch-inference-pipeline"></a>Compilar e executar o pipeline de inferência de lote

Agora você tem tudo o que precisa para criar o pipeline.

### <a name="prepare-the-run-environment"></a>Preparar o ambiente de execução

Primeiro, especifique as dependências para o script. Você usará esse objeto posteriormente quando criar a etapa de pipeline.

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.13.1", "pillow"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
batch_env.spark.precache_packages = False
```

### <a name="specify-the-parameters-for-your-batch-inference-pipeline-step"></a>Especifique os parâmetros para a etapa do pipeline de inferência de lote

`ParallelRunConfig` é a principal configuração para a instância de `ParallelRunStep` de inferência de lote introduzida recentemente no pipeline de Azure Machine Learning. Use-o para encapsular o script e configurar os parâmetros necessários, incluindo todos os itens a seguir:
- `entry_script`: um script de usuário como um caminho de arquivo local que será executado em paralelo em vários nós. Se `source_directly` estiver presente, use um caminho relativo. Caso contrário, use qualquer caminho que esteja acessível no computador.
- `mini_batch_size`: o tamanho do mini-lote passado para uma única chamada de `run()`. (Opcional; o valor padrão é `1`.)
    - Por `FileDataset`, é o número de arquivos com um valor mínimo de `1`. Você pode combinar vários arquivos em um mini-lote.
    - Por `TabularDataset`, é o tamanho dos dados. Os valores de exemplo são `1024`, `1024KB`, `10MB`e `1GB`. O valor recomendado é `1MB`. Observe que o mini-lote de `TabularDataset` nunca irá cruzar os limites do arquivo. Por exemplo, se você tiver arquivos. csv com vários tamanhos, o menor arquivo será 100 KB e o maior será de 10 MB. Se você definir `mini_batch_size = 1MB`, os arquivos com um tamanho menor que 1 MB serão tratados como um mini-lote. Arquivos com um tamanho maior que 1 MB serão divididos em vários mini-lotes.
- `error_threshold`: o número de falhas de registro para falhas de `TabularDataset` e arquivo para `FileDataset` que devem ser ignoradas durante o processamento. Se a contagem de erros de toda a entrada for acima desse valor, o trabalho será interrompido. O limite de erro é para toda a entrada e não para mini-lotes individuais enviados ao método `run()`. O intervalo é `[-1, int.max]`. A parte `-1` indica ignorar todas as falhas durante o processamento.
- `output_action`: um dos seguintes valores indica como a saída será organizada:
    - `summary_only`: o script de usuário armazenará a saída. `ParallelRunStep` usará a saída somente para o cálculo do limite de erro.
    - `append_row`: para todos os arquivos de entrada, somente um arquivo será criado na pasta de saída para acrescentar todas as saídas separadas por linha. O nome do arquivo será parallel_run_step. txt.
- `source_directory`: caminhos para pastas que contêm todos os arquivos a serem executados no destino de computação (opcional).
- `compute_target`: há suporte apenas para `AmlCompute`.
- `node_count`: o número de nós de computação a ser usado para executar o script do usuário.
- `process_count_per_node`: o número de processos por nó.
- `environment`: a definição de ambiente do Python. Você pode configurá-lo para usar um ambiente Python existente ou para configurar um ambiente temporário para o experimento. A definição também é responsável por definir as dependências de aplicativo necessárias (opcional).
- `logging_level`: detalhes do log. Os valores no detalhamento crescente são: `WARNING`, `INFO`e `DEBUG`. O padrão é `INFO` (opcional).
- `run_invocation_timeout`: o tempo limite de invocação do método `run()` em segundos. O valor padrão é `60`.

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

### <a name="create-the-pipeline-step"></a>Criar a etapa de pipeline

Crie a etapa de pipeline usando o script, a configuração de ambiente e os parâmetros. Especifique o destino de computação que você já anexou ao seu espaço de trabalho como o destino de execução para o script. Use `ParallelRunStep` para criar a etapa pipeline de inferência de lote, que usa todos os seguintes parâmetros:
- `name`: o nome da etapa, com as seguintes restrições de nomenclatura: Unique, 3-32 caracteres e Regex ^\[a-z\]([-a-Z0-9] * [a-Z0-9])? $.
- `models`: zero ou mais nomes de modelo já registrados no registro de modelo de Azure Machine Learning.
- `parallel_run_config`: um objeto `ParallelRunConfig`, conforme definido anteriormente.
- `inputs`: um ou mais conjuntos de valores de Azure Machine Learning de tipo único.
- `output`: um objeto `PipelineData` que corresponde ao diretório de saída.
- `arguments`: uma lista de argumentos passados para o script do usuário (opcional).
- `allow_reuse`: se a etapa deve reutilizar os resultados anteriores quando executado com as mesmas configurações/entradas. Se esse parâmetro for `False`, uma nova execução sempre será gerada para essa etapa durante a execução do pipeline. (Opcional; o valor padrão é `True`.)

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

### <a name="run-the-pipeline"></a>Executar o pipeline

Agora, execute o pipeline. Primeiro, crie um objeto `Pipeline` usando sua referência de espaço de trabalho e a etapa de pipeline que você criou. O parâmetro `steps` é uma matriz de etapas. Nesse caso, há apenas uma etapa para a Pontuação do lote. Para criar pipelines que têm várias etapas, coloque as etapas na ordem nessa matriz.

Em seguida, use a função `Experiment.submit()` para enviar o pipeline para execução.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
pipeline_run = Experiment(ws, 'digit_identification').submit(pipeline)
```

## <a name="monitor-the-batch-inference-job"></a>Monitorar o trabalho de inferência de lote

Um trabalho de inferência em lote pode levar muito tempo para ser concluído. Este exemplo monitora o progresso usando um widget Jupyter. Você também pode gerenciar o progresso do trabalho usando:

* Azure Machine Learning Studio. 
* Saída do console do objeto [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py) .

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="next-steps"></a>Passos seguintes

Para ver esse processo funcionando de ponta a ponta, experimente o bloco de notas de [inferência em lote](https://aka.ms/batch-inference-notebooks). 

Para obter diretrizes de depuração e solução de problemas para pipelines, consulte o [Guia de instruções](how-to-debug-pipelines.md).

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

