---
title: Como e onde implementar modelos
titleSuffix: Azure Machine Learning
description: Saiba como e onde implementar os seus modelos de Machine Learning Azure, incluindo instâncias de contentores Azure, Serviço Azure Kubernetes, Azure IoT Edge e conjuntos de portas programáveis em campo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 04/28/2020
ms.custom: seoapril2019
ms.openlocfilehash: 3b1dd474b3b5518029e7cf404cc88b97bfa23e36
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433487"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Implementar modelos com o Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a implementar o seu modelo de machine learning como um serviço web na nuvem Azure ou para dispositivos Azure IoT Edge.

O fluxo de trabalho é semelhante independentemente [do local onde implementa](#target) o seu modelo:

1. Registar o modelo.
1. Preparar a implementação. (Especificar elementos, utilização, destino de computação.)
1. Implementar o modelo para o destino de computação.
1. Teste o modelo implantado, também chamado de serviço web.

Para obter mais informações sobre os conceitos envolvidos no fluxo de trabalho de implantação, consulte [Gerir, implementar e monitorizar modelos com Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma área de trabalho do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho para aprendizagem de máquinas Azure.](how-to-manage-workspace.md)

- Um modelo. Se não tiver um modelo treinado, pode utilizar os ficheiros de modelo e dependência fornecidos [neste tutorial](https://aka.ms/azml-deploy-cloud).

- A [extensão Azure CLI para o serviço machine learning](reference-azure-machine-learning-cli.md), o [Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), ou a extensão do Código do Estúdio Visual de [Aprendizagem de Máquinas Azure.](tutorial-setup-vscode-extension.md)

## <a name="connect-to-your-workspace"></a>Ligar à sua área de trabalho

O seguinte código mostra como ligar-se a um espaço de trabalho Azure Machine Learning utilizando informações em cache para o ambiente de desenvolvimento local:

+ **Utilizar o SDK**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Para obter mais informações sobre a utilização do SDK para ligar a um espaço de trabalho, consulte o [Azure Machine Learning SDK para](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) documentação Python.

+ **Utilização do CLI**

   Quando utilizar o CLI, utilize o `-w` `--workspace-name` ou parâmetro para especificar o espaço de trabalho para o comando.

+ **Utilizar o Visual Studio Code**

   Quando utilizar o Código do Estúdio Visual, selecione o espaço de trabalho utilizando uma interface gráfica. Para obter mais informações, consulte [Implementar e gerir modelos](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model) na documentação de extensão do Código do Estúdio Visual.

## <a name="register-your-model"></a><a id="registermodel"></a>Registe o seu modelo

Um modelo registado é um recipiente lógico para um ou mais ficheiros que compõem o seu modelo. Por exemplo, se tiver um modelo que esteja armazenado em vários ficheiros, pode registá-los como um único modelo no espaço de trabalho. Depois de registar os ficheiros, pode então descarregar ou implementar o modelo registado e receber todos os ficheiros que registou.

> [!TIP]
> Ao registar um modelo, fornece-se o caminho de uma localização em nuvem (de uma corrida de treino) ou de um diretório local. Este caminho é apenas para localizar os ficheiros para upload como parte do processo de registo. Não precisa de corresponder ao caminho usado no roteiro de entrada. Para obter mais informações, consulte [localizar ficheiros de modelos no seu script de entrada](#load-model-files-in-your-entry-script).

Os modelos de machine learning estão registados no seu espaço de trabalho Azure Machine Learning. O modelo pode vir de Azure Machine Learning ou de outro lugar. Ao registar um modelo, pode opcionalmente fornecer metadados sobre o modelo. Os `tags` `properties` dicionários que se aplica a um registo de modelo podem então ser usados para filtrar modelos.

Os exemplos que se seguem demonstram como registar um modelo.

### <a name="register-a-model-from-an-experiment-run"></a>Registar um modelo de uma experiência

Os fragmentos de código nesta secção demonstram como registar um modelo a partir de uma formação:

> [!IMPORTANT]
> Para utilizar estes snippets, é necessário ter realizado previamente uma corrida de treino e tem de ter acesso ao `Run` objeto (exemplo SDK) ou ao valor de ID de execução (exemplo CLI). Para obter mais informações sobre modelos de treino, consulte [Configurar metas de computação para a formação de modelos.](how-to-set-up-training-targets.md)

+ **Utilizar o SDK**

  Quando utilizar o SDK para treinar um modelo, pode receber um objeto [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) ou um objeto [AutoMLRun,](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) dependendo da forma como treinou o modelo. Cada objeto pode ser usado para registar um modelo criado por uma experiência.

  + Registar um modelo a partir de um `azureml.core.Run` objeto:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    O `model_path` parâmetro refere-se à localização em nuvem do modelo. Neste exemplo, o caminho de um único ficheiro é usado. Para incluir vários ficheiros no registo do modelo, definido `model_path` para o caminho de uma pasta que contém os ficheiros. Para mais informações, consulte a documentação [run.register_model.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-)

  + Registar um modelo a partir de um `azureml.train.automl.run.AutoMLRun` objeto:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    Neste exemplo, os `metric` parâmetros e `iteration` parâmetros não são especificados, pelo que a iteração com a melhor métrica primária será registada. O `model_id` valor devolvido da corrida é usado em vez de um nome de modelo.

    Para mais informações, consulte a documentação [AutoMLRun.register_model.](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-)

+ **Utilização do CLI**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
  ```

  [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

  O `--asset-path` parâmetro refere-se à localização em nuvem do modelo. Neste exemplo, o caminho de um único ficheiro é usado. Para incluir vários ficheiros no registo do modelo, definido `--asset-path` para o caminho de uma pasta que contém os ficheiros.

+ **Utilizar o Visual Studio Code**

  Registar modelos utilizando quaisquer ficheiros ou pastas de modelo utilizando a extensão [do Código do Estúdio Visual.](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)

### <a name="register-a-model-from-a-local-file"></a>Registar um modelo a partir de um arquivo local

Pode registar um modelo fornecendo o percurso local do modelo. Pode fornecer o caminho de uma pasta ou de um único ficheiro. Pode utilizar este método para registar modelos treinados com Azure Machine Learning e depois descarregados. Também pode utilizar este método para registar modelos treinados fora do Azure Machine Learning.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **Utilizando o SDK e o ONNX**

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

  Para incluir vários ficheiros no registo do modelo, definido `model_path` para o caminho de uma pasta que contém os ficheiros.

+ **Utilização do CLI**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Para incluir vários ficheiros no registo do modelo, definido `-p` para o caminho de uma pasta que contém os ficheiros.

**Estimativa de tempo:** Aproximadamente 10 segundos.

Para mais informações, consulte a documentação para a [classe Modelo.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)

Para obter mais informações sobre o trabalho com modelos treinados fora do Azure Machine Learning, consulte [Como implementar um modelo existente.](how-to-deploy-existing-model.md)

<a name="target"></a>

## <a name="single-versus-multi-model-endpoints"></a>Pontos finais individuais versus multi-modelos
O Azure ML suporta a implementação de modelos individuais ou múltiplos atrás de um único ponto final.

Os pontos finais multi-modelo utilizam um recipiente partilhado para hospedar vários modelos. Isto ajuda a reduzir os custos gerais, melhora a utilização e permite-lhe acorrentar módulos em conjuntos. Os modelos que especifica no seu script de implantação são montados e disponibilizados no disco do recipiente de serviço - pode carregá-los na memória a pedido e pontuar com base no modelo específico que está a ser solicitado na hora de pontuação.

Para um exemplo E2E, que mostra como usar vários modelos atrás de um único ponto final contentorizado, veja [este exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-multi-model)

## <a name="prepare-to-deploy"></a>Preparar para implementar

Para implementar o modelo como um serviço, precisa dos seguintes componentes:

* **Defina o ambiente de inferência.** Este ambiente engloba as dependências necessárias para executar o seu modelo para inferência.
* **Defina o código de pontuação**. Este script aceita pedidos, pontua os pedidos utilizando o modelo e devolve os resultados.
* **Defina a configuração da inferência.** A configuração da inferência especifica a configuração do ambiente, o script de entrada e outros componentes necessários para executar o modelo como um serviço.

Uma vez que tenha os componentes necessários, pode perfilar o serviço que será criado como resultado da implementação do seu modelo para compreender os seus requisitos de CPU e memória.

### <a name="1-define-inference-environment"></a>1. Definir ambiente de inferência

Uma configuração de inferência descreve como configurar o serviço web que contém o seu modelo. É usado mais tarde, quando se implanta o modelo.

A configuração de inferência utiliza ambientes de aprendizagem automática Azure para definir as dependências de software necessárias para a sua implementação. Os ambientes permitem criar, gerir e reutilizar as dependências de software necessárias para o treino e implantação. Pode criar um ambiente a partir de ficheiros de dependência personalizados ou usar um dos ambientes de aprendizagem automática Azure com curadoria. O YAML seguinte é um exemplo de um ficheiro de dependências Conda para inferência. Note que deve indicar padrão de azureml com verion >= 1.0.45 como uma dependência de pip, porque contém a funcionalidade necessária para hospedar o modelo como um serviço web. Se quiser utilizar a geração automática de esquemas, o seu script de entrada também deve importar os `inference-schema` pacotes.

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
> Se a sua dependência estiver disponível tanto através da Conda como do pip (do PyPi), a Microsoft recomenda a utilização da versão Conda, uma vez que os pacotes Conda normalmente vêm com binários pré-construídos que tornam a instalação mais fiável.
>
> Para mais informações, consulte [Understanding Conda e Pip.](https://www.anaconda.com/understanding-conda-and-pip/)
>
> Para verificar se a sua dependência está disponível através da Conda, utilize o `conda search <package-name>` comando ou utilize os índices de pacotes em e [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo) .

Pode utilizar o ficheiro dependências para criar um objeto ambiental e guardá-lo no seu espaço de trabalho para utilização futura:

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                             file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

### <a name="2-define-scoring-code"></a><a id="script"></a>2. Defina o código de pontuação

O script de entrada recebe os dados enviados para um serviço Web implementado e transmite-os para o modelo. Em seguida, seleciona a resposta devolvida pelo modelo e devolve-a ao cliente. *O script é específico do seu modelo.* Deve compreender os dados que o modelo espera e devolve.

O script contém duas funções que carregam e executam o modelo:

* `init()`: Normalmente, esta função coloca o modelo num objeto global. Esta função é executada apenas uma vez, quando o recipiente Docker para o seu serviço web é iniciado.

* `run(input_data)`: Esta função utiliza o modelo para prever um valor baseado nos dados de entrada. Regra geral, as entradas e as saídas da execução utilizam JSON para a serialização e a desserialização. Também pode trabalhar com dados binários não processados. Pode transformar os dados antes de enviá-los para o modelo ou antes de devolvê-los ao cliente.

#### <a name="load-model-files-in-your-entry-script"></a>Carregue ficheiros de modelos no seu script de entrada

Existem duas formas de localizar modelos no seu script de entrada:
* `AZUREML_MODEL_DIR`: Uma variável ambiental que contenha o caminho para a localização do modelo.
* `Model.get_model_path`: Uma API que devolve o caminho para o ficheiro modelo utilizando o nome do modelo registado.

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

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
```python
# Example when the model is a file, and the deployment contains multiple models
model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_model', '1', 'sklearn_regression_model.pkl')
```

##### <a name="get_model_path"></a>get_model_path

Quando regista um modelo, fornece-se um nome modelo que é usado para gerir o modelo no registo. Utilize este nome com o método [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) para recuperar o caminho do ficheiro modelo ou ficheiros no sistema de ficheiros local. Se registar uma pasta ou uma recolha de ficheiros, esta API devolve o caminho do diretório que contém esses ficheiros.

Quando se regista um modelo, dá-se-lhe um nome. O nome corresponde ao local onde o modelo é colocado, localmente ou durante a colocação de serviço.

#### <a name="optional-define-model-web-service-schema"></a>(Opcional) Definir esquema de serviço web modelo

Para gerar automaticamente um esquema para o seu serviço web, forneça uma amostra da entrada e/ou saída no construtor para um dos objetos do tipo definido. O tipo e a amostra são utilizados para criar automaticamente o esquema. A Azure Machine Learning cria então uma especificação [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) para o serviço web durante a implementação.

Estes tipos são atualmente suportados:

* `pandas`
* `numpy`
* `pyspark`
* Objeto Python padrão

Para utilizar a geração de esquemas, inclua o pacote de código aberto `inference-schema` no seu ficheiro de dependências. Para obter mais informações sobre este pacote, [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema) consulte. Defina os formatos de amostra de entrada e saída nas `input_sample` variáveis e `output_sample` variáveis, que representam os formatos de pedido e resposta para o serviço web. Utilize estas amostras nos decoradores da função de entrada e saída na `run()` função. O exemplo de aprendizagem de scikit-learn usa a geração de esquemas.

##### <a name="example-entry-script"></a>Script de entrada de exemplo

O exemplo a seguir demonstra como aceitar e devolver dados da JSON:

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

O exemplo a seguir demonstra como definir os dados de entrada como um `<key: value>` dicionário utilizando um DataFrame. Este método é suportado para consumir o serviço web implantado a partir do Power BI. ([Saiba mais sobre como consumir o serviço web a partir do Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration).)

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

Para mais exemplos, consulte os seguintes scripts:

* [PyTorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch)
* [TensorFlow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow)
* [Keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* [AutoML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features)
* [ONNX](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* [Dados Binários](#binary)
* [CORS](#cors)

### <a name="3-define-inference-configuration"></a><a id="script"></a>3. Definir configuração de inferência
    
O exemplo a seguir demonstra o carregamento de um ambiente a partir do seu espaço de trabalho e, em seguida, a sua utilização com a configuração de inferência:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
```

Para obter mais informações sobre ambientes, consulte [Criar e gerir ambientes de formação e implantação.](how-to-use-environments.md)

Para obter mais informações sobre a configuração da inferência, consulte a documentação da classe [InferenceConfig.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)

Para obter informações sobre a utilização de uma imagem personalizada do Docker com uma configuração de inferência, consulte [Como implementar um modelo utilizando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md).

#### <a name="cli-example-of-inferenceconfig"></a>Exemplo CLI de InferenceConfig

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

O seguinte comando demonstra como implantar um modelo utilizando o CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

Neste exemplo, a configuração especifica as seguintes definições:

* Que o modelo requer Python.
* O [script de entrada](#script), que é utilizado para lidar com pedidos web enviados para o serviço implantado.
* O ficheiro Conda que descreve os pacotes Python necessários para a inferência.

Para obter informações sobre a utilização de uma imagem personalizada do Docker com uma configuração de inferência, consulte [Como implementar um modelo utilizando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md).

### <a name="4-optional-profile-your-model-to-determine-resource-utilization"></a><a id="profilemodel"></a>4. Perfil (Opcional) Perfilar o seu modelo para determinar a utilização de recursos

Uma vez registado o seu modelo e preparado os outros componentes necessários para a sua implantação, pode determinar o CPU e a memória de que o serviço implantado necessitará. O perfil testa o serviço que executa o seu modelo e devolve informações como o uso do CPU, o uso da memória e a latência da resposta. Também fornece uma recomendação para o CPU e memória com base na utilização de recursos.

Para perfilar o seu modelo, necessitará:
* Um modelo registado.
* Uma configuração de inferência baseada no seu script de entrada e definição de ambiente de inferência.
* Um conjunto de dados tabular de coluna única, onde cada linha contém uma cadeia que representa dados de pedido de amostra.

> [!IMPORTANT]
> Neste momento, apenas apoiamos o perfil de serviços que esperam que os seus dados de pedido sejam uma cadeia, por exemplo: json serializado de cordas, texto, imagem serializada de cordas, etc. O conteúdo de cada linha do conjunto de dados (cadeia) será colocado no corpo do pedido HTTP e enviado para o serviço que encapsula o modelo para pontuação.

Abaixo está um exemplo de como é possível construir um conjunto de dados de entrada para perfilar um serviço que espera que os dados do seu pedido de entrada contenham json serializado. Neste caso, criámos um conjunto de dados baseado em cem casos do mesmo conteúdo de dados de pedido. Em cenários reais, sugerimos que utilize conjuntos de dados maiores que contenham várias entradas, especialmente se o uso/comportamento do seu modelo de recursos depende.

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

Assim que tiver o conjunto de dados que contém dados de pedido de amostra prontos, crie uma configuração de inferência. A configuração da inferência baseia-se na score.py e na definição de ambiente. O exemplo a seguir demonstra como criar a configuração de inferência e executar perfis:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

O seguinte comando demonstra como perfilar um modelo utilizando o CLI:

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> Para persistir a informação devolvida por perfis, utilize etiquetas ou propriedades para o modelo. A utilização de tags ou propriedades armazena os dados com o modelo no registo do modelo. Os seguintes exemplos demonstram a adição de uma nova etiqueta que contém as `requestedCpu` `requestedMemoryInGb` informações:
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

## <a name="deploy-to-target"></a>Implementar para o alvo

A implementação utiliza a configuração de configuração de inferência para implantar os modelos. O processo de implantação é semelhante independentemente do alvo do cálculo. A implantação em AKS é ligeiramente diferente porque deve fornecer uma referência ao cluster AKS.

### <a name="choose-a-compute-target"></a>Escolha um alvo de cálculo

Pode utilizar os seguintes alvos de cálculo, ou recursos de cálculo, para hospedar a sua implementação de serviço web:

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

### <a name="define-your-deployment-configuration"></a>Defina a sua configuração de implementação

Antes de implementar o seu modelo, tem de definir a configuração de implementação. *A configuração de implementação é específica do alvo do cálculo que irá hospedar o serviço web.* Por exemplo, quando implementa um modelo localmente, deve especificar a porta onde o serviço aceita pedidos. A configuração de implementação não faz parte do seu script de entrada. É usado para definir as características do alvo do cálculo que irá hospedar o modelo e o script de entrada.

Também poderá ter de criar o recurso compute, se, por exemplo, ainda não tiver uma instância do Serviço Azure Kubernetes (AKS) associada ao seu espaço de trabalho.

A tabela a seguir fornece um exemplo de criação de uma configuração de implementação para cada alvo de cálculo:

| Destino de computação | Exemplo de configuração de implementação |
| ----- | ----- |
| Localização | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

As classes para instâncias locais, azure container e serviços web AKS podem ser importadas a partir `azureml.core.webservice` de:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

### <a name="securing-deployments-with-tls"></a>Assegurar implantações com TLS

Para obter mais informações sobre como garantir uma implementação de serviço web, consulte [Enable TLS e implemente](how-to-secure-web-service.md#enable).

### <a name="local-deployment"></a><a id="local"></a>Implantação local

Para implementar um modelo localmente, precisa de ter o Docker instalado na sua máquina local.

#### <a name="using-the-sdk"></a>Utilizar o SDK

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Para mais informações, consulte a documentação para [LocalWebservice,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py) [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)e [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Utilização do CLI

Para implementar um modelo utilizando o CLI, utilize o seguinte comando. `mymodel:1`Substitua-o pelo nome e versão do modelo registado:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Para mais informações, consulte o [modelo az ml a implementar](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) documentação.

### <a name="understanding-service-state"></a>Estado de serviço de compreensão

Durante a implementação do modelo, poderá ver a alteração do estado de serviço enquanto este se desdobra completamente.

A tabela a seguir descreve os diferentes estados de serviço:

| Estado do serviço web | Description | Estado final?
| ----- | ----- | ----- |
| Transição | O serviço está em processo de implantação. | No |
| Mau estado de funcionamento | O serviço foi implantado, mas está atualmente inacessível.  | No |
| Insodulável | O serviço não pode ser implantado neste momento devido à falta de recursos. | No |
| Falhou | O serviço falhou em ser acionado devido a um erro ou acidente. | Yes |
| Bom estado de funcionamento | O serviço é saudável e o ponto final está disponível. | Yes |

### <a name="compute-instance-web-service-devtest"></a><a id="notebookvm"></a>Serviço web de instância computacional (dev/teste)

Consulte [implementar um modelo para a azure machine learning computação .](how-to-deploy-local-container-notebook-vm.md)

### <a name="azure-container-instances-devtest"></a><a id="aci"></a>Instâncias do contentor Azure (dev/teste)

Ver [Implementar para instâncias de contentores Azure](how-to-deploy-azure-container-instance.md).

### <a name="azure-kubernetes-service-devtest-and-production"></a><a id="aks"></a>Serviço Azure Kubernetes (dev/teste e produção)

Consulte [o Serviço Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

### <a name="ab-testing-controlled-rollout"></a>Teste a/B (lançamento controlado)
Consulte [o lançamento controlado dos modelos ML](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview) para obter mais informações.

## <a name="consume-web-services"></a>Consumir os serviços web

Cada serviço web implantado fornece um ponto final REST, para que possa criar aplicações de clientes em qualquer linguagem de programação.
Se tiver ativado a autenticação baseada em chaves para o seu serviço, tem de fornecer uma chave de serviço como símbolo no seu cabeçalho de pedido.
Se tiver ativado a autenticação baseada em fichas para o seu serviço, precisa de fornecer um Azure Machine Learning JSON Web Token (JWT) como símbolo portador no seu cabeçalho de pedido. 

A principal diferença é que **as teclas são estáticas e podem ser regeneradas manualmente,** e **os tokens precisam de ser atualizados após a expiração**. O auth baseado em chaves é suportado para Azure Container Instance e Azure Kubernetes Service implantados web-services, e auth baseada em token está disponível **apenas** para implementações do Serviço Azure Kubernetes. Consulte a [autenticação](how-to-setup-authentication.md#web-service-authentication) para obter mais informações e amostras de código específicas.

> [!TIP]
> Pode recuperar o documento de esquema JSON depois de implementar o serviço. Utilize a [propriedade swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) a partir do serviço web implantado (por exemplo, ) para obter `service.swagger_uri` o URI para o ficheiro Swagger do serviço web local.

### <a name="request-response-consumption"></a>Consumo de resposta a pedidos

Aqui está um exemplo de como invocar o seu serviço em Python:
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

Para obter mais informações, consulte [Criar aplicações para o cliente para consumir serviços web.](how-to-consume-web-service.md)

### <a name="web-service-schema-openapi-specification"></a>Esquema de serviço web (especificação OpenAPI)

Se utilizar a geração de esquemas automáticos com a sua implantação, pode obter o endereço da especificação OpenAPI para o serviço utilizando a [propriedade swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). (Por exemplo, `print(service.swagger_uri)` .) Utilize um pedido GET ou abra o URI num browser para recuperar a especificação.

O seguinte documento JSON é um exemplo de um esquema (especificação OpenAPI) gerado para uma implantação:

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

Para mais informações, consulte [a especificação OpenAPI.](https://swagger.io/specification/)

Para um utilitário que possa criar bibliotecas de clientes a partir da especificação, consulte [swagger-codegen](https://github.com/swagger-api/swagger-codegen).

### <a name="batch-inference"></a><a id="azuremlcompute"></a>Inferência do lote
Os alvos Azure Machine Learning Compute são criados e geridos pela Azure Machine Learning. Podem ser utilizados para a previsão de lote a partir de oleodutos Azure Machine Learning.

Para uma passagem de inferência de lote com Azure Machine Learning Compute, consulte [Como executar previsões de lotes](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a>Inferência IoT Edge
O suporte para implantação na borda está em pré-visualização. Para obter mais informações, consulte [implementar a Azure Machine Learning como um módulo IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a name="update-web-services"></a><a id="update"></a>Atualizar serviços web

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Implementar continuamente modelos

Pode implementar continuamente os modelos utilizando a extensão machine learning para [Azure DevOps](https://azure.microsoft.com/services/devops/). Pode utilizar a extensão machine learning para Azure DevOps para desencadear um oleoduto de implantação quando um novo modelo de aprendizagem automática estiver registado num espaço de trabalho de Aprendizagem automática Azure.

1. Inscreva-se nos [Pipelines Azure,](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)o que torna possível a integração contínua e entrega da sua aplicação em qualquer plataforma ou nuvem. (Note que os gasodutos Azure não são os mesmos que [os gasodutos de machine learning](concept-ml-pipelines.md#compare).)

1. [Criar um projeto Azure DevOps.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Instale a [extensão machine learning para a Azure Pipelines](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Utilize ligações de serviço para configurar uma ligação principal de serviço ao seu espaço de trabalho Azure Machine Learning para que possa aceder aos seus artefactos. Vá para as definições do projeto, selecione **as ligações de serviço**e, em seguida, selecione **Azure Resource Manager**:

    [![Selecione Azure Resource Manager](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. Na lista **de níveis scope,** selecione **AzureMLWorkspace**e, em seguida, introduza o resto dos valores:

    ![Selecione AzureMLWorkspace](./media/how-to-deploy-and-where/resource-manager-connection.png)

1. Para implementar continuamente o seu modelo de aprendizagem automática utilizando gasodutos Azure, sob oleodutos, selecione **o lançamento**. Adicione um novo artefacto e, em seguida, selecione o artefacto **do Modelo AzureML** e a ligação de serviço que criou anteriormente. Selecione o modelo e a versão para desencadear uma implementação:

    [![Selecione modelo AzureML](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Ative o gatilho do modelo no seu artefacto modelo. Quando liga o gatilho, sempre que a versão especificada (ou seja, a versão mais recente) desse modelo está registada no seu espaço de trabalho, é acionado um pipeline de libertação Azure DevOps.

    [![Ativar o gatilho do modelo](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Para mais projetos e exemplos de amostras, consulte estes repos de amostra no GitHub:

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>Descarregue um modelo
Se pretender descarregar o seu modelo para o utilizar no seu próprio ambiente de execução, pode fazê-lo com os seguintes comandos SDK/CLI:

SDK:
```python
model_path = Model(ws,'mymodel').download()
```

CLI:
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="preview-no-code-model-deployment"></a>(Pré-visualização) Implementação de modelo sem código

A implementação de modelos sem código está atualmente em pré-visualização e suporta os seguintes quadros de aprendizagem automática:

### <a name="tensorflow-savedmodel-format"></a>Formato Tensorflow SavedModel
Os modelos tensorflow precisam de ser registados no **formato SaveModel** para funcionarem sem implementação de modelos sem código.

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

O registo e implantação do modelo ONNX é suportado para qualquer gráfico de inferência ONNX. As etapas de pré-processamento e pós-processamento não são suportadas atualmente.

Aqui está um exemplo de como registar e implementar um modelo MNIST ONNX:

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

Se estiver a utilizar o Pytorch, [os modelos de exportação de PyTorch para ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) têm os detalhes sobre a conversão e limitações. 

### <a name="scikit-learn-models"></a>Modelos de aprendizagem de scikit

Nenhuma implementação de modelo de código é suportada para todos os tipos de modelos de aprendizagem de scikit-learn incorporados.

Aqui está um exemplo de como registar e implementar um modelo sklearn sem código extra:

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

NOTA: Os modelos que suportam predict_proba utilizarão este método por defeito. Para anular isto para usar, pode modificar o corpo POST como abaixo:
```python
import json


input_payload = json.dumps({
    'data': [
        [ 0.03807591,  0.05068012,  0.06169621, 0.02187235, -0.0442235,
         -0.03482076, -0.04340085, -0.00259226, 0.01990842, -0.01764613]
    ],
    'method': 'predict'  # If you have a classification model, the default behavior is to run 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

NOTA: Estas dependências estão incluídas no recipiente de inferência de sklearn pré-construído:

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

## <a name="package-models"></a>Modelos de pacotes

Em alguns casos, pode querer criar uma imagem Docker sem implementar o modelo (se, por exemplo, [pretender implantar-se no Azure App Service).](how-to-deploy-app-service.md) Ou talvez queira descarregar a imagem e executá-la numa instalação local do Docker. Pode até querer descarregar os ficheiros utilizados para construir a imagem, inspecioná-los, modificá-los e construir a imagem manualmente.

A embalagem do modelo permite-lhe fazer estas coisas. Embala todos os ativos necessários para hospedar um modelo como um serviço web e permite-lhe descarregar uma imagem docker totalmente construída ou os ficheiros necessários para construir um. Existem duas formas de utilizar a embalagem modelo:

**Faça o download de um modelo embalado:** Descarregue uma imagem do Docker que contenha o modelo e outros ficheiros necessários para a hospedar como um serviço web.

**Gerar um Dockerfile:** Descarregue o Dockerfile, modelo, script de entrada e outros ativos necessários para construir uma imagem do Docker. Em seguida, pode inspecionar os ficheiros ou fazer alterações antes de construir a imagem localmente.

Ambos os pacotes podem ser usados para obter uma imagem local do Docker.

> [!TIP]
> Criar um pacote é semelhante à implementação de um modelo. Utiliza um modelo registado e uma configuração de inferência.

> [!IMPORTANT]
> Para descarregar uma imagem totalmente construída ou construir uma imagem localmente, você precisa ter [Docker](https://www.docker.com) instalado no seu ambiente de desenvolvimento.

### <a name="download-a-packaged-model"></a>Descarregue um modelo embalado

O exemplo a seguir constrói uma imagem, que está registada no registo do contentor Azure para o seu espaço de trabalho:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Depois de criar um pacote, pode usar `package.pull()` para puxar a imagem para o seu ambiente local do Docker. A saída deste comando mostrará o nome da imagem. Por exemplo: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Depois de descarregar o modelo, use o `docker images` comando para listar as imagens locais:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

Para iniciar um recipiente local com base nesta imagem, utilize o seguinte comando para iniciar um contentor nomeado a partir da concha ou da linha de comando. Substitua o `<imageid>` valor pelo ID de imagem devolvido pelo `docker images` comando.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Este comando inicia a versão mais recente da imagem chamada `myimage` . Ele mapeia a porta local 6789 para o porto no recipiente em que o serviço web está a ouvir (5001). Atribui também o nome `mycontainer` ao recipiente, o que facilita a paragem do recipiente. Após o início do contentor, pode submeter pedidos a `http://localhost:6789/score` .

### <a name="generate-a-dockerfile-and-dependencies"></a>Gerar um Dockerfile e dependências

O exemplo a seguir mostra como descarregar o Dockerfile, modelo e outros ativos necessários para construir uma imagem localmente. O `generate_dockerfile=True` parâmetro indica que quer os ficheiros, não uma imagem totalmente construída.

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

Este código descarrega os ficheiros necessários para construir a imagem para o `imagefiles` diretório. O Dockerfile incluído nos ficheiros guardados refere uma imagem base armazenada num registo de contentores Azure. Quando constrói a imagem na instalação local do Docker, tem de utilizar o endereço, o nome de utilizador e a palavra-passe para autenticar o registo. Utilize os seguintes passos para construir a imagem utilizando uma instalação local do Docker:

1. A partir de uma sessão de concha ou linha de comando, utilize o seguinte comando para autenticar Docker com o registo do contentor Azure. `<address>` `<username>` Substitua, e pelos `<password>` valores recuperados por `package.get_container_registry()` .

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Para construir a imagem, utilize o seguinte comando. `<imagefiles>`Substitua-o pelo caminho do diretório onde `package.save()` guardou os ficheiros.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Este comando define o nome de imagem para `myimage` .

Para verificar se a imagem foi construída, use o `docker images` comando. Deve ver a `myimage` imagem na lista:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

Para iniciar um novo recipiente com base nesta imagem, utilize o seguinte comando:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Este comando inicia a versão mais recente da imagem chamada `myimage` . Ele mapeia a porta local 6789 para o porto no recipiente em que o serviço web está a ouvir (5001). Atribui também o nome `mycontainer` ao recipiente, o que facilita a paragem do recipiente. Após o início do contentor, pode submeter pedidos a `http://localhost:6789/score` .

### <a name="example-client-to-test-the-local-container"></a>Cliente de exemplo para testar o recipiente local

O seguinte código é um exemplo de um cliente Python que pode ser usado com o recipiente:

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

Por exemplo, clientes de outras linguagens de programação, consulte [os modelos Consume implementados como serviços web](how-to-consume-web-service.md).

### <a name="stop-the-docker-container"></a>Pare o recipiente Docker

Para parar o recipiente, utilize o seguinte comando a partir de uma concha ou linha de comando diferente:

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>Limpar recursos

Para eliminar um serviço web implantado, utilize `service.delete()` .
Para eliminar um modelo registado, utilize `model.delete()` .

Para obter mais informações, consulte a documentação para [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) e [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

<a id="advanced-entry-script"></a>
## <a name="advanced-entry-script-authoring"></a>Autoria de script de entrada avançada

<a id="binary"></a>

### <a name="binary-data"></a>Dados binários

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
res = request.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/octet-stream'})
```

<a id="cors"></a>

### <a name="cross-origin-resource-sharing-cors"></a>Partilha de recursos de origem cruzada (CORS)

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

## <a name="next-steps"></a>Passos seguintes

* [Como implementar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Resolução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Utilize o TLS para garantir um serviço web através do Azure Machine Learning](how-to-secure-web-service.md)
* [Consumir um modelo de Aprendizagem automática Azure implementado como um serviço web](how-to-consume-web-service.md)
* [Monitorize os seus modelos de machine learning Azure com Insights de Aplicações](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)
* [Criar alertas e gatilhos de eventos para implementações de modelos](how-to-use-event-grid.md)

