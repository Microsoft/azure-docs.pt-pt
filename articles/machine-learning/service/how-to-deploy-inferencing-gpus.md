---
title: Implementar um modelo do inferência de tipos com GPU
titleSuffix: Azure Machine Learning service
description: Saiba como implementar um modelo de aprendizagem profunda como um serviço web que utiliza uma GPU para inferência de tipos. Neste artigo, é implementado um modelo de Tensorflow a um cluster do Azure Kubernetes Service. O cluster utiliza uma VM com GPU ativada para o serviço web do anfitrião e pedidos de inferência de tipos de pontuação.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 64d42b9082895e372bb780d2db023294c1a0a380
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65884730"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Implementar um modelo de aprendizagem profunda para inferência de tipos com GPU

Saiba como utilizar a inferência de tipos GPU para um modelo implementado como um serviço web machine learning. Inferência de tipos ou modelo de classificação, é a fase em que o modelo implementado é utilizado para predição, mais comumente nos dados de produção.

Este artigo mostra como utilizar o serviço Azure Machine Learning para implementar um exemplo de modelo para um cluster do Azure Kubernetes Service (AKS) numa máquina de virtual (VM) ativadas para GPU de aprendizagem profundo do Tensorflow. Quando as solicitações são enviadas para o serviço, o modelo utiliza a GPU para executar as cargas de trabalho de inferência de tipos.

As GPUs oferecem vantagens de desempenho em relação ao CPUs na computação altamente ponto pode ser paralelizada. Casos de utilização excelente para as VMs ativadas para GPU incluem a aprendizagem profunda formação e inferência, especialmente para lotes grandes de pedidos de modelo.

Este exemplo demonstra como implementar um TensorFlow guardado o modelo para o Azure Machine Learning. Siga os passos seguintes:

* Criar um cluster do AKS com GPU ativada
* Implementar um modelo de GPU do Tensorflow

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho de serviços do Azure Machine Learning
* Uma distribuição do Python
* Um Tensorflow registado guardar modelo. Para saber como registar modelos, veja [implementar modelos](../service/how-to-deploy-and-where.md#registermodel).

Este artigo baseia-se do bloco de notas do Jupyter [Implantando o Tensorflow modelos para AKS](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/production-deploy-to-aks-gpu/production-deploy-to-aks-gpu.ipynb). O bloco de notas do Jupyter utiliza TensorFlow guardado modelos e implementa-los para um cluster do AKS. Também pode aplicar o bloco de notas para qualquer arquitetura que suporta GPUs fazendo pequenas alterações para o ficheiro de classificação e o ficheiro de ambiente de aprendizagem automática.  

## <a name="provision-an-aks-cluster-with-gpus"></a>Aprovisionar um cluster do AKS com GPUs

O Azure tem muitas opções diferentes de GPU. Pode utilizar qualquer um deles para inferência. Ver [a lista de VMs de série N](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) para uma divisão completa de capacidades e os custos.

Para obter mais informações sobre como utilizar o AKS com o serviço Azure Machine Learning, consulte [como implementar e, em que](../service/how-to-deploy-and-where.md#deploy-aks).

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

## <a name="write-the-entry-script"></a>Escrever o script de entrada

Guarde o seguinte código no seu diretório de trabalho como `score.py`. Este ficheiro pontua imagens à medida que são enviados para o seu serviço. Carrega o modelo do TensorFlow guardado, passa a imagem de entrada para a sessão do TensorFlow em cada pedido POST e, em seguida, devolve as pontuações resultantes. Outras estruturas de inferência requerem diferentes arquivos de classificação.

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

## <a name="define-the-conda-environment"></a>Definir o ambiente de conda

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

## <a name="define-the-gpu-inferenceconfig-class"></a>Definir a classe InferenceConfig de GPU

Criar um `InferenceConfig` objeto que permite que as GPUs e assegura que executam CUDA é instalado com a imagem do Docker.

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

Para obter mais informações, consulte:

- [Classe de InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)
- [Classe de AksServiceDeploymentConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)

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
> O serviço do Azure Machine Learning não implementar um modelo com um `InferenceConfig` objeto que espera GPU seja ativado para um cluster que não tem uma GPU.

Para obter mais informações, consulte [classe de modelo](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-deployed-model"></a>Emitir uma consulta de exemplo para o seu modelo implementado

Envie uma consulta de teste para o modelo implementado. Quando envia uma imagem jpeg para o modelo, ele pontua a imagem.

```python
scoring_url = aks_service.scoring_uri
api_key = aks_service.get_key()(0)
IMAGEURL = "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Lynx_lynx_poing.jpg/220px-Lynx_lynx_poing.jpg"

headers = {'Authorization':('Bearer '+ api_key)}
img_data = read_image_from(IMAGEURL).read()
r = requests.post(scoring_url, data = img_data, headers=headers)
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
