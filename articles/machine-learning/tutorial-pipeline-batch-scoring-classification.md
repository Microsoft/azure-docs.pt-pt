---
title: 'Tutorial: Gasodutos ML para pontuação de lotes'
titleSuffix: Azure Machine Learning
description: Neste tutorial, você constrói um pipeline de machine learning para executar pontuação de lotes num modelo de classificação de imagem. A Azure Machine Learning permite-lhe focar-se na aprendizagem automática em vez de infraestrutura e automação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: lobrien
ms.author: laobri
ms.reviewer: laobri
ms.date: 03/11/2020
ms.custom: contperfq4, devx-track-python
ms.openlocfilehash: f02638acdc60bba710280dcbeba0717173ed8b83
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320511"
---
# <a name="tutorial-build-an-azure-machine-learning-pipeline-for-batch-scoring"></a>Tutorial: Construir um pipeline de aprendizagem automática Azure para pontuação de lotes



Neste tutorial avançado, você aprende a construir um [pipeline Azure Machine Learning](concept-ml-pipelines.md) para executar um trabalho de pontuação de lote. Os oleodutos de aprendizagem automática otimizam o seu fluxo de trabalho com velocidade, portabilidade e reutilização, para que possa focar-se na aprendizagem automática em vez de infraestrutura e automação. Depois de construir e publicar um oleoduto, configura um ponto final REST que pode utilizar para acionar o pipeline a partir de qualquer biblioteca HTTP em qualquer plataforma. 

