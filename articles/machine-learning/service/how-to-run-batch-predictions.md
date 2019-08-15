---
title: Executar previsões de lote em dados grandes com pipelines
titleSuffix: Azure Machine Learning service
description: Saiba como fazer predições de batch de forma assíncrona em grandes quantidades de dados com o serviço Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens, garye
ms.author: jordane
author: jpe316
ms.date: 07/12/2019
ms.openlocfilehash: 97b66bff0e6413b7774a054a01a0dc73649c9cce
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68962989"
---
# <a name="run-batch-predictions-on-large-data-sets-with-azure-machine-learning-pipelines"></a>Executar previsões de lote em conjuntos de dados grandes com pipelines de Azure Machine Learning

Neste artigo, você aprenderá a fazer previsões sobre grandes quantidades de dados de forma assíncrona usando os pipelines de ML com o serviço Azure Machine Learning.

A previsão de lote (ou a pontuação em lote) fornece inferência econômica, com taxa de transferência incomparável para aplicativos assíncronos. Pipelines de predição de batch podem ser dimensionado para realizar a inferência de tipos em terabytes de dados de produção. A previsão de lote é otimizada para alta taxa de transferência, previsões de acionamento e esquecida para uma grande coleção de dados.

>[!TIP]
> Se o seu sistema exigir processamento de baixa latência (para processar um único documento ou um pequeno conjunto de documentos rapidamente), use a [Pontuação em tempo real](how-to-consume-web-service.md) em vez da previsão de lote.

