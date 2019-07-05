---
title: Como utilizar um modelo existente
titleSuffix: Azure Machine Learning service
description: Saiba como pode utilizar o serviço Azure Machine Learning com modelos que foram treinados fora o serviço. Pode registar modelos criados fora do serviço Azure Machine Learning e, em seguida, implementá-las como um serviço web ou o módulo do Azure IoT Edge.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/19/2019
ms.openlocfilehash: 332129c9847c317369d5631c3af584da9430e9dd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453687"
---
# <a name="how-to-use-an-existing-model-with-azure-machine-learning-service"></a>Como utilizar um modelo existente com o serviço Azure Machine Learning

Saiba como utilizar uma máquina existente, o modelo com o serviço Azure Machine Learning.

Se tiver um modelo de machine learning que foi treinado fora o serviço Azure Machine Learning, pode usar o serviço para implementar o modelo como um serviço web ou um dispositivo IoT Edge. 

> [!TIP]
> Este artigo fornece informações básicas sobre como registar e implementar um modelo existente. Depois de implementada, o serviço Azure Machine Learning fornece monitorização para o seu modelo. Ele também permite-lhe armazenar dados de entrada enviados para a implementação, o que pode ser utilizada para dados descompassos análise ou treinamento novas versões do modelo.
>
> Para obter mais informações sobre os conceitos e termos utilizados aqui, consulte [gerir, implementar e monitorizar os modelos de machine learning](concept-model-management-and-deployment.md).
>
> Para obter informações gerais sobre o processo de implementação, consulte [implementar modelos com o serviço Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho de serviço do Azure Machine Learning. Para obter mais informações, consulte [criar uma área de trabalho](setup-create-workspace.md).

    > [!TIP]
    > Os exemplos de Python neste artigo partem do princípio de que o `ws` variável é definida para a área de trabalho do serviço do Azure Machine Learning.
    >
    > Os exemplos CLI utilizam um marcador de posição de `myworkspace` e `myresourcegroup`. Substitua-os com o nome da sua área de trabalho e o grupo de recursos que o contém.

* O Azure Machine Learning SDK. Para obter mais informações, consulte a secção Python SDK [criar uma área de trabalho](setup-create-workspace.md#sdk).

* O [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e [extensão da CLI do Machine Learning](reference-azure-machine-learning-cli.md).

* Um modelo preparado. O modelo de persistência para um ou mais ficheiros no seu ambiente de desenvolvimento.

    > [!NOTE]
    > Para demonstrar a registar um modelo treinado fora de serviço do Azure Machine Learning, os fragmentos de código de exemplo neste artigo utilizam os modelos criados pelo projeto de análise de sentimentos de Twitter de Paulo Ripamonti: [ https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis ](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis).

## <a name="register-the-models"></a>Registre-se o modelo (s)

Registar um modelo permite-lhe armazenar, versão e controlar metadados sobre os modelos na sua área de trabalho. Nos exemplos seguintes de Python e CLI, o `models` diretório contém a `model.h5`, `model.w2v`, `encoder.pkl`, e `tokenizer.pkl` ficheiros. Este exemplo carrega os arquivos contidos no `models` diretório como um novo registo de modelo com o nome `sentiment`:

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning service",
                       workspace = ws)
```

Para obter mais informações, consulte a [Model.register()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none-) referência.

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

Para obter mais informações, consulte a [Registre-se modelo de ml de az](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register) referência.


Para obter mais informações sobre o registo do modelo em geral, consulte [gerir, implementar e monitorizar os modelos de machine learning](concept-model-management-and-deployment.md).


## <a name="define-inference-configuration"></a>Definir a configuração de inferência de tipos

A configuração de inferência de tipos define o ambiente utilizado para executar o modelo implementado. A configuração de inferência de tipos referencia os seguintes ficheiros que são utilizados para executar o modelo quando for implementada:

* O tempo de execução. O único valor válido para o tempo de execução é atualmente o Python.
* Um script de entrada. Este ficheiro (com o nome `score.py`) carrega o modelo quando inicia o serviço implementado. Também é responsável por receber dados, passando-o para o modelo e, em seguida, retornar uma resposta.
* Um ficheiro de ambiente de conda. Esse arquivo define os pacotes Python necessários para executar o script de modelo e de entrada. 

O exemplo seguinte mostra uma configuração de inferência de tipos básicos com o SDK de Python:

```python
from azureml.core.model import InferenceConfig

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml")
```

Para obter mais informações, consulte a [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) referência.

A CLI carrega a configuração de inferência de tipos a partir de um ficheiro YAML:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Para obter mais informações sobre a configuração de inferência de tipos, consulte [implementar modelos com o serviço Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="entry-script"></a>Script de entrada

O script de entrada tem apenas duas funções necessárias, `init()` e `run(data)`. Estas funções são usadas para inicializar o serviço na inicialização e executar o modelo de dados de pedido passados por um cliente a utilizar. O restante do script manipula o carregamento e a execução do modelo (s).

> [!IMPORTANT]
> Lá, não é um script de entrada genérico que funciona para todos os modelos. É sempre específico para o modelo que é utilizado. Tem de compreender como carregar o modelo, o formato de dados que o modelo de espera e como classificar dados com o modelo.

O código de Python seguinte é um script de entrada de exemplo (`score.py`):

```python
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec
from azureml.core.model import Model

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

    # Get the path where the model(s) registered as the name 'sentiment' can be found.
    model_path = Model.get_model_path('sentiment')
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

Para obter mais informações sobre scripts de entrada, consulte [implementar modelos com o serviço Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="conda-environment"></a>Ambiente de Conda

O YAML seguinte descreve o ambiente de conda necessário para executar o script de modelo e entrada:

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
```

Para obter mais informações, consulte [implementar modelos com o serviço Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Definir implementação

O [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) pacote contém as classes usadas para a implementação. A classe que usar determina em que o modelo é implementado. Por exemplo, para implementar como um serviço web no serviço Kubernetes do Azure, utilize [AksWebService.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none-) para criar a configuração de implementação.

O código de Python seguinte define uma configuração de implementação para uma implantação local. Esta configuração implementa o modelo como um serviço web para o computador local.

> [!IMPORTANT]
> Uma implementação local necessita de uma instalação do [Docker](https://www.docker.com/) no seu computador local:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Para obter mais informações, consulte a [LocalWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-) referência.

A CLI carrega a configuração de implementação a partir de um ficheiro YAML:

```YAML
{
    "computeType": "LOCAL"
}
```

A implantação para um destino de computação diferentes, como o serviço de Kubernetes do Azure na cloud do Azure, é tão fácil quanto a alteração da configuração de implementação. Para obter mais informações, consulte [como e onde implementar modelos](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>Implementar o modelo

O exemplo seguinte carrega informações sobre o modelo registado com o nome `sentiment`e, em seguida, implementa-o como um serviço com o nome `sentiment`. Durante a implementação, a configuração de inferência de tipos e a configuração de implementação são utilizados para criar e configurar o ambiente de serviço:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Para obter mais informações, consulte a [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-) referência.

Para implementar o modelo a partir da CLI, utilize o seguinte comando. Este comando implementa versão 1 do modelo registado (`sentiment:1`) a utilizar a configuração de inferência de tipos e a implantação armazenada no `inferenceConfig.json` e `deploymentConfig.json` ficheiros:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Para obter mais informações, consulte a [implementação de modelo de ml az](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) referência.

Para obter mais informações sobre a implementação, consulte [como e onde implementar modelos](how-to-deploy-and-where.md).

## <a name="request-response-consumption"></a>Consumo de solicitação-resposta

Após a implementação, é apresentado o URI de classificação. Este URI pode ser utilizado pelos clientes para submeter pedidos para o serviço. O exemplo seguinte é um cliente de Python básico que envia dados para o serviço e apresenta a resposta:

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

Para obter mais informações sobre como consumir o serviço implementado, consulte [criar um cliente](how-to-consume-web-service.md).

## <a name="next-steps"></a>Passos Seguintes

* [Monitorizar os seus modelos do Azure Machine Learning com o Application Insights](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)
* [Como e onde implementar modelos](how-to-deploy-and-where.md)
* [Como criar um cliente de um modelo implementado](how-to-consume-web-service.md)