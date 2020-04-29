---
title: Utilizar e implementar modelos existentes
titleSuffix: Azure Machine Learning
description: Saiba como pode utilizar o Azure Machine Learning com modelos que foram treinados fora do serviço. Pode registar modelos criados fora do Azure Machine Learning e, em seguida, implantá-los como um serviço web ou módulo Azure IoT Edge.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 03/17/2020
ms.openlocfilehash: 924bd2fdba2359e6f1108c39802ad3ce95ebdf07
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79472380"
---
# <a name="use-an-existing-model-with-azure-machine-learning"></a>Use um modelo existente com Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a utilizar um modelo de aprendizagem automática existente com o Azure Machine Learning.

Se tiver um modelo de machine learning que foi treinado fora do Azure Machine Learning, ainda pode utilizar o serviço para implementar o modelo como um serviço web ou para um dispositivo IoT Edge. 

> [!TIP]
> Este artigo fornece informações básicas sobre o registo e implementação de um modelo existente. Uma vez implantado, o Azure Machine Learning fornece monitorização para o seu modelo. Permite também armazenar dados de entrada enviados para a implementação, que podem ser utilizados para análise de deriva de dados ou para treinar novas versões do modelo.
>
> Para obter mais informações sobre os conceitos e termos aqui utilizados, consulte [Gerir, implementar e monitorizar modelos](concept-model-management-and-deployment.md)de machine learning.
>
> Para obter informações gerais sobre o processo de implantação, consulte [modelos de implantação com aprendizagem automática Azure](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Azure Machine Learning. Para mais informações, consulte [Criar um espaço de trabalho.](how-to-manage-workspace.md)

    > [!TIP]
    > Os exemplos python neste artigo `ws` assumem que a variável está definida para o seu espaço de trabalho Azure Machine Learning.
    >
    > Os exemplos cli usam `myworkspace` um `myresourcegroup`espaço reservado de e . Substitua-os pelo nome do seu espaço de trabalho e pelo grupo de recursos que o contém.

* O [SDK de Aprendizagem automática Azure.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)  

* A extensão [CLI azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e [machine learning CLI](reference-azure-machine-learning-cli.md).

* Um modelo treinado. O modelo deve ser perinedido a um ou mais ficheiros sobre o seu ambiente de desenvolvimento.

    > [!NOTE]
    > Para demonstrar o registo de um modelo treinado fora do Azure Machine Learning, o exemplo de código snippets neste artigo usa os modelos criados pelo projeto de análise de sentimentos de Paolo Ripamonti no Twitter: [https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis).

## <a name="register-the-models"></a>Registar os modelos(s)

Registar um modelo permite-lhe armazenar, versão e rastrear metadados sobre modelos no seu espaço de trabalho. Nos exemplos seguintes da Python `models` e do `model.h5` `model.w2v`CLI, o diretório contém os ficheiros `encoder.pkl`e `tokenizer.pkl` ficheiros. Este exemplo envia os ficheiros `models` contidos no diretório `sentiment`como um novo registo de modelos chamado:

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

Para mais informações, consulte a referência [Model.register()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model(class)?view=azure-ml-py#register-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-)

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> Também pode definir `tags` `properties` adicionar e dicionários objetos para o modelo registado. Estes valores podem ser usados mais tarde para ajudar a identificar um modelo específico. Por exemplo, a estrutura utilizada, os parâmetros de treino, etc.

Para mais informações, consulte a referência do registo do [modelo Az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register)


Para obter mais informações sobre o registo do modelo em geral, consulte [Gerir, implementar e monitorizar modelos](concept-model-management-and-deployment.md)de machine learning.

## <a name="define-inference-configuration"></a>Definir configuração de inferência

A configuração de inferência define o ambiente utilizado para executar o modelo implantado. A configuração da inferência refere as seguintes entidades, que são usadas para executar o modelo quando é implantado:

* Um script de entrada. Este ficheiro `score.py`(nomeado) carrega o modelo quando o serviço implantado começa. É também responsável por receber dados, passá-lo para o modelo e, em seguida, devolver uma resposta.
* Um [ambiente](how-to-use-environments.md)de aprendizagem automática azure. Um ambiente define as dependências de software necessárias para executar o modelo e o script de entrada.

O exemplo que se segue mostra como usar o SDK para criar um ambiente e depois usá-lo com uma configuração de inferência:

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

Para obter mais informações, veja os artigos seguintes:

+ [Como utilizar ambientes.](how-to-use-environments.md)
+ [Referência InferenceConfig.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)


O CLI carrega a configuração de inferência de um ficheiro YAML:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Com o CLI, o ambiente de `myenv.yml` conda é definido no ficheiro referenciado pela configuração de inferência. O seguinte YAML é o conteúdo deste ficheiro:

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

Para obter mais informações sobre a configuração de inferência, consulte [Modelos de implantação com aprendizagem automática Azure](how-to-deploy-and-where.md).

### <a name="entry-script"></a>Script de entrada

O script de entrada tem apenas duas funções necessárias, `init()` e `run(data)`. Estas funções são usadas para inicializar o serviço no arranque e executar o modelo utilizando dados de pedido transmitidos por um cliente. O resto do script trata de carregar e executar o ou os modelos.

> [!IMPORTANT]
> Não há um guião genérico que funcione para todos os modelos. É sempre específico do modelo que é utilizado. Deve compreender como carregar o modelo, o formato de dados que o modelo espera e como marcar dados usando o modelo.

O seguinte código Python é`score.py`um roteiro de entrada de exemplo ( ):

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

Para obter mais informações sobre scripts de entrada, consulte [Modelos de implantação com Aprendizagem automática Azure](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Definir a implantação

O pacote [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice?view=azure-ml-py) contém as classes utilizadas para a implantação. A classe que usa determina onde o modelo é implantado. Por exemplo, para implementar como um serviço web no Serviço Azure Kubernetes, utilize [aksWebService.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-) para criar a configuração de implementação.

O seguinte código Python define uma configuração de implementação para uma implantação local. Esta configuração implementa o modelo como um serviço web para o seu computador local.

> [!IMPORTANT]
> Uma implementação local requer uma instalação de trabalho do [Docker](https://www.docker.com/) no seu computador local:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Para mais informações, consulte a referência [LocalWebservice.deploy_configuration().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.localwebservice?view=azure-ml-py#deploy-configuration-port-none-)

O CLI carrega a configuração de implementação a partir de um ficheiro YAML:

```YAML
{
    "computeType": "LOCAL"
}
```

A implantação para um alvo de computação diferente, como o Serviço Azure Kubernetes na nuvem Azure, é tão fácil como mudar a configuração de implementação. Para mais informações, consulte [Como e onde implementar modelos.](how-to-deploy-and-where.md)

## <a name="deploy-the-model"></a>Implementar o modelo

O exemplo seguinte carrega informações `sentiment`sobre o modelo registado chamado `sentiment`, e depois implanta-o como um serviço chamado . Durante a implementação, a configuração de inferência e implementação são usadas para criar e configurar o ambiente de serviço:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Para mais informações, consulte a referência [Model.deploy()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)

Para utilizar o modelo a partir do CLI, utilize o seguinte comando. Este comando implementa a versão`sentiment:1`1 do modelo registado ( ) utilizando `inferenceConfig.json` `deploymentConfig.json` a configuração de inferência e implementação armazenada nos ficheiros e ficheiros:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Para mais informações, consulte a referência de implementação do [modelo Az ml.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)

Para obter mais informações sobre a implementação, consulte [Como e onde implementar modelos](how-to-deploy-and-where.md).

## <a name="request-response-consumption"></a>Consumo de pedido-resposta

Após a implantação, é apresentado o URI de pontuação. Este URI pode ser usado pelos clientes para submeter pedidos ao serviço. O exemplo seguinte é um cliente python básico que submete dados ao serviço e exibe a resposta:

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

* [Monitorize os seus modelos de Aprendizagem automática Azure com Insights de Aplicação](how-to-enable-app-insights.md)
* [Recolher dados para modelos em produção](how-to-enable-data-collection.md)
* [Como e onde implementar modelos](how-to-deploy-and-where.md)
* [Como criar um cliente para um modelo implantado](how-to-consume-web-service.md)
