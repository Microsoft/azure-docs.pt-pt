---
title: Utilizar e implementar modelos existentes
titleSuffix: Azure Machine Learning
description: Aprenda a trazer modelos ML treinados fora de Azure para a nuvem Azure com Azure Machine Learning. Em seguida, desloque o modelo como um serviço web ou módulo IoT.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 0a536781f3218807c36f6eefe738b9a375de8d4b
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213135"
---
# <a name="deploy-your-existing-model-with-azure-machine-learning"></a>Implemente o seu modelo existente com Azure Machine Learning


Neste artigo, aprende a registar-se e a implementar um modelo de machine learning que treinou fora do Azure Machine Learning. Pode implementar como serviço web ou para um dispositivo IoT Edge.  Uma vez implementado, pode monitorizar o seu modelo e detetar a deriva de dados no Azure Machine Learning. 

Para obter mais informações sobre os conceitos e termos deste artigo, consulte [Gerir, implementar e monitorizar modelos de machine learning.](concept-model-management-and-deployment.md)

## <a name="prerequisites"></a>Pré-requisitos

* [Uma área de trabalho do Azure Machine Learning](how-to-manage-workspace.md)
  + Os exemplos de Python assumem que a `ws` variável está definida para o seu espaço de trabalho de aprendizagem de máquinas Azure. Para obter mais informações sobre como ligar-se ao espaço de trabalho, consulte o [Azure Machine Learning SDK para documentação Python](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#&preserve-view=trueworkspace).
  
  + Os exemplos CLI utilizam espaços reservados `myworkspace` de `myresourcegroup` e, que deve substituir pelo nome do seu espaço de trabalho e pelo grupo de recursos que o contém.

* [A máquina azul aprendendo Python SDK.](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)  

* A extensão CLI do [Azure CLI](/cli/azure/install-azure-cli) e [machine learning CLI](reference-azure-machine-learning-cli.md).

* Um modelo treinado. O modelo deve ser persistido a um ou mais ficheiros sobre o seu ambiente de desenvolvimento. <br><br>Para demonstrar o registo de um modelo treinado, o código de exemplo neste artigo utiliza os modelos do projeto de [análise de sentimentos do Twitter de Paolo Ripamonti.](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis)

## <a name="register-the-models"></a>Registar o(s) modelo(s)

Registar um modelo permite-lhe armazenar, ver e rastrear metadados sobre modelos no seu espaço de trabalho. Nos seguintes exemplos python e CLI, o `models` diretório contém, `model.h5` `model.w2v` e `encoder.pkl` `tokenizer.pkl` ficheiros. Este exemplo faz o upload dos ficheiros contidos no `models` diretório como um novo modelo de registo denominado `sentiment` :

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

Para mais informações, consulte a referência [Modelo.register().](/python/api/azureml-core/azureml.core.model%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-)

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> Também pode definir objetos adicionais `tags` e `properties` dicionários para o modelo registado. Estes valores podem ser usados mais tarde para ajudar a identificar um modelo específico. Por exemplo, a estrutura utilizada, os parâmetros de formação, etc.

Para mais informações, consulte a referência [de registo do modelo az ml.](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-register)


Para obter mais informações sobre o registo do modelo em geral, consulte [Gerir, implementar e monitorizar modelos de aprendizagem automática.](concept-model-management-and-deployment.md)

## <a name="define-inference-configuration"></a>Definir configuração de inferência

A configuração de inferência define o ambiente utilizado para executar o modelo implantado. A configuração da inferência refere as seguintes entidades, que são usadas para executar o modelo quando é implementado:

* Um script de entrada, denominado `score.py` , carrega o modelo quando o serviço implantado começa. Este script também é responsável por receber dados, transmiti-lo para o modelo e, em seguida, devolver uma resposta.
* Um [ambiente](how-to-use-environments.md)de aprendizagem automática Azure. Um ambiente define as dependências de software necessárias para executar o modelo e o script de entrada.

O exemplo a seguir mostra como usar o SDK para criar um ambiente e, em seguida, usá-lo com uma configuração de inferência:

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
# You must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
conda_dep.add_pip_package("keras")
conda_dep.add_pip_package("gensim")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Para obter mais informações, veja os seguintes artigos:

+ [Como utilizar ambientes.](how-to-use-environments.md)
+ [InferênciaConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig?preserve-view=true&view=azure-ml-py) referência.


O CLI carrega a configuração de inferência a partir de um ficheiro YAML:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Com o CLI, o ambiente conda é definido no `myenv.yml` ficheiro referenciado pela configuração da inferência. O seguinte YAML é o conteúdo deste ficheiro:

```yaml
name: inference_environment
dependencies:
- python=3.6.2
- tensorflow
- numpy
- scikit-learn
- pip:
    - azureml-defaults
    - keras
    - gensim
```

Para obter mais informações sobre a configuração de inferência, consulte [implementar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="entry-script-scorepy"></a>Script de entrada (score.py)

O roteiro de entrada tem apenas duas funções `init()` necessárias, e `run(data)` . Estas funções são utilizadas para inicializar o serviço no arranque e executar o modelo utilizando dados de pedido passados por um cliente. O resto do script lida com o carregamento e execução do modelo.

> [!IMPORTANT]
> Não há um guião de entrada genérica que funcione para todos os modelos. É sempre específico do modelo que é usado. Tem de compreender como carregar o modelo, o formato de dados que o modelo espera e como obter dados utilizando o modelo.

O seguinte código Python é um script de entrada de exemplo `score.py` ( ):

```python
import os
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec

# SENTIMENT
POSITIVE = "POSITIVE"
NEGATIVE = "NEGATIVE"
NEUTRAL = "NEUTRAL"
SENTIMENT_THRESHOLDS = (0.4, 0.7)
SEQUENCE_LENGTH = 300

# Called when the deployed service starts
def init():
    global model
    global tokenizer
    global encoder
    global w2v_model

    # Get the path where the deployed model can be found.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), './models')
    # load models
    model = load_model(model_path + '/model.h5')
    w2v_model = Word2Vec.load(model_path + '/model.w2v')

    with open(model_path + '/tokenizer.pkl','rb') as handle:
        tokenizer = pickle.load(handle)

    with open(model_path + '/encoder.pkl','rb') as handle:
        encoder = pickle.load(handle)

# Handle requests to the service
def run(data):
    try:
        # Pick out the text property of the JSON request.
        # This expects a request in the form of {"text": "some text to score for sentiment"}
        data = json.loads(data)
        prediction = predict(data['text'])
        #Return prediction
        return prediction
    except Exception as e:
        error = str(e)
        return error

# Determine sentiment from score
def decode_sentiment(score, include_neutral=True):
    if include_neutral:
        label = NEUTRAL
        if score <= SENTIMENT_THRESHOLDS[0]:
            label = NEGATIVE
        elif score >= SENTIMENT_THRESHOLDS[1]:
            label = POSITIVE
        return label
    else:
        return NEGATIVE if score < 0.5 else POSITIVE

# Predict sentiment using the model
def predict(text, include_neutral=True):
    start_at = time.time()
    # Tokenize text
    x_test = pad_sequences(tokenizer.texts_to_sequences([text]), maxlen=SEQUENCE_LENGTH)
    # Predict
    score = model.predict([x_test])[0]
    # Decode sentiment
    label = decode_sentiment(score, include_neutral=include_neutral)

    return {"label": label, "score": float(score),
       "elapsed_time": time.time()-start_at}  
```

Para obter mais informações sobre scripts de entrada, consulte [implementar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Definir implantação

O pacote [Webservice](/python/api/azureml-core/azureml.core.webservice?preserve-view=true&view=azure-ml-py) contém as classes utilizadas para a implementação. A classe que utiliza determina onde o modelo é implantado. Por exemplo, para implementar como um serviço web no Serviço Azure Kubernetes, utilize [AksWebService.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) para criar a configuração de implementação.

O seguinte código Python define uma configuração de implantação para uma implantação local. Esta configuração implementa o modelo como um serviço web para o seu computador local.

> [!IMPORTANT]
> Uma implantação local requer uma instalação de trabalho do [Docker](https://www.docker.com/) no seu computador local:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Para mais informações, consulte a referência [LocalWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.localwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-port-none-)

O CLI carrega a configuração de implantação a partir de um ficheiro YAML:

```YAML
{
    "computeType": "LOCAL"
}
```

Implementar para um alvo de computação diferente, como o Serviço Azure Kubernetes na nuvem Azure, é tão fácil como mudar a configuração de implementação. Para mais informações, consulte [como e onde implementar modelos.](how-to-deploy-and-where.md)

## <a name="deploy-the-model"></a>Implementar o modelo

O exemplo a seguir carrega informações sobre o modelo registado denominado `sentiment` , e depois implanta-as como um serviço denominado `sentiment` . Durante a implementação, a configuração e configuração de inferência são usadas para criar e configurar o ambiente de serviço:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Para obter mais informações, consulte a referência [Modelo.deploy().](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)

Para colocar o modelo a partir do CLI, utilize o seguinte comando. Este comando implementa a versão 1 do modelo registado , `sentiment:1` utilizando a configuração de inferência e implantação armazenada nos `inferenceConfig.json` e `deploymentConfig.json` ficheiros:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Para obter mais informações, consulte a referência de implantação do [modelo az ml.](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy)

Para obter mais informações sobre a implementação, consulte [como e onde implementar modelos.](how-to-deploy-and-where.md)

## <a name="request-response-consumption"></a>Consumo de resposta a pedidos

Após a colocação, é apresentado o URI de pontuação. Este URI pode ser utilizado pelos clientes para submeter pedidos ao serviço. O exemplo a seguir é um cliente python simples que envia dados ao serviço e exibe a resposta:

```python
import requests
import json

scoring_uri = 'scoring uri for your service'
headers = {'Content-Type':'application/json'}

test_data = json.dumps({'text': 'Today is a great day!'})

response = requests.post(scoring_uri, data=test_data, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Para obter mais informações sobre como consumir o serviço implantado, consulte [Criar um cliente.](how-to-consume-web-service.md)

## <a name="next-steps"></a>Passos seguintes

* [Monitorize os seus modelos de machine learning Azure com Insights de Aplicações](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)
* [Como criar um cliente para um modelo implantado](how-to-consume-web-service.md)