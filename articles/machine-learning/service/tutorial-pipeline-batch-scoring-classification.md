---
title: 'Tutorial: pipelines ML para pontuação em lote'
titleSuffix: Azure Machine Learning
description: Neste tutorial, você cria um pipeline de Machine Learning para executar a pontuação em lote em um modelo de classificação de imagem no Azure Machine Learning. Os pipelines do Machine Learning otimizam seu fluxo de trabalho com velocidade, portabilidade e reutilização, para que você possa se concentrar em sua experiência-aprendizado de máquina, em vez de infraestrutura e automação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 11/04/2019
ms.openlocfilehash: 11d57031405939e37afade9a9452c5d3a8b9e1e4
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483293"
---
# <a name="tutorial-build-an-azure-machine-learning-pipeline-for-batch-scoring"></a>Tutorial: criar um pipeline de Azure Machine Learning para Pontuação de lote

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste tutorial, você usa um pipeline no Azure Machine Learning para executar um trabalho de pontuação em lotes. O exemplo usa o modelo de Tensorflow de rede neural do intitulada pretreinado [-v3](https://arxiv.org/abs/1512.00567) para classificar imagens sem rótulo. Depois de criar e publicar um pipeline, você configura um ponto de extremidade REST que pode ser usado para disparar o pipeline de qualquer biblioteca HTTP em qualquer plataforma.

Os pipelines do Machine Learning otimizam seu fluxo de trabalho com velocidade, portabilidade e reutilização, para que você possa se concentrar em sua experiência-aprendizado de máquina, em vez de infraestrutura e automação. [Saiba mais sobre pipelines do Machine Learning](concept-ml-pipelines.md).

Neste tutorial, vai concluir as seguintes tarefas:

> [!div class="checklist"]
> * Configurar o espaço de trabalho e baixar dados de exemplo
> * Criar objetos de dados para buscar e gerar dados de saída
> * Baixar, preparar e registrar o modelo em seu espaço de trabalho
> * Provisionar destinos de computação e criar um script de Pontuação
> * Compilar, executar e publicar um pipeline
> * Habilitar um ponto de extremidade REST para o pipeline

Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje.

## <a name="prerequisites"></a>Pré-requisitos

* Se você ainda não tiver uma máquina virtual Azure Machine Learning espaço de trabalho ou notebook, conclua [a parte 1 do tutorial de instalação](tutorial-1st-experiment-sdk-setup.md).
* Ao concluir o tutorial de instalação, use o mesmo servidor de bloco de anotações para abrir o bloco de anotações *tutoriais/tutorial-pipeline-batch-Scoring-Classification. ipynb* .

Se você quiser executar o tutorial de instalação em seu próprio [ambiente local](how-to-configure-environment.md#local), poderá acessar o tutorial no [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials). Execute `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` para obter os pacotes necessários.

## <a name="configure-workspace-and-create-a-datastore"></a>Configurar o espaço de trabalho e criar um repositório de armazenamento

Crie um objeto de espaço de trabalho do espaço de trabalho Azure Machine Learning existente.

- Um [espaço de trabalho](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) é uma classe que aceita suas informações de assinatura e recursos do Azure. O espaço de trabalho também cria um recurso de nuvem que você pode usar para monitorar e acompanhar as execuções de modelo. 
- `Workspace.from_config()` lê o arquivo de `config.json` e, em seguida, carrega os detalhes de autenticação em um objeto chamado `ws`. O objeto `ws` é usado no código durante todo este tutorial.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

### <a name="create-a-datastore-for-sample-images"></a>Criar um repositório de armazenamento para imagens de exemplo

Na conta do `pipelinedata`, obtenha o exemplo de dados públicos de avaliação do ImageNet do contêiner de BLOBs público `sampledata`. Chame `register_azure_blob_container()` para disponibilizar os dados para o espaço de trabalho sob o nome `images_datastore`. Em seguida, defina o armazenamento de datastore padrão do espaço de trabalho como o repositório de armazenamento de saída. Use o repositório de armazenamento de saída para pontuar a saída no pipeline.

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-data-objects"></a>Criar objetos de dados

Quando você cria um pipeline, um objeto de `DataReference` lê dados do armazenamento de espaço de trabalho. Um objeto `PipelineData` transfere dados intermediários entre etapas de pipeline.

> [!Important]
> O exemplo de Pontuação de lote neste tutorial usa apenas uma etapa de pipeline. Em casos de uso que têm várias etapas, o fluxo típico incluirá estas etapas:
>
> 1. Use `DataReference` objetos como *entradas* para buscar dados brutos, executar alguma transformação e, em seguida, *gerar* um objeto de `PipelineData`.
>
> 2. Use o `PipelineData` *objeto de saída* na etapa anterior como um *objeto de entrada*. Repita-o para as etapas subsequentes.

Nesse cenário, você cria `DataReference` objetos que correspondem aos diretórios de armazenamento de dados para as imagens de entrada e os rótulos de classificação (valores de teste y). Você também cria um objeto `PipelineData` para os dados de saída de Pontuação do lote.

```python
from azureml.data.data_reference import DataReference
from azureml.pipeline.core import PipelineData

input_images = DataReference(datastore=batchscore_blob, 
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download"
                            )

label_dir = DataReference(datastore=batchscore_blob, 
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download"                          
                         )

output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

## <a name="download-and-register-the-model"></a>Baixar e registrar o modelo

Baixe o modelo de Tensorflow pretreinado para usá-lo para a pontuação de lote em um pipeline. Primeiro, crie um diretório local onde você armazena o modelo. Em seguida, baixe e extraia o modelo.

```python
import os
import tarfile
import urllib.request

if not os.path.isdir("models"):
    os.mkdir("models")
    
response = urllib.request.urlretrieve("http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz", "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall("models")
```

Em seguida, registre o modelo em seu espaço de trabalho, para que você possa recuperar facilmente o modelo no processo de pipeline. Na função estática `register()`, o parâmetro `model_name` é a chave que você usa para localizar o modelo em todo o SDK.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>Criar e anexar o destino de computação remota

Os pipelines do Machine Learning não podem ser executados localmente, portanto, você os executa em recursos de nuvem ou *destinos de computação remota*. Um destino de computação remota é um ambiente de computação virtual reutilizável no qual você executa experimentos e fluxos de trabalho de aprendizado de máquina. 

Execute o código a seguir para criar um destino de [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) habilitado para GPU e, em seguida, anexe-o ao seu espaço de trabalho. Para obter mais informações sobre destinos de computação, consulte o [artigo conceitual](https://docs.microsoft.com/azure/machine-learning/service/concept-compute-target).


```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.exceptions import ComputeTargetException
compute_name = "gpu-cluster"

# checks to see if compute target already exists in workspace, else create it
try:
    compute_target = ComputeTarget(workspace=ws, name=compute_name)
except ComputeTargetException:
    config = AmlCompute.provisioning_configuration(vm_size="STANDARD_NC6",
                                                   vm_priority="lowpriority", 
                                                   min_nodes=0, 
                                                   max_nodes=1)

    compute_target = ComputeTarget.create(workspace=ws, name=compute_name, provisioning_configuration=config)
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="write-a-scoring-script"></a>Escrever um script de Pontuação

Para fazer a pontuação, crie um script de Pontuação de lote chamado `batch_scoring.py`e, em seguida, grave-o no diretório atual. O script usa imagens de entrada, aplica o modelo de classificação e, em seguida, gera as previsões para um arquivo de resultados.

O script de `batch_scoring.py` usa os seguintes parâmetros, que são passados da etapa de pipeline que você cria posteriormente neste tutorial:

- `--model_name`: o nome do modelo que está sendo usado.
- `--label_dir`: o diretório que contém o arquivo de `labels.txt`.
- `--dataset_path`: o diretório que contém as imagens de entrada.
- `--output_dir`: o diretório de saída para o arquivo de `results-label.txt` depois que o script executa o modelo nos dados.
- `--batch_size`: o tamanho do lote usado na execução do modelo.

A infraestrutura de pipeline usa a classe `ArgumentParser` para passar parâmetros em etapas de pipeline. Por exemplo, no código a seguir, o primeiro argumento `--model_name` recebe o identificador de propriedade `model_name`. Na função `main()`, `Model.get_model_path(args.model_name)` é usado para acessar essa propriedade.


```python
%%writefile batch_scoring.py

import os
import argparse
import datetime
import time
import tensorflow as tf
from math import ceil
import numpy as np
import shutil
from tensorflow.contrib.slim.python.slim.nets import inception_v3
from azureml.core.model import Model

slim = tf.contrib.slim

parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
parser.add_argument('--model_name', dest="model_name", required=True)
parser.add_argument('--label_dir', dest="label_dir", required=True)
parser.add_argument('--dataset_path', dest="dataset_path", required=True)
parser.add_argument('--output_dir', dest="output_dir", required=True)
parser.add_argument('--batch_size', dest="batch_size", type=int, required=True)

args = parser.parse_args()

image_size = 299
num_channel = 3

# create output directory if it does not exist
os.makedirs(args.output_dir, exist_ok=True)


def get_class_label_dict(label_file):
    label = []
    proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


class DataIterator:
    def __init__(self, data_dir):
        self.file_paths = []
        image_list = os.listdir(data_dir)
        self.file_paths = [data_dir + '/' + file_name.rstrip() for file_name in image_list]
        self.labels = [1 for file_name in self.file_paths]

    @property
    def size(self):
        return len(self.labels)

    def input_pipeline(self, batch_size):
        images_tensor = tf.convert_to_tensor(self.file_paths, dtype=tf.string)
        labels_tensor = tf.convert_to_tensor(self.labels, dtype=tf.int64)
        input_queue = tf.train.slice_input_producer([images_tensor, labels_tensor], shuffle=False)
        labels = input_queue[1]
        images_content = tf.read_file(input_queue[0])

        image_reader = tf.image.decode_jpeg(images_content, channels=num_channel, name="jpeg_reader")
        float_caster = tf.cast(image_reader, tf.float32)
        new_size = tf.constant([image_size, image_size], dtype=tf.int32)
        images = tf.image.resize_images(float_caster, new_size)
        images = tf.divide(tf.subtract(images, [0]), [255])

        image_batch, label_batch = tf.train.batch([images, labels], batch_size=batch_size, capacity=5 * batch_size)
        return image_batch


def main(_):
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)
    count = 0

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
        with open(out_filename, "w") as result_file:
            i = 0
            while count < total_size and not coord.should_stop():
                test_images_batch = sess.run(test_images)
                file_names_batch = test_feeder.file_paths[i * args.batch_size:
                                                          min(test_feeder.size, (i + 1) * args.batch_size)]
                results = sess.run(probabilities, feed_dict={input_images: test_images_batch})
                new_add = min(args.batch_size, total_size - count)
                count += new_add
                i += 1
                for j in range(new_add):
                    result_file.write(os.path.basename(file_names_batch[j]) + ": " + label_dict[results[j]] + "\n")
                result_file.flush()
            coord.request_stop()
            coord.join(threads)

        shutil.copy(out_filename, "./outputs/")

if __name__ == "__main__":
    tf.app.run()

```

> [!TIP]
> O pipeline neste tutorial tem apenas uma etapa e grava a saída em um arquivo. Para pipelines de várias etapas, você também usa `ArgumentParser` para definir um diretório para gravar dados de saída para entrada em etapas subsequentes. Para obter um exemplo de como passar dados entre várias etapas de pipeline usando o padrão de design `ArgumentParser`, consulte o [bloco de anotações](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb).

## <a name="build-and-run-the-pipeline"></a>Compilar e executar o pipeline

Antes de executar o pipeline, crie um objeto que defina o ambiente do Python e crie as dependências que o script de `batch_scoring.py` requer. A principal dependência necessária é Tensorflow, mas você também instala `azureml-defaults` do SDK para processos em segundo plano. Crie um objeto `RunConfiguration` usando as dependências. Além disso, especifique o suporte do Docker e do Docker-GPU.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE
from azureml.core.runconfig import CondaDependencies, RunConfiguration

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.13.1", "azureml-defaults"])

amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="parameterize-the-pipeline"></a>Parametrizar o pipeline

Defina um parâmetro personalizado para o pipeline para controlar o tamanho do lote. Depois que o pipeline é publicado e exposto por meio de um ponto de extremidade REST, todos os parâmetros configurados também são expostos. Você pode especificar parâmetros personalizados na carga JSON ao executar novamente o pipeline por meio de uma solicitação HTTP.

Crie um objeto `PipelineParameter` para habilitar esse comportamento e definir um nome e um valor padrão.

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(name="param_batch_size", default_value=20)
```

### <a name="create-the-pipeline-step"></a>Criar o passo de pipeline

Uma etapa de pipeline é um objeto que encapsula tudo o que você precisa para executar um pipeline, incluindo:

* Configurações de dependência e ambiente
* O recurso de computação no qual executar o pipeline
* Dados de entrada e saída e quaisquer parâmetros personalizados
* Referência a uma lógica de script ou SDK a ser executada durante a etapa

Várias classes herdam da classe pai [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py). Você pode escolher classes para usar estruturas ou pilhas específicas para criar uma etapa. Neste exemplo, você usa a classe [`PythonScriptStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) para definir a lógica da etapa usando um script Python personalizado. Se um argumento para o script for uma entrada para a etapa ou uma saída da etapa, o argumento deverá ser definido *tanto* na matriz de `arguments` *quanto* no parâmetro `input` ou `output`, respectivamente. 

Em cenários em que há mais de uma etapa, uma referência de objeto na matriz de `outputs` se torna disponível como uma *entrada* para uma etapa de pipeline subsequente.

```python
from azureml.pipeline.steps import PythonScriptStep

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_scoring.py",
    arguments=["--dataset_path", input_images, 
               "--model_name", "inception",
               "--label_dir", label_dir, 
               "--output_dir", output_dir, 
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config
)
```

Para obter uma lista de todas as classes que você pode usar para diferentes tipos de etapa, consulte o [pacote de etapas](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py).

### <a name="run-the-pipeline"></a>Executar o pipeline

Agora, execute o pipeline. Primeiro, crie um objeto `Pipeline` usando a referência do espaço de trabalho e a etapa do pipeline que você criou. O parâmetro `steps` é uma matriz de etapas. Nesse caso, há apenas uma etapa para a Pontuação do lote. Para criar pipelines que têm várias etapas, coloque as etapas na ordem nessa matriz.

Em seguida, use a função `Experiment.submit()` para enviar o pipeline para execução. Você também especifica o parâmetro personalizado `param_batch_size`. A função `wait_for_completion` gera logs durante o processo de compilação do pipeline. Você pode usar os logs para ver o progresso atual.

> [!IMPORTANT]
> A primeira execução do pipeline leva aproximadamente *15 minutos*. Todas as dependências devem ser baixadas, uma imagem do Docker é criada e o ambiente do Python é provisionado e criado. Executar o pipeline novamente leva significativamente menos tempo porque esses recursos são reutilizados em vez de criados. No entanto, o tempo total de execução para o pipeline depende da carga de trabalho de seus scripts e dos processos em execução em cada etapa de pipeline.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_parameters={"param_batch_size": 20})
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Baixar e examinar a saída

Execute o código a seguir para baixar o arquivo de saída que é criado a partir do script de `batch_scoring.py`. Em seguida, explore os resultados da pontuação.

```python
import pandas as pd

step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head(10)
```

<div>
<style scoped>. dataframe tbody TR th: somente de tipo {vertical-align: Middle;}

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Nome do arquivo</th>
      <th>predição</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>ILSVRC2012_val_00000102. JPEG</td>
      <td>Rhodesian Ridgeback</td>
    </tr>
    <tr>
      <td>1</td>
      <td>ILSVRC2012_val_00000103. JPEG</td>
      <td>tripod</td>
    </tr>
    <tr>
      <td>2</td>
      <td>ILSVRC2012_val_00000104. JPEG</td>
      <td>teclado de Typewriter</td>
    </tr>
    <tr>
      <td>3</td>
      <td>ILSVRC2012_val_00000105. JPEG</td>
      <td>silky terrier</td>
    </tr>
    <tr>
      <td>4</td>
      <td>ILSVRC2012_val_00000106. JPEG</td>
      <td>Vínculo do Windsor</td>
    </tr>
    <tr>
      <td>5</td>
      <td>ILSVRC2012_val_00000107. JPEG</td>
      <td>colheitar</td>
    </tr>
    <tr>
      <td>6</td>
      <td>ILSVRC2012_val_00000108. JPEG</td>
      <td>violino</td>
    </tr>
    <tr>
      <td>7</td>
      <td>ILSVRC2012_val_00000109. JPEG</td>
      <td>loudspeaker</td>
    </tr>
    <tr>
      <td>8</td>
      <td>ILSVRC2012_val_00000110. JPEG</td>
      <td>apron</td>
    </tr>
    <tr>
      <td>9</td>
      <td>ILSVRC2012_val_00000111. JPEG</td>
      <td>American Lobster</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="publish-and-run-from-a-rest-endpoint"></a>Publicar e executar de um ponto de extremidade REST

Execute o código a seguir para publicar o pipeline em seu espaço de trabalho. Em seu espaço de trabalho no Azure Machine Learning Studio, você pode ver metadados para o pipeline, incluindo histórico de execução e durações. Você também pode executar o pipeline manualmente no estúdio.

A publicação do pipeline habilita um ponto de extremidade REST que você pode usar para executar o pipeline de qualquer biblioteca HTTP em qualquer plataforma.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Para executar o pipeline do ponto de extremidade REST, você precisa de um cabeçalho de autenticação do tipo portador OAuth2. O exemplo a seguir usa a autenticação interativa (para fins de ilustração), mas para a maioria dos cenários de produção que exigem autenticação automatizada ou sem periféricos, use a autenticação de entidade de serviço conforme [descrito neste bloco de anotações](https://aka.ms/pl-restep-auth).

A autenticação da entidade de serviço envolve a criação de um *registro de aplicativo* no *Azure Active Directory*. Primeiro, você gera um segredo do cliente e, em seguida, concede *acesso à função* da entidade de serviço ao seu espaço de trabalho do Machine Learning. Use a classe [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) para gerenciar seu fluxo de autenticação. 

[`InteractiveLoginAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.interactiveloginauthentication?view=azure-ml-py) e `ServicePrincipalAuthentication` herdam de `AbstractAuthentication`. Em ambos os casos, use a função [`get_authentication_header()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.abstractauthentication?view=azure-ml-py#get-authentication-header--) da mesma maneira de buscar o cabeçalho:

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Obtenha a URL REST da propriedade `endpoint` do objeto de pipeline publicado. Você também pode encontrar a URL REST em seu espaço de trabalho no Azure Machine Learning Studio. 

Crie uma solicitação HTTP POST para o ponto de extremidade. Especifique o cabeçalho de autenticação na solicitação. Adicione um objeto de carga JSON que tem o nome do experimento e o parâmetro de tamanho do lote. Conforme observado anteriormente no tutorial, `param_batch_size` é passado para o script `batch_scoring.py` porque você o definiu como um objeto `PipelineParameter` na configuração da etapa.

Faça a solicitação para disparar a execução. Inclua o código para acessar a chave de `Id` do dicionário de resposta para obter o valor da ID de execução.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
run_id = response.json()["Id"]
```

Use a ID de execução para monitorar o status da nova execução. A nova execução leva mais de 10-15 minutos para ser concluída. 

A nova execução será semelhante ao pipeline executado anteriormente no tutorial. Você pode optar por não exibir a saída completa.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Limpar recursos

Não conclua esta seção se você planeja executar outros tutoriais de Azure Machine Learning.

### <a name="stop-the-notebook-vm"></a>Parar a VM do notebook

[!INCLUDE [aml-stop-server](../../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Excluir tudo

Se você não planeja usar os recursos que criou, exclua-os, para não incorrer em nenhum encargo:

1. No portal do Azure, no menu à esquerda, selecione **grupos de recursos**.
1. Na lista de grupos de recursos, selecione o grupo de recursos que você criou.
1. Selecione **Eliminar grupo de recursos**.
1. Insira o nome do grupo de recursos. Em seguida, selecione **excluir**.

Você também pode manter o grupo de recursos, mas excluir um único espaço de trabalho. Exiba as propriedades do espaço de trabalho e, em seguida, selecione **excluir**.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial de pipelines do Machine Learning, você fez as seguintes tarefas:

> [!div class="checklist"]
> * Criou um pipeline com dependências de ambiente para execução em um recurso de computação de GPU remota.
> * Criou um script de Pontuação para executar previsões de lote usando um modelo de Tensorflow pretreinado.
> * Publicou um pipeline e o habilitou para ser executado de um ponto de extremidade REST.

Para obter mais exemplos de como criar pipelines usando o SDK do Machine Learning, consulte o [repositório do bloco de anotações](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines).
