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
ms.date: 07/08/2019
ms.custom: seoapril2019
ms.openlocfilehash: fb23e61142a639420d74c08e5a9a41324acab18b
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706287"
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

Um contentor lógico modelo registado para um ou mais arquivos que formam o seu modelo. Por exemplo, se tiver um modelo que está armazenado em múltiplos ficheiros, pode registá-los como um modelo único na área de trabalho. Depois do Registro, pode, em seguida, transferir ou implementar o modelo registado e receber todos os ficheiros que foram registados.

Modelos de Machine learning são registrados na sua área de trabalho do Azure Machine Learning. O modelo pode ser provenientes do Azure Machine Learning ou pode vir de outro lugar. Os exemplos seguintes demonstram como registar um modelo do ficheiro:

### <a name="register-a-model-from-an-experiment-run"></a>Registe-se de um modelo a partir de uma execução de experimentação

+ **Exemplo de Scikit-Saiba com o SDK**
  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```

  > [!TIP]
  > Para incluir vários ficheiros do registo de modelo, definir `model_path` para o diretório que contém os ficheiros.

+ **Com a CLI**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
  ```

  > [!TIP]
  > Para incluir vários ficheiros do registo de modelo, definir `--asset-path` para o diretório que contém os ficheiros.

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

  > [!TIP]
  > Para incluir vários ficheiros do registo de modelo, definir `model_path` para o diretório que contém os ficheiros.

+ **Com a CLI**
  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  > [!TIP]
  > Para incluir vários ficheiros do registo de modelo, definir `-p` para o diretório que contém os ficheiros.

**Estimativa de tempo**: Aproximadamente 10 segundos.

