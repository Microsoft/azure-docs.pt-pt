---
title: Modelos de rastreio e implantação de experiências
titleSuffix: Azure Data Science Virtual Machine
description: Saiba como rastrear e registar experiências a partir da Máquina Virtual de Ciência de Dados com Azure Machine Learning e/ou MLFlow.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 7726fce7ac69ebfaabf44049abc49c53f8175718
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704545"
---
# <a name="track-experiments-and-deploy-models-in-azure-machine-learning"></a>Acompanhe experiências e implemente modelos em Azure Machine Learning

Melhore o processo de criação de modelos rastreando as suas experiências e monitorizando as métricas de execução. Neste artigo, aprenda a adicionar código de registo ao seu script de treino utilizando a API [MLflow](https://mlflow.org/) e acompanhe a experiência em Azure Machine Learning.

O diagrama seguinte ilustra que, com o MLflow Tracking, rastreia as métricas de execução de uma experiência e armazena artefactos de modelo no seu espaço de trabalho Azure Machine Learning.

![rastrear experiências](./media/how-to-track-experiments/mlflow-diagram-track.png)

## <a name="prerequisites"></a>Pré-requisitos

* Você precisará de providenciar um espaço de trabalho de [aprendizagem de máquinas Azure](../how-to-manage-workspace.md#create-a-workspace)

## <a name="create-a-new-notebook"></a>Criar um novo Notebook

O Azure Machine Learning e o MLFlow SDK estão pré-instalados no VM da Ciência dos Dados e podem ser acedidos no ambiente **azureml_py36_ \** _ conda. No JupyterLab, clique no lançador e selecione o seguinte núcleo:

![seleção de kernel](./media/how-to-track-experiments/experiment-tracking-1.png)

## <a name="set-up-the-workspace"></a>Configurar o espaço de trabalho

Vá ao [portal Azure](https://portal.azure.com) e selecione o espaço de trabalho que aprovisionou como parte dos pré-requisitos. Você verá __Download config.jsem__ (ver abaixo) - descarregue o config e certifique-se de que está armazenado no seu diretório de trabalho no DSVM.

![Obtenha o ficheiro config](./media/how-to-track-experiments/experiment-tracking-2.png)

O config contém informações como o nome do espaço de trabalho, subscrição, etc. e significa que você não precisa de codificar estes parâmetros.

## <a name="track-dsvm-runs"></a>Pista DSVM corre

Adicione o seguinte código ao seu portátil (ou script) para definir o objeto de espaço de trabalho AzureML.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
O URI de rastreio é válido até uma hora ou menos. Se reiniciar o seu script após algum tempo inativo, use a API get_mlflow_tracking_uri para obter um novo URI.

### <a name="load-the-data"></a>Carregar os dados

Este exemplo usa o conjunto de dados da diabetes, um conjunto de pequenos dados bem conhecido que vem com a aprendizagem de scikit. Esta célula carrega o conjunto de dados e divide-o em conjuntos de treino e teste aleatórios.

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
from sklearn.externals import joblib

X, y = load_diabetes(return_X_y = True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"X": X_train, "y": y_train},
    "test":{"X": X_test, "y": y_test}
}

print ("Data contains", len(data['train']['X']), "training samples and",len(data['test']['X']), "test samples")
```

### <a name="add-tracking"></a>Adicionar rastreio

Adicione o rastreio de experiências usando o Azure Machine Learning SDK e carreque um modelo persistido no registo de execução da experiência. O código seguinte adiciona registos e envia um ficheiro modelo para a execução da experiência. O modelo também está registado no registo do modelo Azure Machine Learning.

```python
# Get an experiment object from Azure Machine Learning
from azureml.mlflow import register_model
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    # Log the algorithm parameter alpha to the run
    mlflow.log_param('alpha', 0.03)

    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])

    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    mlflow.log_metric('mse', mean_squared_error(data['test']['y'], preds))

    # Save the model
    model_file_name = 'model.pkl'
    joblib.dump(value = regression_model, filename = model_file_name)

    # upload the model file explicitly into artifacts
    mlflow.log_artifact(model_file_name)
    # register the model
    register_model(mlflow.active_run(), 'diabetes_model', 'model.pkl', model_framework="ScikitLearn")
