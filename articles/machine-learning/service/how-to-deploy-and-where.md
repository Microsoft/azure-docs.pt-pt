---
title: Como e onde implementar modelos
titleSuffix: Azure Machine Learning service
description: 'Saiba como e onde implementar os seus modelos de serviço do Azure Machine Learning, incluindo: Matrizes de porta do Container Instances, serviço Kubernetes do Azure, Azure IoT Edge e campos programáveis do Azure.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 05/21/2019
ms.custom: seoapril2019
ms.openlocfilehash: 4685d02fa9a1f08d86bdbe2915b94f177235b864
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2019
ms.locfileid: "66016422"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Implementar modelos com o serviço Azure Machine Learning

Saiba como implementar o modelo de machine learning como um serviço web na cloud do Azure ou para dispositivos do IoT Edge. 

O fluxo de trabalho é semelhante, independentemente de [onde implementar](#target) seu modelo:

1. Registe o modelo.
1. Preparar a implementação (especificar destino de computação de ativos, utilização,)
1. Implemente o modelo para o destino de computação.
1. Teste o modelo implementado, também chamado de serviço web.

Para obter mais informações sobre os conceitos envolvidos no fluxo de trabalho de implantação, consulte [gerir, implementar e monitorizar os modelos de serviço do Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Pré-requisitos

- Um modelo. Se não tiver um modelo preparado, pode usar o modelo e os ficheiros de dependência fornecida no [deste tutorial](https://aka.ms/azml-deploy-cloud).

- O [extensão de CLI do Azure para o serviço Machine Learning](reference-azure-machine-learning-cli.md), [SDK de Python do Azure Machine Learning](https://aka.ms/aml-sdk), ou o [extensão do Azure Machine Learning Visual Studio Code](how-to-vscode-tools.md).

## <a id="registermodel"></a> Registar o seu modelo

Registre-se os modelos de machine learning na sua área de trabalho do Azure Machine Learning. O modelo pode ser provenientes do Azure Machine Learning ou pode vir de outro lugar. Os exemplos seguintes demonstram como registar um modelo do ficheiro:

### <a name="register-a-model-from-an-experiment-run"></a>Registe-se de um modelo a partir de uma execução de experimentação

+ **Exemplo de Scikit-Saiba com o SDK**
  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```
+ **Com a CLI**
  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
  ```


+ **Utilizar o VS Code**

  Registe-se de modelos através de quaisquer ficheiros de modelo ou pastas com o [VS Code](how-to-vscode-tools.md#deploy-and-manage-models) extensão.

### <a name="register-an-externally-created-model"></a>Registe-se de um modelo criado externamente

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

Pode registrar um modelo criado externamente, fornecendo uma **caminho local** ao modelo. Pode fornecer uma pasta ou um único ficheiro.

+ **Exemplo ONNX com o SDK de Python:**
  ```python
  onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
  urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
  !tar xvzf mnist.tar.gz
  
  model = Model.register(workspace = ws,
                         model_path ="mnist/model.onnx",
                         model_name = "onnx_mnist",
                         tags = {"onnx": "demo"},
                         description = "MNIST image classification CNN from ONNX Model Zoo",)
  ```

+ **Com a CLI**
  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

**Estimativa de tempo**: Aproximadamente 10 segundos.

Para obter mais informações, consulte a documentação de referência para o [classe de modelo](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Escolha um destino de computação

Os seguintes destinos, de computação ou recursos de computação, podem ser utilizadas para alojar a sua implementação do serviço web. 

| Destino de computação | Utilização | Descrição |
| ----- | ----- | ----- |
| [Serviço local web](#local) | Teste/depuração | Vale a limitado de teste e resolução de problemas.
| [Serviço Kubernetes do Azure (AKS)](#aks) | Inferência de tipos em tempo real | Ideal para implementações de produção de grande escala. Fornece o dimensionamento automático e tempos de resposta rápidos. |
| [Azure Container Instances (ACI)](#aci) | A testar | Vale a pequena escala, com base na CPU a cargas de trabalho. |
| [Computação do Azure Machine Learning](how-to-run-batch-predictions.md) | (Pré-visualização) Inferência de tipos do batch | Execute a computação sem servidor de classificação de batch. Suporta VMs normais e de baixa prioridade. |
| [Azure IoT Edge](#iotedge) | (Pré-visualização) Módulo de IoT | Implementar e servir os modelos de ML em dispositivos IoT. |


## <a name="prepare-to-deploy"></a>Preparar para implementar

Para implementar como um serviço web, tem de criar uma configuração de inferência de tipos (`InferenceConfig`) e uma configuração de implementação. Inferência de tipos ou modelo de classificação, é a fase em que o modelo implementado é utilizado para predição, mais comumente nos dados de produção. A configuração de inferência de tipos, especifique os scripts e as dependências necessárias para atender o seu modelo. A configuração de implementação é especificar detalhes de como a servir o modelo no destino de computação.


### <a id="script"></a> 1. Definir o seu script de entrada e dependências

O script de entrada recebe dados enviados para um serviço web implementado e o transmite para o modelo. Em seguida, usa a resposta devolvida pelo modelo e retorna que para o cliente. **O script é específico ao seu modelo**; tem de compreender os dados que o modelo de espera e retorna.

O script contém duas funções que carregar e executam o modelo:

* `init()`: Normalmente, esta função carrega o modelo de um objeto global. Esta função é executada apenas uma vez quando o contentor do Docker para o seu serviço web é iniciado.

* `run(input_data)`: Essa função usa o modelo para prever um valor com base nos dados de entrada. Entradas e saídas para a execução normalmente usam JSON para a serialização e desserialização. Também pode trabalhar com dados binários não processados. Pode transformar os dados antes de enviar para o modelo ou antes de o devolver ao cliente.

#### <a name="optional-automatic-swagger-schema-generation"></a>(Opcional) Geração automática de esquema do Swagger

Para automaticamente gerar um esquema para o seu serviço web, fornecer um exemplo da entrada e/ou de saída no construtor para um dos objetos de tipo definido e o tipo e o exemplo são utilizados para criar automaticamente o esquema. O serviço do Azure Machine Learning, em seguida, cria um [OpenAPI](https://swagger.io/docs/specification/about/) especificação (Swagger) para o serviço web durante a implementação.

Atualmente são suportados os seguintes tipos:

* `pandas`
* `numpy`
* `pyspark`
* objeto de Python padrão

Para utilizar a geração do esquema, inclua o `inference-schema` pacote no seu ficheiro de ambiente de conda. O exemplo seguinte utiliza `[numpy-support]` uma vez que o script de entrada utiliza um tipo de parâmetro numpy: 

#### <a name="example-dependencies-file"></a>Exemplo de ficheiro de dependências
Segue-se um exemplo de um ficheiro de dependências de Conda para inferência de tipos.
```python
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn==0.20.0
    - inference-schema[numpy-support]
```

Se pretender utilizar a geração automática de esquema, o script de entrada **tem** importar o `inference-schema` pacotes. 

Definir a entrada e saída de formatos de exemplo no `input_sample` e `output_sample` variáveis, que representam os formatos de solicitação e resposta para o serviço web. Utilize estes exemplos na entrada e saída decoradores de função no `run()` função. O scikit-saiba exemplo abaixo utiliza a geração de esquema.

> [!TIP]
> Depois de implementar o serviço, utilize o `swagger_uri` propriedade para recuperar o documento JSON de esquema.

#### <a name="example-entry-script"></a>Script de entrada de exemplo

O exemplo seguinte demonstra como aceitar e retornar dados JSON:

```python
#example: scikit-learn and Swagger
import json
import numpy as np
from sklearn.externals import joblib
from sklearn.linear_model import Ridge
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType

def init():
    global model
    # note here "sklearn_regression_model.pkl" is the name of the model registered under
    # this is a different behavior than before when the code is run locally, even though the code is the same.
    model_path = Model.get_model_path('sklearn_regression_model.pkl')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)

input_sample = np.array([[10,9,8,7,6,5,4,3,2,1]])
output_sample = np.array([3726.995])

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Para obter mais scripts de exemplo, veja os exemplos seguintes:

* Pytorch: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* Classificação em relação a dados binários: [Como consumir um serviço web](how-to-consume-web-service.md)

### <a name="2-define-your-inferenceconfig"></a>2. Definir sua InferenceConfig

A configuração de inferência de tipos descreve como configurar o modelo para fazer previsões. O exemplo seguinte demonstra como criar uma configuração de inferência de tipos:

```python
inference_config = InferenceConfig(source_directory="C:/abc",
                                   runtime= "python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

Neste exemplo, a configuração contém os seguintes itens:

* Um diretório que contém os recursos necessários para inferência de tipos
* Este modelo necessita de Python
* O [script de entrada](#script), que é utilizada para processar pedidos web enviados para o serviço implementado
* O ficheiro de conda que descreve os pacotes Python necessários para inferência de tipos

Para obter informações sobre a funcionalidade de InferenceConfig, consulte a [configuração avançada](#advanced-config) secção.

### <a name="3-define-your-deployment-configuration"></a>3. Definir a configuração da implementação

Antes de implementar, tem de definir a configuração de implementação. A configuração de implementação é específica para o destino de computação que irá alojar o serviço web. Por exemplo, tem de especificar a porta em que o serviço aceita solicitações ao implantar localmente.

Também poderá criar o recurso de computação. Por exemplo, se fizer isso ainda não tiver um serviço de Kubernetes do Azure associado sua área de trabalho.

A tabela seguinte fornece um exemplo de criação de uma configuração de implementação para cada destino de computação:

| Destino de computação | Exemplo de configuração de implementação |
| ----- | ----- |
| Local | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Instância de Contentor do Azure | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Serviço Kubernetes do Azure | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

As secções seguintes demonstram como criar a configuração de implementação e, em seguida, utilizá-lo para implementar o serviço web.

## <a name="deploy-to-target"></a>Implementar no destino

### <a id="local"></a> Implementação de local

Para implementar localmente, tem de ter **Docker instalado** no seu computador local.

Os exemplos nesta secção utilizam [deploy_from_image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-), que requer que se Registre o modelo e a imagem antes de fazer uma implantação. Para obter mais informações sobre outros métodos de implantação, consulte [implementar](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) e [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-).


+ **Com o SDK**

  ```python
  deployment_config = LocalWebservice.deploy_configuration(port=8890)
  service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **Com a CLI**

  ```azurecli-interactive
  az ml model deploy -m sklearn_mnist:1 -ic inferenceconfig.json -dc deploymentconfig.json
  ```

### <a id="aci"></a> Instâncias de contentor do Azure (Dev/Test)

Utilização do Azure Container Instances para implementar os seus modelos como um serviço da web se um ou mais das seguintes condições for verdadeiro:
- Precisa implementar e validar o seu modelo rapidamente.
- Está a testar um modelo que está em desenvolvimento. 

Para ver a disponibilidade de região e quota para o ACI, consulte a [Quotas e disponibilidade das regiões do Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) artigo.

+ **Com o SDK**

  ```python
  deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **Com a CLI**

  ```azurecli-interactive
  az ml model deploy -m sklearn_mnist:1 -n aciservice -ic inferenceconfig.json -dc deploymentconfig.json
  ```


+ **Utilizar o VS Code**

  Para [implementar os seus modelos com o VS Code](how-to-vscode-tools.md#deploy-and-manage-models) não precisa de criar um contentor do ACI para testar com antecedência, porque os contentores ACI são criados em tempo real.

Para obter mais informações, consulte a documentação de referência para o [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) e [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) classes.

### <a id="aks"></a>Serviço Kubernetes do Azure (produção)

Pode utilizar um cluster do AKS existente ou crie um novo com o SDK do Azure Machine Learning, a CLI ou o portal do Azure.

<a id="deploy-aks"></a>

Se já tiver um cluster do AKS ligado, pode implementar a ele. Se ainda não tiver criado ou anexado um cluster do AKS, siga o processo para <a href="#create-attach-aks">criar um novo cluster do AKS</a>.

+ **Com o SDK**

  ```python
  aks_target = AksCompute(ws,"myaks")
  deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aksservice", [model], inference_config, deployment_config, aks_target)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  print(service.get_logs())
  ```

+ **Com a CLI**

  ```azurecli-interactive
  az ml model deploy -ct myaks -m mymodel:1 -n aksservice -ic inferenceconfig.json -dc deploymentconfig.json
  ```

+ **Utilizar o VS Code**

  Também pode [implementar no AKS através da extensão do VS Code](how-to-vscode-tools.md#deploy-and-manage-models), mas também precisará configurar clusters do AKS com antecedência.

Saiba mais sobre implementação do AKS e Dimensionar automaticamente na [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice) referência.

#### Criar um novo cluster do AKS<a id="create-attach-aks"></a>
**Estimativa de tempo:** Cerca de cinco minutos.

> [!IMPORTANT]
> Criar ou anexar um cluster do AKS é uma hora de um processo para a área de trabalho. Pode reutilizar este cluster para várias implementações. Se eliminar o cluster ou o grupo de recursos que o contém, tem de criar um novo cluster da próxima vez que precisa implantar.

Para obter mais informações sobre a definição `autoscale_target_utilization`, `autoscale_max_replicas`, e `autoscale_min_replicas`, consulte o [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none-) referência.
O exemplo seguinte demonstra como criar um novo cluster do serviço Kubernetes do Azure:

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Para obter mais informações sobre como criar um cluster do AKS fora do SDK do Azure Machine Learning, consulte os artigos seguintes:
* [Criar um cluster do AKS](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Criar um cluster do AKS (portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)


> [!IMPORTANT]
> Para [ `provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), se escolher valores personalizados para agent_count e vm_size, tem de certificar-se de que agent_count multiplicado por vm_size é maior que ou igual a 12 CPUs virtuais. Por exemplo, se usar um vm_size de "Standard_D3_v2", que tem 4 CPUs virtuais, em seguida, deve escolher um agent_count 3 ou superior.

**Estimativa de tempo**: Cerca de 20 minutos.

#### <a name="attach-an-existing-aks-cluster"></a>Anexar um cluster do AKS

Se já tiver um cluster do AKS na sua subscrição do Azure e é a versão 1.12. # # e tem, pelo menos, 12 CPUs virtuais, pode usá-lo para implementar a imagem. O código a seguir demonstra como anexar um 1.12 existente do AKS. # # cluster para a área de trabalho:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
```

## <a name="consume-web-services"></a>Consumir os serviços web

Cada serviço web implementado fornece uma API REST, para que possa criar aplicações cliente numa variedade de linguagens de programação. Se ativou a autenticação para o seu serviço, terá de fornecer uma chave de serviço como um token no cabeçalho de pedido.

### <a name="request-response-consumption"></a>Consumo de solicitação-resposta

Eis um exemplo de como invocar o serviço em Python:
```python
import requests
import json

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Para obter mais informações, consulte [criar cliente aplicações para consumir webservices](how-to-consume-web-service.md).


### <a id="azuremlcompute"></a> Consumo de batch
Destinos de computação do Machine Learning do Azure são criados e geridos pelo serviço Azure Machine Learning. Eles podem ser usados para predição de batch do Machine Learning Pipelines do Azure.

Para obter instruções de inferência de tipos do batch com a computação do Azure Machine Learning, leia os [como executar o Batch previsões](how-to-run-batch-predictions.md) artigo.

### <a id="iotedge"></a> Inferência de tipos do IoT Edge
O suporte para implementar no Edge está em pré-visualização. Para obter mais informações, consulte a [implementar o Azure Machine Learning como um módulo do IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) artigo.


## <a id="update"></a> Atualize o web services

Quando cria um novo modelo, tem de atualizar manualmente cada serviço que pretende usar o novo modelo. Para atualizar o serviço web, utilize o `update` método. O código a seguir demonstra como atualizar o serviço web para utilizar um novo modelo:

```python
from azureml.core.webservice import Webservice
from azureml.core.model import Model

# register new model
new_model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# Update to new model(s)
service.update(models = [new_model])
print(service.state)
print(service.get_logs())
```

<a id="advanced-config"></a>

## <a name="advanced-settings"></a>Definições avançadas 

**<a id="customimage"></a> Utilizar uma imagem base personalizada**

Internamente, InferenceConfig cria uma imagem do Docker que contém o modelo e outros recursos necessários ao serviço. Se não for especificado, é utilizada uma imagem de base do padrão.

Ao criar uma imagem para utilizar com a sua configuração de inferência de tipos, a imagem tem de cumprir os seguintes requisitos:

* Ubuntu 16.04 ou superior.
* Conda 4.5. # ou superior.
* Python 3.5. # ou 3.6. #.

Para utilizar uma imagem personalizada, defina o `base_image` propriedade da configuração da inferência de tipos para o endereço da imagem. O exemplo seguinte demonstra como utilizar uma imagem do tanto um público e privado Azure Container Registry:

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"

# or, use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

A imagem seguinte URIs são para imagens fornecidas pela Microsoft e pode ser utilizado sem fornecer um valor de palavra-passe ou nome de utilizador:

* `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03`

Para utilizar estas imagens, defina o `base_image` para o URI a partir da lista acima. Definir `base_image_registry.address` para `mcr.microsoft.com`.

> [!IMPORTANT]
> Imagens da Microsoft que utilizam CUDA ou TensorRT devem ser utilizadas apenas em serviços do Microsoft Azure.

Para obter mais informações sobre o carregamento de suas próprias imagens para o Azure Container Registry, veja [enviar a sua primeira imagem para um registo de contentor do Docker privado](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli).

Se é preparado o modelo na computação do Azure Machine Learning, utilizando __versão 1.0.22 ou superior__ do SDK do Azure Machine Learning, uma imagem é criada durante o treinamento. O exemplo seguinte demonstra como utilizar esta imagem:

```python
# Use an image built during training with SDK 1.0.22 or greater
image_config.base_image = run.properties["AzureML.DerivedImageName"]
```

## <a name="clean-up-resources"></a>Limpar recursos
Para eliminar um serviço web implementado, utilize `service.delete()`.
Para eliminar um modelo registado, utilize `model.delete()`.

Para obter mais informações, consulte a documentação de referência [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), e [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Passos Seguintes
* [Resolução de problemas de implementação](how-to-troubleshoot-deployment.md)
* [Proteger serviços da web do Azure Machine Learning com SSL](how-to-secure-web-service.md)
* [Consumir um modelo de ML implementado como um serviço web](how-to-consume-web-service.md)
* [Monitorizar os seus modelos do Azure Machine Learning com o Application Insights](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)

