---
title: Implementar um modelo para inferência com GPU
titleSuffix: Azure Machine Learning
description: Este artigo ensina-o a usar a Azure Machine Learning para implementar um modelo de aprendizagem profunda com fluxo de GPU como serviço web.service e pedidos de inferência de pontuação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 6797c32ded5c12bbac3fafa1eabd1e6f74d28e07
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519253"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Implementar um modelo de aprendizagem profunda para inferência com GPU


Este artigo ensina-o a usar a Azure Machine Learning para implementar um modelo ativado pela GPU como um serviço web. A informação neste artigo baseia-se na implementação de um modelo no Serviço Azure Kubernetes (AKS). O cluster AKS fornece um recurso GPU que é usado pelo modelo para inferência.

Inferência, ou pontuação de modelos, é a fase em que o modelo implantado é usado para fazer previsões. A utilização de GPUs em vez de CPUs oferece vantagens de desempenho em cálculos altamente paralizáveis.

> [!IMPORTANT]
> Para implementações de serviço web, a inferência gpu só é suportada no Serviço Azure Kubernetes. Para inferência utilizando um __pipeline de aprendizagem automática,__ as GPUs são suportadas apenas no Azure Machine Learning Compute. Para obter mais informações sobre a utilização de gasodutos ML, consulte [Tutorial: Construa um pipeline Azure Machine Learning para pontuação de lotes](tutorial-pipeline-batch-scoring-classification.md). 

> [!TIP]
> Embora os códigos neste artigo utilizem um modelo TensorFlow, pode aplicar a informação a qualquer quadro de aprendizagem automática que suporte GPUs.

> [!NOTE]
> A informação neste artigo baseia-se nas informações do artigo do [Serviço De Como implementar para o Serviço Azure Kubernetes.](how-to-deploy-azure-kubernetes-service.md) Sempre que esse artigo geralmente abrange a implantação para a AKS, este artigo cobre a implantação específica da GPU.

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho para aprendizagem de máquinas Azure.](how-to-manage-workspace.md)

* Um ambiente de desenvolvimento Python com o Azure Machine Learning SDK instalado. Para mais informações, consulte [a Azure Machine Learning SDK](/python/api/overview/azure/ml/install).  

