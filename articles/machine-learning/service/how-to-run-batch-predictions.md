---
title: Executar predições do batch em dados de grande dimensão
titleSuffix: Azure Machine Learning service
description: Saiba como fazer predições de batch de forma assíncrona em grandes quantidades de dados com o serviço Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens, garye
ms.author: jordane
author: jpe316
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 1e403ac0d2fbe9572a44fb3cde9d25e4df9b3db4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57885004"
---
# <a name="run-batch-predictions-on-large-data-sets-with-azure-machine-learning-service"></a>Executar predições do batch em grandes conjuntos de dados com o serviço Azure Machine Learning

Neste artigo, irá aprender como fazer previsões de indisponibilidade de grandes quantidades de dados de forma assíncrona, com o serviço Azure Machine Learning.

Predição de lote (ou classificação de lote) fornece económica inferência de tipos, com um débito sem precedentes para aplicativos assíncronos. Pipelines de predição de batch podem ser dimensionado para realizar a inferência de tipos em terabytes de dados de produção. Predição de batch está otimizada para débito elevado, disparar e esquecer predições para uma grande coleção de dados.

>[!TIP]
> Se o seu sistema requer processamento de baixa latência (para processar rapidamente um documento único ou pequeno conjunto de documentos), utilize [classificação em tempo real](how-to-consume-web-service.md) em vez de predição de batch.

