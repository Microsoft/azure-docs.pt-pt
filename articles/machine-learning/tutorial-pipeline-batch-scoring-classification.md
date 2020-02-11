---
title: 'Tutorial: oleodutos ML para pontuação de lotes'
titleSuffix: Azure Machine Learning
description: Neste tutorial, você constrói um pipeline de machine learning para executar pontuação de lote em um modelo de classificação de imagem em Azure Machine Learning. Os gasodutos de aprendizagem automática otimizam o seu fluxo de trabalho com velocidade, portabilidade e reutilização, para que possa focar-se na sua experiência - machine learning - em vez de em infraestruturas e automação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 02/10/2020
ms.openlocfilehash: 3dc0af3f0d1236e902f6fa845fae95e3f2a500d1
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77116462"
---
# <a name="tutorial-build-an-azure-machine-learning-pipeline-for-batch-scoring"></a>Tutorial: Construa um oleoduto azure machine learning para pontuação de lotes

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste tutorial, você usa um oleoduto em Azure Machine Learning para executar um trabalho de pontuação de lote. O exemplo utiliza o modelo de rede neural convolucional [De início-V3 pré-treinado](https://arxiv.org/abs/1512.00567) Tensorflow para classificar imagens não marcadas. Depois de construir e publicar um pipeline, configura um ponto final REST que pode utilizar para acionar o pipeline a partir de qualquer biblioteca HTTP em qualquer plataforma.

Os gasodutos de aprendizagem automática otimizam o seu fluxo de trabalho com velocidade, portabilidade e reutilização, para que possa focar-se na sua experiência - machine learning - em vez de em infraestruturas e automação. [Saiba mais sobre os gasodutos de aprendizagem automática.](concept-ml-pipelines.md)

Neste tutorial, vai concluir as seguintes tarefas:

> [!div class="checklist"]
> * Configure o espaço de trabalho e descarregue os dados da amostra
> * Criar objetos de dados para recolher e obter dados
> * Descarregue, prepare e registe o modelo no seu espaço de trabalho
> * Objetivos de computação de provisão e criar um roteiro de pontuação
> * Construir, correr e publicar um oleoduto
> * Ativar um ponto final DE REPOUSO para o gasoduto

Se não tiver uma subscrição Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

## <a name="prerequisites"></a>Pré-requisitos

* Se ainda não tiver um espaço de trabalho azure Machine Learning ou uma máquina virtual de caderno, complete a [Parte 1 do tutorial de configuração](tutorial-1st-experiment-sdk-setup.md).
* Quando terminar o tutorial de configuração, utilize o mesmo servidor de portátil para abrir os *tutoriais/machine-learning-pipeline-pipeline-pipeline-batch-scoring-classification.ipynb.*

Se quiser executar o tutorial de configuração no seu próprio [ambiente local,](how-to-configure-environment.md#local)pode aceder ao tutorial no [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials). Faça `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` para obter os pacotes necessários.

## <a name="configure-workspace-and-create-a-datastore"></a>Configure espaço de trabalho e crie uma loja de dados

Crie um objeto espaço de trabalho a partir do espaço de trabalho de Aprendizagem automática Azure existente.

- Um [espaço de trabalho](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) é uma classe que aceita a sua informação de subscrição azure e recursos. O espaço de trabalho também cria um recurso em nuvem que pode usar para monitorizar e rastrear as execuções do seu modelo. 
- `Workspace.from_config()` lê o ficheiro `config.json` e, em seguida, coloca os detalhes da autenticação num objeto chamado `ws`. O objeto `ws` é usado no código ao longo deste tutorial.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

### <a name="create-a-datastore-for-sample-images"></a>Criar uma loja de dados para imagens de amostra

Na conta `pipelinedata`, obtenha a amostra de dados públicos de avaliação imageNet do `sampledata` recipiente de blob público. Ligue `register_azure_blob_container()` para disponibilizar os dados ao espaço de trabalho com o nome `images_datastore`. Em seguida, detete tede o espaço de trabalho padrão datastore como a loja de dados de saída. Utilize a loja de dados de saída para marcar a saída no pipeline.

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

Quando se constrói um pipeline, um `DataReference` objeto lê dados da loja de dados do espaço de trabalho. Um objeto `PipelineData` transfere dados intermédios entre as etapas do gasoduto.

> [!Important]
> O exemplo de pontuação do lote neste tutorial usa apenas um passo de oleoduto. Nos casos de utilização que têm múltiplas etapas, o fluxo típico incluirá estes passos:
>
> 1. Use `DataReference` *objetos como inputs* para obter dados brutos, realizar alguma transformação e, em seguida, *obter* um objeto `PipelineData`.
>
> 2. Utilize o objeto de *saída* `PipelineData` no passo anterior como objeto de *entrada*. Repita-o para os passos seguintes.

Neste cenário, cria-se `DataReference` objetos que correspondam aos diretórios da datastore tanto para as imagens de entrada como para as etiquetas de classificação (valores de teste y). Também cria um `PipelineData` objeto para os dados de saída de pontuação do lote.

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

## <a name="download-and-register-the-model"></a>Descarregue e registe o modelo

Descarregue o modelo tensorflow pré-treinado para usá-lo para pontuação de lote num pipeline. Primeiro, crie um diretório local onde guarde o modelo. Em seguida, faça o download e extrate o modelo.

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

Em seguida, registe o modelo no seu espaço de trabalho, para que possa recuperar facilmente o modelo no processo do gasoduto. Na função estática `register()`, o parâmetro `model_name` é a chave que utiliza para localizar o seu modelo em todo o SDK.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>Criar e fixar o alvo de computação remota

Os gasodutos de aprendizagem automática não podem ser executados localmente, por isso executa-os em recursos em nuvem ou *alvos de computação remota.* Um alvo de computação remota é um ambiente de computação virtual reutilizável onde você executa experiências e fluxos de trabalho de aprendizagem automática. 

Execute o seguinte código para criar um alvo [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) ativado por GPU e, em seguida, anexe-o ao seu espaço de trabalho. Para obter mais informações sobre alvos de cálculo, consulte o [artigo conceptual](https://docs.microsoft.com/azure/machine-learning/concept-compute-target).


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

## <a name="write-a-scoring-script"></a>Escreva um roteiro de pontuação

Para fazer a pontuação, crie um roteiro de pontuação de lote chamado `batch_scoring.py`, e depois escreva-o para o atual diretório. O script tira imagens de entrada, aplica o modelo de classificação e, em seguida, produz as previsões para um ficheiro de resultados.

O roteiro `batch_scoring.py` toma os seguintes parâmetros, que são passados do passo do oleoduto que cria mais tarde neste tutorial:

- `--model_name`: O nome do modelo que está a ser utilizado.
- `--label_dir`: O diretório que detém o ficheiro `labels.txt`.
- `--dataset_path`: O diretório que contém as imagens de entrada.
- `--output_dir`: O diretório de saída do ficheiro `results-label.txt` após o script executa o modelo nos dados.
- `--batch_size`: O tamanho do lote utilizado na execução do modelo.

A infraestrutura do gasoduto utiliza a classe `ArgumentParser` para passar parâmetros em passos de gasoduto. Por exemplo, no seguinte código, o primeiro argumento `--model_name` é dado ao identificador de propriedade `model_name`. Na função `main()`, `Model.get_model_path(args.model_name)` é usado para aceder a esta propriedade.


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
> O oleoduto neste tutorial tem apenas um passo, e escreve a saída para um ficheiro. Para gasodutos em várias etapas, também utiliza `ArgumentParser` para definir um diretório para escrever dados de saída para entrada em etapas posteriores. Para um exemplo de passagem de dados entre vários passos de pipeline utilizando o padrão de design `ArgumentParser`, consulte o [caderno](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb).

## <a name="build-and-run-the-pipeline"></a>Construir e executar o oleoduto

Antes de executar o oleoduto, crie um objeto que defina o ambiente Python e crie as dependências que o seu `batch_scoring.py` script requer. A principal dependência necessária é a Tensorflow, mas também instala `azureml-defaults` do SDK para processos de fundo. Crie um objeto `RunConfiguration` usando as dependências. Além disso, especifique o apoio do Docker e do Docker-GPU.

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE
azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.13.1", "azureml-defaults"])

amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="parameterize-the-pipeline"></a>Parametrize o oleoduto

Defina um parâmetro personalizado para o gasoduto controlar o tamanho do lote. Após a publicação e exposição do gasoduto através de um ponto final REST, os parâmetros configurados também são expostos. Pode especificar parâmetros personalizados na carga útil JSON quando reproduzir o gasoduto através de um pedido HTTP.

Crie um objeto `PipelineParameter` para ativar este comportamento e definir um nome e valor predefinido.

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(name="param_batch_size", default_value=20)
```

### <a name="create-the-pipeline-step"></a>Criar o passo de pipeline

Um passo de oleoduto é um objeto que encapsula tudo o que precisa para executar um oleoduto, incluindo:

* Ambiente e regulação da dependência
* O recurso computacional para executar o oleoduto em
* Dados de entrada e saída, e quaisquer parâmetros personalizados
* Referência a uma lógica de script ou SDK para executar durante o passo

Várias classes herdam da classe dos pais [`PipelineStep`. ](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) Você pode escolher aulas para usar quadros ou pilhas específicos para construir um passo. Neste exemplo, você usa a classe [`PythonScriptStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) para definir a sua lógica de passo usando um script python personalizado. Se um argumento para o seu script for uma entrada para o passo ou uma saída do passo, o argumento deve ser definido *tanto* na matriz `arguments` *como* no `input` ou no parâmetro `output`, respectivamente. 

Nos cenários em que há mais de um passo, uma referência de objeto na matriz de `outputs` fica disponível como *entrada* para um passo de pipeline subsequente.

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

Para uma lista de todas as classes que pode utilizar para diferentes tipos de passos, consulte o [pacote de passos](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py).

### <a name="run-the-pipeline"></a>Executar o pipeline

Agora, corre o oleoduto. Em primeiro lugar, crie um objeto `Pipeline` utilizando a referência do espaço de trabalho e o passo do gasoduto que criou. O parâmetro `steps` é uma variedade de passos. Neste caso, só há um passo para marcar lotes. Para construir oleodutos com vários passos, coloque os passos em ordem nesta matriz.

Em seguida, utilize a função `Experiment.submit()` para submeter o gasoduto para execução. Especifica também o parâmetro personalizado `param_batch_size`. A função `wait_for_completion` os registos de saída durante o processo de construção do gasoduto. Pode utilizar os registos para ver o progresso atual.

> [!IMPORTANT]
> A primeira execução do gasoduto demora cerca de *15 minutos*. Todas as dependências devem ser descarregadas, uma imagem do Docker é criada, e o ambiente Python é aprovisionado e criado. A execução do gasoduto volta a demorar significativamente menos tempo, porque esses recursos são reutilizados em vez de serem criados. No entanto, o tempo total de execução para o gasoduto depende da carga de trabalho dos seus scripts e dos processos que estão a decorrer em cada passo do gasoduto.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_parameters={"param_batch_size": 20})
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Descarregar e rever saída

Execute o seguinte código para descarregar o ficheiro de saída que é criado a partir do script `batch_scoring.py`. Depois, explore os resultados das pontuações.

```python
import pandas as pd

step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head(10)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

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
      <th>Nome de ficheiro</th>
      <th>predição</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>ILSVRC2012_val_00000102.JPEG</td>
      <td>Cume da Rodésia</td>
    </tr>
    <tr>
      <td>1</td>
      <td>ILSVRC2012_val_00000103.JPEG</td>
      <td>tripé</td>
    </tr>
    <tr>
      <td>2</td>
      <td>ILSVRC2012_val_00000104.JPEG</td>
      <td>teclado de máquina de escrever</td>
    </tr>
    <tr>
      <td>3</td>
      <td>ILSVRC2012_val_00000105.JPEG</td>
      <td>terrier sedoso</td>
    </tr>
    <tr>
      <td>4</td>
      <td>ILSVRC2012_val_00000106.JPEG</td>
      <td>Gravata Windsor</td>
    </tr>
    <tr>
      <td>5</td>
      <td>ILSVRC2012_val_00000107.JPEG</td>
      <td>ceifeiro</td>
    </tr>
    <tr>
      <td>6</td>
      <td>ILSVRC2012_val_00000108.JPEG</td>
      <td>violino</td>
    </tr>
    <tr>
      <td>7</td>
      <td>ILSVRC2012_val_00000109.JPEG</td>
      <td>altifalante</td>
    </tr>
    <tr>
      <td>8</td>
      <td>ILSVRC2012_val_00000110.JPEG</td>
      <td>avental</td>
    </tr>
    <tr>
      <td>9</td>
      <td>ILSVRC2012_val_00000111.JPEG</td>
      <td>Lagosta americana</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="publish-and-run-from-a-rest-endpoint"></a>Publicar e executar a partir de um ponto final REST

Execute o seguinte código para publicar o gasoduto no seu espaço de trabalho. No seu espaço de trabalho no estúdio Azure Machine Learning, pode ver metadados para o pipeline, incluindo histórico de execução e durações. Também pode executar o gasoduto manualmente a partir do estúdio.

A publicação do pipeline permite um ponto final REST que pode utilizar para executar o pipeline a partir de qualquer biblioteca HTTP em qualquer plataforma.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Para executar o gasoduto a partir do ponto final REST, você precisa de um cabeçalho de autenticação tipo OAuth2. O exemplo seguinte utiliza a autenticação interativa (para fins de ilustração), mas para a maioria dos cenários de produção que requerem autenticação automatizada ou sem cabeça, utilize a autenticação principal do serviço [como descrito neste caderno.](https://aka.ms/pl-restep-auth)

A autenticação principal do serviço envolve a criação de um Registo de *Aplicações* no *Diretório Ativo Azure.* Primeiro, gera um segredo de cliente e depois concede o seu *papel* principal de serviço ao seu espaço de trabalho de aprendizagem automática. Utilize a classe [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) para gerir o seu fluxo de autenticação. 

Tanto [`InteractiveLoginAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.interactiveloginauthentication?view=azure-ml-py) como `ServicePrincipalAuthentication` herdam de `AbstractAuthentication`. Em ambos os casos, utilize a [função`get_authentication_header()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.abstractauthentication?view=azure-ml-py#get-authentication-header--) da mesma forma para obter o cabeçalho:

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Obtenha o URL REST da propriedade `endpoint` do objeto de pipeline publicado. Também pode encontrar o URL REST no seu espaço de trabalho no estúdio Azure Machine Learning. 

Construa um pedido HTTP POST para o ponto final. Especifique o seu cabeçalho de autenticação no pedido. Adicione um objeto de carga útil JSON que tenha o nome da experiência e o parâmetro do tamanho do lote. Como notado anteriormente no tutorial, `param_batch_size` é passado para o seu roteiro de `batch_scoring.py` porque o definiu como um objeto `PipelineParameter` na configuração do passo.

Faça o pedido para desencadear a corrida. Inclua código para aceder à chave `Id` do dicionário de resposta para obter o valor do ID de execução.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
run_id = response.json()["Id"]
```

Utilize o ID de execução para monitorizar o estado da nova execução. A nova corrida leva mais 10-15 min para terminar. 

A nova corrida será semelhante ao oleoduto que correu mais cedo no tutorial. Pode optar por não ver a saída completa.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Limpar recursos

Não complete esta secção se planeia executar outros tutoriais de Aprendizagem automática azure.

### <a name="stop-the-compute-instance"></a>Pare a instância computacional

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Apagar tudo

Se não planeia usar os recursos que criou, elimine-os, para que não incorra em quaisquer acusações:

1. No portal Azure, no menu esquerdo, selecione **Grupos de Recursos.**
1. Na lista de grupos de recursos, selecione o grupo de recursos que criou.
1. Selecione **Eliminar grupo de recursos**.
1. Insira o nome do grupo de recursos. Em seguida, selecione **Delete**.

Também pode manter o grupo de recursos, mas eliminar um único espaço de trabalho. Mostrar as propriedades do espaço de trabalho e, em seguida, selecionar **Apagar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial de pipelines de aprendizagem automática, fez as seguintes tarefas:

> [!div class="checklist"]
> * Construiu um oleoduto com dependências ambientais para funcionar com um recurso de computação GPU remoto.
> * Criou um script de pontuação para executar previsões de lote usando um modelo tenso tenso.
> * Publicou um oleoduto e permitiu que fosse executado a partir de um ponto final REST.

Para mais exemplos de como construir oleodutos utilizando o SDK de aprendizagem automática, consulte o [repositório](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)de cadernos .
