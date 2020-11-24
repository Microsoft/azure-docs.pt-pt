---
title: Como executar e implementar localmente
titleSuffix: Azure Machine Learning
description: Aprenda a executar modelos treinados na sua máquina local.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to, deploy
ms.openlocfilehash: fcea7c162e5c978a7c8ff1b42e09ffe0afb98f3c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95550093"
---
# <a name="deploy-on-your-local-machines-models-trained-with-azure-machine-learning"></a>Implemente nos modelos de máquinas locais treinados com Azure Machine Learning

Este artigo ensina-o a usar o computador local como alvo para treinar ou implementar modelos criados em Azure Machine Learning. A flexibilidade da Azure Machine Learning permite-lhe trabalhar com a maioria dos quadros de aprendizagem de máquinas Python. As soluções de aprendizagem automática geralmente têm dependências complexas que podem ser difíceis de duplicar. Este artigo irá mostrar-lhe como trocar o controlo total versus facilidade de utilização.

Alguns cenários para implantação local incluem:

* Rapidamente iterando dados, scripts e modelos no início de um projeto
* Depuração e resolução de problemas em fases posteriores
* Implementação final em hardware gerido pelo utilizador

## <a name="prerequisites"></a>Pré-requisitos

- Uma área de trabalho do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho para aprendizagem de máquinas Azure](how-to-manage-workspace.md)
- Um modelo e um ambiente. Se não tiver um modelo treinado, pode utilizar o modelo e os ficheiros de dependência fornecidos [neste tutorial](tutorial-train-models-with-aml.md)
- O [kit de desenvolvimento de software Azure Machine Learning (SDK) para Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py)
- Um gestor de conda como Anaconda ou miniconda, se você quiser espelhar as dependências de pacotes da Azure Machine Learning
- Docker, se quiser usar uma versão contentorizada do ambiente de Aprendizagem automática do Azure

## <a name="prepare-your-local-machine"></a>Prepare a sua máquina local

A forma mais fiável de executar localmente um modelo de Aprendizagem automática Azure é com uma imagem de Docker. Uma imagem docker fornece uma experiência isolada e contentorizada que duplica, exceto para problemas de hardware, o ambiente de execução Azure. Para obter mais informações sobre a instalação e configuração do Docker para cenários de desenvolvimento, consulte [a visão geral do desenvolvimento remoto do Docker no Windows](/windows/dev-environment/docker/overview).

