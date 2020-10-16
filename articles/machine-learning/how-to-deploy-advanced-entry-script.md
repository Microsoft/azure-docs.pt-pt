---
title: Script de entrada de autor para cenários avançados
titleSuffix: Azure Machine Learning entry script authoring
description: Aprenda a escrever scripts de entrada de Azure Machine Learning para pré e pós-processamento durante a implementação.
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 09/17/2020
ms.author: gopalv
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 3bd4953812ec88f28ac16956a85c95afc5bb8a38
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999218"
---
# <a name="advanced-entry-script-authoring"></a>Criação de um script de entrada avançado

Este artigo mostra como escrever scripts de entrada para casos de uso especializado.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que já tem um modelo de machine learning treinado que pretende implementar com a Azure Machine Learning. Para saber mais sobre a implementação do modelo, consulte [este tutorial.](how-to-deploy-and-where.md)

## <a name="automatically-generate-a-swagger-schema"></a>Gerar automaticamente um esquema de Swagger

Para gerar automaticamente um esquema para o seu serviço web, forneça uma amostra da entrada e/ou saída no construtor para um dos objetos do tipo definido. O tipo e a amostra são utilizados para criar automaticamente o esquema. A Azure Machine Learning cria então uma especificação [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) para o serviço web durante a implementação.

Estes tipos são atualmente suportados:

* `pandas`
* `numpy`
* `pyspark`
* Objeto Python padrão

Para utilizar a geração de esquemas, inclua a `inference-schema` versão 1.1.0 ou superior do pacote de código aberto no ficheiro de dependências. Para obter mais informações sobre este pacote, [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema) consulte. Para gerar um consumo de serviço web automatizado em conformidade com swagger, a função de script de pontuação() deve ter a forma API de:
* Um primeiro parâmetro do tipo "StandardPythonParameterType", denominado Inputs, aninhado contendo PandasDataframeParameterTypes.
* Um segundo parâmetro opcional do tipo "StandardPythonParameterType", chamado GlobalParameter, que não está aninhado.
* Devolva um dicionário do tipo "StandardPythonParameterType", que talvez aninhado contendo PandasDataFrameParameterTypes.
Defina os formatos de amostra de entrada e saída nas `input_sample` variáveis e `output_sample` variáveis, que representam os formatos de pedido e resposta para o serviço web. Utilize estas amostras nos decoradores da função de entrada e saída na `run()` função. O exemplo de aprendizagem de scikit-learn usa a geração de esquemas.



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
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')

    # If your model were stored in the same directory as your score.py, you could also use the following:
    # model_path = os.path.abspath(os.path.join(os.path.dirname(__file_), 'sklearn_mnist_model.pkl')

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

## <a name="power-bi-compatible-endpoint"></a>Ponto final compatível com Power BI 

O exemplo a seguir demonstra como definir a forma da API de acordo com a instrução acima. Este método é suportado para consumir o serviço web implantado a partir do Power BI. ([Saiba mais sobre como consumir o serviço web a partir do Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration).)

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from sklearn.linear_model import Ridge

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.standard_py_parameter_type import StandardPythonParameterType
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType


def init():
    global model
    # Replace filename if needed.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')
    # Deserialize the model file back into a sklearn model.
    model = joblib.load(model_path)

# providing 3 sample inputs for schema generation
numpy_sample_input = NumpyParameterType(np.array([[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]],dtype='float64'))
pandas_sample_input = PandasParameterType(pd.DataFrame({'name': ['Sarah', 'John'], 'age': [25, 26]}))
standard_sample_input = StandardPythonParameterType(0.0)

# This is a nested input sample, any item wrapped by `ParameterType` will be described by schema
sample_input = StandardPythonParameterType({'input1': numpy_sample_input, 
                                            'input2': pandas_sample_input, 
                                            'input3': standard_sample_input})

sample_global_parameters = StandardPythonParameterType(1.0) #this is optional
sample_output = StandardPythonParameterType([1.0, 1.0])

