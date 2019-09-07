---
title: Como e onde implantar modelos
titleSuffix: Azure Machine Learning service
description: Saiba como e onde implantar seus modelos de serviço de Azure Machine Learning, incluindo instâncias de contêiner do Azure, serviço kubernetes do Azure, Azure IoT Edge e matrizes de portão programáveis por campo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 08/06/2019
ms.custom: seoapril2019
ms.openlocfilehash: cf72a83035e318d3a937176bbaaebd8e298d3ad2
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390575"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Implementar modelos com o serviço Azure Machine Learning

Saiba como implantar o modelo de aprendizado de máquina como um serviço Web na nuvem do Azure ou para Azure IoT Edge dispositivos.

O fluxo de trabalho é semelhante [, independentemente de onde você implanta](#target) seu modelo:

1. Registe o modelo.
1. Prepare-se para implantar. (Especifique ativos, uso e destino de computação.)
1. Implante o modelo no destino de computação.
1. Teste o modelo implantado, também chamado de serviço Web.

Para obter mais informações sobre os conceitos envolvidos no fluxo de trabalho de implantação, consulte [gerenciar, implantar e monitorar modelos com o serviço Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma área de trabalho de serviço do Azure Machine Learning. Para obter mais informações, consulte [criar um Azure Machine Learning espaço de trabalho de serviço](how-to-manage-workspace.md).

- Um modelo. Se você não tiver um modelo treinado, poderá usar os arquivos de modelo e de dependência fornecidos neste [tutorial](https://aka.ms/azml-deploy-cloud).

- A [extensão CLI do Azure para o serviço Machine Learning](reference-azure-machine-learning-cli.md), o [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou a [extensão Azure Machine Learning Visual Studio Code](how-to-vscode-tools.md).

## <a name="connect-to-your-workspace"></a>Conectar-se ao seu espaço de trabalho

O código a seguir mostra como se conectar a um espaço de trabalho de serviço do Azure Machine Learning usando informações armazenadas em cache para o ambiente de desenvolvimento local:

+ **Usando o SDK**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Para obter mais informações sobre como usar o SDK para se conectar a um espaço de trabalho, consulte a documentação [Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) .

+ **Usando a CLI**

   Ao usar a CLI, use o `-w` parâmetro `--workspace-name` ou para especificar o espaço de trabalho para o comando.

+ **Usando VS Code**

   Ao usar VS Code, você seleciona o espaço de trabalho usando uma interface gráfica. Para obter mais informações, consulte [implantar e gerenciar modelos](how-to-vscode-tools.md#deploy-and-manage-models) na documentação de extensão de vs Code.

## <a id="registermodel"></a>Registrar seu modelo

Um modelo registrado é um contêiner lógico para um ou mais arquivos que compõem seu modelo. Por exemplo, se você tiver um modelo armazenado em vários arquivos, poderá registrá-los como um único modelo no espaço de trabalho. Depois de registrar os arquivos, você pode baixar ou implantar o modelo registrado e receber todos os arquivos que você registrou.

> [!TIP]
> Ao registrar um modelo, você fornece o caminho de um local de nuvem (de uma execução de treinamento) ou um diretório local. Esse caminho é apenas para localizar os arquivos para upload como parte do processo de registro. Ele não precisa corresponder ao caminho usado no script de entrada. Para obter mais informações, consulte [o que é get_model_path?](#what-is-get_model_path).

Os modelos de aprendizado de máquina são registrados em seu espaço de trabalho do Azure Machine Learning. O modelo pode vir de Azure Machine Learning ou de outro lugar. Os exemplos a seguir demonstram como registrar um modelo.

### <a name="register-a-model-from-an-experiment-run"></a>Registrar um modelo de uma execução de experimento

Os trechos de código nesta seção demonstram como registrar um modelo de uma execução de treinamento:

> [!IMPORTANT]
> Para usar esses trechos de código, você precisa ter executado anteriormente uma execução de treinamento e precisa ter acesso ao `Run` objeto (exemplo de SDK) ou ao valor da ID de execução (exemplo de CLI). Para obter mais informações sobre modelos de treinamento, consulte [Configurar destinos de computação para treinamento de modelo](how-to-set-up-training-targets.md).

+ **Usando o SDK**

  Ao usar o SDK para treinar um modelo, você pode receber um objeto de [execução](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master) ou um objeto [AutoMLRun](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master) , dependendo de como você treinou o modelo. Cada objeto pode ser usado para registrar um modelo criado por uma execução de experimento.

  + Registrar um modelo de um `azureml.core.Run` objeto:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    O `model_path` parâmetro refere-se ao local da nuvem do modelo. Neste exemplo, o caminho de um único arquivo é usado. Para incluir vários arquivos no registro do modelo, defina `model_path` para o caminho de uma pasta que contém os arquivos. Para obter mais informações, consulte a documentação do [Run. register_model](https://review.docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py&branch=master#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none----kwargs-) .

  + Registrar um modelo de um `azureml.train.automl.run.AutoMLRun` objeto:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description)

        print(run.model_id)
    ```

    Neste exemplo, os `metric` parâmetros e `iteration` não são especificados, portanto, a iteração com a melhor métrica primária será registrada. O `model_id` valor retornado da execução é usado em vez de um nome de modelo.

    Para obter mais informações, consulte a documentação do [AutoMLRun. register_model](https://review.docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.run.automlrun?view=azure-ml-py&branch=master#register-model-description-none--tags-none--iteration-none--metric-none-) .

+ **Usando a CLI**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  [!INCLUDE [install extension](../../../includes/machine-learning-service-install-extension.md)]

  O `--asset-path` parâmetro refere-se ao local da nuvem do modelo. Neste exemplo, o caminho de um único arquivo é usado. Para incluir vários arquivos no registro do modelo, defina `--asset-path` para o caminho de uma pasta que contém os arquivos.

+ **Usando VS Code**

  Registre modelos usando qualquer arquivo ou pasta de modelo usando a extensão [vs Code](how-to-vscode-tools.md#deploy-and-manage-models) .

### <a name="register-a-model-from-a-local-file"></a>Registrar um modelo de um arquivo local

Você pode registrar um modelo fornecendo o caminho local do modelo. Você pode fornecer o caminho de uma pasta ou de um único arquivo. Você pode usar esse método para registrar modelos treinados com o serviço de Azure Machine Learning e, em seguida, baixado. Você também pode usar esse método para registrar modelos treinados fora do Azure Machine Learning.

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

+ **Usando o SDK e o ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Para incluir vários arquivos no registro do modelo, defina `model_path` para o caminho de uma pasta que contém os arquivos.

+ **Usando a CLI**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Para incluir vários arquivos no registro do modelo, defina `-p` para o caminho de uma pasta que contém os arquivos.

**Tempo estimado**: Aproximadamente 10 segundos.

Para obter mais informações, consulte a documentação da [classe Model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Para obter mais informações sobre como trabalhar com modelos treinados fora do serviço de Azure Machine Learning, consulte [como implantar um modelo existente](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Escolher um destino de computação

Você pode usar os seguintes destinos de computação ou recursos de computação para hospedar a implantação do serviço Web:

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>Preparar para implementar

Para implantar o modelo, você precisa dos seguintes itens:

* **Um script de entrada**. Esse script aceita solicitações, pontua as solicitações usando o modelo e retorna os resultados.

    > [!IMPORTANT]
    > * O script de entrada é específico para seu modelo. Ele deve entender o formato dos dados de solicitação de entrada, o formato dos dados esperados pelo seu modelo e o formato dos dados retornados aos clientes.
    >
    >   Se os dados da solicitação estiverem em um formato que não pode ser usado pelo seu modelo, o script poderá transformá-lo em um formato aceitável. Ele também pode transformar a resposta antes de retorná-la ao cliente.
    >
    > * O SDK do Azure Machine Learning não fornece uma maneira para serviços Web ou implantações de IoT Edge para acessar seu armazenamento de dados ou conjuntos de dados. Se o modelo implantado precisar acessar dados armazenados fora da implantação, como dados em uma conta de armazenamento do Azure, você deverá desenvolver uma solução de código personalizada usando o SDK relevante. Por exemplo, o [SDK do armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python).
    >
    >   Uma alternativa que pode funcionar para seu cenário é a [previsão de lote](how-to-run-batch-predictions.md), que fornece acesso a armazenamentos de dados durante a pontuação.

* **Dependências**, como scripts auxiliares ou pacotes python/Conda necessários para executar o script ou modelo de entrada.

* **A configuração de implantação** para o destino de computação que hospeda o modelo implantado. Essa configuração descreve coisas como requisitos de memória e CPU necessários para executar o modelo.

Esses itens são encapsulados em uma *configuração de inferência* e uma *configuração de implantação*. A configuração de inferência referencia o script de entrada e outras dependências. Você define essas configurações programaticamente ao usar o SDK para executar a implantação. Você os define em arquivos JSON ao usar a CLI.

### <a id="script"></a> 1. Definir o script de entrada e as dependências

O script de entrada recebe dados enviados para um serviço Web implantado e os transmite para o modelo. Em seguida, ele pega a resposta retornada pelo modelo e a retorna ao cliente. *O script é específico para seu modelo*. Ele deve entender os dados esperados e retornados pelo modelo.

O script contém duas funções que carregam e executam o modelo:

* `init()`: Normalmente, essa função carrega o modelo em um objeto global. Essa função é executada apenas uma vez, quando o contêiner do Docker para o serviço Web é iniciado.

* `run(input_data)`: Essa função usa o modelo para prever um valor com base nos dados de entrada. As entradas e saídas da execução normalmente usam JSON para serialização e desserialização. Você também pode trabalhar com dados binários brutos. Você pode transformar os dados antes de enviá-los para o modelo ou antes de retorná-los para o cliente.

#### <a name="what-is-get_model_path"></a>O que é o get_model_path?

Ao registrar um modelo, você fornece um nome de modelo que é usado para gerenciar o modelo no registro. Use esse nome com o método [Model. Get _model_path ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) para recuperar o caminho do arquivo de modelo ou arquivos no sistema de arquivos local. Se você registrar uma pasta ou uma coleção de arquivos, essa API retornará o caminho do diretório que contém esses arquivos.

Ao registrar um modelo, você lhe dá um nome. O nome corresponde ao local em que o modelo é colocado, seja localmente ou durante a implantação do serviço.

> [!IMPORTANT]
> Se você usou o Machine Learning automatizado para treinar um modelo `model_id` , um valor é usado como o nome do modelo. Para obter um exemplo de registro e implantação de um modelo treinado com o Machine Learning automatizado, consulte [Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-with-deployment) no github.

O exemplo a seguir retornará um caminho para um único arquivo chamado `sklearn_mnist_model.pkl` (que foi registrado com o nome `sklearn_mnist`):

```python
model_path = Model.get_model_path('sklearn_mnist')
```

<a id="schema"></a>

#### <a name="optional-automatic-schema-generation"></a>Adicional Geração de esquema automática

Para gerar automaticamente um esquema para o serviço Web, forneça um exemplo de entrada e/ou saída no construtor para um dos objetos de tipo definidos. O tipo e o exemplo são usados para criar automaticamente o esquema. Em seguida, o serviço de Azure Machine Learning cria uma especificação de [openapi](https://swagger.io/docs/specification/about/) (Swagger) para o serviço Web durante a implantação.

Atualmente, esses tipos têm suporte:

* `pandas`
* `numpy`
* `pyspark`
* Objeto Python padrão

Para usar a geração de esquema, `inference-schema` inclua o pacote em seu arquivo de ambiente Conda.

##### <a name="example-dependencies-file"></a>Arquivo de dependências de exemplo

O YAML a seguir é um exemplo de um arquivo de dependências Conda para inferência:

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn==0.20.0
    - inference-schema[numpy-support]
```

Se você quiser usar a geração de esquema automática, seu script de entrada deverá `inference-schema` importar os pacotes.

Defina os formatos de exemplo de entrada e saída `input_sample` nas `output_sample` variáveis e, que representam os formatos de solicitação e resposta para o serviço Web. Use esses exemplos nos decoradores da função de entrada e saída na `run()` função. O exemplo a seguir scikit-Learn usa a geração de esquema.

##### <a name="example-entry-script"></a>Exemplo de script de entrada

O exemplo a seguir demonstra como aceitar e retornar dados JSON:

```python
#Example: scikit-learn and Swagger
import json
import numpy as np
from sklearn.externals import joblib
from sklearn.linear_model import Ridge
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # Note that here "sklearn_regression_model.pkl" is the name of the model registered under.
    # This is a different behavior than before when the code is run locally, even though the code is the same.
    model_path = Model.get_model_path('sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = np.array([[10, 9, 8, 7, 6, 5, 4, 3, 2, 1]])
output_sample = np.array([3726.995])


@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

O exemplo a seguir demonstra como definir os dados de entrada como `<key: value>` um dicionário usando um dataframe. Esse método tem suporte para o consumo do serviço Web implantado do Power BI. ([Saiba mais sobre como consumir o serviço Web do Power bi](https://docs.microsoft.com/power-bi/service-machine-learning-integration).)

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
    # Replace model_name with your actual model name, if necessary.
    model_path = Model.get_model_path('model_name')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)


input_sample = pd.DataFrame(data=[{
    # This is a decimal type sample. Use the data type that reflects this column in your data.
    "input_name_1": 5.1,
    # This is a string type sample. Use the data type that reflects this column in your data.
    "input_name_2": "value2",
    # This is an integer type sample. Use the data type that reflects this column in your data.
    "input_name_3": 3
}])

# This is an integer type sample. Use the data type that reflects the expected result.
output_sample = np.array([0])


@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Para obter mais exemplos, consulte os seguintes scripts:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

<a id="binary"></a>

#### <a name="binary-data"></a>Dados binários

Se o modelo aceitar dados binários, como uma imagem, você deverá modificar `score.py` o arquivo usado para sua implantação para aceitar solicitações HTTP brutas. Para aceitar dados brutos, use `AMLRequest` a classe em seu script de entrada e `@rawhttp` adicione o decorador à `run()` função.

Aqui está um exemplo de um `score.py` que aceita dados binários:

```python
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse


def init():
    print("This is init()")


@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example just returns the posted data as the response.
        return AMLResponse(reqBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> A `AMLRequest` classe está `azureml.contrib` no namespace. As entidades neste namespace são alteradas com frequência enquanto trabalhamos para melhorar o serviço. Qualquer coisa neste namespace deve ser considerada uma versão prévia que não tem suporte total da Microsoft.
>
> Se precisar testar isso em seu ambiente de desenvolvimento local, você poderá instalar os componentes usando o seguinte comando:
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

#### <a name="cross-origin-resource-sharing-cors"></a>CORS (compartilhamento de recursos entre origens)

O compartilhamento de recursos entre origens é uma maneira de permitir que recursos em uma página da Web sejam solicitados de outro domínio. O CORS funciona por meio de cabeçalhos HTTP enviados com a solicitação do cliente e retornado com a resposta do serviço. Para obter mais informações sobre CORS e cabeçalhos válidos, consulte [compartilhamento de recursos entre origens](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) na Wikipédia.

Para configurar sua implantação de modelo para dar suporte a CORS `AMLResponse` , use a classe em seu script de entrada. Essa classe permite que você defina os cabeçalhos no objeto de resposta.

O exemplo a seguir define `Access-Control-Allow-Origin` o cabeçalho para a resposta do script de entrada:

```python
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    if request.method == 'GET':
        # For this example, just return the URL for GETs.
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        # For a real-world solution, you would load the data from reqBody
        # and send it to the model. Then return the response.

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> A `AMLResponse` classe está `azureml.contrib` no namespace. As entidades neste namespace são alteradas com frequência enquanto trabalhamos para melhorar o serviço. Qualquer coisa neste namespace deve ser considerada uma versão prévia que não tem suporte total da Microsoft.
>
> Se precisar testar isso em seu ambiente de desenvolvimento local, você poderá instalar os componentes usando o seguinte comando:
>
> ```shell
> pip install azureml-contrib-services
> ```

### <a name="2-define-your-inferenceconfig"></a>2. Definir seu InferenceConfig

A configuração de inferência descreve como configurar o modelo para fazer previsões. Essa configuração não faz parte do seu script de entrada. Ele faz referência ao seu script de entrada e é usado para localizar todos os recursos exigidos pela implantação. Ele é usado posteriormente, quando você implanta o modelo.

A configuração de inferência pode usar ambientes Azure Machine Learning para definir as dependências de software necessárias para sua implantação. Os ambientes permitem que você crie, gerencie e reutilize as dependências de software necessárias para treinamento e implantação. O exemplo a seguir demonstra como carregar um ambiente do seu espaço de trabalho e usá-lo com a configuração de inferência:

```python
from azureml.core import Environment
from azureml.core.model import InferenceConfig

deploy_env = Environment.get(workspace=ws,name="myenv",version="1")
inference_config = InferenceConfig(entry_script="x/y/score.py",
                                   environment=deploy_env)
```

Para obter mais informações sobre ambientes, consulte [criar e gerenciar ambientes para treinamento e implantação](how-to-use-environments.md).

Você também pode especificar diretamente as dependências sem usar um ambiente. O exemplo a seguir demonstra como criar uma configuração de inferência que carrega as dependências de software de um arquivo Conda:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime="python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

Para obter mais informações, consulte a documentação da classe [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) .

Para obter informações sobre como usar uma imagem personalizada do Docker com uma configuração de inferência, consulte [como implantar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md).

### <a name="cli-example-of-inferenceconfig"></a>Exemplo de CLI de InferenceConfig

[!INCLUDE [inference config](../../../includes/machine-learning-service-inference-config.md)]

O comando a seguir demonstra como implantar um modelo usando a CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

Neste exemplo, a configuração especifica as seguintes configurações:

* Se o modelo requer Python.
* O [script de entrada](#script), que é usado para manipular solicitações da Web enviadas ao serviço implantado.
* O arquivo Conda que descreve os pacotes do python necessários para a inferência.

Para obter informações sobre como usar uma imagem personalizada do Docker com uma configuração de inferência, consulte [como implantar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. Definir sua configuração de implantação

Antes de implantar seu modelo, você deve definir a configuração de implantação. *A configuração de implantação é específica para o destino de computação que hospedará o serviço Web.* Por exemplo, ao implantar um modelo localmente, você deve especificar a porta onde o serviço aceita solicitações. A configuração de implantação não faz parte do script de entrada. Ele é usado para definir as características do destino de computação que hospedará o modelo e o script de entrada.

Talvez você também precise criar o recurso de computação, se, por exemplo, você ainda não tiver uma instância do AKS (serviço kubernetes do Azure) associada ao seu espaço de trabalho.

A tabela a seguir fornece um exemplo de criação de uma configuração de implantação para cada destino de computação:

| Destino de computação | Exemplo de configuração de implantação |
| ----- | ----- |
| Localização | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Serviço Kubernetes do Azure | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

As classes para local, instâncias de contêiner do Azure e serviços Web AKS podem ser importadas de `azureml.core.webservice`:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

#### <a name="profiling"></a>Criação de perfis

Antes de implantar seu modelo como um serviço, talvez você queira criar um perfil para determinar os requisitos de CPU e memória ideais. Você pode usar o SDK ou a CLI para criar um perfil para o modelo. Os exemplos a seguir mostram como criar um perfil de um modelo usando o SDK.

> [!IMPORTANT]
> Quando você usa a criação de perfil, a configuração de inferência que você fornece não pode fazer referência a um ambiente de Azure Machine Learning. Em vez disso, defina as dependências de `conda_file` software usando o `InferenceConfig` parâmetro do objeto.

```python
import json
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10]
]})

profile = Model.profile(ws, "profilemymodel", [model], inference_config, test_data)
profile.wait_for_profiling(true)
profiling_results = profile.get_results()
print(profiling_results)
```

Esse código exibe um resultado semelhante à seguinte saída:

```python
{'cpu': 1.0, 'memoryInGB': 0.5}
```

Os resultados de criação de perfil de modelo são `Run` emitidos como um objeto.

Para saber mais sobre como usar a criação de perfil da CLI, confira [perfil de modelo AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

Para obter mais informações, consulte estes documentos:

* [ModelProfile](https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py)
* [Perfil ()](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-)
* [Esquema do arquivo de configuração de inferência](reference-azure-machine-learning-cli.md#inference-configuration-schema)

## <a name="deploy-to-target"></a>Implantar no destino

A implantação usa a configuração de implantação de configuração de inferência para implantar os modelos. O processo de implantação é semelhante, independentemente do destino de computação. A implantação em AKS é um pouco diferente, pois você deve fornecer uma referência ao cluster AKS.

### <a id="local"></a>Implantação local

Para implantar um modelo localmente, você precisa ter o Docker instalado no computador local.

#### <a name="using-the-sdk"></a>Utilizar o SDK

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Para obter mais informações, consulte a documentação de [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [Model. Deployment ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)e [WebService](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Usando a CLI

Para implantar um modelo usando a CLI, use o comando a seguir. Substituir `mymodel:1` pelo nome e versão do modelo registrado:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../../includes/machine-learning-service-local-deploy-config.md)]

Para obter mais informações, consulte a documentação de [implantação do modelo AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

### <a id="notebookvm"></a>Serviço Web de VM do notebook (desenvolvimento/teste)

Consulte [implantar um modelo para VMs de notebook](how-to-deploy-local-container-notebook-vm.md).

### <a id="aci"></a>Instâncias de contêiner do Azure (desenvolvimento/teste)

Consulte [implantar em instâncias de contêiner do Azure](how-to-deploy-azure-container-instance.md).

### <a id="aks"></a>Serviço kubernetes do Azure (desenvolvimento/teste e produção)

Consulte [implantar no serviço kubernetes do Azure](how-to-deploy-azure-kubernetes-service.md).

## <a name="consume-web-services"></a>Consumir os serviços web

Cada serviço Web implantado fornece uma API REST, para que você possa criar aplicativos cliente em uma variedade de linguagens de programação.
Se você habilitou a autenticação de chave para seu serviço, precisará fornecer uma chave de serviço como um token no seu cabeçalho de solicitação.
Se você tiver habilitado a autenticação de token para seu serviço, será necessário fornecer um token Azure Machine Learning JWT como um token de portador no cabeçalho da solicitação.

> [!TIP]
> Você pode recuperar o documento JSON do esquema depois de implantar o serviço. Use a [Propriedade swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) do serviço Web implantado (por exemplo `service.swagger_uri`,) para obter o URI para o arquivo Swagger do serviço Web local.

### <a name="request-response-consumption"></a>Consumo de solicitação-resposta

Aqui está um exemplo de como invocar seu serviço no Python:
```python
import requests
import json

headers = {'Content-Type': 'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]
elif service.token_auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_token()[0]

print(headers)

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

response = requests.post(
    service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Para obter mais informações, consulte [criar aplicativos cliente para consumir serviços Web](how-to-consume-web-service.md).

### <a name="web-service-schema-openapi-specification"></a>Esquema de serviço Web (especificação OpenAPI)

Se você usou a geração de esquema automática com sua implantação, você pode obter o endereço da especificação OpenAPI para o serviço usando a [Propriedade swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). (Por exemplo, `print(service.swagger_uri)`.) Use uma solicitação GET ou abra o URI em um navegador para recuperar a especificação.

O documento JSON a seguir é um exemplo de um esquema (especificação de OpenAPI) gerado para uma implantação:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for the Azure Machine Learning service myservice",
        "version": "1.0"
    },
    "schemes": [
        "https"
    ],
    "consumes": [
        "application/json"
    ],
    "produces": [
        "application/json"
    ],
    "securityDefinitions": {
        "Bearer": {
            "type": "apiKey",
            "name": "Authorization",
            "in": "header",
            "description": "For example: Bearer abc123"
        }
    },
    "paths": {
        "/": {
            "get": {
                "operationId": "ServiceHealthCheck",
                "description": "Simple health check endpoint to ensure the service is up at any given point.",
                "responses": {
                    "200": {
                        "description": "If service is up and running, this response will be returned with the content 'Healthy'",
                        "schema": {
                            "type": "string"
                        },
                        "examples": {
                            "application/json": "Healthy"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        },
        "/score": {
            "post": {
                "operationId": "RunMLService",
                "description": "Run web service's model and get the prediction output",
                "security": [
                    {
                        "Bearer": []
                    }
                ],
                "parameters": [
                    {
                        "name": "serviceInputPayload",
                        "in": "body",
                        "description": "The input payload for executing the real-time machine learning service.",
                        "schema": {
                            "$ref": "#/definitions/ServiceInput"
                        }
                    }
                ],
                "responses": {
                    "200": {
                        "description": "The service processed the input correctly and provided a result prediction, if applicable.",
                        "schema": {
                            "$ref": "#/definitions/ServiceOutput"
                        }
                    },
                    "default": {
                        "description": "The service failed to execute due to an error.",
                        "schema": {
                            "$ref": "#/definitions/ErrorResponse"
                        }
                    }
                }
            }
        }
    },
    "definitions": {
        "ServiceInput": {
            "type": "object",
            "properties": {
                "data": {
                    "type": "array",
                    "items": {
                        "type": "array",
                        "items": {
                            "type": "integer",
                            "format": "int64"
                        }
                    }
                }
            },
            "example": {
                "data": [
                    [ 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 ]
                ]
            }
        },
        "ServiceOutput": {
            "type": "array",
            "items": {
                "type": "number",
                "format": "double"
            },
            "example": [
                3726.995
            ]
        },
        "ErrorResponse": {
            "type": "object",
            "properties": {
                "status_code": {
                    "type": "integer",
                    "format": "int32"
                },
                "message": {
                    "type": "string"
                }
            }
        }
    }
}
```

Para obter mais informações, consulte [especificação de openapi](https://swagger.io/specification/).

Para um utilitário que pode criar bibliotecas de cliente a partir da especificação, consulte [Swagger-CodeGen](https://github.com/swagger-api/swagger-codegen).

### <a id="azuremlcompute"></a>Inferência de lote
Azure Machine Learning destinos de computação são criados e gerenciados pelo serviço de Azure Machine Learning. Eles podem ser usados para previsão de lote de pipelines de Azure Machine Learning.

Para obter uma explicação sobre a inferência de lote com Azure Machine Learning computação, consulte [como executar previsões de lote](how-to-run-batch-predictions.md).

### <a id="iotedge"></a>Inferência de IoT Edge
O suporte para a implantação no Edge está em versão prévia. Para obter mais informações, consulte [implantar Azure Machine Learning como um módulo IOT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a id="update"></a>Atualizar serviços Web

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Implantar modelos continuamente

Você pode implantar modelos continuamente usando a extensão Machine Learning para o [Azure DevOps](https://azure.microsoft.com/services/devops/). Você pode usar a extensão Machine Learning do Azure DevOps para disparar um pipeline de implantação quando um novo modelo de aprendizado de máquina for registrado em um espaço de trabalho do serviço Azure Machine Learning.

1. Inscreva-se para [Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops), que torna a integração contínua e a entrega de seu aplicativo a qualquer plataforma ou nuvem possível. (Observe que Azure Pipelines não é o mesmo que [Machine Learning pipelines](concept-ml-pipelines.md#compare).)

1. [Crie um projeto DevOps do Azure.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Instale a [extensão de Machine Learning para Azure pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Use conexões de serviço para configurar uma conexão de entidade de serviço com o espaço de trabalho do Azure Machine Learning Service para que você possa acessar seus artefatos. Vá para configurações do projeto, selecione **conexões de serviço**e, em seguida, selecione **Azure Resource Manager**:

    [![Selecionar Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. Na lista **nível de escopo** , selecione **AzureMLWorkspace**e, em seguida, insira o restante dos valores:

    ![Selecionar AzureMLWorkspace](media/how-to-deploy-and-where/resource-manager-connection.png)

1. Para implantar continuamente o modelo de aprendizado de máquina usando Azure Pipelines, em pipelines, selecione **liberar**. Adicione um novo artefato e, em seguida, selecione o artefato do **modelo do AzureML** e a conexão de serviço que você criou anteriormente. Selecione o modelo e a versão para disparar uma implantação:

    [![Selecionar modelo do AzureML](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Habilite o gatilho de modelo em seu artefato de modelo. Quando você ativa o gatilho, toda vez que a versão especificada (ou seja, a versão mais recente) desse modelo é registrada em seu espaço de trabalho, um pipeline de versão do Azure DevOps é disparado.

    [![Habilitar o gatilho de modelo](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Para obter mais exemplos e projetos de exemplo, consulte estes repositórios de exemplo no GitHub:

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="package-models"></a>Modelos de pacote

Em alguns casos, talvez você queira criar uma imagem do Docker sem implantar o modelo (se, por exemplo, você planeja [implantar no serviço Azure app](how-to-deploy-app-service.md)). Ou talvez você queira baixar a imagem e executá-la em uma instalação local do Docker. Talvez você queira até mesmo baixar os arquivos usados para criar a imagem, inspecioná-los, modificá-los e criar a imagem manualmente.

O empacotamento de modelo permite que você faça essas coisas. Ele empacota todos os ativos necessários para hospedar um modelo como um serviço Web e permite que você baixe uma imagem do Docker totalmente criada ou os arquivos necessários para criar um. Há duas maneiras de usar o empacotamento de modelo:

**Baixar um modelo empacotado:** Baixe uma imagem do Docker que contém o modelo e outros arquivos necessários para hospedá-lo como um serviço Web.

**Gerar um Dockerfile:** Baixe o Dockerfile, o modelo, o script de entrada e outros ativos necessários para criar uma imagem do Docker. Você pode inspecionar os arquivos ou fazer alterações antes de criar a imagem localmente.

Ambos os pacotes podem ser usados para obter uma imagem local do Docker.

> [!TIP]
> A criação de um pacote é semelhante à implantação de um modelo. Você usa um modelo registrado e uma configuração de inferência.

> [!IMPORTANT]
> Para baixar uma imagem totalmente criada ou criar uma imagem localmente, você precisa ter o [Docker](https://www.docker.com) instalado em seu ambiente de desenvolvimento.

### <a name="download-a-packaged-model"></a>Baixar um modelo empacotado

O exemplo a seguir cria uma imagem, que é registrada no registro de contêiner do Azure para seu espaço de trabalho:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Depois de criar um pacote, você pode usar `package.pull()` o para efetuar pull da imagem para o ambiente do Docker local. A saída desse comando exibirá o nome da imagem. Por exemplo: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Depois de baixar o modelo, use o `docker images` comando para listar as imagens locais:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43GB
```

Para iniciar um contêiner local com base nessa imagem, use o comando a seguir para iniciar um contêiner nomeado do Shell ou da linha de comando. Substitua o `<imageid>` valor pela ID da imagem retornada `docker images` pelo comando.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Esse comando inicia a versão mais recente da imagem chamada `myimage`. Ele mapeia a porta local 6789 para a porta no contêiner em que o serviço Web está escutando (5001). Ele também atribui o nome `mycontainer` ao contêiner, o que torna o contêiner mais fácil de parar. Depois que o contêiner é iniciado, você pode enviar solicitações `http://localhost:6789/score`para.

### <a name="generate-a-dockerfile-and-dependencies"></a>Gerar um Dockerfile e dependências

O exemplo a seguir mostra como baixar o Dockerfile, o modelo e outros ativos necessários para criar uma imagem localmente. O `generate_dockerfile=True` parâmetro indica que você deseja os arquivos, não uma imagem totalmente compilada.

```python
package = Model.package(ws, [model], inference_config, generate_dockerfile=True)
package.wait_for_creation(show_output=True)
# Download the package.
package.save("./imagefiles")
# Get the Azure container registry that the model/Dockerfile uses.
acr=package.get_container_registry()
print("Address:", acr.address)
print("Username:", acr.username)
print("Password:", acr.password)
```

Esse código baixa os arquivos necessários para criar a imagem `imagefiles` no diretório. O Dockerfile incluído nos arquivos salvos faz referência a uma imagem base armazenada em um registro de contêiner do Azure. Ao criar a imagem na instalação local do Docker, você precisa usar o endereço, o nome de usuário e a senha para se autenticar no registro. Use as etapas a seguir para criar a imagem usando uma instalação local do Docker:

1. Em uma sessão de linha de comando ou Shell, use o comando a seguir para autenticar o Docker com o registro de contêiner do Azure. Substitua `<address>`, `<username>` `package.get_container_registry()`e pelosvaloresrecuperadospor`<password>` .

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Para criar a imagem, use o comando a seguir. Substitua `<imagefiles>` pelo caminho do diretório em que `package.save()` os arquivos foram salvos.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Este comando define o nome da imagem `myimage`como.

Para verificar se a imagem foi criada, use o `docker images` comando. Você deve ver a `myimage` imagem na lista:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43GB
myimage         latest              739f22498d64        3 minutes ago       1.43GB
```

Para iniciar um novo contêiner com base nessa imagem, use o seguinte comando:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Esse comando inicia a versão mais recente da imagem chamada `myimage`. Ele mapeia a porta local 6789 para a porta no contêiner em que o serviço Web está escutando (5001). Ele também atribui o nome `mycontainer` ao contêiner, o que torna o contêiner mais fácil de parar. Depois que o contêiner é iniciado, você pode enviar solicitações `http://localhost:6789/score`para.

### <a name="example-client-to-test-the-local-container"></a>Exemplo de cliente para testar o contêiner local

O código a seguir é um exemplo de um cliente Python que pode ser usado com o contêiner:

```python
import requests
import json

# URL for the web service.
scoring_uri = 'http://localhost:6789/score'

# Two sets of data to score, so we get two results back.
data = {"data":
        [
            [ 1,2,3,4,5,6,7,8,9,10 ],
            [ 10,9,8,7,6,5,4,3,2,1 ]
        ]
        }
# Convert to JSON string.
input_data = json.dumps(data)

# Set the content type.
headers = {'Content-Type': 'application/json'}

# Make the request and display the response.
resp = requests.post(scoring_uri, input_data, headers=headers)
print(resp.text)
```

Por exemplo, clientes em outras linguagens de programação, consulte [consumir modelos implantados como serviços Web](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>Parar o contêiner do Docker

Para parar o contêiner, use o seguinte comando de um shell ou linha de comando diferente:

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>Limpar recursos

Para eliminar um serviço web implementado, utilize `service.delete()`.
Para eliminar um modelo registado, utilize `model.delete()`.

Para obter mais informações, consulte a documentação de [WebService. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) e [Model. Delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Passos Seguintes
* [Como implantar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Solução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Proteger serviços da web do Azure Machine Learning com SSL](how-to-secure-web-service.md)
* [Consumir um modelo de Azure Machine Learning implantado como um serviço Web](how-to-consume-web-service.md)
* [Monitore seus modelos de Azure Machine Learning com Application Insights](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)