Embora seja possível anexar um depurador a um processo em execução em Docker (ver [Anexar a um recipiente em funcionamento),](https://code.visualstudio.com/docs/remote/attach-container)pode preferir depurar e iteraçãor do seu código Python sem envolver o Docker. Neste cenário, é importante que a sua máquina local utilize as mesmas bibliotecas que são usadas quando faz a sua experiência no Azure Machine Learning. Para gerir as dependências python, Azure usa [conda](https://docs.conda.io/). Embora possa recriar o ambiente utilizando outros gestores de pacotes, instalar e configurar a configuração na sua máquina local é a forma mais fácil de sincronizar. 

## <a name="prepare-your-entry-script"></a>Prepare o seu script de entrada

Mesmo que uses o Docker para gerir o modelo e as dependências, o script de pontuação Python deve ser local. O guião deve ter dois métodos:

- Um `init()` método que não aceita argumentos e não devolve nada 
- Um `run()` método que pega numa corda formatada por JSON e devolve um objeto json-serializável.

O argumento do `run()` método será da forma: 

```json
{
    "data": <model-specific-data-structure>
}
```

O objeto que regressar do `run()` método deve `toJSON() -> string` implementar.

O exemplo a seguir demonstra como carregar um modelo de aprendizagem de scikit registado e marcá-lo com dados numpiados. Este exemplo baseia-se no modelo e dependência [deste tutorial:](tutorial-train-models-with-aml.md)

```python
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It is the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION)
    # For multiple models, it points to the folder containing all deployed models (./azureml-models)
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

Para exemplos mais avançados, incluindo a geração automática de esquemas de Swagger e como obter dados binários (por exemplo, imagens), leia [a autoria do guião de entrada avançada.](how-to-deploy-advanced-entry-script.md) 

## <a name="deploy-as-a-local-web-service-using-docker"></a>Implementar como um serviço web local usando Docker

A forma mais fácil de replicar o ambiente usado pela Azure Machine Learning é implementar um serviço web usando Docker. Com o Docker a funcionar na tua máquina local, vais:

1. Ligue-se ao espaço de trabalho Azure Machine Learning no qual o seu modelo está registado
1. Criar um `Model` objeto que represente o modelo
1. Crie um `Environment` objeto que contenha as dependências e defina o ambiente de software em que o seu código será executado
1. Criar um `InferenceConfig` objeto que associe o script de entrada e o `Environment`
1. Criar um `DeploymentConfiguration` objeto da subclasse `LocalWebserviceDeploymentConfiguration`
1. Use `Model.deploy()` para criar um `Webservice` objeto. Este método descarrega a imagem do Docker e associa-a ao `Model` `InferenceConfig` , e `DeploymentConfiguration`
1. Ativar o `Webservice` com `Webservice.wait_for_deployment()`

O seguinte código mostra estes passos:

```python
from azureml.core.webservice import Webservice
from azure.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

deployment_config = LocalWebservice.deploy_configuration(port=6789)

local_service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-local', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config = deployment_config)

local_service.wait_for_deployment(show_output=True)
print(f"Scoring URI is : {local_service.scoring_uri}")
```

A ligação `Model.deploy()` pode demorar alguns minutos. Depois de ter sido implantado inicialmente, é mais eficiente usar o `update()` método em vez de começar do zero. Consulte [Update a um serviço web implantado](how-to-deploy-update-web-service.md).

### <a name="test-your-local-deployment"></a>Teste a sua implantação local

Quando executar o script de implementação anterior, irá desausar o URI ao qual pode postar dados para pontuação (por exemplo, `http://localhost:6789/score` ). A amostra que se segue mostra um script que marca dados de amostra com o `"sklearn-mnist-local"` modelo implementado localmente. O modelo, se devidamente treinado, infere que `normalized_pixel_values` deve ser interpretado como um "2". 

```python
import requests

normalized_pixel_values = "[\
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.7, 1.0, 1.0, 0.6, 0.4, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.9, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 0.8, 0.6, 0.7, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.2, 1.0, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.8, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.3, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.1, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.3, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.8, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.9, 0.2, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.9, 1.0, 0.9, 0.1, \
0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.6, \
0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.7, \
0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.8, 1.0, 1.0, \
1.0, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, \
1.0, 0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 1.0, \
1.0, 1.0, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, \
1.0, 1.0, 1.0, 0.2, 0.1, 0.1, 0.1, 0.1, 0.0, 0.0, 0.0, 0.1, 0.1, 0.1, 0.6, 0.6, 0.6, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.6, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.5, 0.5, 0.7, 1.0, 1.0, 1.0, 0.6, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]"

input_data = "{\"data\": [" + normalized_pixel_values + "]}"

headers = {'Content-Type': 'application/json'}

scoring_uri = "http://localhost:6789/score"
resp = requests.post(scoring_uri, input_data, headers=headers)

print("Should be predicted as '2'")
print("prediction:", resp.text)
```

## <a name="download-and-run-your-model-directly"></a>Faça o download e execute o seu modelo diretamente

Ao usar o Docker para implementar o seu modelo como um serviço web é a opção mais comum, também pode querer executar o seu código diretamente com scripts Python locais. Há dois elementos importantes que precisará: 

- O próprio modelo
- As dependências em que o modelo se baseia 

O download do modelo pode ser feito:  

- A partir do portal, escolhendo o separador **Modelos,** selecionando o modelo pretendido, e a partir da página **Detalhes,** escolhendo **Download**
- A partir da linha de comando, utilizando `az ml model download` (ver referência de descarregamento do [modelo)](/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext_azure_cli_ml_az_ml_model_download&preserve-view=false)
- Com o Python SDK, utilizando o `Model.download()` método (ver [referência API modelo)](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#download-target-dir------exist-ok-false--exists-ok-none-&preserve-view=false)

Um modelo Azure é um ou mais objetos Python serializados, embalados como um ficheiro picles Python (extensão **.pkl).** O conteúdo do ficheiro pickles depende da biblioteca ml ou técnica utilizada para treinar o modelo. Por exemplo, com o modelo do tutorial, pode carregar o modelo com:

```python
import pickle

with open('sklearn_mnist_model.pkl', 'rb') as f : 
    logistic_model = pickle.load(f, encoding='latin1')
```

As dependências são sempre difíceis de acertar, especialmente com machine learning, onde muitas vezes pode haver uma teia vertiginosa de requisitos específicos de versão. Pode recriar um ambiente de aprendizagem automática Azure na sua máquina local, quer como um ambiente conda completo, quer como uma imagem do Docker utilizando o `Environment` método da `build_local()` classe. 

```python
ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
myenv.build_local(workspace=ws, useDocker=False) #Creates conda env
```

Se definir `build_local()` o argumento `useDocker` `True` para, a função criará uma imagem de Docker em vez de um ambiente conda. Se quiser mais controlo, pode utilizar `Environment` o método do `save_to_directory()` método, que escreve **conda_dependencies.yml** e **azureml_environment.jsem** ficheiros de definição que pode afinar e usar como base para a extensão. 

A `Environment` classe tem uma série de outros métodos para sincronizar ambientes através do seu hardware compute, seu espaço de trabalho Azure e imagens Docker. Para mais informações, consulte a [ `Environment` referência API.](/python/api/azureml-core/azureml.core.environment(class))

Depois de ter descarregado o modelo e resolvido as suas dependências, não existem restrições definidas pelo Azure sobre como executa a pontuação, afina o modelo, usa a aprendizagem de transferências, etc. 

## <a name="upload-a-retrained-model-to-azure-machine-learning"></a>Faça o upload de um modelo retreinado para Azure Machine Learning

Se tiver um modelo treinado localmente ou retreinado, pode registá-lo com a Azure. Uma vez registado, pode então continuar a afinar o cálculo do Azure ou implantá-lo utilizando instalações da Azure [Kubernetes](how-to-deploy-azure-kubernetes-service.md) ou [DoRton Inference Server (Preview)](how-to-deploy-with-triton.md).

Para ser utilizado com o Python SDK da Azure Machine Learning, um modelo deve ser armazenado como um objeto Python serializado em formato picles (um ficheiro **pkl)** e deve implementar um `predict(data)` método que devolva um objeto json-serializável. Por exemplo, você pode armazenar um modelo de diabetes de aprendizagem de scikit-learn localmente com: 

```python
import joblib

from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge

dataset_x, dataset_y = load_diabetes(return_X_y=True)

sk_model = Ridge().fit(dataset_x, dataset_y)

joblib.dump(sk_model, "sklearn_regression_model.pkl")
```

Para disponibilizar o modelo em Azure, pode então utilizar o `register()` método da `Model` classe:

```python
from azureml.core.model import Model

model = Model.register(model_path="sklearn_regression_model.pkl",
                       model_name="sklearn_regression_model",
                       tags={'area': "diabetes", 'type': "regression"},
                       description="Ridge regression model to predict diabetes",
                       workspace=ws)
```

Pode então encontrar o seu modelo recém-registado no separador Modelo de **Aprendizagem** automática Azure:

:::image type="content" source="media/how-to-deploy-local/registered-model.png" alt-text="Screenshot do separador Azure Machine Learning Model, mostrando o modelo carregado.":::

Para obter mais informações sobre o upload e atualização de modelos e ambientes, consulte [o modelo de Registo e implemente localmente com utilizações avançadas.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local-advanced.ipynb)

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre a gestão de ambientes, consulte [Criar & utilizar ambientes de software em Azure Machine Learning](how-to-use-environments.md)
- Para saber mais sobre o acesso aos dados da sua loja de dados, consulte [o Connect para os serviços de armazenamento no Azure](how-to-access-data.md)