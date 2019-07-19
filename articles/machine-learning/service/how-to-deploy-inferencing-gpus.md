---
title: Implantar um modelo para inferência com GPU
titleSuffix: Azure Machine Learning service
description: Este artigo ensina como usar o serviço de Azure Machine Learning para implantar um modelo de aprendizado profundo Tensorflow habilitado para GPU como um serviço Web. solicitações de inferência de serviço e pontuação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/01/2019
ms.openlocfilehash: eeb1bc35e0438a7e99ea5ed8284f0c8611108da0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326995"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Implantar um modelo de aprendizado profundo para inferência com GPU

Este artigo ensina como usar o serviço de Azure Machine Learning para implantar um modelo de aprendizado profundo Tensorflow habilitado para GPU como um serviço Web.

Implante seu modelo em um cluster AKS (serviço de kubernetes do Azure) para realizar inferência habilitadas para GPU. Inferência, ou pontuação de modelo, é a fase em que o modelo implantado é usado para previsão. O uso de GPUs em vez de CPUs oferece vantagens de desempenho na computação altamente paralelizáveis.

Embora este exemplo use um modelo TensorFlow, você pode aplicar as etapas a seguir a qualquer estrutura de Machine Learning que dê suporte a GPUs fazendo pequenas alterações no arquivo de Pontuação e no arquivo de ambiente. 

Neste artigo, você executará as seguintes etapas:

* Criar um cluster AKS habilitado para GPU
* Implantar um modelo de GPU Tensorflow
* Emitir uma consulta de exemplo para seu modelo implantado

## <a name="prerequisites"></a>Pré-requisitos

* Um espaço de trabalho dos serviços Azure Machine Learnings.
* Um distribuição Python.
* Um modelo salvo do Tensorflow registrado.
    * Para saber como registrar modelos, consulte [implantar modelos](../service/how-to-deploy-and-where.md#registermodel).

Você pode concluir a parte um desta série de instruções, [como treinar um modelo TensorFlow](how-to-train-tensorflow.md)para atender aos pré-requisitos necessários.

## <a name="provision-an-aks-cluster-with-gpus"></a>Provisionar um cluster AKS com GPUs

O Azure tem muitas opções de GPU diferentes. Você pode usar qualquer um deles para inferência. Consulte [a lista de VMs da série N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) para obter uma análise completa dos recursos e dos custos.

Para obter mais informações sobre como usar o AKS com o serviço Azure Machine Learning, consulte [como implantar e onde](../service/how-to-deploy-and-where.md#deploy-aks).

```Python
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
> O Azure cobrará você desde que o cluster AKS seja provisionado. Certifique-se de excluir o cluster AKS quando tiver terminado.

## <a name="write-the-entry-script"></a>Gravar o script de entrada

Salve o código a seguir em seu diretório de `score.py`trabalho como. Esse arquivo pontua imagens conforme elas são enviadas para o serviço. Ele carrega o modelo TensorFlow salvo, passa a imagem de entrada para a sessão TensorFlow em cada solicitação POST e, em seguida, retorna as pontuações resultantes. Outras estruturas inferência exigem diferentes arquivos de pontuação.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model

def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = Model.get_model_path('tf-dnn-mnist')
    saver = tf.train.import_meta_graph(os.path.join(model_root, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")
    
    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, 'mnist-tf.model'))

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()

```
## <a name="define-the-conda-environment"></a>Definir o ambiente Conda

Crie um arquivo de ambiente Conda `myenv.yml` chamado para especificar as dependências do seu serviço. É importante especificar que você está usando `tensorflow-gpu` o para obter um desempenho acelerado.

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  - azureml-defaults==1.0.43.*
- numpy
- tensorflow-gpu=1.12
channels:
- conda-forge
```

## <a name="define-the-gpu-inferenceconfig-class"></a>Definir a classe InferenceConfig GPU

Crie um `InferenceConfig` objeto que habilite as GPUs e garanta que o CUDA seja instalado com a imagem do Docker.

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='aks-dnn-mnist'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"tf-dnn-mnist")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   enable_gpu=True)
```

Para obter mais informações, consulte:

- [Classe InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)
- [Classe AksServiceDeploymentConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)

## <a name="deploy-the-model"></a>Implementar o modelo

Implante o modelo em seu cluster AKS e aguarde até que ele crie seu serviço.

```python
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config, 
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!NOTE]
> Azure Machine Learning serviço não implantará um modelo `InferenceConfig` com um objeto que espera que a GPU seja habilitada para um cluster que não tenha uma GPU.

Para obter mais informações, consulte [classe de modelo](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-model"></a>Emitir uma consulta de exemplo para seu modelo

Envie uma consulta de teste para o modelo implantado. Quando você envia uma imagem JPEG para o modelo, ela classifica a imagem. O exemplo de código a seguir usa uma função de utilitário externo para carregar imagens. Você pode encontrar o código relevante em PIR [TensorFlow sample no GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/utils.py). 

```python
# Used to test your webservice
from utils import load_data 

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

> [!IMPORTANT]
> Para minimizar a latência e otimizar a taxa de transferência, verifique se o cliente está na mesma região do Azure que o ponto de extremidade. Neste exemplo, as APIs são criadas na região do Azure leste dos EUA.

## <a name="clean-up-the-resources"></a>Limpar os recursos

Se você criou o cluster AKS especificamente para este exemplo, exclua seus recursos depois de terminar.

> [!IMPORTANT]
> O Azure cobra com base em quanto tempo o cluster AKS é implantado. Certifique-se de limpá-lo depois de concluir a tarefa.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Passos Seguintes

* [Implantar modelo em FPGA](../service/how-to-deploy-fpga-web-service.md)
* [Implantar modelo com ONNX](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [Treinar modelos do Tensorflow DNN](../service/how-to-train-tensorflow.md)
