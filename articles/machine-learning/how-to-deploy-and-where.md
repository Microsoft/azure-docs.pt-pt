---
title: Como e onde implementar modelos
titleSuffix: Azure Machine Learning
description: Saiba como e onde implementar os seus modelos de Machine Learning Azure, incluindo Instâncias de Contentores Azure, Serviço Azure Kubernetes, Borda Azure IoT e matrizes de porta programáveis em campo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 02/27/2020
ms.custom: seoapril2019
ms.openlocfilehash: 3fe13dcb35e6985d160f52b7ee3f9da4accd7806
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756665"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Implementar modelos com o Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a implementar o seu modelo de machine learning como um serviço web na nuvem Azure ou nos dispositivos Azure IoT Edge.

O fluxo de trabalho é semelhante independentemente de [onde implementa](#target) o seu modelo:

1. Registar o modelo.
1. Preparar a implementação. (Especificar elementos, utilização, destino de computação.)
1. Implementar o modelo para o destino de computação.
1. Teste o modelo implantado, também chamado de serviço web.

Para obter mais informações sobre os conceitos envolvidos no fluxo de trabalho de implantação, consulte [Gerir, implementar e monitorizar modelos com o Azure Machine Learning.](concept-model-management-and-deployment.md)

## <a name="prerequisites"></a>Pré-requisitos

- Uma área de trabalho do Azure Machine Learning. Para mais informações, consulte Criar um espaço de [trabalho azure machine learning](how-to-manage-workspace.md).

- Um modelo. Se não tiver um modelo treinado, pode utilizar o modelo e os ficheiros de dependência fornecidos [neste tutorial](https://aka.ms/azml-deploy-cloud).

- A [extensão Azure CLI para o serviço de Machine Learning,](reference-azure-machine-learning-cli.md)o [Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou a extensão do Código visual de [aprendizagem automática Azure.](tutorial-setup-vscode-extension.md)

## <a name="connect-to-your-workspace"></a>Ligar à sua área de trabalho

O seguinte código mostra como se conectar a um espaço de trabalho de Aprendizagem automática Azure utilizando informações em cache para o ambiente de desenvolvimento local:

+ **Utilizar o SDK**

   ```python
   from azureml.core import Workspace
   ws = Workspace.from_config(path=".file-path/ws_config.json")
   ```

  Para obter mais informações sobre a utilização do SDK para se ligar a um espaço de trabalho, consulte o [Azure Machine Learning SDK para](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) documentação python.

+ **Usando o CLI**

   Quando utilizar o CLI, utilize o `-w` ou `--workspace-name` parâmetro para especificar o espaço de trabalho para o comando.

+ **Utilizar o Visual Studio Code**

   Quando utilizar o Código do Estúdio Visual, selecione o espaço de trabalho utilizando uma interface gráfica. Para mais informações, consulte [implementar e gerir modelos](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model) na documentação de extensão do Código do Estúdio Visual.

## <a name="register-your-model"></a><a id="registermodel"></a>Registe o seu modelo

Um modelo registado é um recipiente lógico para um ou mais ficheiros que compõem o seu modelo. Por exemplo, se tiver um modelo que esteja armazenado em vários ficheiros, pode registá-los como um único modelo no espaço de trabalho. Depois de registar os ficheiros, pode então descarregar ou implementar o modelo registado e receber todos os ficheiros que registou.

> [!TIP]
> Ao registar um modelo, fornece o caminho de uma localização em nuvem (de um treino) ou de um diretório local. Este caminho é apenas para localizar os ficheiros para upload como parte do processo de registo. Não precisa de corresponder ao caminho usado no guião de entrada. Para mais informações, consulte [localizar ficheiros do modelo no seu script de entrada](#load-model-files-in-your-entry-script).

Os modelos de aprendizagem automática estão registados no seu espaço de trabalho Azure Machine Learning. O modelo pode vir de Azure Machine Learning ou de outro lugar. Ao registar um modelo, pode fornecer opcionalmente metadados sobre o modelo. Os `tags` `properties` dicionários e dicionários que se aplica a um registo de modelo suplicou então para filtrar modelos.

Os exemplos que se seguem demonstram como registar um modelo.

### <a name="register-a-model-from-an-experiment-run"></a>Registe um modelo de uma experiência

Os fragmentos de código nesta secção demonstram como registar um modelo a partir de uma execução de formação:

> [!IMPORTANT]
> Para utilizar estes snippets, precisa de ter realizado previamente um treino `Run` e precisa de ter acesso ao objeto (exemplo SDK) ou ao valor de ID de execução (exemplo CLI). Para obter mais informações sobre modelos de formação, consulte Configurar metas de [cálculo para formação](how-to-set-up-training-targets.md)de modelos .

+ **Utilizar o SDK**

  Quando utiliza o SDK para treinar um modelo, pode receber um objeto [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) ou um objeto [AutoMLRun,](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) dependendo da forma como treinou o modelo. Cada objeto pode ser usado para registar um modelo criado por uma experiência executada.

  + Registe um `azureml.core.Run` modelo a partir de um objeto:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    O `model_path` parâmetro refere-se à localização da nuvem do modelo. Neste exemplo, o caminho de um único ficheiro é utilizado. Para incluir vários ficheiros `model_path` no registo do modelo, detete tede ao caminho de uma pasta que contenha os ficheiros. Para mais informações, consulte a documentação [Run.register_model.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-)

  + Registe um `azureml.train.automl.run.AutoMLRun` modelo a partir de um objeto:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    Neste exemplo, `metric` os `iteration` parâmetros e parâmetros não são especificados, pelo que a iteração com a melhor métrica primária será registada. O `model_id` valor devolvido da corrida é usado em vez de um nome modelo.

    Para mais informações, consulte a documentação [AutoMLRun.register_model.](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-)

+ **Usando o CLI**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
  ```

  [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

  O `--asset-path` parâmetro refere-se à localização da nuvem do modelo. Neste exemplo, o caminho de um único ficheiro é utilizado. Para incluir vários ficheiros `--asset-path` no registo do modelo, detete tede ao caminho de uma pasta que contenha os ficheiros.

+ **Utilizar o Visual Studio Code**

  Registe os modelos utilizando quaisquer ficheiros ou pastas de modelo utilizando a extensão do Código do [Estúdio Visual.](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)

### <a name="register-a-model-from-a-local-file"></a>Registe um modelo a partir de um ficheiro local

Pode registar um modelo fornecendo o percurso local do modelo. Pode fornecer o caminho de uma pasta ou de um único ficheiro. Pode utilizar este método para registar modelos treinados com O Machine Learning E depois descarregados. Também pode utilizar este método para registar modelos treinados fora do Azure Machine Learning.

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

  Para incluir vários ficheiros `model_path` no registo do modelo, detete tede ao caminho de uma pasta que contenha os ficheiros.

+ **Usando o CLI**

  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  Para incluir vários ficheiros `-p` no registo do modelo, detete tede ao caminho de uma pasta que contenha os ficheiros.

**Estimativa de tempo**: Aproximadamente 10 segundos.

Para mais informações, consulte a documentação para a [classe Modelo](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

Para obter mais informações sobre o trabalho com modelos treinados fora do Azure Machine Learning, consulte [como implementar um modelo existente](how-to-deploy-existing-model.md).

<a name="target"></a>

## <a name="single-versus-multi-model-endpoints"></a>Pontos finais simples versus multi-modelo
O Azure ML suporta a implementação de modelos únicos ou múltiplos atrás de um único ponto final.

Os pontos finais multi-modelo utilizam um recipiente partilhado para acolher vários modelos. Isto ajuda a reduzir os custos gerais, melhora a utilização e permite-lhe acorrentar módulos em conjuntos. Os modelos que especifica no seu script de implementação são montados e disponibilizados no disco do recipiente de serviço - pode carregá-los na memória a pedido e pontuar com base no modelo específico que está a ser solicitado no momento da pontuação.

Para um exemplo E2E, que mostra como usar vários modelos por trás de um único ponto final contentorizado, consulte [este exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-multi-model)

## <a name="prepare-to-deploy"></a>Preparar para implementar

Para implementar o modelo como um serviço, precisa dos seguintes componentes:

* **Defina o ambiente de inferência.** Este ambiente engloba as dependências necessárias para executar o seu modelo de inferência.
* **Defina o código de pontuação.** Este script aceita pedidos, marca os pedidos utilizando o modelo e devolve os resultados.
* **Defina a configuração de inferência.** A configuração de inferência especifica a configuração do ambiente, o script de entrada e outros componentes necessários para executar o modelo como um serviço.

Uma vez que tenha os componentes necessários, pode perfilar o serviço que será criado como resultado da implementação do seu modelo para compreender os seus requisitos de CPU e memória.

### <a name="1-define-inference-environment"></a>1. Definir ambiente de inferência

Uma configuração de inferência descreve como configurar o serviço web contendo o seu modelo. É usado mais tarde, quando se implanta o modelo.

A configuração de inferência utiliza ambientes de Aprendizagem automática Azure para definir as dependências de software necessárias para a sua implementação. Os ambientes permitem-lhe criar, gerir e reutilizar as dependências de software necessárias para a formação e implementação. Você pode criar um ambiente a partir de ficheiros de dependência personalizada ou usar um dos ambientes de aprendizagem automática Azure com curadoria. O Seguinte YAML é um exemplo de um ficheiro de dependências da Conda para inferência. Note que deve indicar incumprimentos em azureml com verion >= 1,0,45 como dependência do pip, porque contém a funcionalidade necessária para hospedar o modelo como um serviço web. Se quiser utilizar a geração automática de esquemas, `inference-schema` o seu script de entrada também deve importar os pacotes.

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
> Se a sua dependência estiver disponível através da Conda e pip (da PyPi), a Microsoft recomenda a utilização da versão Conda, uma vez que os pacotes Conda normalmente vêm com binários pré-construídos que tornam a instalação mais fiável.
>
> Para mais informações, consulte [Understanding Conda e Pip.](https://www.anaconda.com/understanding-conda-and-pip/)
>
> Para verificar se a sua dependência está `conda search <package-name>` disponível através da Conda, utilize o comando ou utilize os índices de embalagem em [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) e [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo).

Pode utilizar o ficheiro de dependências para criar um objeto ambiental e guardá-lo para o seu espaço de trabalho para uso futuro:

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                             file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

### <a name="2-define-scoring-code"></a><a id="script"></a>2. Definir código de pontuação

O script de entrada recebe os dados enviados para um serviço Web implementado e transmite-os para o modelo. Em seguida, seleciona a resposta devolvida pelo modelo e devolve-a ao cliente. *O guião é específico do seu modelo.* Deve compreender os dados que o modelo espera e devolve.

O script contém duas funções que carregam e executam o modelo:

* `init()`: Normalmente, esta função coloca o modelo num objeto global. Esta função é executada apenas uma vez, quando o recipiente Docker para o seu serviço web é iniciado.

* `run(input_data)`: Esta função utiliza o modelo para prever um valor com base nos dados de entrada. Regra geral, as entradas e as saídas da execução utilizam JSON para a serialização e a desserialização. Também pode trabalhar com dados binários não processados. Pode transformar os dados antes de enviá-los para o modelo ou antes de devolvê-los ao cliente.

#### <a name="load-model-files-in-your-entry-script"></a>Carregar ficheiros do modelo no seu script de entrada

Existem duas formas de localizar modelos no seu script de entrada:
* `AZUREML_MODEL_DIR`: Uma variável ambiental que contenha o caminho para a localização do modelo.
* `Model.get_model_path`: Uma API que devolve o caminho para o ficheiro modelo utilizando o nome do modelo registado.

##### <a name="azureml_model_dir"></a>AZUREML_MODEL_DIR

AZUREML_MODEL_DIR é uma variável ambiental criada durante a implantação do serviço. Pode utilizar esta variável ambiental para encontrar a localização dos modelos implantados.

O quadro seguinte descreve o valor da AZUREML_MODEL_DIR dependendo do número de modelos implantados:

| Implementação | Valor variável ambiental |
| ----- | ----- |
| Modelo único | O caminho para a pasta que contém o modelo. |
| Vários modelos | O caminho para a pasta que contém todos os modelos. Os modelos estão localizados`$MODEL_NAME/$VERSION`pelo nome e pela versão nesta pasta ( ) |

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

Ao registar um modelo, fornece um nome modelo que é usado para gerir o modelo no registo. Utiliza este nome com o método [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) para recuperar o caminho do ficheiro ou ficheiros do modelo no sistema de ficheiros local. Se registar uma pasta ou uma recolha de ficheiros, esta API devolve o caminho do diretório que contém esses ficheiros.

Quando registas um modelo, dás-lhe um nome. O nome corresponde ao local onde o modelo é colocado, quer localmente quer durante a implantação do serviço.

#### <a name="optional-define-model-web-service-schema"></a>(Opcional) Definir esquema de serviço web modelo

Para gerar automaticamente um esquema para o seu serviço web, forneça uma amostra da entrada e/ou saída no construtor para um dos objetos do tipo definido. O tipo e a amostra são utilizados para criar automaticamente o esquema. O Azure Machine Learning cria então uma especificação [OpenAPI](https://swagger.io/docs/specification/about/) (Swagger) para o serviço web durante a implantação.

Estes tipos são atualmente suportados:

* `pandas`
* `numpy`
* `pyspark`
* Objeto Python padrão

Para utilizar a geração de esquemas, inclua o pacote de código `inference-schema` aberto no ficheiro de dependências. Para mais informações sobre [https://github.com/Azure/InferenceSchema](https://github.com/Azure/InferenceSchema)este pacote, consulte . Defina os formatos de `input_sample` amostra `output_sample` de entrada e saída nos formatos e variáveis, que representam os formatos de pedido e resposta para o serviço web. Utilize estas amostras nos decoradores da `run()` função de entrada e saída na função. O exemplo de scikit-learn que se segue utiliza a geração schema.

##### <a name="example-entry-script"></a>Roteiro de entrada de exemplo

O exemplo que se segue demonstra como aceitar e devolver dados da JSON:

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

O exemplo seguinte demonstra como definir os `<key: value>` dados de entrada como um dicionário utilizando um DataFrame. Este método é suportado para consumir o serviço web implantado a partir do Power BI. ([Saiba mais sobre como consumir o serviço web a partir do Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-integration).)

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
    
O exemplo que se segue demonstra carregar um ambiente do seu espaço de trabalho e depois usá-lo com a configuração de inferência:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
```

Para obter mais informações sobre ambientes, consulte [Criar e gerir ambientes para formação e implantação.](how-to-use-environments.md)

Para obter mais informações sobre a configuração da inferência, consulte a documentação da classe [InferenceConfig.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)

Para obter informações sobre a utilização de uma imagem personalizada do Docker com uma configuração de inferência, consulte [como implementar um modelo utilizando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md).

#### <a name="cli-example-of-inferenceconfig"></a>Exemplo cli de InferenceConfig

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

O seguinte comando demonstra como implementar um modelo utilizando o CLI:

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

Neste exemplo, a configuração especifica as seguintes definições:

* Que o modelo requer Python.
* O [script de entrada](#script), que é usado para lidar com pedidos web enviados para o serviço implantado.
* O ficheiro Da Conda que descreve os pacotes Python necessários para a inferência.

Para obter informações sobre a utilização de uma imagem personalizada do Docker com uma configuração de inferência, consulte [como implementar um modelo utilizando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md).

### <a name="4-optional-profile-your-model-to-determine-resource-utilization"></a><a id="profilemodel"></a>4. (Opcional) Perfile o seu modelo para determinar a utilização de recursos

Depois de ter registado o seu modelo e preparado os outros componentes necessários para a sua implantação, pode determinar o CPU e a memória de que o serviço implantado necessitará. O perfil testa o serviço que executa o seu modelo e devolve informações como o uso do CPU, o uso da memória e a latência de resposta. Também fornece uma recomendação para o CPU e memória com base na utilização de recursos.

Para perfilar o seu modelo, necessitará:
* Um modelo registado.
* Uma configuração de inferência baseada no seu script de entrada e definição de ambiente de inferência.
* Um conjunto de dados tabular de coluna única, onde cada linha contém uma sequência que representa dados de pedido de amostra.

> [!IMPORTANT]
> Neste momento apenas apoiamos o perfil de serviços que esperam que os seus dados de pedido sejam uma cadeia, por exemplo: json serializado de cordas, texto, imagem serializada de cordas, etc. O conteúdo de cada linha do conjunto de dados (cadeia) será colocado no corpo do pedido HTTP e enviado para o serviço encapsulando o modelo de pontuação.

Abaixo está um exemplo de como pode construir um conjunto de dados de entrada para perfilar um serviço que espera que os seus dados de pedido de entrada contenham json serializado. Neste caso, criámos um conjunto de dados baseado em cem casos do mesmo conteúdo de dados de pedidos. Em cenários do mundo real sugerimos que você use conjuntos de dados maiores contendo várias entradas, especialmente se o seu modelo de utilização/comportamento de recursos é dependente de entrada.

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

Assim que tiver o conjunto de dados que contém dados de pedido de amostra, crie uma configuração de inferência. A configuração da inferência baseia-se no score.py e na definição de ambiente. O exemplo que se segue demonstra como criar a configuração da inferência e executar perfis:

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
> Para persistir a informação devolvida por perfis, etiquetas de utilização ou propriedades para o modelo. A utilização de etiquetas ou propriedades armazena os dados com o modelo no registo do modelo. Os seguintes exemplos demonstram a `requestedCpu` `requestedMemoryInGb` adição de uma nova etiqueta contendo a e informação:
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

## <a name="deploy-to-target"></a>Desdobre para o alvo

A implementação utiliza a configuração de configuração de inferência para implementar os modelos. O processo de implantação é semelhante independentemente do objetivo do cálculo. A implantação para AKS é ligeiramente diferente porque deve fornecer uma referência ao cluster AKS.

### <a name="choose-a-compute-target"></a>Escolha um alvo de computação

Pode utilizar os seguintes alvos de cálculo, ou calcular recursos, para acolher a sua implementação do serviço web:

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

### <a name="define-your-deployment-configuration"></a>Defina a sua configuração de implementação

Antes de implementar o seu modelo, deve definir a configuração de implementação. *A configuração de implementação é específica para o alvo da computação que irá acolher o serviço web.* Por exemplo, quando implementa um modelo localmente, deve especificar a porta onde o serviço aceita pedidos. A configuração de implementação não faz parte do seu script de entrada. É usado para definir as características do alvo computacional que irá acolher o modelo e o script de entrada.

Você também pode precisar de criar o recurso computacional, se, por exemplo, você não tiver já uma instância azure Kubernetes Service (AKS) associada ao seu espaço de trabalho.

A tabela seguinte fornece um exemplo de criação de uma configuração de implementação para cada alvo do cálculo:

| Destino de computação | Exemplo de configuração de implementação |
| ----- | ----- |
| Localização | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

As aulas para os serviços web locais, azure container `azureml.core.webservice`e AKS podem ser importadas de:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

### <a name="securing-deployments-with-tls"></a>Assegurar implementações com TLS

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

Para mais informações, consulte a documentação para [LocalWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)e [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

#### <a name="using-the-cli"></a>Usando o CLI

Para implantar um modelo utilizando o CLI, utilize o seguinte comando. Substitua `mymodel:1` pelo nome e versão do modelo registado:

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Para mais informações, consulte o [modelo az ml implementar](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) documentação.

### <a name="understanding-service-state"></a>Compreender o estado de serviço

Durante a implementação do modelo, pode ver a alteração do estado de serviço enquanto se desdobra totalmente.

A tabela seguinte descreve os diferentes estados de serviço:

| Estado de serviço web | Descrição | Estado final?
| ----- | ----- | ----- |
| Transição | O serviço está em fase de implantação. | Não |
| Mau estado de funcionamento | O serviço foi acionado, mas está inacessível.  | Não |
| Inescalável | O serviço não pode ser implementado neste momento por falta de recursos. | Não |
| Falhou | O serviço falhou na sua implantação devido a um erro ou a um acidente. | Sim |
| Bom estado de funcionamento | O serviço é saudável e o ponto final está disponível. | Sim |

### <a name="compute-instance-web-service-devtest"></a><a id="notebookvm"></a>Serviço web de instância computacional (dev/teste)

Consulte [a implementação de um modelo para a instância de computação](how-to-deploy-local-container-notebook-vm.md)de machine learning azure .

### <a name="azure-container-instances-devtest"></a><a id="aci"></a>Instâncias de contentores azure (dev/teste)

Consulte [a implantação para as instâncias](how-to-deploy-azure-container-instance.md)do contentor Azure .

### <a name="azure-kubernetes-service-devtest-and-production"></a><a id="aks"></a>Serviço Azure Kubernetes (dev/teste e produção)

Consulte [o Serviço Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).

### <a name="ab-testing-controlled-rollout"></a>Ensaio a/B (lançamento controlado)
Consulte o [lançamento controlado dos modelos ML](how-to-deploy-azure-kubernetes-service.md#deploy-models-to-aks-using-controlled-rollout-preview) para obter mais informações.

## <a name="consume-web-services"></a>Consumir os serviços web

Cada serviço web implantado fornece um ponto final REST, para que possa criar aplicações de clientes em qualquer idioma de programação.
Se tiver ativado a autenticação baseada na chave para o seu serviço, precisa de fornecer uma chave de serviço como um símbolo no seu cabeçalho de pedido.
Se tiver ativado a autenticação baseada em token para o seu serviço, precisa de fornecer um Web Token Web De aprendizagem automática Azure (JWT) como um símbolo portador no seu cabeçalho de pedido. 

A principal diferença é que **as teclas são estáticas e podem ser regeneradas manualmente,** e **as fichas precisam de ser refrescadas após a expiração**. O auth baseado em chaves é suportado para a Instância de Contentores Azure e o Serviço Azure Kubernetes implantado sipróprio si, e o auth baseado em token **só** está disponível para implementações do Serviço Azure Kubernetes. Consulte o [como fazer](how-to-setup-authentication.md#web-service-authentication) na autenticação para obter mais informações e amostras específicas de código.

> [!TIP]
> Pode recuperar o documento schema JSON depois de implementar o serviço. Utilize a [propriedade swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri) a partir do `service.swagger_uri`serviço web implantado (por exemplo, ) para levar o URI ao ficheiro Swagger do serviço web local.

### <a name="request-response-consumption"></a>Consumo de pedido-resposta

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

Para mais informações, consulte [Criar aplicações de clientes para consumir serviços web.](how-to-consume-web-service.md)

### <a name="web-service-schema-openapi-specification"></a>Esquema de serviço web (especificação OpenAPI)

Se usou a geração de esquemas automáticos com a sua implementação, pode obter o endereço da especificação OpenAPI para o serviço utilizando a [propriedade swagger_uri](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py#swagger-uri). (Por exemplo, `print(service.swagger_uri)`.) Utilize um pedido GET ou abra o URI num browser para recuperar a especificação.

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

Para mais informações, consulte [a especificação OpenAPI](https://swagger.io/specification/).

Para um utilitário que possa criar bibliotecas de clientes a partir da especificação, consulte [swagger-codegen](https://github.com/swagger-api/swagger-codegen).

### <a name="batch-inference"></a><a id="azuremlcompute"></a>Inferência do lote
Os alvos da Azure Machine Learning Compute são criados e geridos pela Azure Machine Learning. Podem ser utilizados para a previsão de lotes a partir de oleodutos Azure Machine Learning.

Para uma passagem de inferência de lote com A Computação de Aprendizagem automática Azure, consulte [como executar as previsões](tutorial-pipeline-batch-scoring-classification.md)do lote .

### <a name="iot-edge-inference"></a><a id="iotedge"></a>Inferência ioT edge
O suporte para a implantação para a borda está em pré-visualização. Para mais informações, consulte [a Deploy Azure Machine Learning como um módulo IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a name="update-web-services"></a><a id="update"></a>Atualizar os serviços web

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="continuously-deploy-models"></a>Implementar continuamente modelos

Pode implantar continuamente modelos utilizando a extensão de Machine Learning para [O Azure DevOps](https://azure.microsoft.com/services/devops/). Pode utilizar a extensão machine learning para o Azure DevOps para acionar um pipeline de implantação quando um novo modelo de machine learning está registado num espaço de trabalho de Machine Learning Azure.

1. Inscreva-se na [Azure Pipelines,](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)o que torna possível a integração contínua e entrega da sua aplicação a qualquer plataforma ou nuvem. (Note que os gasodutos Azure não são os mesmos que [os gasodutos de Machine Learning](concept-ml-pipelines.md#compare).)

1. [Crie um projeto Azure DevOps.](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

1. Instale a [extensão de Machine Learning para os gasodutos Azure](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml&targetId=6756afbe-7032-4a36-9cb6-2771710cadc2&utm_source=vstsproduct&utm_medium=ExtHubManageList).

1. Utilize ligações de serviço para configurar uma ligação principal de serviço ao seu espaço de trabalho Azure Machine Learning para que possa aceder aos seus artefactos. Vá às definições do projeto, selecione ligações de **serviço,** e, em seguida, selecione **Azure Resource Manager:**

    [![Selecione Gestor de Recursos Azure](media/how-to-deploy-and-where/view-service-connection.png)](media/how-to-deploy-and-where/view-service-connection-expanded.png)

1. Na lista de **nível scope,** selecione **AzureMLWorkspace,** e, em seguida, introduza o resto dos valores:

    ![Selecione AzureMLWorkspace](./media/how-to-deploy-and-where/resource-manager-connection.png)

1. Para implantar continuamente o seu modelo de aprendizagem automática utilizando pipelines Azure, sob gasodutos, selecione **a libertação**. Adicione um novo artefacto e, em seguida, selecione o artefacto **do Modelo AzureML** e a ligação de serviço que criou anteriormente. Selecione o modelo e a versão para desencadear uma implementação:

    [![Selecione Modelo AzureML](media/how-to-deploy-and-where/enable-modeltrigger-artifact.png)](media/how-to-deploy-and-where/enable-modeltrigger-artifact-expanded.png)

1. Ative o gatilho do modelo no seu artefacto modelo. Quando liga o gatilho, sempre que a versão especificada (ou seja, a versão mais recente) desse modelo está registada no seu espaço de trabalho, é acionado um pipeline de libertação DoAzure DevOps.

    [![Ativar o gatilho do modelo](media/how-to-deploy-and-where/set-modeltrigger.png)](media/how-to-deploy-and-where/set-modeltrigger-expanded.png)

Para mais projetos de amostra e exemplos, consulte estes repos de amostra no GitHub:

* [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
* [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

## <a name="download-a-model"></a>Descarregue um modelo
Se quiser descarregar o seu modelo para o utilizar no seu próprio ambiente de execução, pode fazê-lo com os seguintes comandos SDK/CLI:

SDK:
```python
model_path = Model(ws,'mymodel').download()
```

CLI:
```azurecli-interactive
az ml model download --model-id mymodel:1 --target-dir model_folder
```

## <a name="preview-no-code-model-deployment"></a>(Pré-visualização) Implementação do modelo sem código

A implementação do modelo sem código encontra-se atualmente em pré-visualização e suporta os seguintes quadros de aprendizagem automática:

### <a name="tensorflow-savedmodel-format"></a>Formato Tensorflow SavedModel
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

O registo e implementação do modelo ONNX é suportado para qualquer gráfico de inferência ONNX. Não são atualmente suportadas etapas de pré-processo e pós-processo.

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

Se estiver a utilizar o Pytorch, [os modelos de exportação do PyTorch para o ONNX](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) têm os detalhes sobre conversão e limitações. 

### <a name="scikit-learn-models"></a>Modelos de aprendizagem de scikit

Nenhuma implementação de modelo de código é suportada para todos os tipos de modelos de aprendizagem de scikit incorporados.

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

NOTA: Os modelos que suportam predict_proba utilizarão este método por defeito. Para anular isto para usar, preveja que pode modificar o corpo do POST como abaixo:
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

NOTA: Estas dependências estão incluídas no recipiente de inferência sklearn pré-construído:

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

## <a name="package-models"></a>Modelos de pacotes

Em alguns casos, é melhor criar uma imagem Do Docker sem implementar o modelo (se, por exemplo, planeia ser implantado para o [Azure App Service).](how-to-deploy-app-service.md) Ou talvez queira descarregar a imagem e executá-la numa instalação local do Docker. Pode até querer descarregar os ficheiros utilizados para construir a imagem, inspecioná-los, modificá-los e construir a imagem manualmente.

A embalagem do modelo permite-lhe fazer estas coisas. Embala todos os ativos necessários para hospedar um modelo como um serviço web e permite-lhe descarregar uma imagem do Docker totalmente construída ou os ficheiros necessários para construir um. Existem duas formas de utilizar a embalagem modelo:

**Faça o download de um modelo embalado:** Descarregue uma imagem do Docker que contenha o modelo e outros ficheiros necessários para a hospedar como um serviço web.

**Gerar um Dockerfile:** Descarregue o Dockerfile, modelo, script de entrada e outros bens necessários para construir uma imagem do Docker. Em seguida, pode inspecionar os ficheiros ou fazer alterações antes de construir a imagem localmente.

Ambos os pacotes podem ser usados para obter uma imagem local do Docker.

> [!TIP]
> A criação de um pacote é semelhante à implementação de um modelo. Utiliza um modelo registado e uma configuração de inferência.

> [!IMPORTANT]
> Para descarregar uma imagem totalmente construída ou construir uma imagem localmente, precisa de ter o [Docker](https://www.docker.com) instalado no seu ambiente de desenvolvimento.

### <a name="download-a-packaged-model"></a>Descarregue um modelo embalado

O exemplo seguinte constrói uma imagem, que está registada no registo de contentores Azure para o seu espaço de trabalho:

```python
package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
```

Depois de criar um pacote, pode usar `package.pull()` para puxar a imagem para o seu ambiente local do Docker. A saída deste comando mostrará o nome da imagem. Por exemplo: 

`Status: Downloaded newer image for myworkspacef78fd10.azurecr.io/package:20190822181338`. 

Depois de descarregar o `docker images` modelo, use o comando para listar as imagens locais:

```text
REPOSITORY                               TAG                 IMAGE ID            CREATED             SIZE
myworkspacef78fd10.azurecr.io/package    20190822181338      7ff48015d5bd        4 minutes ago       1.43 GB
```

Para iniciar um contentor local com base nesta imagem, utilize o seguinte comando para iniciar um recipiente nomeado a partir da concha ou linha de comando. Substitua `<imageid>` o valor pelo ID `docker images` de imagem devolvido pelo comando.

```bash
docker run -p 6789:5001 --name mycontainer <imageid>
```

Este comando inicia a versão `myimage`mais recente da imagem chamada . Mapeia a porta local 6789 para a porta do contentor em que o serviço web está a ouvir (5001). Atribui também o `mycontainer` nome ao recipiente, o que facilita a paragem do recipiente. Após o início do recipiente, pode `http://localhost:6789/score`submeter pedidos a .

### <a name="generate-a-dockerfile-and-dependencies"></a>Gerar um Dockerfile e dependências

O exemplo que se segue mostra como descarregar o Dockerfile, modelo e outros ativos necessários para construir uma imagem localmente. O `generate_dockerfile=True` parâmetro indica que quer os ficheiros, não uma imagem totalmente construída.

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

Este código descarrega os ficheiros necessários para construir a imagem para o `imagefiles` diretório. O Dockerfile incluído nos ficheiros guardados refere uma imagem base armazenada num registo de contentores Azure. Quando constrói a imagem na instalação local do Docker, tem de utilizar o endereço, o nome do utilizador e a palavra-passe para autenticar o registo. Utilize os seguintes passos para construir a imagem utilizando uma instalação local do Docker:

1. A partir de uma sessão de shell ou linha de comando, utilize o seguinte comando para autenticar o Docker com o registo de contentores Azure. Substitua, `<address>` `<username>` `<password>` e com os `package.get_container_registry()`valores recuperados por .

    ```bash
    docker login <address> -u <username> -p <password>
    ```

2. Para construir a imagem, use o seguinte comando. Substitua `<imagefiles>` pelo percurso do `package.save()` diretório onde guardou os ficheiros.

    ```bash
    docker build --tag myimage <imagefiles>
    ```

    Este comando define o `myimage`nome da imagem para .

Para verificar se a imagem `docker images` foi construída, utilize o comando. Devia ver `myimage` a imagem na lista:

```text
REPOSITORY      TAG                 IMAGE ID            CREATED             SIZE
<none>          <none>              2d5ee0bf3b3b        49 seconds ago      1.43 GB
myimage         latest              739f22498d64        3 minutes ago       1.43 GB
```

Para iniciar um novo recipiente com base nesta imagem, utilize o seguinte comando:

```bash
docker run -p 6789:5001 --name mycontainer myimage:latest
```

Este comando inicia a versão `myimage`mais recente da imagem chamada . Mapeia a porta local 6789 para a porta do contentor em que o serviço web está a ouvir (5001). Atribui também o `mycontainer` nome ao recipiente, o que facilita a paragem do recipiente. Após o início do recipiente, pode `http://localhost:6789/score`submeter pedidos a .

### <a name="example-client-to-test-the-local-container"></a>Cliente exemplo para testar o contentor local

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

Por exemplo, clientes de outras linguagens de programação, consulte [os modelos Consumir implantados como serviços web.](how-to-consume-web-service.md)

### <a name="stop-the-docker-container"></a>Pare o recipiente docker

Para parar o recipiente, utilize o seguinte comando de uma camada ou linha de comando diferente:

```bash
docker kill mycontainer
```

## <a name="clean-up-resources"></a>Limpar recursos

Para eliminar um serviço web `service.delete()`implantado, utilize .
Para eliminar um modelo `model.delete()`registado, utilize .

Para mais informações, consulte a documentação para [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) e [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

<a id="advanced-entry-script"></a>
## <a name="advanced-entry-script-authoring"></a>Autor de script de entrada avançada

<a id="binary"></a>

### <a name="binary-data"></a>Dados binários

Se o seu modelo aceitar dados binários, como `score.py` uma imagem, deve modificar o ficheiro utilizado para a sua implementação para aceitar pedidos http crus. Para aceitar dados brutos, utilize a `AMLRequest` classe `@rawhttp` no seu `run()` script de entrada e adicione o decorador à função.

Aqui está um exemplo `score.py` de um que aceita dados binários:

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
> A `AMLRequest` aula está `azureml.contrib` no espaço do nome. As entidades neste espaço de nome mudam frequentemente à medida que trabalhamos para melhorar o serviço. Qualquer coisa neste espaço de nome deve ser considerada uma pré-visualização que não seja totalmente suportada pela Microsoft.
>
> Se precisar de testar isto no seu ambiente de desenvolvimento local, pode instalar os componentes utilizando o seguinte comando:
>
> ```shell
> pip install azureml-contrib-services
> ```

A `AMLRequest` classe só permite aceder aos dados publicados em bruto no score.py, não existe nenhum componente do lado do cliente. De um cliente, publica dados normalmente. Por exemplo, o seguinte código Python lê um ficheiro de imagem e publica os dados:

```python
import requests
# Load image data
data = open('example.jpg', 'rb').read()
# Post raw data to scoring URI
res = request.post(url='<scoring-uri>', data=data, headers={'Content-Type': 'application/octet-stream'})
```

<a id="cors"></a>

### <a name="cross-origin-resource-sharing-cors"></a>Partilha de recursos de origem cruzada (CORS)

A partilha de recursos de origem cruzada é uma forma de permitir que os recursos numa página web sejam solicitados a partir de outro domínio. O CORS trabalha através de cabeçalhos HTTP enviados com o pedido do cliente e devolvidos com a resposta de serviço. Para obter mais informações sobre cors e cabeçalhos válidos, consulte a [partilha de recursos de origem cruzada](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) na Wikipédia.

Para configurar a implementação do seu `AMLResponse` modelo para suportar o CORS, utilize a classe no seu script de entrada. Esta aula permite-lhe definir os cabeçalhos no objeto de resposta.

O exemplo seguinte `Access-Control-Allow-Origin` define o cabeçalho para a resposta do script de entrada:

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
> A `AMLResponse` aula está `azureml.contrib` no espaço do nome. As entidades neste espaço de nome mudam frequentemente à medida que trabalhamos para melhorar o serviço. Qualquer coisa neste espaço de nome deve ser considerada uma pré-visualização que não seja totalmente suportada pela Microsoft.
>
> Se precisar de testar isto no seu ambiente de desenvolvimento local, pode instalar os componentes utilizando o seguinte comando:
>
> ```shell
> pip install azureml-contrib-services
> ```


> [!WARNING]
> O Azure Machine Learning irá encaminhar apenas os pedidos POST e GET para os contentores que executam o serviço de pontuação. Isto pode causar erros devido aos navegadores que usam pedidos OP para pedidos CORS pré-voo.
> 

## <a name="next-steps"></a>Passos seguintes

* [Como implementar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Resolução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Use tLS para garantir um serviço web através de Azure Machine Learning](how-to-secure-web-service.md)
* [Consumir um modelo azure machine learning implementado como um serviço web](how-to-consume-web-service.md)
* [Monitorize os seus modelos de Aprendizagem automática Azure com Insights de Aplicação](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)
* [Criar alertas de eventos e gatilhos para implementações de modelos](how-to-use-event-grid.md)

