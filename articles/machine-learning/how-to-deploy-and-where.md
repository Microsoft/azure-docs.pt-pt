---
title: Como e onde implantar modelos
titleSuffix: Azure Machine Learning
description: Saiba como e onde implantar seus modelos de Azure Machine Learning, incluindo instâncias de contêiner do Azure, serviço kubernetes do Azure, Azure IoT Edge e matrizes de portão programáveis por campo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 12/27/2019
ms.custom: seoapril2019
ms.openlocfilehash: bbb0992eaeef7892e5940130131ac139a339b47d
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77083232"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Implantar modelos com Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Saiba como implantar o modelo de aprendizado de máquina como um serviço Web na nuvem do Azure ou para Azure IoT Edge dispositivos.

O fluxo de trabalho é semelhante independentemente de [onde implementa](#target) o seu modelo:

1. Registe o modelo.
1. Preparem-se para partir. (Especificar ativos, utilização, alvo de cálculo.)
1. Implante o modelo para o alvo da computação.
1. Teste o modelo implantado, também chamado de serviço Web.

Para obter mais informações sobre os conceitos envolvidos no fluxo de trabalho de implantação, consulte [Gerir, implementar e monitorizar modelos com o Azure Machine Learning.](concept-model-management-and-deployment.md)

## <a name="prerequisites"></a>Pré-requisitos

- Uma área de trabalho do Azure Machine Learning. Para mais informações, consulte Criar um espaço de [trabalho azure machine learning](how-to-manage-workspace.md).

- Um modelo. Se não tiver um modelo treinado, pode utilizar o modelo e os ficheiros de dependência fornecidos [neste tutorial](https://aka.ms/azml-deploy-cloud).

- A [extensão Azure CLI para o serviço de Machine Learning,](reference-azure-machine-learning-cli.md)o [Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou a extensão do Código visual de [aprendizagem automática Azure.](tutorial-setup-vscode-extension.md)

## <a name="connect-to-your-workspace"></a>Ligue-se ao seu espaço de trabalho

O código a seguir mostra como se conectar a um espaço de trabalho Azure Machine Learning usando informações armazenadas em cache para o ambiente de desenvolvimento local:

+ **Usando o SDK**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Para obter mais informações sobre a utilização do SDK para se ligar a um espaço de trabalho, consulte o [Azure Machine Learning SDK para](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) documentação python.

+ **Usando o CLI**

   Quando utilizar o CLI, utilize o `-w` ou `--workspace-name` parâmetro para especificar o espaço de trabalho para o comando.

+ **Usando o Código VS**

   Ao usar VS Code, você seleciona o espaço de trabalho usando uma interface gráfica. Para mais informações, consulte [A implementação e gestão](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model) de modelos na documentação de extensão do Código VS.

## <a id="registermodel"></a>Registe o seu modelo

Um modelo registado é um recipiente lógico para um ou mais ficheiros que compõem o seu modelo. Por exemplo, se você tiver um modelo armazenado em vários arquivos, poderá registrá-los como um único modelo no espaço de trabalho. Depois de registrar os arquivos, você pode baixar ou implantar o modelo registrado e receber todos os arquivos que você registrou.

> [!TIP]
> Ao registrar um modelo, você fornece o caminho de um local de nuvem (de uma execução de treinamento) ou um diretório local. Esse caminho é apenas para localizar os arquivos para upload como parte do processo de registro. Ele não precisa corresponder ao caminho usado no script de entrada. Para mais informações, consulte [localizar ficheiros do modelo no seu script de entrada](#locate-model-files-in-your-entry-script).

Os modelos de aprendizado de máquina são registrados em seu espaço de trabalho do Azure Machine Learning. O modelo pode vir de Azure Machine Learning ou de outro lugar. Os exemplos a seguir demonstram como registrar um modelo.

### <a name="register-a-model-from-an-experiment-run"></a>Registrar um modelo de uma execução de experimento

Os trechos de código nesta seção demonstram como registrar um modelo de uma execução de treinamento:

> [!IMPORTANT]
> Para utilizar estes snippets, precisa de ter realizado previamente um treino e precisa de ter acesso ao `Run` objeto (exemplo SDK) ou ao valor de ID de execução (exemplo CLI). Para obter mais informações sobre modelos de formação, consulte Configurar metas de [cálculo para formação](how-to-set-up-training-targets.md)de modelos .

+ **Usando o SDK**

  Quando utiliza o SDK para treinar um modelo, pode receber um objeto [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) ou um objeto [AutoMLRun,](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) dependendo da forma como treinou o modelo. Cada objeto pode ser usado para registrar um modelo criado por uma execução de experimento.

  + Registe um modelo de um objeto `azureml.core.Run`:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    O parâmetro `model_path` refere-se à localização da nuvem do modelo. Neste exemplo, o caminho de um único arquivo é usado. Para incluir vários ficheiros no registo do modelo, detete `model_path` no caminho de uma pasta que contenha os ficheiros. Para mais informações, consulte a documentação [Run.register_model.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-)

  + Registe um modelo de um objeto `azureml.train.automl.run.AutoMLRun`:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description)

        print(run.model_id)
    ```

    Neste exemplo, os parâmetros `metric` e `iteration` não são especificados, pelo que a iteração com a melhor métrica primária será registada. O valor `model_id` devolvido da corrida é usado em vez de um nome modelo.

    Para mais informações, consulte a documentação [AutoMLRun.register_model.](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-)

+ **Usando o CLI**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid
  ```

  [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

  O parâmetro `--asset-path` refere-se à localização da nuvem do modelo. Neste exemplo, o caminho de um único arquivo é usado. Para incluir vários ficheiros no registo do modelo, detete `--asset-path` no caminho de uma pasta que contenha os ficheiros.

+ **Usando o Código VS**

  Registe os modelos utilizando quaisquer ficheiros ou pastas de modelo utilizando a extensão do [Código VS.](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)

### <a name="register-a-model-from-a-local-file"></a>Registrar um modelo de um arquivo local

Você pode registrar um modelo fornecendo o caminho local do modelo. Você pode fornecer o caminho de uma pasta ou de um único arquivo. Você pode usar esse método para registrar modelos treinados com Azure Machine Learning e, em seguida, baixados. Você também pode usar esse método para registrar modelos treinados fora do Azure Machine Learning.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **Usando o SDK e o ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
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

  Para incluir vários ficheiros no registo do modelo, detete `model_path` no caminho de uma pasta que contenha os ficheiros.

+ **Usando o CLI**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Para incluir vários ficheiros no registo do modelo, detete `-p` no caminho de uma pasta que contenha os ficheiros.

**Estimativa de tempo**: Aproximadamente 10 segundos.

Para mais informações, consulte a documentação para a [classe Modelo](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Para obter mais informações sobre o trabalho com modelos treinados fora do Azure Machine Learning, consulte [como implementar um modelo existente](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Escolher um destino de computação

Você pode usar os seguintes destinos de computação ou recursos de computação para hospedar a implantação do serviço Web:

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="single-versus-multi-model-endpoints"></a>Pontos de extremidade únicos versus vários modelos
O Azure ML dá suporte à implantação de modelos únicos ou múltiplos por trás de um único ponto de extremidade.

Os pontos de extremidade de vários modelos usam um contêiner compartilhado para hospedar vários modelos. Isso ajuda a reduzir os custos de sobrecarga, a melhorar a utilização e permite encadear módulos em conjuntos. Os modelos que você especificar em seu script de implantação são montados e disponibilizados no disco do contêiner de serviço – você pode carregá-los na memória sob demanda e pontuar com base no modelo específico que está sendo solicitado no momento da pontuação.

Para um exemplo E2E que mostra como usar vários modelos por trás de um único ponto final contentorizado, consulte [este exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-multi-model)

## <a name="prepare-deployment-artifacts"></a>Preparar artefactos de implantação

Para implementar o modelo, precisa do seguinte:

* **Script de entrada e dependências**de código fonte . Esse script aceita solicitações, pontua as solicitações usando o modelo e retorna os resultados.

    > [!IMPORTANT]
    > * O script de entrada é específico do seu modelo. Ele deve entender o formato dos dados de solicitação de entrada, o formato dos dados esperados pelo seu modelo e o formato dos dados retornados aos clientes.
    >
    >   Se os dados da solicitação estiverem em um formato que não pode ser usado pelo seu modelo, o script poderá transformá-lo em um formato aceitável. Ele também pode transformar a resposta antes de retorná-la ao cliente.
    >
    > * Os serviços web e as implementações do IoT Edge não são capazes de aceder a datastores ou conjuntos de dados do espaço de trabalho. Se o seu serviço implementado necessitar de aceder a dados armazenados fora da implementação, como dados numa conta de armazenamento Azure, deve desenvolver uma solução de código personalizada utilizando o SDK relevante. Por exemplo, o [SDK](https://github.com/Azure/azure-storage-python)de Armazenamento Azure para Python .
    >
    >   Uma alternativa que pode funcionar para o seu cenário é a previsão do [lote,](how-to-use-parallel-run-step.md)que fornece acesso a lojas de dados durante a pontuação.

* **Ambiente de inferência.** A imagem base com dependências de pacotes instalados necessárias para executar o modelo.

* **Configuração de implementação** para o alvo da computação que acolhe o modelo implantado. Esta configuração descreve coisas como os requisitos de memória e CPU necessários para executar o modelo.

Estes itens são encapsulado numa configuração de *inferência* e numa configuração de *implementação*. A configuração da inferência refere o script de entrada e outras dependências. Define estas configurações programáticamente quando utiliza o SDK para executar a implementação. Define-os em ficheiros JSON quando utiliza o CLI.

### <a id="script"></a>1. Defina o seu roteiro de entrada e dependências

O script de entrada recebe dados submetidos a um serviço web implantado e passa-os para o modelo. Em seguida, leva a resposta devolvida pelo modelo e devolve-a ao cliente. *O guião é específico do seu modelo.* Ele deve entender os dados esperados e retornados pelo modelo.

O script contém duas funções que carregam e executam o modelo:

* `init()`: Normalmente, esta função coloca o modelo num objeto global. Essa função é executada apenas uma vez, quando o contêiner do Docker para o serviço Web é iniciado.

* `run(input_data)`: Esta função utiliza o modelo para prever um valor com base nos dados de entrada. As inputs e saídas da corrida normalmente usam jSON para serialização e desserialização. Também pode trabalhar com dados binários brutos. Pode transformar os dados antes de enviá-los para o modelo ou antes de os devolver ao cliente.

#### <a name="locate-model-files-in-your-entry-script"></a>Localizar arquivos de modelo em seu script de entrada

Há duas maneiras de localizar modelos em seu script de entrada:
* `AZUREML_MODEL_DIR`: Uma variável ambiental que contenha o caminho para a localização do modelo.
* `Model.get_model_path`: Uma API que devolve o caminho para o ficheiro modelo utilizando o nome do modelo registado.

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR é uma variável ambiental criada durante a implantação do serviço. Você pode usar essa variável de ambiente para localizar o local dos modelos implantados.

O quadro seguinte descreve o valor da AZUREML_MODEL_DIR dependendo do número de modelos implantados:

| Implementação | Valor da variável de ambiente |
| ----- | ----- |
| Modelo único | O caminho para a pasta que contém o modelo. |
| Vários modelos | O caminho para a pasta que contém todos os modelos. Os modelos estão localizados pelo nome e pela versão nesta pasta (`$MODEL_NAME/$VERSION`) |

Durante o registo e implantação do modelo, os modelos são colocados no caminho AZUREML_MODEL_DIR, e os seus nomes de ficheiros originais são preservados.

Para obter o caminho para um ficheiro modelo no seu script de entrada, combine a variável ambiental com o caminho de arquivo que procura.

**Exemplo de modelo único**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**Exemplo de modelo múltiplo**
```python
# Example when the model is a file, and the deployment contains multiple models
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_model', '1', 'sklearn_regression_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

Ao registrar um modelo, você fornece um nome de modelo que é usado para gerenciar o modelo no registro. Utiliza este nome com o método [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) para recuperar o caminho do ficheiro ou ficheiros do modelo no sistema de ficheiros local. Se você registrar uma pasta ou uma coleção de arquivos, essa API retornará o caminho do diretório que contém esses arquivos.

Ao registrar um modelo, você lhe dá um nome. O nome corresponde ao local em que o modelo é colocado, seja localmente ou durante a implantação do serviço.

> [!IMPORTANT]
> Se usou machine learning automatizado para treinar um modelo, um valor `model_id` é usado como nome modelo. Para um exemplo de registo e implementação de um modelo treinado com machine learning automatizado, consulte [Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features) no GitHub.

O exemplo seguinte devolverá um caminho para um único ficheiro chamado `sklearn_mnist_model.pkl` (que foi registado com o nome `sklearn_mnist`):

```python
model_path = Model.get_model_path('sklearn_mnist')
```

<a id="schema"></a>

#### <a name="optional-automatic-schema-generation"></a>Adicional Geração de esquema automática

Para gerar automaticamente um esquema para o serviço Web, forneça um exemplo de entrada e/ou saída no construtor para um dos objetos de tipo definidos. O tipo e o exemplo são usados para criar automaticamente o esquema. O Azure Machine Learning cria então uma especificação [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) para o serviço web durante a implantação.

Atualmente, esses tipos têm suporte:

* `pandas`
* `numpy`
* `pyspark`
* Objeto Python padrão

Para utilizar a geração de esquemas, inclua o pacote `inference-schema` no seu ficheiro ambiente Conda. Para mais informações sobre este pacote, consulte [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema).

##### <a name="example-dependencies-file"></a>Arquivo de dependências de exemplo

O YAML a seguir é um exemplo de um arquivo de dependências Conda para inferência. Por favor, note que deve indicar incumprimentos em azureml com verion >= 1.0.45 como dependência de pip, porque contém a funcionalidade necessária para hospedar o modelo como um serviço web.

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - scikit-learn=0.20.0
  - pip:
      # You must list azureml-defaults as a pip dependency
    - azureml-defaults>=1.0.45
    - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Se sua dependência estiver disponível por meio de Conda e PIP (de PyPi), a Microsoft recomenda usar a versão Conda, já que os pacotes Conda normalmente são fornecidos com binários pré-criados que tornam a instalação mais confiável.
>
> Para mais informações, consulte [Understanding Conda e Pip.](https://www.anaconda.com/understanding-conda-and-pip/)
>
> Para verificar se a sua dependência está disponível através da Conda, utilize o comando `conda search <package-name>` ou utilize os índices de embalagem em [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) e [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo).

Se quiser utilizar a geração automática de esquemas, o seu script de entrada deve importar os pacotes `inference-schema`.

Defina os formatos de amostra de entrada e saída nas variáveis `input_sample` e `output_sample`, que representam os formatos de pedido e resposta para o serviço web. Utilize estas amostras nos decoradores da função de entrada e saída na função `run()`. O exemplo a seguir scikit-Learn usa a geração de esquema.

##### <a name="example-entry-script"></a>Exemplo de script de entrada

O exemplo a seguir demonstra como aceitar e retornar dados JSON:

```python
#Example: scikit-learn and Swagger
import json
import numpy as np
import os
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment. Join this path with the filename of the model file.
    # It holds the path to the directory that contains the deployed model (./azureml-models/$MODEL_NAME/$VERSION).
    # If there are multiple models, this value is the path to the directory containing all deployed models (./azureml-models).
    # Alternatively: model_path = Model.get_model_path('sklearn_mnist')
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    # Deserialize the model file back into a sklearn model
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

O exemplo seguinte demonstra como definir os dados de entrada como um dicionário `<key: value>` utilizando um DataFrame. Esse método tem suporte para o consumo do serviço Web implantado do Power BI. ([Saiba mais sobre como consumir o serviço web a partir do Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration).)

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
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'model_file.pkl')
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

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Rio Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

<a id="binary"></a>

#### <a name="binary-data"></a>Dados binários

Se o seu modelo aceitar dados binários, como uma imagem, deve modificar o ficheiro `score.py` utilizado para a sua implementação para aceitar pedidos HTTP crus. Para aceitar dados brutos, utilize a classe `AMLRequest` no seu script de entrada e adicione o decorador `@rawhttp` à função `run()`.

Aqui está um exemplo de uma `score.py` que aceita dados binários:

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
> A aula de `AMLRequest` está no espaço de `azureml.contrib` nome. As entidades neste namespace são alteradas com frequência enquanto trabalhamos para melhorar o serviço. Qualquer coisa neste namespace deve ser considerada uma versão prévia que não tem suporte total da Microsoft.
>
> Se precisar testar isso em seu ambiente de desenvolvimento local, você poderá instalar os componentes usando o seguinte comando:
>
> ```shell
> pip install azureml-contrib-services
> ```

<a id="cors"></a>

#### <a name="cross-origin-resource-sharing-cors"></a>CORS (compartilhamento de recursos entre origens)

O compartilhamento de recursos entre origens é uma maneira de permitir que recursos em uma página da Web sejam solicitados de outro domínio. O CORS funciona por meio de cabeçalhos HTTP enviados com a solicitação do cliente e retornado com a resposta do serviço. Para obter mais informações sobre cors e cabeçalhos válidos, consulte a [partilha de recursos de origem cruzada](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) na Wikipédia.

Para configurar a implementação do seu modelo para suportar o CORS, utilize a classe `AMLResponse` no seu script de entrada. Essa classe permite que você defina os cabeçalhos no objeto de resposta.

O exemplo que se segue define o cabeçalho `Access-Control-Allow-Origin` para a resposta do script de entrada:

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
> A aula de `AMLResponse` está no espaço de `azureml.contrib` nome. As entidades neste namespace são alteradas com frequência enquanto trabalhamos para melhorar o serviço. Qualquer coisa neste namespace deve ser considerada uma versão prévia que não tem suporte total da Microsoft.
>
> Se precisar testar isso em seu ambiente de desenvolvimento local, você poderá instalar os componentes usando o seguinte comando:
>
> ```shell
> pip install azureml-contrib-services
> ```

### <a name="2-define-your-inference-environment"></a>2. Defina o seu ambiente de inferência

A configuração de inferência descreve como configurar o modelo para fazer previsões. Essa configuração não faz parte do seu script de entrada. Ele faz referência ao seu script de entrada e é usado para localizar todos os recursos exigidos pela implantação. Ele é usado posteriormente, quando você implanta o modelo.

A configuração de inferência usa ambientes Azure Machine Learning para definir as dependências de software necessárias para sua implantação. Os ambientes permitem que você crie, gerencie e reutilize as dependências de software necessárias para treinamento e implantação. O exemplo a seguir demonstra como carregar um ambiente do seu espaço de trabalho e usá-lo com a configuração de inferência:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig

myenv = Environment.get(workspace=ws, name="myenv", version="1")
inference_config = InferenceConfig(entry_script="x/y/score.py",
                                   environment=myenv)
```

Para obter mais informações sobre ambientes, consulte [Criar e gerir ambientes para formação e implantação.](how-to-use-environments.md)

Você também pode especificar diretamente as dependências sem usar um ambiente. O exemplo a seguir demonstra como criar uma configuração de inferência que carrega as dependências de software de um arquivo Conda:

Para obter mais informações sobre ambientes, consulte [Criar e gerir ambientes para formação e implantação.](how-to-use-environments.md)

Para obter mais informações sobre a configuração da inferência, consulte a documentação da classe [InferenceConfig.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)

Para obter informações sobre a utilização de uma imagem personalizada do Docker com uma configuração de inferência, consulte [como implementar um modelo utilizando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md).

### <a name="cli-example-of-inferenceconfig"></a>Exemplo de CLI de InferenceConfig

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

O comando a seguir demonstra como implantar um modelo usando a CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

Neste exemplo, a configuração especifica as seguintes configurações:

* Se o modelo requer Python.
* O [script de entrada](#script), que é usado para lidar com pedidos web enviados para o serviço implantado.
* O arquivo Conda que descreve os pacotes do python necessários para a inferência.

Para obter informações sobre a utilização de uma imagem personalizada do Docker com uma configuração de inferência, consulte [como implementar um modelo utilizando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md).

### <a name="3-define-your-deployment-configuration"></a>3. definir sua configuração de implantação

Antes de implantar seu modelo, você deve definir a configuração de implantação. *A configuração de implementação é específica para o alvo da computação que irá acolher o serviço web.* Por exemplo, ao implantar um modelo localmente, você deve especificar a porta onde o serviço aceita solicitações. A configuração de implantação não faz parte do script de entrada. Ele é usado para definir as características do destino de computação que hospedará o modelo e o script de entrada.

Talvez você também precise criar o recurso de computação, se, por exemplo, você ainda não tiver uma instância do AKS (serviço kubernetes do Azure) associada ao seu espaço de trabalho.

A tabela a seguir fornece um exemplo de criação de uma configuração de implantação para cada destino de computação:

| Destino de computação | Exemplo de configuração de implantação |
| ----- | ----- |
| Localização | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

As aulas para os serviços web locais, Azure Container e AKS podem ser importadas a partir de `azureml.core.webservice`:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

## <a name="deploy-to-target"></a>Implantar no destino

A implantação usa a configuração de implantação de configuração de inferência para implantar os modelos. O processo de implantação é semelhante, independentemente do destino de computação. A implantação em AKS é um pouco diferente, pois você deve fornecer uma referência ao cluster AKS.

### <a name="securing-deployments-with-ssl"></a>Protegendo implantações com SSL

Para obter mais informações sobre como garantir uma implementação de serviço web, consulte [Use SSL para garantir um serviço web](how-to-secure-web-service.md#enable).

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

Para mais informações, consulte a documentação para [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)e [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Usando a CLI

Para implantar um modelo usando a CLI, use o comando a seguir. Substitua `mymodel:1` pelo nome e versão do modelo registado:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Para mais informações, consulte o [modelo az ml implementar](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) documentação.

### <a id="notebookvm"></a>Serviço web de instância computacional (dev/teste)

Consulte [a implementação de um modelo para a instância de computação](how-to-deploy-local-container-notebook-vm.md)de machine learning azure .

### <a id="aci"></a>Instâncias de contentores azure (dev/teste)

Consulte [a implantação para as instâncias](how-to-deploy-azure-container-instance.md)do contentor Azure .

### <a id="aks"></a>Serviço Azure Kubernetes (dev/teste e produção)

Consulte [o Serviço Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

### <a name="ab-testing-controlled-rollout"></a>Teste A/B (distribuição controlada)
Consulte o [lançamento controlado dos modelos ML](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview) para obter mais informações.

## <a name="consume-web-services"></a>Consumir os serviços web

Cada serviço Web implantado fornece um ponto de extremidade REST, para que você possa criar aplicativos cliente em qualquer linguagem de programação.
Se você habilitou a autenticação baseada em chave para seu serviço, precisará fornecer uma chave de serviço como um token no cabeçalho da solicitação.
Se você habilitou a autenticação baseada em token para seu serviço, será necessário fornecer um Azure Machine Learning JWT (token Web JSON) como um token de portador no cabeçalho da solicitação. 

A principal diferença é que **as teclas são estáticas e podem ser regeneradas manualmente,** e **as fichas precisam de ser refrescadas após a expiração**. O auth baseado em chaves é suportado para a Instância de Contentores Azure e o Serviço Azure Kubernetes implantado sipróprio si, e o auth baseado em token **só** está disponível para implementações do Serviço Azure Kubernetes. Consulte o [como fazer](how-to-setup-authentication.md#web-service-authentication) na autenticação para obter mais informações e amostras específicas de código.

> [!TIP]
> Você pode recuperar o documento JSON do esquema depois de implantar o serviço. Utilize a [propriedade swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) a partir do serviço web implantado (por exemplo, `service.swagger_uri`) para levar o URI ao ficheiro Swagger do serviço web local.

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

Para mais informações, consulte [Criar aplicações de clientes para consumir serviços web.](how-to-consume-web-service.md)

### <a name="web-service-schema-openapi-specification"></a>Esquema de serviço Web (especificação OpenAPI)

Se usou a geração de esquemas automáticos com a sua implementação, pode obter o endereço da especificação OpenAPI para o serviço utilizando a [propriedade swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). (Por exemplo, `print(service.swagger_uri)`.) Utilize um pedido GET ou abra o URI num browser para recuperar a especificação.

O documento JSON a seguir é um exemplo de um esquema (especificação de OpenAPI) gerado para uma implantação:

```json
{
    "swagger": "2.0",
    "info": {
        "title": "myservice",
        "description": "API specification for Azure Machine Learning myservice",
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

Para mais informações, consulte [a especificação OpenAPI](https://swagger.io/specification/).

Para um utilitário que possa criar bibliotecas de clientes a partir da especificação, consulte [swagger-codegen](https://github.com/swagger-api/swagger-codegen).

### <a id="azuremlcompute"></a>Inferência do lote
Azure Machine Learning destinos de computação são criados e gerenciados pelo Azure Machine Learning. Eles podem ser usados para previsão de lote de pipelines de Azure Machine Learning.

Para uma passagem de inferência de lote com A Computação de Aprendizagem automática Azure, consulte [como executar as previsões](tutorial-pipeline-batch-scoring-classification.md)do lote .

### <a id="iotedge"></a>Inferência ioT edge
O suporte para a implantação no Edge está em versão prévia. Para mais informações, consulte [a Deploy Azure Machine Learning como um módulo IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a id="update"></a>Atualizar os serviços web

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Implantar modelos continuamente

Pode implantar continuamente modelos utilizando a extensão de Machine Learning para [O Azure DevOps](https://azure.microsoft.com/services/devops/). Você pode usar a extensão Machine Learning do Azure DevOps para disparar um pipeline de implantação quando um novo modelo de aprendizado de máquina for registrado em um espaço de trabalho do Azure Machine Learning.

1. Inscreva-se na [Azure Pipelines,](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)o que torna possível a integração contínua e entrega da sua aplicação a qualquer plataforma ou nuvem. (Note que os gasodutos Azure não são os mesmos que [os gasodutos de Machine Learning](concept-ml-pipelines.md#compare).)

1. [Crie um projeto Azure DevOps.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Instale a [extensão de Machine Learning para os gasodutos Azure](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Use conexões de serviço para configurar uma conexão de entidade de serviço com seu espaço de trabalho Azure Machine Learning para que você possa acessar seus artefatos. Vá às definições do projeto, selecione ligações de **serviço,** e, em seguida, selecione **Azure Resource Manager:**

    [![Selecionar Gestor de Recursos Azure](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. Na lista de **nível scope,** selecione **AzureMLWorkspace,** e, em seguida, introduza o resto dos valores:

    ![Selecionar AzureMLWorkspace](./media/how-to-deploy-and-where/resource-manager-connection.png)

1. Para implantar continuamente o seu modelo de aprendizagem automática utilizando pipelines Azure, sob gasodutos, selecione **a libertação**. Adicione um novo artefacto e, em seguida, selecione o artefacto **do Modelo AzureML** e a ligação de serviço que criou anteriormente. Selecione o modelo e a versão para disparar uma implantação:

    [modelo AzureML ![Selecione](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Habilite o gatilho de modelo em seu artefato de modelo. Quando você ativa o gatilho, toda vez que a versão especificada (ou seja, a versão mais recente) desse modelo é registrada em seu espaço de trabalho, um pipeline de versão do Azure DevOps é disparado.

    [![Ativar o gatilho do modelo](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Para obter mais exemplos e projetos de exemplo, consulte estes repositórios de exemplo no GitHub:

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>Baixar um modelo
Se você quiser baixar seu modelo para usá-lo em seu próprio ambiente de execução, poderá fazer isso com os seguintes comandos SDK/CLI:

SDK
```python
model_path = Model(ws,'mymodel').download()
```

CLI:
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="preview-no-code-model-deployment"></a>Apresentação Implantação de modelo sem código

No momento, a implantação de modelo sem código está em versão prévia e dá suporte às seguintes estruturas de Machine Learning:

### <a name="tensorflow-savedmodel-format"></a>Tensorflow SavedModel Format
Os modelos tensorflow precisam de ser registados no **formato SavedModel** para funcionarem sem implementação do modelo sem código.

Consulte [este link](https://www.tensorflow.org/guide/saved_model) para obter informações sobre como criar um SavedModel.

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="onnx-models"></a>Modelos ONNX

O registro e a implantação do modelo de ONNX têm suporte para qualquer grafo de inferência ONNX. Atualmente, não há suporte para as etapas de pré-processamento e postprocess.

Aqui está um exemplo de como registrar e implantar um modelo MNIST ONNX:

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

### <a name="scikit-learn-models"></a>Scikit-modelos de aprendizado

Não há suporte para implantação de modelo de código para todos os tipos de modelo scikit-Learn internos.

Aqui está um exemplo de como registrar e implantar um modelo sklearn sem código extra:

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

Observação: essas dependências estão incluídas no contêiner de inferência de sklearn predefinido:

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

## <a name="package-models"></a>Modelos de pacote

Em alguns casos, é melhor criar uma imagem Do Docker sem implementar o modelo (se, por exemplo, planeia ser implantado para o [Azure App Service).](how-to-deploy-app-service.md) Ou talvez você queira baixar a imagem e executá-la em uma instalação local do Docker. Talvez você queira até mesmo baixar os arquivos usados para criar a imagem, inspecioná-los, modificá-los e criar a imagem manualmente.

O empacotamento de modelo permite que você faça essas coisas. Ele empacota todos os ativos necessários para hospedar um modelo como um serviço Web e permite que você baixe uma imagem do Docker totalmente criada ou os arquivos necessários para criar um. Há duas maneiras de usar o empacotamento de modelo:

**Faça o download de um modelo embalado:** Descarregue uma imagem do Docker que contenha o modelo e outros ficheiros necessários para a hospedar como um serviço web.

**Gerar um Dockerfile:** Descarregue o Dockerfile, modelo, script de entrada e outros bens necessários para construir uma imagem do Docker. Você pode inspecionar os arquivos ou fazer alterações antes de criar a imagem localmente.

Ambos os pacotes podem ser usados para obter uma imagem local do Docker.

> [!TIP]
> A criação de um pacote é semelhante à implantação de um modelo. Você usa um modelo registrado e uma configuração de inferência.

> [!IMPORTANT]
> Para descarregar uma imagem totalmente construída ou construir uma imagem localmente, precisa de ter o [Docker](https://www.docker.com) instalado no seu ambiente de desenvolvimento.

### <a name="download-a-packaged-model"></a>Baixar um modelo empacotado

O exemplo a seguir cria uma imagem, que é registrada no registro de contêiner do Azure para seu espaço de trabalho:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Depois de criar um pacote, pode usar `package.pull()` para puxar a imagem para o seu ambiente local do Docker. A saída desse comando exibirá o nome da imagem. Por exemplo: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Depois de descarregar o modelo, utilize o comando `docker images` para listar as imagens locais:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43GB
```

Para iniciar um contêiner local com base nessa imagem, use o comando a seguir para iniciar um contêiner nomeado do Shell ou da linha de comando. Substitua o valor `<imageid>` pelo ID de imagem devolvido pelo comando `docker images`.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Este comando inicia a versão mais recente da imagem chamada `myimage`. Ele mapeia a porta local 6789 para a porta no contêiner em que o serviço Web está escutando (5001). Atribui também o nome `mycontainer` ao recipiente, o que facilita a paragem do recipiente. Após o início do recipiente, pode submeter pedidos para `http://localhost:6789/score`.

### <a name="generate-a-dockerfile-and-dependencies"></a>Gerar um Dockerfile e dependências

O exemplo a seguir mostra como baixar o Dockerfile, o modelo e outros ativos necessários para criar uma imagem localmente. O parâmetro `generate_dockerfile=True` indica que quer os ficheiros, não uma imagem totalmente construída.

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

Este código descarrega os ficheiros necessários para construir a imagem para o diretório `imagefiles`. O Dockerfile incluído nos arquivos salvos faz referência a uma imagem base armazenada em um registro de contêiner do Azure. Ao criar a imagem na instalação local do Docker, você precisa usar o endereço, o nome de usuário e a senha para se autenticar no registro. Use as etapas a seguir para criar a imagem usando uma instalação local do Docker:

1. Em uma sessão de linha de comando ou Shell, use o comando a seguir para autenticar o Docker com o registro de contêiner do Azure. Substitua `<address>`, `<username>`e `<password>` pelos valores recuperados por `package.get_container_registry()`.

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Para criar a imagem, use o comando a seguir. Substitua `<imagefiles>` pelo percurso do diretório onde `package.save()` guardou os ficheiros.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Este comando define o nome da imagem para `myimage`.

Para verificar se a imagem foi construída, utilize o comando `docker images`. Devia ver a imagem `myimage` na lista:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43GB
myimage         latest              739f22498d64        3 minutes ago       1.43GB
```

Para iniciar um novo contêiner com base nessa imagem, use o seguinte comando:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Este comando inicia a versão mais recente da imagem chamada `myimage`. Ele mapeia a porta local 6789 para a porta no contêiner em que o serviço Web está escutando (5001). Atribui também o nome `mycontainer` ao recipiente, o que facilita a paragem do recipiente. Após o início do recipiente, pode submeter pedidos para `http://localhost:6789/score`.

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

Por exemplo, clientes de outras linguagens de programação, consulte [os modelos Consumir implantados como serviços web.](how-to-consume-web-service.md)

### <a name="stop-the-docker-container"></a>Parar o contêiner do Docker

Para parar o contêiner, use o seguinte comando de um shell ou linha de comando diferente:

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>Limpar recursos

Para eliminar um serviço web implantado, utilize `service.delete()`.
Para eliminar um modelo registado, utilize `model.delete()`.

Para mais informações, consulte a documentação para [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) e [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Passos seguintes

* [Como implementar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Resolução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Serviços web Secure Azure Machine Learning com SSL](how-to-secure-web-service.md)
* [Consumir um modelo azure machine learning implementado como um serviço web](how-to-consume-web-service.md)
* [Monitorize os seus modelos de Aprendizagem automática Azure com Insights de Aplicação](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)