Para obter mais informações, consulte a documentação de referência para o [classe de modelo](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Para obter mais informações sobre como trabalhar com modelos treinados de serviço fora do Azure Machine Learning, consulte [como implementar um modelo existente](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Escolha um destino de computação

Os seguintes destinos, de computação ou recursos de computação, podem ser utilizadas para alojar a sua implementação do serviço web. 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Preparar para implementar

Para implementar como um serviço web, tem de criar uma configuração de inferência de tipos (`InferenceConfig`) e uma configuração de implementação. Inferência de tipos ou modelo de classificação, é a fase em que o modelo implementado é utilizado para predição, mais comumente nos dados de produção. A configuração de inferência de tipos, especifique os scripts e as dependências necessárias para atender o seu modelo. A configuração de implementação é especificar detalhes de como a servir o modelo no destino de computação.


### <a id="script"></a> 1. Definir o seu script de entrada e dependências

O script de entrada recebe dados enviados para um serviço web implementado e o transmite para o modelo. Em seguida, usa a resposta devolvida pelo modelo e retorna que para o cliente. **O script é específico ao seu modelo**; tem de compreender os dados que o modelo de espera e retorna.

O script contém duas funções que carregar e executam o modelo:

* `init()`: Normalmente, esta função carrega o modelo de um objeto global. Esta função é executada apenas uma vez quando o contentor do Docker para o seu serviço web é iniciado.

* `run(input_data)`: Essa função usa o modelo para prever um valor com base nos dados de entrada. Entradas e saídas para a execução normalmente usam JSON para a serialização e desserialização. Também pode trabalhar com dados binários não processados. Pode transformar os dados antes de enviar para o modelo ou antes de o devolver ao cliente.

#### <a name="what-is-getmodelpath"></a>O que é get_model_path?

Quando registra um modelo, é fornecer um nome de modelo utilizado para gerir o modelo no Registro. Utilize este nome com o [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) para obter o caminho dos ficheiros de modelo no sistema de ficheiros local. Se registar uma pasta ou uma coleção de arquivos, esta API devolve o caminho para o diretório que contém os arquivos.

Quando registra um modelo, atribuir um nome, que corresponde ao qual o modelo é colocado, localmente ou durante a implementação de serviço.

O exemplo abaixo irá devolver um caminho para um único arquivo chamado `sklearn_mnist_model.pkl` (que foi registado com o nome `sklearn_mnist`):

```python
model_path = Model.get_model_path('sklearn_mnist')
``` 

#### <a name="optional-automatic-swagger-schema-generation"></a>(Opcional) Geração automática de esquema do Swagger

Para automaticamente gerar um esquema para o seu serviço web, fornecer um exemplo da entrada e/ou de saída no construtor para um dos objetos de tipo definido e o tipo e o exemplo são utilizados para criar automaticamente o esquema. O serviço do Azure Machine Learning, em seguida, cria um [OpenAPI](https://swagger.io/docs/specification/about/) especificação (Swagger) para o serviço web durante a implementação.

Atualmente são suportados os seguintes tipos:

* `pandas`
* `numpy`
* `pyspark`
* objeto de Python padrão

Para utilizar a geração do esquema, inclua o `inference-schema` pacote no seu ficheiro de ambiente de conda. O exemplo seguinte utiliza `[numpy-support]` uma vez que o script de entrada utiliza um tipo de parâmetro numpy: 

#### <a name="example-dependencies-file"></a>Exemplo de ficheiro de dependências
O YAML seguinte é um exemplo de um ficheiro de dependências de Conda para inferência de tipos.

```YAML
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

#### <a name="example-script-with-dictionary-input-support-consumption-from-power-bi"></a>Script de exemplo com entradas de dicionário (consumo de suporte do Power BI)

O exemplo seguinte demonstra como definir os dados de entrada como < chave: valor > dictionary, usando o pacote de dados. Este método é suportado para consumir o serviço web implementado através do Power BI ([Saiba mais sobre como consumir o serviço web do Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration)):

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType

def init():
    global model
    model_path = Model.get_model_path('model_name')   # replace model_name with your actual model name, if needed
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)

input_sample = pd.DataFrame(data=[{
              "input_name_1": 5.1,         # This is a decimal type sample. Use the data type that reflects this column in your data
              "input_name_2": "value2",    # This is a string type sample. Use the data type that reflects this column in your data
              "input_name_3": 3            # This is a integer type sample. Use the data type that reflects this column in your data
            }])

output_sample = np.array([0])              # This is a integer type sample. Use the data type that reflects the expected result

@input_schema('data', PandasParameterType(input_sample))
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

A configuração de inferência de tipos descreve como configurar o modelo para fazer previsões. O exemplo seguinte demonstra como criar uma configuração de inferência de tipos. Esta configuração especifica o tempo de execução, o script de entrada e (opcionalmente) o ficheiro de ambiente de conda:

```python
inference_config = InferenceConfig(runtime= "python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

Para obter mais informações, consulte a [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) referência de classe.

Para obter informações sobre como utilizar uma imagem personalizada do Docker com a configuração de inferência de tipos, consulte [como implementar um modelo com uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md).

### <a name="cli-example-of-inferenceconfig"></a>Exemplo da CLI de InferenceConfig

O documento JSON seguinte é um exemplo de configuração de inferência de tipos para utilização com o machine learning CLI:

```JSON
{
   "entryScript": "x/y/score.py",
   "runtime": "python",
   "condaFile": "env/myenv.yml",
   "sourceDirectory":"C:/abc",
}
```

As entidades a seguir são válidas neste ficheiro:

* __entryScript__: Caminho do ficheiro local que contém o código para execução para a imagem.
* __runtime__: O tempo de execução a utilizar para a imagem. Tempos de execução atuais suportados são "spark-py" e "python".
* __condaFile__ (optional): Caminho do ficheiro local com uma definição de ambiente de conda a utilizar para a imagem.
* __extraDockerFileSteps__ (optional): Caminho do ficheiro local com os passos adicionais de Docker para ser executada quando a configuração de imagem.
* __sourceDirectory__ (opcional): Caminho para pastas que contém todos os ficheiros para criar a imagem.
* __enableGpu__ (optional): Se deve ou não ativar a GPU suportam na imagem. A imagem GPU tem de ser utilizada em serviços do Microsoft Azure, como o Azure Container Instances, a computação do Azure Machine Learning, máquinas virtuais do Azure e serviço Kubernetes do Azure. Predefinição é False.
* __baseImage__ (opcional): Uma imagem personalizada a ser utilizado como imagem base. Não se for fornecida nenhuma imagem base, em seguida, a imagem base será utilizada com a base do fornecidos parâmetro de tempo de execução.
* __baseImageRegistry__ (optional): Registo de imagem que contém a imagem base.
* __cudaVersion__ (opcional): Versão do CUDA instalar para imagens que têm suporte GPU. A imagem GPU tem de ser utilizada em serviços do Microsoft Azure, como o Azure Container Instances, a computação do Azure Machine Learning, máquinas virtuais do Azure e serviço Kubernetes do Azure. Versões suportadas são 9.0, 9.1 e 10.0. Se "enable_gpu" estiver definido, por predefinição '9.1'.

Estas entidades mapeiam para os parâmetros para o [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) classe.

Thee o comando a seguir demonstra como implementar um modelo com a CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

Neste exemplo, a configuração contém os seguintes itens:

* Um diretório que contém os recursos necessários para inferência de tipos
* Este modelo necessita de Python
* O [script de entrada](#script), que é utilizada para processar pedidos web enviados para o serviço implementado
* O ficheiro de conda que descreve os pacotes Python necessários para inferência de tipos

Para obter informações sobre como utilizar uma imagem personalizada do Docker com a configuração de inferência de tipos, consulte [como implementar um modelo com uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. Definir a configuração da implementação

Antes de implementar, tem de definir a configuração de implementação. A configuração de implementação é específica para o destino de computação que irá alojar o serviço web. Por exemplo, tem de especificar a porta em que o serviço aceita solicitações ao implantar localmente.

Também poderá criar o recurso de computação. Por exemplo, se fizer isso ainda não tiver um serviço de Kubernetes do Azure associado sua área de trabalho.

A tabela seguinte fornece um exemplo de criação de uma configuração de implementação para cada destino de computação:

| Destino de computação | Exemplo de configuração de implementação |
| ----- | ----- |
| Localização | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Instância de Contentor do Azure | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Serviço Kubernetes do Azure | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

As secções seguintes demonstram como criar a configuração de implementação e, em seguida, utilizá-lo para implementar o serviço web.

### <a name="optional-profile-your-model"></a>Opcional: Seu modelo de perfil
Antes de implementar o seu modelo como um serviço, pode criar o perfil para determinar a CPU ideal e requisitos de memória com o SDK ou a CLI.  Resultados de criação de perfis de modelos são emitidos como um `Run` objeto. Todos os detalhes de [o esquema do modelo de perfil pode ser encontrado na documentação da API](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)

Saiba mais no [como seu modelo com o SDK de perfil](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-)

## <a name="deploy-to-target"></a>Implementar no destino

### <a id="local"></a> Implementação de local

Para implementar localmente, tem de ter **Docker instalado** no seu computador local.

+ **Com o SDK**

  ```python
  deployment_config = LocalWebservice.deploy_configuration(port=8890)
  service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **Com a CLI**

    Para implementar com a CLI, utilize o seguinte comando. Substitua `mymodel:1` com o nome e versão do modelo registado:

  ```azurecli-interactive
  az ml model deploy -m mymodel:1 -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    As entradas na `deploymentconfig.json` mapa do documento para os parâmetros para [LocalWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). A tabela seguinte descreve o mapeamento entre as entidades no documento JSON e os parâmetros para o método:

    | Entidade JSON | Parâmetro do método | Descrição |
    | ----- | ----- | ----- |
    | `computeType` | ND | O destino de computação. Para o local, o valor tem de ser `local`. |
    | `port` | `port` | A porta local em que pretende expor o ponto final de HTTP do serviço. |

    O JSON seguinte é um exemplo de configuração de implementação para utilização com a CLI:

    ```json
    {
        "computeType": "local",
        "port": 32267
    }
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

    Para implementar com a CLI, utilize o seguinte comando. Substitua `mymodel:1` com o nome e versão do modelo registado. Substitua `myservice` com o nome para dar a esse serviço:

    ```azurecli-interactive
    az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
    ```

    As entradas na `deploymentconfig.json` mapa do documento para os parâmetros para [AciWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration?view=azure-ml-py). A tabela seguinte descreve o mapeamento entre as entidades no documento JSON e os parâmetros para o método:

    | Entidade JSON | Parâmetro do método | Descrição |
    | ----- | ----- | ----- |
    | `computeType` | ND | O destino de computação. Para o ACI, o valor tem de ser `ACI`. |
    | `containerResourceRequirements` | ND | Contém elementos de configuração para a CPU e memória alocada para o contentor. |
    | &emsp;&emsp;`cpu` | `cpu_cores` | O número de núcleos de CPU para alocar para esse serviço da web. Padrões, `0.1` |
    | &emsp;&emsp;`memoryInGB` | `memory_gb` | A quantidade de memória (em GB) para alocar para esse serviço da web. Padrão, `0.5` |
    | `location` | `location` | A região do Azure para implementar este serviço Web para. Se não for especificado a área de trabalho localização será utilizada. Obter mais detalhes sobre as regiões disponíveis podem ser encontradas aqui: [Regiões do ACI](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
    | `authEnabled` | `auth_enabled` | Se deve ou não ativar a autenticação para este serviço Web. Predefinição é falso |
    | `sslEnabled` | `ssl_enabled` | Se deve ou não ativar o SSL para este serviço Web. Predefinição é False. |
    | `appInsightsEnabled` | `enable_app_insights` | Se deve ou não ativar AppInsights para este serviço Web. Predefinição é falso |
    | `sslCertificate` | `ssl_cert_pem_file` | O ficheiro de certificado necessário se o SSL esteja ativado |
    | `sslKey` | `ssl_key_pem_file` | O ficheiro de chave necessário se o SSL esteja ativado |
    | `cname` | `ssl_cname` | O cname para se o SSL está ativado |
    | `dnsNameLabel` | `dns_name_label` | A etiqueta de nome de dns para o ponto final de classificação. Se não for especificada uma etiqueta de nome dns exclusivo será gerada para o ponto final de classificação. |

    O JSON seguinte é um exemplo de configuração de implementação para utilização com a CLI:

    ```json
    {
        "computeType": "aci",
        "containerResourceRequirements":
        {
            "cpu": 0.5,
            "memoryInGB": 1.0
        },
        "authEnabled": true,
        "sslEnabled": false,
        "appInsightsEnabled": false
    }
    ```

+ **Utilizar o VS Code**

  Para [implementar os seus modelos com o VS Code](how-to-vscode-tools.md#deploy-and-manage-models) não precisa de criar um contentor do ACI para testar com antecedência, porque os contentores ACI são criados em tempo real.

Para obter mais informações, consulte a documentação de referência para o [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) e [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) classes.

### <a id="aks"></a>Serviço Kubernetes do Azure (Dev/Test e produção)

Pode utilizar um cluster do AKS existente ou crie um novo com o SDK do Azure Machine Learning, a CLI ou o portal do Azure.

<a id="deploy-aks"></a>

Se já tiver um cluster do AKS ligado, pode implementar a ele. Se ainda não tiver criado ou anexado um cluster do AKS, siga o processo para <a href="#create-attach-aks">criar um novo cluster do AKS</a>.

+ **Com o SDK**

  ```python
  aks_target = AksCompute(ws,"myaks")
  # If deploying to a cluster configured for dev/test, ensure that it was created with enough
  # cores and memory to handle this deployment configuration. Note that memory is also used by
  # things such as dependencies and AML components.
  deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aksservice", [model], inference_config, deployment_config, aks_target)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  print(service.get_logs())
  ```

+ **Com a CLI**

    Para implementar com a CLI, utilize o seguinte comando. Substitua `myaks` destino de computação com o nome do AKS. Substitua `mymodel:1` com o nome e versão do modelo registado. Substitua `myservice` com o nome para dar a esse serviço:

  ```azurecli-interactive
  az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
  ```

    As entradas na `deploymentconfig.json` mapa do documento para os parâmetros para [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py). A tabela seguinte descreve o mapeamento entre as entidades no documento JSON e os parâmetros para o método:

    | Entidade JSON | Parâmetro do método | Descrição |
    | ----- | ----- | ----- |
    | `computeType` | ND | O destino de computação. Para o AKS, o valor tem de ser `aks`. |
    | `autoScaler` | ND | Contém elementos de configuração do dimensionamento automático. Consulte a tabela de dimensionamento automático. |
    | &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Se deve ou não ativar o dimensionamento automático para o serviço web. Se `numReplicas`  =  `0`, `True`; caso contrário, `False`. |
    | &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | O número mínimo de contentores para utilizar quando o dimensionamento automático esse web service. Predefinido, `1`. |
    | &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | O número máximo de contentores para utilizar quando o dimensionamento automático esse web service. Predefinido, `10`. |
    | &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | A frequência com que o dimensionamento automático tenta Dimensionar este serviço web. Predefinido, `1`. |
    | &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | Utilização de destino (em percentagem 100) que o dimensionamento automático deve tentar manter para esse serviço da web. Predefinido, `70`. |
    | `dataCollection` | ND | Contém elementos de configuração para a recolha de dados. |
    | &emsp;&emsp;`storageEnabled` | `collect_model_data` | Se deve ou não ativar a recolha de dados de modelo para o serviço web. Predefinido, `False`. |
    | `authEnabled` | `auth_enabled` | Se deve ou não ativar a autenticação para o serviço web. Predefinido, `True`. |
    | `containerResourceRequirements` | ND | Contém elementos de configuração para a CPU e memória alocada para o contentor. |
    | &emsp;&emsp;`cpu` | `cpu_cores` | O número de núcleos de CPU para alocar para esse serviço da web. Padrões, `0.1` |
    | &emsp;&emsp;`memoryInGB` | `memory_gb` | A quantidade de memória (em GB) para alocar para esse serviço da web. Padrão, `0.5` |
    | `appInsightsEnabled` | `enable_app_insights` | Se deve ou não ativar o registo do Application Insights para o serviço web. Predefinido, `False`. |
    | `scoringTimeoutMs` | `scoring_timeout_ms` | Um tempo limite para impor a chamadas para o serviço web de classificação. Predefinido, `60000`. |
    | `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | Os pedidos em simultâneo máximos por nó para este serviço web. Predefinido, `1`. |
    | `maxQueueWaitMs` | `max_request_wait_time` | O tempo máximo que uma solicitação permanecerá no thee fila (em milissegundos) antes de um 503 é devolvido o erro. Predefinido, `500`. |
    | `numReplicas` | `num_replicas` | O número de contentores para alocar para esse serviço da web. Não existem valores predefinidos. Se este parâmetro não for definido, o dimensionamento automático está ativado por predefinição. |
    | `keys` | ND | Contém elementos de configuração para as chaves. |
    | &emsp;&emsp;`primaryKey` | `primary_key` | Uma chave de autenticação principal para utilizar para este serviço Web |
    | &emsp;&emsp;`secondaryKey` | `secondary_key` | Uma chave de autenticação secundária a utilizar para este serviço Web |
    | `gpuCores` | `gpu_cores` | O número de núcleos GPU para alocar para este serviço Web. A predefinição é 1. |
    | `livenessProbeRequirements` | ND | Contém elementos de configuração para os requisitos de sonda de liveness. |
    | &emsp;&emsp;`periodSeconds` | `period_seconds` | Frequência (em segundos) para efetuar à sonda de atividade. Como padrão para 10 segundos. Valor mínimo é 1. |
    | &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Número de segundos depois do contentor foi iniciada antes de sondas de liveness são iniciadas. Predefinição é 310 |
    | &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Número de segundos após o qual a a sonda de exceder o tempo limite. Assume a predefinição de 2 segundos. Valor mínimo é 1 |
    | &emsp;&emsp;`successThreshold` | `success_threshold` | Êxitos consecutivos mínimo para a sonda ser considerado com êxito após ter falhou. Predefinição é 1. Valor mínimo é 1. |
    | &emsp;&emsp;`failureThreshold` | `failure_threshold` | Quando inicia um Pod e a a sonda de atividade falha, o Kubernetes tentará failureThreshold vezes antes de desistir. Predefinição é 3. Valor mínimo é 1. |
    | `namespace` | `namespace` | O namespace do Kubernetes que o serviço Web é implementada num. Até 63 minúsculas de alfanuméricos ('a'-'z', '0'-"9") e hífen ('-') carateres. Os primeiro e último carateres não podem ser hífenes. |

    O JSON seguinte é um exemplo de configuração de implementação para utilização com a CLI:

    ```json
    {
        "computeType": "aks",
        "autoScaler":
        {
            "autoscaleEnabled": true,
            "minReplicas": 1,
            "maxReplicas": 3,
            "refreshPeriodInSeconds": 1,
            "targetUtilization": 70
        },
        "dataCollection":
        {
            "storageEnabled": true
        },
        "authEnabled": true,
        "containerResourceRequirements":
        {
            "cpu": 0.5,
            "memoryInGB": 1.0
        }
    }
    ```

+ **Utilizar o VS Code**

  Também pode [implementar no AKS através da extensão do VS Code](how-to-vscode-tools.md#deploy-and-manage-models), mas também precisará configurar clusters do AKS com antecedência.

Saiba mais sobre implementação do AKS e Dimensionar automaticamente na [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice) referência.

#### Criar um novo cluster do AKS<a id="create-attach-aks"></a>
**Estimativa de tempo**: Cerca de 20 minutos.

Criar ou anexar um cluster do AKS é uma hora de um processo para a área de trabalho. Pode reutilizar este cluster para várias implementações. Se eliminar o cluster ou o grupo de recursos que o contém, tem de criar um novo cluster da próxima vez que precisa implantar. Pode ter vários clusters do AKS anexados à sua área de trabalho.

Se pretender criar um cluster do AKS para desenvolvimento, validação e teste, defina `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` ao utilizar [ `provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py). Um cluster criado com esta definição só terá um nó.

> [!IMPORTANT]
> Definição `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` cria um cluster do AKS que não é adequado para processar o tráfego de produção. Horas de inferência de tipos podem ser mais do que num cluster criado para produção. Tolerância a falhas também não é garantida para os clusters de programador/teste.
>
> Recomendamos que os clusters criados para programação/teste utilizam, pelo menos, duas CPUs virtuais.

O exemplo seguinte demonstra como criar um novo cluster do serviço Kubernetes do Azure:

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksComputee.ClusterPurpose.DEV_TEST)
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

Para obter mais informações sobre o `cluster_purpose` parâmetro, consulte a [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) referência.

> [!IMPORTANT]
> Para [ `provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), se escolher valores personalizados para agent_count e vm_size, tem de certificar-se de que agent_count multiplicado por vm_size é maior que ou igual a 12 CPUs virtuais. Por exemplo, se usar um vm_size de "Standard_D3_v2", que tem 4 CPUs virtuais, em seguida, deve escolher um agent_count 3 ou superior.
>
> O SDK do Azure Machine Learning não fornece suporte dimensionar um cluster do AKS. Para dimensionar os nós do cluster, utilize a interface do Usuário do seu cluster do AKS no portal do Azure. Só é possível alterar a contagem de nós, não o tamanho da VM do cluster.

#### <a name="attach-an-existing-aks-cluster"></a>Anexar um cluster do AKS
**Estimativa de tempo:** Cerca de cinco minutos.

Se já tiver um cluster do AKS na sua subscrição do Azure e é a versão 1.12. # #, pode usá-lo para implementar a imagem.

> [!WARNING]
> Ao anexar um cluster do AKS para uma área de trabalho, pode definir como irá utilizar o cluster ao definir o `cluster_purpose` parâmetro.
>
> Se não definir o `cluster_purpose` parâmetro ou conjunto `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`, em seguida, o cluster tem de ter, pelo menos, 12 CPUs virtuais disponíveis.
>
> Se definir `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, em seguida, o cluster não precisa de ter 12 CPUs virtuais. No entanto, um cluster que está configurado para programação/teste não será adequado para o tráfego ao nível da produção e pode aumentar os tempos de inferência de tipos.

O código a seguir demonstra como anexar um 1.12 existente do AKS. # # cluster para a área de trabalho:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
```

Para obter mais informações sobre `attack_configuration()`, consulte a [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-) referência.

Para obter mais informações sobre o `cluster_purpose` parâmetro, consulte a [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) referência.

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


### <a id="azuremlcompute"></a> Inferência de tipos do batch
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

## <a name="continuous-model-deployment"></a>Implementação contínua do modelo 

Pode implementar modelos com a extensão de Machine Learning para continuamente [do Azure DevOps](https://azure.microsoft.com/services/devops/). Ao utilizar a extensão de Machine Learning para DevOps do Azure, pode acionar um pipeline de implementação quando um novo modelo de aprendizagem automática é registado no Azure Machine Learning a área de trabalho de serviço. 

1. Inscreva-se [Pipelines do Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), que possibilita a integração contínua e entrega da sua aplicação de qualquer plataforma de/para qualquer cloud. Pipelines do Azure [difere pipelines de ML](concept-ml-pipelines.md#compare). 

1. [Crie um projeto de DevOps do Azure.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Instalar o [extensão de Machine Learning para Pipelines do Azure](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList) 

1. Uso __ligações de serviço__ para configurar uma ligação de principal de serviço para a área de trabalho do serviço do Azure Machine Learning para aceder a todos os seus artefactos. Aceda às definições do projeto e clicar em conexões de serviço para selecionar o Azure Resource Manager.

    ![view-service-connection](media/how-to-deploy-and-where/view-service-connection.png) 

1. Definir AzureMLWorkspace como o __âmbito de nível__ e preencha os parâmetros subsequentes.

    ![view-azure-resource-manager](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Em seguida, para implementar continuamente o seu modelo de aprendizagem automática com os Pipelines do Azure, em pipelines, selecione __versão__. Adicionar um artefato de novo, selecione o artefacto de modelo do AzureML e a ligação de serviço que foi criada no passo anterior. Selecione o modelo e versão para acionar uma implementação. 

    ![select-AzureMLmodel-artifact](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)

1. Ative o acionador de modelo no artefacto seu modelo. Ao ativar o acionador, sempre que a versão especificada (ou seja a versão mais recente) desse modelo é Registre-se na sua área de trabalho, um pipeline de lançamento de DevOps do Azure é acionado. 

    ![enable-model-trigger](media/how-to-deploy-and-where/set-modeltrigger.png)

Para projetos de exemplo e exemplos, confira [o repositório de MLOps](https://github.com/Microsoft/MLOps)

## <a name="clean-up-resources"></a>Limpar recursos
Para eliminar um serviço web implementado, utilize `service.delete()`.
Para eliminar um modelo registado, utilize `model.delete()`.

Para obter mais informações, consulte a documentação de referência [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), e [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Passos seguintes
* [Como implementar um modelo com uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Resolução de problemas de implementação](how-to-troubleshoot-deployment.md)
* [Proteger serviços da web do Azure Machine Learning com SSL](how-to-secure-web-service.md)
* [Consumir um modelo de ML implementado como um serviço web](how-to-consume-web-service.md)
* [Monitorizar os seus modelos do Azure Machine Learning com o Application Insights](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)