O exemplo utiliza um modelo de rede neural convolucional [inception-V3 pré-treinado](https://arxiv.org/abs/1512.00567) implementado em Tensorflow para classificar imagens não identificadas. 

Neste tutorial, vai concluir as seguintes tarefas:

> [!div class="checklist"]
> * Configurar a área de trabalho 
> * Descarregue e guarde dados de amostras
> * Criar objetos de conjunto de dados para obter e produzir dados
> * Faça o download, prepare e registe o modelo no seu espaço de trabalho
> * Provisão de metas de cálculo e criar um script de pontuação
> * Use a `ParallelRunStep` classe para pontuar em lotes async
> * Construa, corra e publique um oleoduto
> * Ativar um ponto final REST para o gasoduto

Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning.](https://aka.ms/AMLFree)

## <a name="prerequisites"></a>Pré-requisitos

* Se ainda não tiver um espaço de trabalho de aprendizagem automática Azure ou uma máquina virtual de portátil, complete [a Parte 1 do tutorial de configuração](tutorial-1st-experiment-sdk-setup.md).
* Quando terminar o tutorial de configuração, utilize o mesmo servidor de caderno para abrir os *tutoriais/machine-learning-pipelines-advanced/tutorial-pipeline-batch-scoreing-classification.ipynb.*

Se quiser executar o tutorial de configuração no seu próprio [ambiente local,](how-to-configure-environment.md#local)pode aceder ao tutorial no [GitHub.](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) Corra `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` para obter os pacotes necessários.

## <a name="configure-workspace-and-create-a-datastore"></a>Configure o espaço de trabalho e crie uma datastore

Crie um objeto de espaço de trabalho a partir do espaço de trabalho de Aprendizagem da Máquina de Azure existente.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

> [!IMPORTANT]
> Este código de corte espera que a configuração do espaço de trabalho seja guardada no diretório atual ou no seu progenitor. Para obter mais informações sobre a criação de um espaço de trabalho, consulte [Criar e gerir espaços de trabalho de Aprendizagem automática Azure.](how-to-manage-workspace.md) Para obter mais informações sobre a poupança da configuração para arquivar, consulte [Criar um ficheiro de configuração do espaço de trabalho](how-to-configure-environment.md#workspace).

## <a name="create-a-datastore-for-sample-images"></a>Criar uma loja de dados para imagens de amostras

Por `pipelinedata` conta, obtenha a amostra de dados públicos de avaliação ImageNet do `sampledata` recipiente de bolhas públicas. Ligue `register_azure_blob_container()` para disponibilizar os dados ao espaço de trabalho com o nome `images_datastore` . Em seguida, desafine a loja de dados predefinido do espaço de trabalho como a datas de saída. Utilize a loja de dados de saída para obter a saída no oleoduto.

Para obter mais informações sobre o acesso aos dados, consulte [Como aceder aos dados.](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#python-sdk)

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-dataset-objects"></a>Criar objetos de conjunto de dados

Ao construir oleodutos, `Dataset` os objetos são usados para ler dados a partir de datas do espaço de trabalho, e `PipelineData` os objetos são usados para transferir dados intermédios entre etapas de gasoduto.

> [!Important]
> O exemplo de pontuação do lote neste tutorial utiliza apenas um passo de pipeline. Em casos de utilização que tenham múltiplos passos, o fluxo típico incluirá estes passos:
>
> 1. Use `Dataset` os objetos como entradas para recolher *dados brutos,* realizar alguma transformação e, em seguida, *desafetar* um `PipelineData` objeto.
>
> 2. Utilize o `PipelineData` *objeto de saída* no passo anterior como objeto de *entrada*. Repita-o para os passos seguintes.

Neste cenário, `Dataset` cria-se objetos que correspondem aos diretórios de datas-loja tanto para as imagens de entrada como para as etiquetas de classificação (valores de teste y). Também cria um `PipelineData` objeto para os dados de saída de pontuação do lote.

```python
from azureml.core.dataset import Dataset
from azureml.pipeline.core import PipelineData

input_images = Dataset.File.from_files((batchscore_blob, "batchscoring/images/"))
label_ds = Dataset.File.from_files((batchscore_blob, "batchscoring/labels/"))
output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

Em seguida, registe os conjuntos de dados para o espaço de trabalho.

```python

input_images = input_images.register(workspace = ws, name = "input_images")
label_ds = label_ds.register(workspace = ws, name = "label_ds")
```

## <a name="download-and-register-the-model"></a>Faça o download e registe o modelo

Descarregue o modelo de Tensorflow pré-treinado para o utilizar para a pontuação do lote num oleoduto. Primeiro, crie um diretório local onde guarde o modelo. Em seguida, descarregue e extraia o modelo.

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

Em seguida, registe o modelo no seu espaço de trabalho, para que possa facilmente recuperar o modelo no processo de pipeline. Na `register()` função estática, o `model_name` parâmetro é a chave que utiliza para localizar o seu modelo em todo o SDK.

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>Criar e anexar o alvo de computação remota

Os oleodutos de aprendizagem automática não podem ser executados localmente, por isso executá-los em recursos em nuvem ou *alvos de computação remota.* Um alvo de computação remota é um ambiente de computação virtual reutilizável onde você executa experiências e fluxos de trabalho de aprendizagem automática. 

Executar o seguinte código para criar um alvo ativado pela GPU [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py&preserve-view=true) e, em seguida, anexá-lo ao seu espaço de trabalho. Para obter mais informações sobre metas de computação, consulte o [artigo conceptual.](https://docs.microsoft.com/azure/machine-learning/concept-compute-target)


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

Para fazer a pontuação, crie um script de pontuação de lote chamado `batch_scoring.py` , e, em seguida, escreva-o para o diretório atual. O script tira imagens de entrada, aplica o modelo de classificação e, em seguida, faz as previsões para um ficheiro de resultados.

O `batch_scoring.py` script toma os seguintes parâmetros, que são passados a partir do que cria mais `ParallelRunStep` tarde:

- `--model_name`: O nome do modelo a ser utilizado.
- `--labels_dir`: A localização do `labels.txt` ficheiro.

A infraestrutura do gasoduto utiliza a `ArgumentParser` classe para passar parâmetros em passos de gasoduto. Por exemplo, no seguinte código, o primeiro argumento `--model_name` é dado ao identificador de `model_name` propriedade. Na `init()` função, `Model.get_model_path(args.model_name)` é utilizado para aceder a esta propriedade.


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

from azureml.core import Run
from azureml.core.model import Model
from azureml.core.dataset import Dataset

slim = tf.contrib.slim

image_size = 299
num_channel = 3


def get_class_label_dict(labels_dir):
    label = []
    labels_path = os.path.join(labels_dir, 'labels.txt')
    proto_as_ascii_lines = tf.gfile.GFile(labels_path).readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


def init():
    global g_tf_sess, probabilities, label_dict, input_images

    parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
    parser.add_argument('--model_name', dest="model_name", required=True)
    parser.add_argument('--labels_dir', dest="labels_dir", required=True)
    args, _ = parser.parse_known_args()

    label_dict = get_class_label_dict(args.labels_dir)
    classes_num = len(label_dict)

    with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
        input_images = tf.placeholder(tf.float32, [1, image_size, image_size, num_channel])
        logits, _ = inception_v3.inception_v3(input_images,
                                              num_classes=classes_num,
                                              is_training=False)
        probabilities = tf.argmax(logits, 1)

    config = tf.ConfigProto()
    config.gpu_options.allow_growth = True
    g_tf_sess = tf.Session(config=config)
    g_tf_sess.run(tf.global_variables_initializer())
    g_tf_sess.run(tf.local_variables_initializer())

    model_path = Model.get_model_path(args.model_name)
    saver = tf.train.Saver()
    saver.restore(g_tf_sess, model_path)


def file_to_tensor(file_path):
    image_string = tf.read_file(file_path)
    image = tf.image.decode_image(image_string, channels=3)

    image.set_shape([None, None, None])
    image = tf.image.resize_images(image, [image_size, image_size])
    image = tf.divide(tf.subtract(image, [0]), [255])
    image.set_shape([image_size, image_size, num_channel])
    return image


def run(mini_batch):
    result_list = []
    for file_path in mini_batch:
        test_image = file_to_tensor(file_path)
        out = g_tf_sess.run(test_image)
        result = g_tf_sess.run(probabilities, feed_dict={input_images: [out]})
        result_list.append(os.path.basename(file_path) + ": " + label_dict[result[0]])
    return result_list
```

> [!TIP]
> O oleoduto neste tutorial tem apenas um passo, e escreve a saída para um ficheiro. Para os oleodutos em várias etapas, também utiliza `ArgumentParser` para definir um diretório para escrever dados de saída para entrada em etapas subsequentes. Para um exemplo de passagem de dados entre várias etapas de gasoduto utilizando o `ArgumentParser` padrão de design, consulte o [caderno](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb).

## <a name="build-the-pipeline"></a>Construir o oleoduto

Antes de executar o oleoduto, crie um objeto que defina o ambiente Python e crie as dependências que o seu `batch_scoring.py` script requer. A principal dependência necessária é o Tensorflow, mas também instala `azureml-core` e que é exigido pela `azureml-dataprep[fuse]` ParallelRunStep. Além disso, especifique o apoio do Docker e do Docker-GPU.

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.15.2",
                                            "azureml-core", "azureml-dataprep[fuse]"])
env = Environment(name="parallelenv")
env.python.conda_dependencies = cd
env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="create-the-configuration-to-wrap-the-script"></a>Crie a configuração para embrulhar o script

Crie o passo do pipeline utilizando o script, a configuração do ambiente e os parâmetros. Especifique o alvo de cálculo que já anexou ao seu espaço de trabalho.

```python
from azureml.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    environment=env,
    entry_script="batch_scoring.py",
    source_directory=".",
    output_action="append_row",
    mini_batch_size="20",
    error_threshold=1,
    compute_target=compute_target,
    process_count_per_node=2,
    node_count=1
)
```

### <a name="create-the-pipeline-step"></a>Criar o passo do gasoduto

Um passo de oleoduto é um objeto que encapsula tudo o que precisa para executar um oleoduto, incluindo:

* Ambiente e definições de dependência
* O recurso computacional para executar o oleoduto em
* Dados de entrada e saída, e quaisquer parâmetros personalizados
* Referência a um script ou lógica SDK para executar durante o passo

Várias classes herdam da classe dos [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py&preserve-view=true) pais. Pode escolher aulas para usar quadros ou pilhas específicos para construir um passo. Neste exemplo, você usa a `ParallelRunStep` classe para definir a sua lógica de passo usando um script Python personalizado. Se um argumento para o seu script for uma entrada para o passo ou uma saída do passo, o argumento deve ser definido *tanto* na matriz como `arguments` no *and* `input` `output` parâmetro, respectivamente. 

Em cenários em que há mais de um passo, uma referência de objeto na `outputs` matriz torna-se disponível como *entrada* para um passo de pipeline subsequente.

```python
from azureml.pipeline.steps import ParallelRunStep
from datetime import datetime

parallel_step_name = "batchscoring-" + datetime.now().strftime("%Y%m%d%H%M")

label_config = label_ds.as_named_input("labels_input")

batch_score_step = ParallelRunStep(
    name=parallel_step_name,
    inputs=[input_images.as_named_input("input_images")],
    output=output_dir,
    arguments=["--model_name", "inception",
               "--labels_dir", label_config],
    side_inputs=[label_config],
    parallel_run_config=parallel_run_config,
    allow_reuse=False
)
```

Para obter uma lista de todas as classes que pode utilizar para diferentes tipos de passos, consulte o [pacote de passos.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py&preserve-view=true)

## <a name="submit-the-pipeline"></a>Enviar o oleoduto

Agora, corre o oleoduto. Em primeiro lugar, crie um `Pipeline` objeto utilizando a referência do seu espaço de trabalho e o passo de pipeline que criou. O `steps` parâmetro é uma variedade de passos. Neste caso, só há um passo para a pontuação do lote. Para construir oleodutos que tenham múltiplos passos, coloque os passos em ordem nesta matriz.

Em seguida, utilize a `Experiment.submit()` função para submeter o gasoduto para execução. Os `wait_for_completion` registos de saídas de função durante o processo de construção do gasoduto. Pode usar os registos para ver o progresso atual.

> [!IMPORTANT]
> O primeiro gasoduto demora cerca de *15 minutos*. Todas as dependências devem ser descarregadas, uma imagem docker é criada, e o ambiente Python é provisionado e criado. A execução do gasoduto de novo demora significativamente menos tempo porque esses recursos são reutilizados em vez de serem criados. No entanto, o tempo total de funcionamento do gasoduto depende da carga de trabalho dos seus scripts e dos processos que estão a decorrer em cada passo do pipeline.

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline)
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>Baixar e rever saída

Execute o seguinte código para descarregar o ficheiro de saída que é criado a partir do `batch_scoring.py` script. Então, explore os resultados da pontuação.

```python
import pandas as pd

batch_run = next(pipeline_run.get_children())
batch_output = batch_run.get_output_data("scores")
batch_output.download(local_path="inception_results")

for root, dirs, files in os.walk("inception_results"):
    for file in files:
        if file.endswith("parallel_run_step.txt"):
            result_file = os.path.join(root, file)

df = pd.read_csv(result_file, delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
print("Prediction has ", df.shape[0], " rows")
df.head(10)
```

## <a name="publish-and-run-from-a-rest-endpoint"></a>Publicar e correr a partir de um ponto final REST

Execute o seguinte código para publicar o pipeline no seu espaço de trabalho. No seu espaço de trabalho no estúdio Azure Machine Learning, pode ver metadados para o pipeline, incluindo história de execução e durações. Também pode executar o gasoduto manualmente a partir do estúdio.

A publicação do pipeline permite um ponto final REST que pode utilizar para executar o gasoduto a partir de qualquer biblioteca HTTP em qualquer plataforma.

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

Para executar o gasoduto a partir do ponto final REST, precisa de um cabeçalho de autenticação do tipo Portador OAuth2. O exemplo a seguir utiliza a autenticação interativa (para fins de ilustração), mas para a maioria dos cenários de produção que exijam autenticação automatizada ou sem cabeça, utilize a autenticação principal do serviço, conforme [descrito neste artigo.](how-to-setup-authentication.md)

A autenticação principal do serviço envolve a criação de um *Registo de Aplicações* em *Diretório Ativo Azure.* Primeiro, gera um segredo de cliente, e depois concede ao seu serviço *o acesso* ao seu espaço de trabalho de aprendizagem automática. Utilize a [`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py&preserve-view=true) classe para gerir o seu fluxo de autenticação. 

Ambos [`InteractiveLoginAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.interactiveloginauthentication?view=azure-ml-py&preserve-view=true) e `ServicePrincipalAuthentication` herdar `AbstractAuthentication` de. Em ambos os casos, utilize a [`get_authentication_header()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.abstractauthentication?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-authentication-header--) função da mesma forma para ir buscar o cabeçalho:

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

Obtenha o URL REST da `endpoint` propriedade do objeto de gasoduto publicado. Também pode encontrar o URL REST no seu espaço de trabalho no estúdio Azure Machine Learning. 

Construa um pedido HTTP POST para o ponto final. Especifique o seu cabeçalho de autenticação no pedido. Adicione um objeto de carga JSON que tenha o nome da experiência.

Faça o pedido para desencadear a fuga. Inclua código para aceder à `Id` chave do dicionário de resposta para obter o valor do ID de execução.

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"process_count_per_node": 6}})
run_id = response.json()["Id"]
```

Utilize o ID de execução para monitorizar o estado da nova execução. A nova corrida leva mais 10-15 minutos para terminar. 

A nova corrida será semelhante ao oleoduto que correu mais cedo no tutorial. Pode optar por não visualizar a saída completa.

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>Limpar os recursos

Não complete esta secção se planeia executar outros tutoriais de Aprendizagem automática Azure.

### <a name="stop-the-compute-instance"></a>Pare a instância computacional

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Apagar tudo

Se não planeia utilizar os recursos que criou, elimine-os, para não incorrer em quaisquer encargos:

1. No portal Azure, no menu esquerdo, selecione **Grupos de Recursos**.
1. Na lista de grupos de recursos, selecione o grupo de recursos que criou.
1. Selecione **Eliminar grupo de recursos**.
1. Insira o nome do grupo de recursos. Em seguida, **selecione Delete**.

Também pode manter o grupo de recursos, mas eliminar um único espaço de trabalho. Mostrar as propriedades do espaço de trabalho e, em seguida, selecionar **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial de gasodutos de aprendizagem automática, fez as seguintes tarefas:

> [!div class="checklist"]
> * Construiu um oleoduto com dependências ambientais para funcionar com um recurso de cálculo remoto da GPU.
> * Criei um script de pontuação para executar previsões de lote usando um modelo de tensorflow pré-treinado.
> * Publicou um oleoduto e permitiu que fosse executado a partir de um ponto final REST.

Para obter mais exemplos de como construir oleodutos utilizando o SDK de aprendizagem automática, consulte o [repositório de cadernos.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)
