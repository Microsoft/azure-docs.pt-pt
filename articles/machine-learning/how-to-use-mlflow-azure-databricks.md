---
title: MLflow Tracking para experiências ML de Azure Databricks
titleSuffix: Azure Machine Learning
description: Configurar mlflow com Azure Machine Learning para registar métricas e artefactos de experiências Ml de Azure Databricks.
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/22/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 776db820ffb1a216c3f6f7e9c6d6b8d90913a063
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97881468"
---
# <a name="track-azure-databricks-ml-experiments-with-mlflow-and-azure-machine-learning-preview"></a>Track Azure Databricks ML experimenta com MLflow e Azure Machine Learning (pré-visualização)

Neste artigo, aprenda a permitir que o URI de rastreio da MLflow e a API, coletivamente conhecida como [MLflow Tracking,](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)liguem as suas experiências Azure Databricks (ADB), MLflow e Azure Machine Learning.

[MLflow](https://www.mlflow.org) é uma biblioteca de código aberto para gerir o ciclo de vida das suas experiências de aprendizagem automática. MLFlow Tracking é um componente do MLflow que regista e rastreia as suas métricas de treino e artefactos de modelo. Saiba mais sobre [a Azure Databricks e mLflow](/azure/databricks/applications/mlflow/). 

Veja [track experiment runs e crie pontos finais com MLflow e Azure Machine Learning](how-to-use-mlflow.md) para integrações adicionais de funcionalidades de MLflow e Azure Machine Learning.

>[!NOTE]
> Como biblioteca de código aberto, o MLflow muda frequentemente. Como tal, a funcionalidade disponibilizada através da integração Azure Machine Learning e MLflow deve ser considerada como uma pré-visualização, e não totalmente suportada pela Microsoft.

> [!TIP]
> A informação neste documento é principalmente para cientistas de dados e desenvolvedores que querem monitorizar o processo de formação de modelos. Se você é um administrador interessado em monitorizar a utilização de recursos e eventos da Azure Machine Learning, tais como quotas, execuções de formação completas ou implementações de modelos concluídas, consulte [Monitoring Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="prerequisites"></a>Pré-requisitos

* Instale o pacote `azureml-mlflow`. 
    * Este pacote traz automaticamente `azureml-core` o [The Azure Machine Learning Python SDK,](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)que fornece a conectividade para o MLflow aceder ao seu espaço de trabalho.
* Um espaço de [trabalho e um cluster Azure Databricks.](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)
* Criar um espaço de trabalho de [aprendizagem de máquinas Azure.](how-to-manage-workspace.md)
    * Veja quais [as permissões de acesso necessárias para realizar as suas operações de MLflow com o seu espaço de trabalho.](how-to-assign-roles.md#mlflow-operations)

## <a name="track-azure-databricks-runs"></a>Track Azure Databricks corre

O MLflow Tracking com Azure Machine Learning permite-lhe armazenar as métricas e artefactos registados a partir dos seus Dados Azure e encontra-se em ambos os seus: 

* Espaço de trabalho Azure Databricks.
* Área de trabalho do Azure Machine Learning

Depois de criar o seu espaço de trabalho e cluster Azure Databricks, 

1. Instale a biblioteca *azureml-mlflow* a partir do PyPi, para garantir que o seu cluster tem acesso às funções e classes necessárias.

1. Prepara o teu caderno de experiências.

1. Ligue o seu espaço de trabalho Azure Databricks e o espaço de trabalho Azure Machine Learning.

Detalhes adicionais para estes passos estão nas seguintes secções para que possa executar com sucesso as suas experiências MLflow com Azure Databricks. 

## <a name="install-libraries"></a>Instalar bibliotecas

Para instalar bibliotecas no seu cluster, navegue no separador **Bibliotecas** e selecione **Instalar Novo**

 ![fluxo de mlflow com databricks azul](./media/how-to-use-mlflow-azure-databricks/azure-databricks-cluster-libraries.png)

No campo **Embalagem,** escreva azureml-mlflow e, em seguida, selecione a instalação. Repita este passo se necessário para instalar outros pacotes adicionais no seu cluster para a sua experiência.

 ![Azure DB instalar biblioteca mlflow](./media/how-to-use-mlflow-azure-databricks/install-libraries.png)

## <a name="set-up-your-notebook"></a>Configurar o seu caderno 

Uma vez que o seu cluster ADB é configurado, 
1. Selecione **espaços de trabalho** no painel de navegação à esquerda. 
1. Expandir o menu de espaços de trabalho e selecionar **Import**
1. Arraste e deixe cair, ou navegue para encontrar, o seu caderno de experiências para importar o seu espaço de trabalho ADB.
1. Selecione **Import** (Importar). O seu caderno de experiências abre automaticamente.
1. Sob o título do portátil na parte superior esquerda, selecione o cluster que quer anexar ao seu caderno de experiências. 

## <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Ligue os seus databricks Azure e espaços de aprendizagem de máquinas Azure

Ligar o seu espaço de trabalho ADB ao seu espaço de trabalho Azure Machine Learning permite-lhe rastrear os seus dados de experiência no espaço de trabalho Azure Machine Learning.

Para ligar o seu espaço de trabalho ADB a um novo ou existente espaço de trabalho de aprendizagem automática Azure, 
1. Inscreva-se no [portal Azure](https://ms.portal.azure.com).
1. Navegue para a página **geral** do seu espaço de trabalho ADB.
1. Selecione o botão **de espaço de trabalho Link Azure Machine Learning** no canto inferior direito. 

 ![Link Azure DB e Azure Machine Learning espaços de trabalho](./media/how-to-use-mlflow-azure-databricks/link-workspaces.png)

## <a name="mlflow-tracking-in-your-workspaces"></a>MLflow Tracking nos seus espaços de trabalho

Depois de instantaneamente o seu espaço de trabalho, o MLflow Tracking é automaticamente programado para ser rastreado em todos os seguintes locais:

* O espaço de trabalho de aprendizagem da máquina Azure ligado.
* Seu espaço de trabalho original da ADB. 

Todas as suas experiências aterram no serviço de rastreio de machine learning Azure.

O seguinte código deve estar no seu caderno de experiências para obter o seu espaço de trabalho de aprendizagem de máquinas Azure. 

Este código, 

*  Obtém os detalhes da sua subscrição Azure para instantaneaizar o seu espaço de trabalho Azure Machine Learning. 

* Assume que tem um grupo de recursos existente e espaço de trabalho Azure Machine Learning, caso contrário poderá [criá-los.](how-to-manage-workspace.md) 

* Define o nome da experiência. O aqui é consistente com o associado com o espaço de `user_name` `user_name` trabalho Azure Databricks.

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)

#Set MLflow experiment. 
experimentName = "/Users/{user_name}/{experiment_folder}/{experiment_name}" 
mlflow.set_experiment(experimentName) 

```

### <a name="set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace"></a>Desconfiem do MLflow Tracking apenas para acompanhar o seu espaço de trabalho de aprendizagem de máquinas Azure

Se preferir gerir as suas experiências rastreadas num local centralizado, pode definir o rastreio de MLflow **apenas** para rastrear apenas o seu espaço de trabalho Azure Machine Learning. 

Inclua o seguinte código no seu script:

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

No seu script de treino, importe `mlflow` usar as APIs de registo de MLflow e comece a registar as suas métricas de execução. O exemplo seguinte, regista a métrica da perda de época. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="register-models-with-mlflow"></a>Registar modelos com MLflow

Depois de o seu modelo ser treinado, pode registar os seus modelos no servidor de rastreio de backend com o `mlflow.<model_flavor>.log_model()` método. `<model_flavor>`, refere-se à estrutura associada ao modelo. [Saiba quais os sabores do modelo suportados.](https://mlflow.org/docs/latest/models.html#model-api) 

O servidor de rastreio de backend é o espaço de trabalho Azure Databricks por padrão; a menos que tenha optado por [definir o MLflow Tracking apenas para rastrear apenas o seu espaço de trabalho de aprendizagem de máquinas Azure](#set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace), então o servidor de rastreio de backend é o espaço de trabalho Azure Machine Learning.   

* **Se um modelo registado com o nome não existir,** o método regista um novo modelo, cria a versão 1 e devolve um objeto ModelVersion MLflow. 

* **Se já existir um modelo registado com o nome,** o método cria uma nova versão modelo e devolve o objeto da versão. 

```python
mlflow.spark.log_model(model, artifact_path = "model", 
                       registered_model_name = 'model_name')  

mlflow.sklearn.log_model(model, artifact_path = "model", 
                         registered_model_name = 'model_name') 
```

## <a name="create-endpoints-for-mlflow-models"></a>Criar pontos finais para modelos MLflow

Quando estiver pronto para criar um ponto final para os seus modelos ML. Pode desdobrar-se como, 

* Um serviço de aprendizagem automática Azure Request-Response para pontuação interativa. Esta implementação permite-lhe alavancar e aplicar a gestão do modelo Azure Machine Learning e as capacidades de deteção de deriva de dados nos seus modelos de produção. 

* Objetos de modelo MLFlow, que podem ser usados em gasodutos de streaming ou lote como funções Python ou Pandas UDFs em espaço de trabalho Azure Databricks.

### <a name="deploy-models-to-azure-machine-learning-endpoints"></a>Implementar modelos para pontos finais de aprendizagem de máquinas Azure 
Pode aproveitar o [mlflow.azureml.deploy](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) API para implementar um modelo no seu espaço de trabalho Azure Machine Learning. Se registou apenas o modelo no espaço de trabalho Azure Databricks, conforme descrito nos modelos de registo com secção [MLflow,](#register-models-with-mlflow) especifique o `model_name` parâmetro para registar o modelo no espaço de trabalho Azure Machine Learning. 

As execuções Azure Databricks podem ser implantadas nos seguintes pontos finais, 
* [Caso do contentor de Azure](how-to-deploy-models-with-mlflow.md#deploy-to-aci)
* [Azure Kubernetes Service](how-to-deploy-models-with-mlflow.md#deploy-to-aks)

### <a name="deploy-models-to-adb-endpoints-for-batch-scoring"></a>Implementar modelos para pontos finais ADB para pontuação de lotes 

Pode escolher os clusters Azure Databricks para a pontuação do lote. O modelo MLFlow é carregado e usado como um Spark Pandas UDF para obter novos dados. 

```python
from pyspark.sql.types import ArrayType, FloatType 

model_uri = "runs:/"+last_run_id+ {model_path} 

#Create a Spark UDF for the MLFlow model 

pyfunc_udf = mlflow.pyfunc.spark_udf(spark, model_uri) 

#Load Scoring Data into Spark Dataframe 

scoreDf = spark.table({table_name}).where({required_conditions}) 


#Make Prediction 

preds = (scoreDf 

           .withColumn('target_column_name', pyfunc_udf('Input_column1', 'Input_column2', ' Input_column3', …)) 

        ) 

display(preds) 
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se não planeia utilizar as métricas e artefactos registados no seu espaço de trabalho, a capacidade de eliminá-las individualmente não está disponível. Em vez disso, elimine o grupo de recursos que contém a conta de armazenamento e o espaço de trabalho, para que não incorre em quaisquer encargos:

1. No portal do Azure, selecione **Grupos de recursos** na extremidade esquerda.

   ![Excluir no portal Azure](./media/how-to-use-mlflow-azure-databricks/delete-resources.png)

1. Na lista, selecione o grupo de recursos que criou.

1. Selecione **Eliminar grupo de recursos**.

1. Introduza o nome do grupo de recursos. Em seguida, selecione **Eliminar**.

## <a name="example-notebooks"></a>Blocos de notas de exemplo

O [MLflow com os cadernos Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) demonstram e expandem-se sobre conceitos apresentados neste artigo.

## <a name="next-steps"></a>Passos seguintes

* [Gerencie os seus modelos.](concept-model-management-and-deployment.md)
* [Track experiment runs and create endpoints with MLflow and Azure Machine Learning](how-to-use-mlflow.md). 
* Saiba mais sobre [a Azure Databricks e mLflow](/azure/databricks/applications/mlflow/).