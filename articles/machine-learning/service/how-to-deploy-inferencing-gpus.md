---
title: Implementar um modelo do inferência de tipos com GPU
titleSuffix: Azure Machine Learning service
description: Este artigo mostra como utilizar o serviço Azure Machine Learning para implementar um modelo como um service.service web e pedidos de inferência de tipos de pontuação de aprendizagem profunda de Tensorflow ativadas para GPU.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/01/2019
ms.openlocfilehash: 8086d059913cc61bff0bca31681368bea6d76777
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543808"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Implementar um modelo de aprendizagem profunda para inferência de tipos com GPU

Este artigo mostra como utilizar o serviço Azure Machine Learning para implementar um modelo como um serviço web de aprendizagem profunda de Tensorflow ativadas para GPU.

Implemente o seu modelo para um cluster do Azure Kubernetes Service (AKS) para fazer inferência ativadas para GPU. Inferência ou modelo de classificação, é a fase em que o modelo implementado é utilizado para predição. Utilizar GPUs, em vez de CPUs oferecem vantagens de desempenho no cálculo altamente ponto pode ser paralelizada.

Embora este exemplo utiliza um modelo do TensorFlow, pode aplicar os seguintes passos para qualquer arquitetura que suporta GPUs fazendo pequenas alterações para o ficheiro de classificação e o ficheiro de ambiente de aprendizagem automática. 

Neste artigo, siga os passos seguintes:

* Criar um cluster do AKS com GPU ativada
* Implementar um modelo de GPU do Tensorflow
* Emitir uma consulta de exemplo para o seu modelo implementado

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho de serviços do Azure Machine Learning.
* Uma distribuição do Python.
* Um Tensorflow registado guardar modelo.
    * Para saber como registar modelos, veja [implementar modelos](../service/how-to-deploy-and-where.md#registermodel).

Pode concluir a primeira parte desta série de procedimentos [como preparar um modelo do TensorFlow](how-to-train-tensorflow.md), para cumprir os pré-requisitos necessários.

## <a name="provision-an-aks-cluster-with-gpus"></a>Aprovisionar um cluster do AKS com GPUs

O Azure tem muitas opções diferentes de GPU. Pode utilizar qualquer um deles para inferência. Ver [a lista de VMs de série N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) para uma divisão completa de capacidades e os custos.

Para obter mais informações sobre como utilizar o AKS com o serviço Azure Machine Learning, consulte [como implementar e, em que](../service/how-to-deploy-and-where.md#deploy-aks).

```Python
# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    compute_target = ComputeTarget(workspace=ws, name=aks_name)
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
> Azure irá cobrar-lhe, desde que o cluster do AKS é aprovisionado. Lembre-se de que elimina o cluster AKS quando tiver terminado com ele.

## <a name="write-the-entry-script"></a>Escrever o script de entrada

Guarde o seguinte código no seu diretório de trabalho como `score.py`. Este ficheiro pontua imagens à medida que são enviados para o seu serviço. Carrega o modelo do TensorFlow guardado, passa a imagem de entrada para a sessão do TensorFlow em cada pedido POST e, em seguida, devolve as pontuações resultantes. Outras estruturas de inferência requerem diferentes arquivos de classificação.

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
## <a name="define-the-conda-environment"></a>Definir o ambiente de conda

Crie um ficheiro de ambiente de conda denominado `myenv.yml` para especificar as dependências para o seu serviço. É importante especificar que está a utilizar `tensorflow-gpu` para obter um desempenho acelerado.

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

## <a name="define-the-gpu-inferenceconfig-class"></a>Definir a classe InferenceConfig de GPU

Criar um `InferenceConfig` objeto que permite que as GPUs e assegura que executam CUDA é instalado com a imagem do Docker.

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

- [Classe de InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)
- [Classe de AksServiceDeploymentConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)

## <a name="deploy-the-model"></a>Implementar o modelo

Implementar o modelo ao seu cluster do AKS e aguarde pela criar o seu serviço.

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
> O serviço do Azure Machine Learning não implementar um modelo com um `InferenceConfig` objeto que espera GPU seja ativado para um cluster que não tem uma GPU.

Para obter mais informações, consulte [classe de modelo](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-model"></a>Emitir uma consulta de exemplo para o seu modelo

Envie uma consulta de teste para o modelo implementado. Quando envia uma imagem jpeg para o modelo, ele pontua a imagem. O código de exemplo seguinte utiliza uma função de utilitário externo para carregar imagens. Pode encontrar o código relevante em pir [TensorFlow exemplo no GitHub](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow/utils.py). 

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
> Para minimizar a latência e otimizar o débito, certificar-se de que o cliente está na mesma região do Azure como o ponto final. Neste exemplo, as APIs são criadas na região do Azure do Leste-nos.

## <a name="clean-up-the-resources"></a>Limpar os recursos

Depois de terminar com este exemplo, elimine os recursos.

> [!IMPORTANT]
> O Azure cobra com base no quanto é implementado o cluster do AKS. Certifique-se para limpá-los depois de terminar com o mesmo.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Passos Seguintes

* [Implementar o modelo em FPGA](../service/how-to-deploy-fpga-web-service.md)
* [Implementar o modelo com ONNX](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [Utilizar modelos do Tensorflow DNN](../service/how-to-train-tensorflow.md)