Nas etapas a seguir, você criará um [pipeline de Machine Learning](concept-ml-pipelines.md) para registrar um modelo de visão de computador pré-treinado ([concepção-v3](https://arxiv.org/abs/1512.00567)). Em seguida, você usa o modelo pré-treinado para fazer a pontuação de lote em imagens disponíveis em sua conta de armazenamento de BLOBs do Azure. Estas imagens usadas para a classificação são sem etiqueta imagens a partir da [ImageNet](http://image-net.org/) conjunto de dados.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do serviço de Azure Machine Learning](https://aka.ms/AMLFree).

- Configure o ambiente de desenvolvimento para instalar o SDK do Azure Machine Learning. Para obter mais informações, consulte [configurar um ambiente de desenvolvimento do Azure Machine Learning](how-to-configure-environment.md).

- Crie uma área de trabalho do Azure Machine Learning que irá conter todos os seus recursos do pipeline. Pode utilizar o código a seguir, ou para obter mais opções, consulte [criar um ficheiro de configuração da área de trabalho](how-to-configure-environment.md#workspace).

  ```python
  from azureml.core import Workspace
  ws = Workspace.create(name = '<workspace-name>',
                        subscription_id = '<subscription-id>',
                        resource_group = '<resource-group>',
                        location = '<workspace_region>',
                        exist_ok = True
                        )
  ```

## <a name="set-up-machine-learning-resources"></a>Configurar recursos de aprendizado de máquina

As etapas a seguir configuram os recursos necessários para executar um pipeline:

- Acessar o arquivo de dados que já tenha o pré-preparadas com o modelo, etiquetas de entrada e imagens para classificar (isso já está configurado para).
- Configure um arquivo de dados para armazenar as saídas.
-  `DataReference`Configure objetos para apontarem para os dados nos armazenamentos anteriores.
- Configure máquinas de computação ou clusters em que executará as etapas do pipeline.

### <a name="access-the-datastores"></a>Acesso os arquivos de dados

Em primeiro lugar, acesse o arquivo de dados com o modelo, etiquetas e imagens.

Use um contêiner de blob público, chamado *SampleData*, na conta *pipelinedata* que contém imagens do conjunto de avaliação ImageNet. É o nome de arquivo de dados para este contentor público *images_datastore*. Registe este arquivo de dados com a sua área de trabalho:

```python
from azureml.core import Datastore

account_name = "pipelinedata"
datastore_name = "images_datastore"
container_name = "sampledata"

batchscore_blob = Datastore.register_azure_blob_container(ws,
                                                          datastore_name=datastore_name,
                                                          container_name=container_name,
                                                          account_name=account_name,
                                                          overwrite=True)
```

Em seguida, configure para usar o repositório de armazenamento padrão para as saídas.

Quando você cria seu espaço de trabalho, os  [arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)e o [armazenamento](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) de BLOBs são anexados ao espaço de trabalho por padrão. O arquivos do Azure é o repositório de armazenamento padrão para um espaço de trabalho, mas você também pode usar o armazenamento de BLOBs como um armazenamento de repositório. Para obter mais informações, consulte [Opções de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-references"></a>Configura as referências de dados

Agora, referenciar os dados no seu pipeline como entradas para passos de pipeline.

Uma origem de dados num pipeline é representada por um [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) objeto.  `DataReference`O objeto aponta para dados que residem no, ou que podem ser acessados de um repositório de armazenamento. Você precisa `DataReference`  de objetos para o diretório usado para imagens de entrada, o diretório no qual o modelo pretreinado está armazenado, o diretório para rótulos e o diretório de saída.

```python
from azureml.data.data_reference import DataReference

input_images = DataReference(datastore=batchscore_blob,
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download")

model_dir = DataReference(datastore=batchscore_blob,
                          data_reference_name="input_model",
                          path_on_datastore="batchscoring/models",
                          mode="download")

label_dir = DataReference(datastore=batchscore_blob,
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download")

output_dir = PipelineData(name="scores",
                          datastore=def_data_store,
                          output_path_on_compute="batchscoring/results")
```

### <a name="set-up-compute-target"></a>Configurar o destino de computação

No Azure Machine Learning, *computação* (ou *destino de computação*) refere-se aos computadores ou clusters que executam as etapas computacionais no pipeline do Machine Learning. Por exemplo, pode criar um `Azure Machine Learning compute`.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

compute_name = "gpucluster"
compute_min_nodes = 0
compute_max_nodes = 4
vm_size = "STANDARD_NC6"

if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target. just use it. ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(
        vm_size=vm_size,  # NC6 is GPU-enabled
        vm_priority='lowpriority',  # optional
        min_nodes=compute_min_nodes,
        max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws,
                                          compute_name,
                                          provisioning_config)

    compute_target.wait_for_completion(
        show_output=True,
        min_node_count=None,
        timeout_in_minutes=20)
```

## <a name="prepare-the-model"></a>Preparar o modelo

Antes de poder utilizar o modelo pretrained, terá de transferir o modelo e registá-lo com a sua área de trabalho.

### <a name="download-the-pretrained-model"></a>Transferir o modelo pretrained

Transferir o pré-preparadas com modelo de imagem digitalizada (InceptionV3) a partir do <http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz>. Em seguida, extraia `models` -o para a subpasta.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url = "http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

### <a name="register-the-model"></a>Registe o modelo

Veja como registrar o modelo:

```python
import shutil
from azureml.core.model import Model

# register downloaded model
model = Model.register(
    model_path="models/inception_v3.ckpt",
    model_name="inception",  # This is the name of the registered model
    tags={'pretrained': "inception"},
    description="Imagenet trained tensorflow inception",
    workspace=ws)
```

## <a name="write-your-scoring-script"></a>Escrever o seu script de classificação

>[!Warning]
>O código a seguir é apenas uma amostra do que está contido no [batch_score. py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/batch_scoring.py) usado pelo [bloco de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/pipeline-batch-scoring.ipynb). Você precisará criar seu próprio script de Pontuação para seu cenário.

O `batch_score.py` script aceita imagens de entrada *dataset_path*, pré-preparadas com modelos no *model_dir,* e produz *resultados label.txt* para *output_dir*.

```python
# Snippets from a sample scoring script
# Refer to the accompanying batch-scoring Notebook
# https://github.com/Azure/MachineLearningNotebooks/blob/master/pipeline/pipeline-batch-scoring.ipynb
# for the implementation script

# Get labels
def get_class_label_dict(label_file):
  label = []
  proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
  for l in proto_as_ascii_lines:
    label.append(l.rstrip())
  return label

class DataIterator:
  # Definition of the DataIterator here

def main(_):
    # Refer to batch-scoring Notebook for implementation.
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)

    # get model from model registry
    model_path = Model.get_model_path(args.model_name)
    with tf.Session() as sess:
        test_images = test_feeder.input_pipeline(batch_size=args.batch_size)
        with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
            input_images = tf.placeholder(tf.float32, [args.batch_size, image_size, image_size, num_channel])
            logits, _ = inception_v3.inception_v3(input_images,
                                                        num_classes=classes_num,
                                                        is_training=False)
            probabilities = tf.argmax(logits, 1)

        sess.run(tf.global_variables_initializer())
        sess.run(tf.local_variables_initializer())
        coord = tf.train.Coordinator()
        threads = tf.train.start_queue_runners(sess=sess, coord=coord)
        saver = tf.train.Saver()
        saver.restore(sess, model_path)
        out_filename = os.path.join(args.output_dir, "result-labels.txt")

        # copy the file to artifacts
        shutil.copy(out_filename, "./outputs/")
```

## <a name="build-and-run-the-batch-scoring-pipeline"></a>Criar e executar o pipeline de classificação de batch

### <a name="prepare-the-run-environment"></a>Preparar o ambiente de execução

Especifica as dependências de conda para o seu script. Você precisará desse objeto mais tarde, quando criar a etapa de pipeline.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

cd = CondaDependencies.create(
    pip_packages=["tensorflow-gpu==1.10.0", "azureml-defaults"])

# Runconfig
amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.gpu_support = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="specify-the-parameter-for-your-pipeline"></a>Especifique o parâmetro para o pipeline

Crie um parâmetro de pipeline usando um objeto [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) com um valor padrão.

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(
    name="param_batch_size",
    default_value=20)
```

### <a name="create-the-pipeline-step"></a>Criar o passo de pipeline

Crie a etapa de pipeline usando o script, a configuração de ambiente e os parâmetros. Especifique o destino de computação já ligado à sua área de trabalho como o destino de execução do script. Uso [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) para criar o passo de pipeline.

```python
from azureml.pipeline.steps import PythonScriptStep
inception_model_name = "inception_v3.ckpt"

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_score.py",
    arguments=["--dataset_path", input_images,
               "--model_name", "inception",
               "--label_dir", label_dir,
               "--output_dir", output_dir,
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config,
    source_directory=scripts_folder
```

### <a name="run-the-pipeline"></a>Executar o pipeline

Agora, execute o pipeline e examine a saída que ele produziu. A saída tem uma pontuação correspondente a cada imagem de entrada.

```python
import pandas as pd
from azureml.pipeline.core import Pipeline

# Run the pipeline
pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(
    pipeline, pipeline_params={"param_batch_size": 20})

# Wait for the run to finish (this might take several minutes)
pipeline_run.wait_for_completion(show_output=True)

# Download and review the output
step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head()
```

## <a name="publish-the-pipeline"></a>Publicar o pipeline

Depois que estiver satisfeito com o resultado da execução, publique o pipeline para que você possa executá-lo com valores de entrada diferentes posteriormente. Ao publicar um pipeline, você obtém um ponto de extremidade REST. Esse ponto de extremidade aceita a invocação do pipeline com o conjunto de parâmetros que você já incorporou usando [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py).

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring",
    description="Batch scoring using Inception v3 model",
    version="1.0")
```

## <a name="rerun-the-pipeline-by-using-the-rest-endpoint"></a>Executar novamente o pipeline usando o ponto de extremidade REST

Para executar novamente o pipeline, você precisará de um token de cabeçalho de autenticação Azure Active Directory, conforme descrito em [classe AzureCliAuthentication](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py).

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.pipeline.core import PublishedPipeline

rest_endpoint = published_pipeline.endpoint
# specify batch size when running the pipeline
response = requests.post(rest_endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})

# Monitor the run
published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)

RunDetails(published_pipeline_run).show()
```

## <a name="next-steps"></a>Passos Seguintes

Para ver esse trabalho de ponta a ponta, experimente o bloco de anotações de pontuação em lote no [GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)ou acesse o [centro de arquitetura do Azure](/azure/architecture/reference-architectures/ai/batch-scoring-python) para ver uma arquitetura de solução de exemplo.