Os passos seguintes, vai criar um [pipeline de machine learning](concept-ml-pipelines.md) para registar um pré-preparadas com modelo de imagem digitalizada ([V3 de concepção](https://arxiv.org/abs/1512.00567)). Em seguida, utilize o modelo pretrained para o lote de classificação nas imagens disponíveis na sua conta de armazenamento de Blobs do Azure. Estas imagens usadas para a classificação são sem etiqueta imagens a partir da [ImageNet](http://image-net.org/) conjunto de dados.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente o [uma versão gratuita ou paga do serviço Azure Machine Learning](https://aka.ms/AMLFree).

- Configure o ambiente de desenvolvimento para instalar o SDK do Azure Machine Learning. Para obter mais informações, consulte [configurar um ambiente de desenvolvimento do Azure Machine Learning](how-to-configure-environment.md).

- Crie uma área de trabalho do Azure Machine Learning que irá conter todos os seus recursos do pipeline. Pode utilizar o código a seguir, ou para obter mais opções, consulte [criar um ficheiro de configuração da área de trabalho](how-to-configure-environment.md#workspace).

  ```python
  ws = Workspace.create(
     name = '<workspace-name>',
     subscription_id = '<subscription-id>',
     resource_group = '<resource-group>',
     location = '<workspace_region>',
     exist_ok = True)
  ```

## <a name="set-up-machine-learning-resources"></a>Configurar recursos de aprendizado de máquina

Os seguintes passos, configure os recursos que necessários para executar um pipeline:

- Acessar o arquivo de dados que já tenha o pré-preparadas com o modelo, etiquetas de entrada e imagens para classificar (isso já está configurado para).
- Configure um arquivo de dados para armazenar as saídas.
- Configurar `DataReference` objetos para que apontem para os dados em arquivos de dados anteriores.
- Configure máquinas de computação ou clusters em que executará as etapas do pipeline.

### <a name="access-the-datastores"></a>Acesso os arquivos de dados

Em primeiro lugar, acesse o arquivo de dados com o modelo, etiquetas e imagens.

Irá utilizar um contentor de BLOBs público, denominado *sampledata*, na *pipelinedata* conta que contém imagens do conjunto de avaliação de ImageNet. É o nome de arquivo de dados para este contentor público *images_datastore*. Registe este arquivo de dados com a sua área de trabalho:

```python
# Public blob container details
account_name = "pipelinedata"
datastore_name="images_datastore"
container_name="sampledata"
 
batchscore_blob = Datastore.register_azure_blob_container(ws,
                      datastore_name=datastore_name,
                      container_name= container_name,
                      account_name=account_name,
                      overwrite=True)
```

Em seguida, configure a utilizar o arquivo de dados padrão para as saídas.

Quando cria a área de trabalho, [ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) e [armazenamento de BLOBs](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) estão anexados à área de trabalho, por predefinição. O Azure Files é o arquivo de dados predefinida para uma área de trabalho, mas também pode utilizar o armazenamento de BLOBs como um arquivo de dados. Para obter mais informações, consulte [opções de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="configure-data-references"></a>Configura as referências de dados

Agora, referenciar os dados no seu pipeline como entradas para passos de pipeline.

Uma origem de dados num pipeline é representada por um [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) objeto. O `DataReference` objeto aponta para dados que residem ou estão acessível a partir de um arquivo de dados. Precisa `DataReference`  objetos para o diretório utilizado para imagens de entrada, o diretório em que o modelo pretrained é armazenado, o diretório para as etiquetas e o diretório de saída.

```python
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

No Azure Machine Learning *computação* (ou *destino de computação*) refere-se a computadores ou clusters que executam os passos de computacionais no seu pipeline de machine learning. Por exemplo, pode criar um `Azure Machine Learning compute`.

```python
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
                     vm_size = vm_size, # NC6 is GPU-enabled
                     vm_priority = 'lowpriority', # optional
                     min_nodes = compute_min_nodes, 
                     max_nodes = compute_max_nodes)

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

Transferir o pré-preparadas com modelo de imagem digitalizada (InceptionV3) a partir do <http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz>. Em seguida, extraia-o para o `models` subpasta.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

### <a name="register-the-model"></a>Registe o modelo

Eis como registar o modelo:

```python
import shutil
from azureml.core.model import Model

# register downloaded model 
model = Model.register(
        model_path = "models/inception_v3.ckpt",
        model_name = "inception", # This is the name of the registered model
        tags = {'pretrained': "inception"},
        description = "Imagenet trained tensorflow inception",
        workspace = ws)
```

## <a name="write-your-scoring-script"></a>Escrever o seu script de classificação

>[!Warning]
>O código seguinte é apenas uma amostra do que está contido no [batch_score.py](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/batch_scoring.py) utilizado pela [bloco de notas do exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/pipeline-batch-scoring/pipeline-batch-scoring.ipynb). Terá de criar seu próprio script de classificação para o seu cenário.

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

Tem tudo o que precisa para criar o pipeline, portanto, agora juntar tudo.

### <a name="prepare-the-run-environment"></a>Preparar o ambiente de execução

Especifica as dependências de conda para o seu script. Precisará deste objeto mais tarde, quando cria o passo de pipeline.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.10.0", "azureml-defaults"])

# Runconfig
amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.gpu_support = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="specify-the-parameter-for-your-pipeline"></a>Especifique o parâmetro para o pipeline

Criar um parâmetro de pipeline com uma [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) objeto com um valor predefinido.

```python
batch_size_param = PipelineParameter(
                    name="param_batch_size", 
                    default_value=20)
```

### <a name="create-the-pipeline-step"></a>Criar o passo de pipeline

Crie o passo de pipeline com o script, a configuração do ambiente e parâmetros. Especifique o destino de computação já ligado à sua área de trabalho como o destino de execução do script. Uso [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) para criar o passo de pipeline.

```python
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

Agora execute o pipeline e examine o resultado que foram produzidos. A saída tem uma pontuação correspondente a cada imagem de entrada.

```python
# Run the pipeline
pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_params={"param_batch_size": 20})

# Wait for the run to finish (this might take several minutes)
pipeline_run.wait_for_completion(show_output=True)

# Download and review the output
step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

import pandas as pd
df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head()
```

## <a name="publish-the-pipeline"></a>Publicar o pipeline

Depois que estiver satisfeito com o resultado da execução, publique o pipeline para que pode executá-lo com diferentes valores de entrada mais tarde. Quando publica um pipeline, obtém um ponto final REST. Este ponto final aceita a invocação do pipeline com o conjunto de parâmetros já tiver incorporado utilizando [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py).

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", 
    description="Batch scoring using Inception v3 model", 
    version="1.0")
```

## <a name="rerun-the-pipeline-by-using-the-rest-endpoint"></a>Voltar a executar o pipeline com o ponto final do REST

Para voltar a executar o pipeline, terá um token de cabeçalho de autenticação do Azure Active Directory, conforme descrito em [AzureCliAuthentication classe](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py).

```python
from azureml.pipeline.core import PublishedPipeline

rest_endpoint = published_pipeline.endpoint
# specify batch size when running the pipeline
response = requests.post(rest_endpoint, 
        headers=aad_token, 
        json={"ExperimentName": "batch_scoring",
               "ParameterAssignments": {"param_batch_size": 50}})

# Monitor the run
from azureml.pipeline.core.run import PipelineRun
published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)

RunDetails(published_pipeline_run).show()
```

## <a name="next-steps"></a>Passos Seguintes

Para ver este trabalho ponto-a-ponto, experimente o bloco de notas de classificação de lote [GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). 

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