@input_schema('inputs', sample_input)
@input_schema('global_parameters', sample_global_parameters) #this is optional
@output_schema(sample_output)
def run(inputs, global_parameters):
    try:
        data = inputs['input1']
        # data will be convert to target format
        assert isinstance(data, np.ndarray)
        result = model.predict(data)
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```


## <a name="binary-ie-image-data"></a><a id="binary-data"></a> Dados binários (ou seja, imagem)

Se o seu modelo aceitar dados binários, como uma imagem, tem de modificar o `score.py` ficheiro utilizado para a sua implementação para aceitar pedidos HTTP brutos. Para aceitar dados brutos, utilize a `AMLRequest` classe no seu script de entrada e adicione o `@rawhttp` decorador à `run()` função.

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
> A `AMLRequest` aula está no espaço de `azureml.contrib` nomes. As entidades neste espaço de nome mudam frequentemente à medida que trabalhamos para melhorar o serviço. Qualquer coisa neste espaço de nome deve ser considerada uma pré-visualização que não seja totalmente suportada pela Microsoft.
>
> Se precisar de testar isto no seu ambiente de desenvolvimento local, pode instalar os componentes utilizando o seguinte comando:
>
> ```shell
> pip install azureml-contrib-services
> ```

A `AMLRequest` classe só permite aceder aos dados publicados em bruto no score.py, não existe nenhum componente do lado do cliente. De um cliente, você posta dados como normal. Por exemplo, o seguinte código Python lê um ficheiro de imagem e publica os dados:

```python
import requests
# Load image data
data = open('example.jpg', 'rb').read()
# Post raw data to scoring URI
res = requests.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/octet-stream'})
```

<a id="cors"></a>

## <a name="cross-origin-resource-sharing-cors"></a>Partilha de recursos de origem cruzada (CORS)

A partilha de recursos de origem cruzada é uma forma de permitir que os recursos numa página web sejam solicitados a partir de outro domínio. O CORS funciona através de cabeçalhos HTTP enviados com o pedido do cliente e devolvidos com a resposta do serviço. Para obter mais informações sobre CORS e cabeçalhos válidos, consulte [a partilha de recursos cross-origin](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) na Wikipédia.

Para configurar a implementação do seu modelo para suportar o CORS, utilize a `AMLResponse` classe no seu script de entrada. Esta classe permite-lhe definir os cabeçalhos no objeto de resposta.

O exemplo a seguir define o `Access-Control-Allow-Origin` cabeçalho para a resposta a partir do roteiro de entrada:

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

        # For demonstration purposes, this example
        # adds a header and returns the request body.
        resp = AMLResponse(reqBody, 200)
        resp.headers['Access-Control-Allow-Origin'] = "http://www.example.com"
        return resp
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> A `AMLResponse` aula está no espaço de `azureml.contrib` nomes. As entidades neste espaço de nome mudam frequentemente à medida que trabalhamos para melhorar o serviço. Qualquer coisa neste espaço de nome deve ser considerada uma pré-visualização que não seja totalmente suportada pela Microsoft.
>
> Se precisar de testar isto no seu ambiente de desenvolvimento local, pode instalar os componentes utilizando o seguinte comando:
>
> ```shell
> pip install azureml-contrib-services
> ```


> [!WARNING]
> A Azure Machine Learning irá encaminhar apenas os pedidos POST e GET para os contentores que executam o serviço de pontuação. Isto pode causar erros devido aos navegadores que usam pedidos OPTIONS para pedidos de CORS pré-voo.
> 


## <a name="load-registered-models"></a>Carregar modelos registados

Existem duas formas de localizar modelos no seu script de entrada:
* `AZUREML_MODEL_DIR`: Uma variável ambiental que contenha o caminho para a localização do modelo.
* `Model.get_model_path`: Uma API que devolve o caminho para o ficheiro modelo utilizando o nome do modelo registado.

#### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR é uma variável ambiental criada durante a implementação do serviço. Pode utilizar esta variável ambiental para encontrar a localização do(s) modelo(s) implantado.

O quadro que se segue descreve o valor das AZUREML_MODEL_DIR dependendo do número de modelos implementados:

| Implementação | Valor variável do ambiente |
| ----- | ----- |
| Modelo único | O caminho para a pasta que contém o modelo. |
| Vários modelos | O caminho para a pasta que contém todos os modelos. Os modelos estão localizados pelo nome e versão nesta pasta ( `$MODEL_NAME/$VERSION` ) |

Durante o registo e implantação do modelo, os modelos são colocados no caminho AZUREML_MODEL_DIR, e os seus formatos de filenames originais são preservados.

Para obter o caminho para um ficheiro modelo no seu script de entrada, combine a variável ambiental com o caminho de arquivo que procura.

**Exemplo de modelo único**
```python
# Example when the model is a file
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_regression_model.pkl')

