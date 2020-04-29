---
title: Implementar um modelo de inferência com AGP
titleSuffix: Azure Machine Learning
description: Este artigo ensina-o a usar o Azure Machine Learning para implementar um modelo de aprendizagem profunda ativado pela GPU como serviço web.service e pedidos de inferência de pontuação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: b0fd537d1930e7c9d5f7a33f56ec5d00b1556562
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78398338"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Implementar um modelo de aprendizagem profunda para inferência com GPU
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este artigo ensina-o a usar o Azure Machine Learning para implementar um modelo ativado pela GPU como um serviço web. A informação neste artigo baseia-se na implementação de um modelo no Serviço Azure Kubernetes (AKS). O cluster AKS fornece um recurso GPU que é usado pelo modelo de inferência.

Inferência, ou pontuação do modelo, é a fase em que o modelo implantado é usado para fazer previsões. A utilização de GPUs em vez de CPUs oferece vantagens de desempenho em cálculos altamente paralelos.

> [!IMPORTANT]
> Para implementações de serviços web, a inferência da GPU só é suportada no Serviço Azure Kubernetes. Para inferência utilizando um pipeline de __aprendizagem automática,__ as GPUs só são suportadas na Computação de Aprendizagem automática Azure. Para obter mais informações sobre a utilização de gasodutos ML, consulte as previsões do [lote de execução](how-to-use-parallel-run-step.md). 

> [!TIP]
> Embora os fragmentos de código neste artigo utilizem um modelo TensorFlow, pode aplicar a informação a qualquer quadro de aprendizagem automática que suporte as GPUs.

> [!NOTE]
> A informação deste artigo baseia-se na informação no artigo do [Serviço De Como Implantar no Serviço Azure Kubernetes.](how-to-deploy-azure-kubernetes-service.md) Sempre que esse artigo cubra geralmente a implantação para a AKS, este artigo abrange a implantação específica da GPU.

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Azure Machine Learning. Para mais informações, consulte Criar um espaço de [trabalho azure machine learning](how-to-manage-workspace.md).

* Um ambiente de desenvolvimento Python com o Azure Machine Learning SDK instalado. Para mais informações, consulte [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).  

* Um modelo registado que usa uma GPU.

    * Para aprender a registar modelos, consulte [Modelos de Implantação](how-to-deploy-and-where.md#registermodel).

    * Para criar e registar o modelo Tensorflow utilizado para criar este documento, consulte [Como Treinar um Modelo TensorFlow](how-to-train-tensorflow.md).

* Uma compreensão geral de [Como e onde implementar modelos.](how-to-deploy-and-where.md)

## <a name="connect-to-your-workspace"></a>Ligar à sua área de trabalho

Para se ligar a um espaço de trabalho existente, utilize o seguinte código:

> [!IMPORTANT]
> Este código de corte espera que a configuração do espaço de trabalho seja guardada no diretório atual ou no seu progenitor. Para obter mais informações sobre a criação de um espaço de trabalho, consulte Criar e gerir espaços de [trabalho azure machine learning.](how-to-manage-workspace.md)   Para obter mais informações sobre como guardar a configuração para arquivar, consulte Criar um ficheiro de [configuração do espaço de trabalho](how-to-configure-environment.md#workspace).

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>Criar um cluster Kubernetes com GPUs

O Serviço Azure Kubernetes oferece muitas opções de GPU diferentes. Podes usar qualquer um deles para inferência de modelo. Consulte [a lista de VMs da série N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) para obter uma desagregação completa de capacidades e custos.

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
> O Azure vai cobrar-te enquanto o cluster AKS existir. Certifique-se de eliminar o seu cluster AKS quando terminar.

Para obter mais informações sobre a utilização de AKS com Azure Machine Learning, consulte [Como se implementar no Serviço Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

## <a name="write-the-entry-script"></a>Escreva o script de entrada

O script de entrada recebe os dados submetidos ao serviço web, passa-os para o modelo e devolve os resultados da pontuação. O seguinte script carrega o modelo Tensorflow no arranque e, em seguida, usa o modelo para marcar dados.

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

Este ficheiro `score.py`chama-se . Para obter mais informações sobre os scripts de entrada, consulte [Como e onde implementar](how-to-deploy-and-where.md).

## <a name="define-the-conda-environment"></a>Defina o ambiente de condomínio

O arquivo ambiental do condomínio especifica as dependências do serviço. Inclui dependências exigidas tanto pelo modelo como pelo script de entrada. Por favor, note que deve indicar incumprimentos em azureml com verion >= 1,0,45 como dependência do pip, porque contém a funcionalidade necessária para hospedar o modelo como um serviço web. O Seguinte YAML define o ambiente para um modelo Tensorflow. `tensorflow-gpu`Especifica, que utilizará a GPU utilizada nesta implantação:

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

Para este exemplo, o `myenv.yml`ficheiro é guardado como .

## <a name="define-the-deployment-configuration"></a>Definir a configuração de implementação

A configuração de implementação define o ambiente de serviço Azure Kubernetes usado para executar o serviço web:

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

Para mais informações, consulte a documentação de referência para [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-).

## <a name="define-the-inference-configuration"></a>Definir a configuração de inferência

A configuração da inferência aponta para o script de entrada e um objeto de ambiente, que usa uma imagem de estivador com suporte de GPU. Por favor, note que o ficheiro YAML utilizado para a definição de ambiente deve listar os incumprimentos em azul com a versão >= 1.0.45 como dependência do pip, porque contém a funcionalidade necessária para hospedar o modelo como um serviço web.

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment, DEFAULT_GPU_IMAGE

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
myenv.docker.base_image = DEFAULT_GPU_IMAGE
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
```

Para obter mais informações sobre ambientes, consulte [Criar e gerir ambientes para formação e implantação.](how-to-use-environments.md)
Para mais informações, consulte a documentação de referência para [inferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py).

## <a name="deploy-the-model"></a>Implementar o modelo

Desloque o modelo para o seu cluster AKS e espere que crie o seu serviço.

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

> [!NOTE]
> Se `InferenceConfig` o `enable_gpu=True`objeto tiver, então o parâmetro deve fazer referência a `deployment_target` um cluster que forneça uma GPU. Caso contrário, o destacamento falhará.

Para mais informações, consulte a documentação de referência para [o Modelo](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-service"></a>Emita uma amostra de consulta ao seu serviço

Envie uma consulta de teste ao modelo implantado. Quando envias uma imagem jpeg para o modelo, marca a imagem. A amostra de código seguinte descarrega dados de teste e, em seguida, seleciona uma imagem de teste aleatória para enviar para o serviço.

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

Para obter mais informações sobre a criação de uma aplicação de cliente, consulte [Criar o cliente para consumir o serviço web implantado.](how-to-consume-web-service.md)

## <a name="clean-up-the-resources"></a>Limpar os recursos

Se criou o cluster AKS especificamente para este exemplo, apague os seus recursos depois de terminar.

> [!IMPORTANT]
> O Azure diz-te com base no tempo que o cluster AKS está implantado. Certifique-se de limpá-lo depois de terminar.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Passos seguintes

* [Modelo de implantação na FPGA](how-to-deploy-fpga-web-service.md)
* [Implementar modelo com ONNX](concept-onnx.md#deploy-onnx-models-in-azure)
* [Modelos DNN de Tensorflow de Comboio](how-to-train-tensorflow.md)