```

### <a name="view-runs-in-azure-machine-learning"></a>Vista corre em Azure Machine Learning

Você pode ver a experiência executada no [Azure Machine Learning Studio](https://ml.azure.com). Clique em __Experiências__ no menu à esquerda e selecione o 'experiment_with_mlflow' (ou se decidiu nomear a sua experiência de forma diferente no corte acima, clique no nome utilizado):

![selecionar experiência](./media/how-to-track-experiments/mlflow-experiments.png)

Deve ver o erro quadrado médio registado (MSE):

![MSE](./media/how-to-track-experiments/mlflow-experiments-2.png)

Se clicar na execução, verá outros detalhes e também o modelo em conserva nos __registos Outputs+__

## <a name="deploy-model-in-azure-machine-learning"></a>Implementar modelo em Azure Machine Learning

Nesta secção, delineamos como implementar modelos treinados num DSVM para Azure Machine Learning.

### <a name="step-1-create-inference-compute"></a>Passo 1: Criar Inferência Compute

No menu à esquerda no [Estúdio AzureML](https://ml.azure.com) clique no __Computação__ e, em seguida, no __separador clusters inferência.__ Em seguida, clique em __+ Novo__ como discutido abaixo:

![Criar Inferência Compute](./media/how-to-track-experiments/mlflow-experiments-6.png)

No painel de agrupamento de __inferência Nova,__ os detalhes preenchem os detalhes para:

_ Nome do cálculo
* Kubernetes Service - selecione criar novos
* Selecione a região
* Selecione o tamanho VM (para efeitos deste tutorial, o padrão de Standard_D3_v2 é suficiente)
* Cluster Purpose - selecione __Dev-test__
* Número de nós deve ser igual __a 1__
* Configuração de rede - Básico

Em seguida, clique em __Criar__.

![detalhes do cálculo](./media/how-to-track-experiments/mlflow-experiments-7.png)

### <a name="step-2-deploy-no-code-inference-service"></a>Passo 2: Implementar serviço de inferência sem código

Quando registámos o modelo no nosso código `register_model` utilizando, especificámos a estrutura como sklearn. A Azure Machine Learning não suporta implementações de código para os seguintes quadros:

* scikit-learn
* Formato Tensorflow SaveModel
* Formato de modelo ONNX

A implementação sem código significa que pode ser implantado diretamente a partir do artefacto do modelo sem precisar de especificar qualquer script de pontuação específico.

Para implementar o modelo de diabetes, vá ao menu à esquerda no [Azure Machine Learning Studio](https://ml.azure.com) e selecione __Modelos.__ Em seguida, clique no diabetes_model registado:

![Selecione modelo](./media/how-to-track-experiments/mlflow-experiments-3.png)

Em seguida, clique no botão __Implementar__ no painel de detalhes do modelo:

![Implementar](./media/how-to-track-experiments/mlflow-experiments-4.png)

Vamos implantar o modelo para o Cluster de Inferências (Serviço Azure Kubernetes) que criamos no passo 1. Preencha os detalhes abaixo, fornecendo um nome para o serviço, e o nome do cluster de computação AKS (criado no passo 1). Recomendamos ainda que aumente a capacidade de reserva do __CPU__ para 1 (de 0,1) e a __capacidade de reserva__ de memória para 1 (a partir de 0,5) - pode fazer este aumento clicando em __Advanced__ e preenchendo os detalhes. Em seguida, clique em __Implementar__.

![implementar detalhes](./media/how-to-track-experiments/mlflow-experiments-5.png)

### <a name="step-3-consume"></a>Passo 3: Consumir

Quando o modelo tiver sido implementado com sucesso, deverá ver o seguinte (para chegar a esta página clique em Endpoints a partir do menu da esquerda > clicar no nome do serviço implantado):

![Consumir modelo](./media/how-to-track-experiments/mlflow-experiments-8.png)

Deve ver que o estado de implantação passa da __transição__ __para__ saudável. Além disso, esta secção de detalhes fornece o ponto final REST e URLs Swagger que um desenvolvedor de aplicações pode usar para integrar o seu modelo ML nas suas apps.

Pode testar o ponto final usando o [Carteiro,](https://www.postman.com/)ou pode utilizar o AzureML SDK:

```python
from azureml.core import Webservice
import json

# if you called your service differently then change the name below
service = Webservice(ws, name="diabetes-service")

input_payload = json.dumps({
    'data': X_test[0:2].tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

### <a name="step-4-clean-up"></a>Passo 4: Limpar

Elimine o Inference Compute que criou no Passo 1 para que não incorra em taxas de computação em curso. No menu à esquerda do Azure Machine Learning Studio, clique em Compute > Inference Clusters > Selecione o > computacional Delete.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [a implementação de modelos em AzureML](../how-to-deploy-and-where.md)