# Example when the model is a folder containing a file
file_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'my_model_folder', 'sklearn_regression_model.pkl')
```

**Exemplo de vários modelos**

Neste cenário, são registados dois modelos no espaço de trabalho:

* `my_first_model`: Contém um ficheiro e `my_first_model.pkl` só há uma versão `1` ().
* `my_second_model`: Contém um ficheiro e `my_second_model.pkl` existem duas versões; `1` e `2` .

Quando o serviço foi implantado, ambos os modelos são fornecidos na operação de implantação:

```python
first_model = Model(ws, name="my_first_model", version=1)
second_model = Model(ws, name="my_second_model", version=2)
service = Model.deploy(ws, "myservice", [first_model, second_model], inference_config, deployment_config)
```

Na imagem do Docker que acolhe o serviço, a `AZUREML_MODEL_DIR` variável ambiente contém o diretório onde os modelos estão localizados.
Neste diretório, cada um dos modelos está localizado num percurso diretório de `MODEL_NAME/VERSION` . Onde `MODEL_NAME` está o nome do modelo registado, e é a versão do `VERSION` modelo. Os ficheiros que compõem o modelo registado estão armazenados nestes diretórios.

Neste exemplo, os caminhos seriam `$AZUREML_MODEL_DIR/my_first_model/1/my_first_model.pkl` `$AZUREML_MODEL_DIR/my_second_model/2/my_second_model.pkl` e.


```python
# Example when the model is a file, and the deployment contains multiple models
first_model_name = 'my_first_model'
first_model_version = '1'
first_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), first_model_name, first_model_version, 'my_first_model.pkl')
second_model_name = 'my_second_model'
second_model_version = '2'
second_model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), second_model_name, second_model_version, 'my_second_model.pkl')
```

### <a name="get_model_path"></a>get_model_path

Quando regista um modelo, fornece-se um nome modelo que é usado para gerir o modelo no registo. Utilize este nome com o método [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-model-path-model-name--version-none---workspace-none-) para recuperar o caminho do ficheiro modelo ou ficheiros no sistema de ficheiros local. Se registar uma pasta ou uma recolha de ficheiros, esta API devolve o caminho do diretório que contém esses ficheiros.

Quando se regista um modelo, dá-se-lhe um nome. O nome corresponde ao local onde o modelo é colocado, localmente ou durante a colocação de serviço.

## <a name="framework-specific-examples"></a>Exemplos específicos do quadro

Mais exemplos de script de entrada para casos específicos de utilização de machine learning podem ser encontrados abaixo:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.yml)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)

## <a name="next-steps"></a>Passos seguintes

* [Resolução de problemas de uma implantação falhada](how-to-troubleshoot-deployment.md)
* [Implementar no Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Criar aplicações de clientes para consumir serviços web](how-to-consume-web-service.md)
* [Atualizar serviços Web](how-to-deploy-update-web-service.md)
* [Como implementar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Utilizar o TLS para proteger um serviço Web através do Azure Machine Learning](how-to-secure-web-service.md)
* [Monitorize os seus modelos de machine learning Azure com Insights de Aplicações](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)
* [Criar alertas e gatilhos de eventos para implementações de modelos](how-to-use-event-grid.md)
