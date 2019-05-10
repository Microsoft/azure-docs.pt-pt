---
title: Como implementar um modelo de aprendizagem profunda para inferência com GPU
titleSuffix: Azure Machine Learning service
description: Saiba como implementar um modelo de aprendizagem profunda como um serviço web que utiliza uma GPU para inferência. Neste artigo, é implementado um modelo de Tensorflow a um cluster do Azure Kubernetes Service. O cluster utiliza uma VM com GPU ativada para o serviço web do anfitrião e pedidos de inferência de pontuação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 5cc0fe0526937245d3ca913afc477f0259e2afd4
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65515178"
---
# <a name="how-to-do-gpu-inferencing"></a>Como fazer a inferência GPU

Saiba como utilizar a inferência GPU para um modelo implementado como um serviço web machine learning. Neste artigo, irá aprender a utilizar o serviço Azure Machine Learning para implementar um exemplo de modelo de aprendizagem profundo do Tensorflow. O modelo de implementação de um cluster do Azure Kubernetes Service (AKS) que utiliza uma VM com GPU ativada para hospedar o serviço. Quando as solicitações são enviadas para o serviço, o modelo utiliza a GPU para realizar a inferência.

As GPUs oferecem vantagens de desempenho em relação ao CPUs na computação altamente ponto pode ser paralelizada. Modelos (especialmente para lotes grandes de pedidos) de aprendizagem profunda de inferência e formação são casos de utilização excelente para GPUs.  

Neste exemplo mostram-lhe como implementar um modelo do TensorFlow guardado para o Azure Machine Learning. 

## <a name="goals-and-prerequisites"></a>Pré-requisitos e objetivos

Siga as instruções para:
* Criar uma GPU ativada cluster do AKS
* Implementar um modelo com GPU do Tensorflow

Pré-requisitos:
* Área de trabalho de serviços do Azure Machine Learning
* Python
* Tensorflow SavedModel registado. Para saber como registar-se ver modelos [implementar modelos](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#registermodel)

Este artigo se baseia em [Implantando o Tensorflow modelos para AKS](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/production-deploy-to-aks-gpu/production-deploy-to-aks-gpu.ipynb), que utiliza o TensorFlow guardado modelos e implementa um cluster do AKS. No entanto, com pequenas alterações para o ficheiro de classificação e o ficheiro de ambiente é aplicável a qualquer estrutura de aprendizado de máquina que suportam GPUs.  

## <a name="provision-aks-cluster-with-gpus"></a>Cluster do AKS aprovisionar com GPUs
O Azure tem muitas opções diferentes de GPU, todos os quais podem ser utilizados para inferência. Ver [a lista de série N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) para uma divisão completa de capacidades e os custos. 

Para obter mais informações sobre como utilizar o AKS com o serviço Azure Machine Learning, consulte o [como implementar e em que o artigo.](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where#create-a-new-cluster)

```python
# Provision AKS cluster with GPU machine
prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

# Create the cluster
aks_target = ComputeTarget.create(
    workspace=ws, name=aks_name, provisioning_configuration=prov_config
)

aks_target.wait_for_deployment()
```

> [!IMPORTANT]
> Azure irá cobrar-lhe, desde que o cluster do AKS é aprovisionado. Lembre-se de que elimina o cluster AKS quando tiver terminado com ele.


## <a name="write-entry-script"></a>Criar script de entrada

Guarde o seguinte no seu diretório de trabalho como `score.py`. Este ficheiro será utilizado para pontuar imagens à medida que são enviadas ao seu serviço. Este ficheiro é carregado o TensorFlow guardado o modelo e, em seguida, em cada mensagem de pedido passa a imagem de entrada para a sessão do TensorFlow e devolve as pontuações resultantes.
Outras estruturas de inferência exigirá a classificação de ficheiros diferentes.

```python
import tensorflow as tf
import numpy as np
import ujson
from azureml.core.model import Model
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    global session
    global input_name
    global output_name
    
    session = tf.Session()

    model_path = Model.get_model_path('resnet50')
    model = tf.saved_model.loader.load(session, ['serve'], model_path)
    if len(model.signature_def['serving_default'].inputs) > 1:
        raise ValueError("This score.py only supports one input")
    input_name = [tensor.name for tensor in model.signature_def['serving_default'].inputs.values()][0]
    output_name = [tensor.name for tensor in model.signature_def['serving_default'].outputs.values()]
    

@rawhttp
def run(request):
    if request.method == 'POST':
        reqBody = request.get_data(False)
        resp = score(reqBody)
        return AMLResponse(resp, 200)
    if request.method == 'GET':
        respBody = str.encode("GET is not supported")
        return AMLResponse(respBody, 405)
    return AMLResponse("bad request", 500)

def score(data):
    result = session.run(output_name, {input_name: [data]})
    return ujson.dumps(result[1])

if __name__ == "__main__":
    init()
    with open("lynx.jpg", 'rb') as f: #load file for testing locally
        content = f.read()
        print(score(content))

```

## <a name="define-conda-environment"></a>Definir o ambiente de Conda
Crie um ficheiro de ambiente de conda denominado `myenv.yml` para especificar as dependências para o seu serviço. É importante especificar que está a utilizar `tensorflow-gpu` para obter um desempenho acelerado.
```yaml
name: aml-accel-perf
channels:
  - defaults
dependencies:
  - tensorflow-gpu = 1.12
  - numpy
  - ujson
  - pip:
    - azureml-core
    - azureml-contrib-services
```

## <a name="define-gpu-inferenceconfig"></a>Definir InferenceConfig GPU

Criar uma [ `InferenceConfig` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) que especifica que pretende ativar a GPU. Isto irá garantir que executam CUDA é instalado com a sua imagem.

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='gpu-rn'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"resnet50")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   gpu_enabled=True)
```

Para obter mais informações, consulte [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) e [AksServiceDeploymentConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py).
## <a name="deploy-the-model"></a>Implementar o modelo

Implementar o modelo ao seu cluster do AKS e aguarde pela criar o seu serviço.

```python
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config, 
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!NOTE]
> O serviço do Azure Machine Learning não irá implementar um modelo com um `InferenceConfig` que espera GPU para um cluster sem GPU.

Para obter mais informações, consulte [modelo](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-sample-query-to-deployed-model"></a>Consulta de exemplo do problema para implementar o modelo

Emita uma consulta de exemplo para o seu modelo implementado. Esse modelo vai classificar qualquer imagem jpeg que enviar para o mesmo como um pedido post. 

```python
scoring_url = aks_service.scoring_uri
api_key = aks_service.get_key()(0)
IMAGEURL = "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Lynx_lynx_poing.jpg/220px-Lynx_lynx_poing.jpg"

headers = {'Authorization':('Bearer '+ api_key)}
img_data = read_image_from(IMAGEURL).read()
r = requests.post(scoring_url, data = img_data, headers=headers)
```

> [!IMPORTANT]
> Para otimizar o débito e latência, o cliente deve ser na mesma região do Azure como o ponto final.  Atualmente as APIs são criadas na região do Azure do Leste-nos.

## <a name="cleaning-up-the-resources"></a>A limpar os recursos

Elimine os recursos depois de terminar com a demonstração.

> [!IMPORTANT]
> Azure será faturado com base no quanto é implementado o cluster do AKS. Certifique-se para limpá-los depois de terminar com o mesmo.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Passos Seguintes

* [Implementar o modelo em FPGA](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service)
* [Implementar o modelo com ONNX](https://docs.microsoft.com/azure/machine-learning/service/how-to-build-deploy-onnx#deploy)
* [Utilizar modelos do Tensorflow DNN](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-tensorflow)