* Um modelo registado que usa uma GPU.

    * Para aprender a registar modelos, consulte [Modelos De Implementação.](how-to-deploy-and-where.md#registermodel)

    * Para criar e registar o modelo Tensorflow utilizado para criar este documento, consulte [Como Treinar um Modelo TensorFlow](how-to-train-tensorflow.md).

* Uma compreensão geral de [como e onde implementar modelos.](how-to-deploy-and-where.md)

## <a name="connect-to-your-workspace"></a>Ligar à sua área de trabalho

Para ligar a um espaço de trabalho existente, utilize o seguinte código:

> [!IMPORTANT]
> Este código de corte espera que a configuração do espaço de trabalho seja guardada no diretório atual ou no seu progenitor. Para obter mais informações sobre a criação de um espaço de trabalho, consulte [Criar e gerir espaços de trabalho de Aprendizagem automática Azure.](how-to-manage-workspace.md)   Para obter mais informações sobre a poupança da configuração para arquivar, consulte [Criar um ficheiro de configuração do espaço de trabalho](how-to-configure-environment.md#workspace).

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>Criar um cluster Kubernetes com GPUs

O Serviço Azure Kubernetes oferece muitas opções de GPU diferentes. Pode usar qualquer um deles para inferência de modelo. Consulte [a lista de VMs da série N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) para uma desagregação completa de capacidades e custos.

O seguinte código demonstra como criar um novo cluster AKS para o seu espaço de trabalho:

```python
from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

    # Create the cluster
    aks_target = ComputeTarget.create(
        workspace=ws, name=aks_name, provisioning_configuration=prov_config
    )

    aks_target.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> O Azure cobrar-te-á enquanto o aglomerado AKS existir. Certifique-se de eliminar o seu cluster AKS quando terminar.

Para obter mais informações sobre a utilização de AKS com Azure Machine Learning, consulte [Como implementar no Serviço Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

## <a name="write-the-entry-script"></a>Escreva o roteiro de entrada

O script de entrada recebe dados submetidos ao serviço web, passa-os para o modelo e devolve os resultados da pontuação. O seguinte script carrega o modelo Tensorflow no arranque e, em seguida, utiliza o modelo para obter dados.

> [!TIP]
> O script de entrada é específico do seu modelo. Por exemplo, o script deve conhecer a estrutura a utilizar com o seu modelo, formatos de dados, etc.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model


def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = os.getenv('AZUREML_MODEL_DIR')
    # the name of the folder in which to look for tensorflow model files
    tf_model_folder = 'model'
    saver = tf.train.import_meta_graph(
        os.path.join(model_root, tf_model_folder, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")

    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, tf_model_folder, 'mnist-tf.model'))


def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()
```

Este ficheiro tem o `score.py` nome. Para obter mais informações sobre scripts de entrada, consulte [como e onde implementar](how-to-deploy-and-where.md).

## <a name="define-the-conda-environment"></a>Defina o ambiente conda

O ficheiro ambiente conda especifica as dependências do serviço. Inclui dependências exigidas tanto pelo modelo como pelo script de entrada. Tenha em atenção que deve indicar padrão de azureml com verion >= 1.0.45 como uma dependência de pip, porque contém a funcionalidade necessária para hospedar o modelo como um serviço web. O YAML seguinte define o ambiente para um modelo tensorflow. Especifica, `tensorflow-gpu` que utilizará a GPU utilizada nesta implantação:

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  # You must list azureml-defaults as a pip dependency
  - azureml-defaults>=1.0.45
  - numpy
  - tensorflow-gpu=1.12
channels:
- conda-forge
```

Para este exemplo, o ficheiro é guardado como `myenv.yml` .

## <a name="define-the-deployment-configuration"></a>Definir a configuração de implementação

> [!IMPORTANT]
> AKS não permite que os pods partilhem GPUs, você pode ter apenas tantas réplicas de um serviço web ativado por GPU como existem GPUs no cluster.

A configuração de implementação define o ambiente de Serviço Azure Kubernetes utilizado para executar o serviço web:

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

Para mais informações, consulte a documentação de referência para [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-).

## <a name="define-the-inference-configuration"></a>Definir a configuração da inferência

A configuração da inferência aponta para o script de entrada e um objeto ambiente, que usa uma imagem de estiva com suporte GPU. Por favor, note que o ficheiro YAML utilizado para a definição de ambiente deve listar os padrão de azureml com a versão >= 1.0.45 como uma dependência de pip, porque contém a funcionalidade necessária para hospedar o modelo como um serviço web.

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment, DEFAULT_GPU_IMAGE

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
myenv.docker.base_image = DEFAULT_GPU_IMAGE
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
```

Para obter mais informações sobre ambientes, consulte [Criar e gerir ambientes de formação e implantação.](how-to-use-environments.md)
Para mais informações, consulte a documentação de referência para [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig).

## <a name="deploy-the-model"></a>Implementar o modelo

Implemente o modelo no seu cluster AKS e aguarde que crie o seu serviço.

```python
from azureml.core.model import Model

# Name of the web service that is deployed
aks_service_name = 'aks-dnn-mnist'
# Get the registerd model
model = Model(ws, "tf-dnn-mnist")
# Deploy the model
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output=True)
print(aks_service.state)
```

Para mais informações, consulte a documentação de referência [do Modelo.](/python/api/azureml-core/azureml.core.model.model)

## <a name="issue-a-sample-query-to-your-service"></a>Emite uma consulta de amostra ao seu serviço

Envie uma consulta de teste para o modelo implantado. Quando envia uma imagem jpeg para o modelo, marca a imagem. A amostra de código que se segue descarrega os dados do teste e, em seguida, seleciona uma imagem de teste aleatória para enviar para o serviço.

```python
# Used to test your webservice
import os
import urllib
import gzip
import numpy as np
import struct
import requests

# load compressed MNIST gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        struct.unpack('I', gz.read(4))
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return res

# one-hot encode a 1-D array
def one_hot_encode(array, num_of_classes):
    return np.eye(num_of_classes)[array.reshape(-1)]

# Download test data
os.makedirs('./data/mnist', exist_ok=True)
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/mnist/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/mnist/test-labels.gz')

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type': 'application/json',
           'Authorization': ('Bearer ' + api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

Para obter mais informações sobre a criação de uma aplicação ao cliente, consulte [Criar cliente para consumir o serviço web implantado.](how-to-consume-web-service.md)

## <a name="clean-up-the-resources"></a>Limpar os recursos

Se criou o cluster AKS especificamente para este exemplo, elimine os seus recursos depois de terminar.

> [!IMPORTANT]
> Azure conta-te com base na duração do cluster AKS. Certifique-se de limpá-lo depois de terminar.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Passos seguintes

* [Implementar modelo na FPGA](how-to-deploy-fpga-web-service.md)
* [Implementar modelo com ONNX](concept-onnx.md#deploy-onnx-models-in-azure)
* [Modelos DNN de fluxo de comboios](how-to-train-tensorflow.md)