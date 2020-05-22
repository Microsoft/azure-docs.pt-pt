---
title: Rastreio de MLflow para experiências ml
titleSuffix: Azure Machine Learning
description: Configurar o MLflow com o Azure Machine Learning para registar métricas e artefactos de modelos ML criados em clusters de Databricks, no seu ambiente local ou no ambiente VM.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 95567a177635dc7d7ed03404487e62c76db8bdac
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779128"
---
# <a name="track-models-metrics-with-mlflow-and-azure-machine-learning-preview"></a>Track models métricas com MLflow e Azure Machine Learning (pré-visualização)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este artigo demonstra como permitir que o mlflow de rastreio uri e a exploração de API, coletivamente conhecido como [MLflow Tracking,](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)conecte as suas experiências de MLflow e o Azure Machine Learning. Ao fazê-lo, permite-lhe rastrear e registar métricas e artefactos de experiência sinuosamente no seu espaço de [trabalho Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces). Se já utilizar o MLflow Tracking para as suas experiências, o espaço de trabalho proporciona uma localização centralizada, segura e escalável para armazenar métricas e modelos de treino.

<!--
+ Deploy your MLflow experiments as an Azure Machine Learning web service. By deploying as a web service, you can apply the Azure Machine Learning monitoring and data drift detection functionalities to your production models. 
-->

[O MLflow](https://www.mlflow.org) é uma biblioteca de código aberto para gerir o ciclo de vida das suas experiências de aprendizagem automática. MlFlow Tracking é um componente do MLflow que regista e rastreia as métricas de treino e os artefactos de modelo, independentemente do ambiente da sua experiência- localmente no seu computador, num alvo de computação remota, numa máquina virtual ou num cluster de Databricks Azure. 

O diagrama seguinte ilustra que, com o MLflow Tracking, você rastreia as métricas de execução de uma experiência e armazena artefactos de modelo no seu espaço de trabalho Azure Machine Learning.

![mlflow com diagrama de aprendizagem de máquina azul](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> A informação neste documento é principalmente para cientistas de dados e desenvolvedores que querem monitorizar o processo de formação de modelos. Se for um administrador interessado em monitorizar a utilização de recursos e eventos do Azure Machine Learning, tais como quotas, execuções de formação concluídas ou implementações de modelos concluídas, consulte [Monitorização do Machine Learning Azure](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Comparar clientes da MLflow e da Azure Machine Learning

 A tabela abaixo resume os diferentes clientes que podem usar o Azure Machine Learning e as respetivas capacidades de função.

 O MLflow Tracking oferece funcionalidades de armazenamento de madeira métrica e de artefactos que só estão disponíveis de outra forma através do [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).


| | Rastreio de MLflow &nbsp; <!--& Deployment--> | Pitão de aprendizagem de máquina sdk azure |  ClI de aprendizagem automática azure | Azure Machine Learning studio|
|---|---|---|---|---|
| Gerir o espaço de trabalho |   | ✓ | ✓ | ✓ |
| Utilizar lojas de dados  |   | ✓ | ✓ | |
| Métricas de log      | ✓ | ✓ |   | |
| Carregar artefactos | ✓ | ✓ |   | |
| Ver métricas     | ✓ | ✓ | ✓ | ✓ |
| Gerir a computação   |   | ✓ | ✓ | ✓ |

<!--| Deploy models    | ✓ | ✓ | ✓ | ✓ |
|Monitor model performance||✓|  |   |
| Detect data drift |   | ✓ |   | ✓ |
-->
## <a name="prerequisites"></a>Pré-requisitos

* [Instale o MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Instale o SDK de Aprendizagem automática Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) no seu computador local O SDK fornece a conectividade para o MLflow aceder ao seu espaço de trabalho.
* Crie um espaço de trabalho de [aprendizagem automática Azure.](how-to-manage-workspace.md)

## <a name="track-local-runs"></a>Acompanhar as corridas locais

O Rastreio de MLflow com o Azure Machine Learning permite-lhe armazenar as métricas e artefactos registados das suas corridas locais para o seu espaço de trabalho azure machine learning.

Instale o `azureml-mlflow` pacote para utilizar o MLflow Tracking com o Azure Machine Learning nas suas experiências executadas localmente num Caderno Jupyter ou editor de código.

```shell
pip install azureml-mlflow
```

>[!NOTE]
>O espaço de nome sinuoso azureml.contrib muda frequentemente, uma vez que trabalhamos para melhorar o serviço. Como tal, qualquer coisa neste espaço de nome deve ser considerada como uma pré-visualização, e não totalmente suportada pela Microsoft.

Importe as `mlflow` e classes para aceder ao URI de rastreio da [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) MLflow e configure o seu espaço de trabalho.

No seguinte código, o método atribui um endereço URI único de rastreio ao espaço de `get_mlflow_tracking_uri()` `ws` trabalho, e `set_tracking_uri()` aponta o MLflow tracking URI para esse endereço.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>O URI de rastreio é válido até uma hora ou menos. Se reiniciar o seu script após algum tempo inativo, use a Get_mlflow_tracking_uri API para obter um novo URI.

Detete o nome da experiência MLflow `set_experiment()` e inicie o seu treino com `start_run()` . Em seguida, utilize para ativar a API de registo de registo de `log_metric()` MLflow e comece a registar as métricas de treino.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Rastrear corridas remotas

O Rastreio de MLflow com o Azure Machine Learning permite-lhe armazenar as métricas e artefactos registados do seu controlo remoto para o seu espaço de trabalho azure Machine Learning.

As corridas remotas permitem-lhe treinar os seus modelos em computaçãos mais poderosas, como gpu ativado por máquinas virtuais, ou clusters machine learning compute. Consulte os alvos de cálculo para o treino de modelos para aprender sobre [diferentes](how-to-set-up-training-targets.md) opções de computação.

Configure o seu ambiente de corrida de computação e treino com a [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) classe. Inclua `mlflow` e `azureml-mlflow` pip pacotes na secção do [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) ambiente. Em seguida, construa [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) com a sua computação remota como o alvo da computação.

```Python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-mlflow'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

No seu roteiro de treino, importe `mlflow` para usar as APIs de registo de MLflow e comece a registar as suas métricas de execução.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Com esta configuração de ensaio e formação, utilize o `Experiment.submit('train.py')` método para submeter uma execução. Este método configura automaticamente o MLflow tracking URI e direciona a exploração madeireira do MLflow para o seu Espaço de Trabalho.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Track Azure Databricks corre

O Rastreio de MLflow com o Azure Machine Learning permite-lhe armazenar as métricas e artefactos registados dos seus Tijolos de Dados Azure no seu espaço de trabalho Azure Machine Learning.

Para executar as suas experiências de Mlflow com os Bricks Azure, precisa primeiro de criar um espaço de [trabalho e cluster Azure Databricks.](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) No seu cluster, certifique-se de instalar a biblioteca *azureml-mlflow* da PyPi, para garantir que o seu cluster tem acesso às funções e classes necessárias.

A partir daqui, importe o seu caderno de experiências, prenda-o ao seu cluster Azure Databricks e execute a sua experiência. 

### <a name="install-libraries"></a>Instalar bibliotecas

Para instalar bibliotecas no seu cluster, navegue para o separador **Bibliotecas** e clique em **Instalar Novo**

 ![mlflow com diagrama de aprendizagem de máquina azul](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

No campo **Pacote,** digite o azureml-mlflow e, em seguida, clique em instalar. Repita este passo conforme necessário para instalar outros pacotes adicionais no seu cluster para a sua experiência.

 ![mlflow com diagrama de aprendizagem de máquina azul](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>Configurar o seu caderno e espaço de trabalho

Assim que o seu cluster estiver configurado, importe o seu caderno de experiências, abra-o e prenda-o ao seu cluster.

O seguinte código deve estar no seu caderno de experiências. Este código obtém os detalhes da sua subscrição Azure para instantaneamente o seu espaço de trabalho. Este código pressupõe que você tem um grupo de recursos existente e espaço de trabalho Azure Machine Learning, caso contrário pode [criá-los](how-to-manage-workspace.md). 

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace
from azureml.mlflow import get_portal_url

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)
```

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Conecte os seus tijolos de dados Azure e espaços de trabalho de aprendizagem automática Azure

No [portal Azure,](https://ms.portal.azure.com)pode ligar o seu espaço de trabalho Azure Databricks (ADB) a um novo ou já existente espaço de trabalho de Aprendizagem automática Azure. Para tal, navegue para o seu espaço de trabalho ADB e selecione o botão de espaço de **trabalho Link Azure Machine Learning** na parte inferior direita. A ligação dos seus espaços de trabalho permite-lhe rastrear os seus dados de experiência no espaço de trabalho azure Machine Learning. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>Link MLflow tracking para o seu espaço de trabalho

Depois de instantaneamente o seu espaço de trabalho, desloque o URI de rastreio de MLflow. Ao fazê-lo, liga o rastreio do MLflow ao espaço de trabalho de Aprendizagem automática Azure. Depois de ligar, todas as suas experiências aterrarão no serviço de rastreio de machine learning azure gerido.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>Coloque diretamente o Rastreio de MLflow no seu caderno

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

No seu roteiro de treino, importe o mlflow para utilizar as APIs de registo de MLflow e comece a registar as suas métricas de execução. O exemplo seguinte, regista a métrica de perda de época. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>Configurar automatizar mLflow Tracking

Em vez de configurar manualmente o URI de rastreio em todas as sessões subsequentes de cadernos de experimentação nos seus clusters, faça-o automaticamente utilizando este [script init](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md)de rastreio de aprendizagem automática Azure .

Quando configurado corretamente, é possível ver os seus dados de rastreio mLflow na API de Aprendizagem Automática Azure e em todos os clientes, e em Tijolos de Dados Azure através da interface de utilizador Do MLflow ou utilizando o cliente MLflow.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Veja métricas e artefactos no seu espaço de trabalho

As métricas e artefactos da exploração madeireira MLflow são mantidos no seu espaço de trabalho. Para vê-los a qualquer momento, navegue para o seu espaço de trabalho e encontre a experiência pelo nome no seu espaço de trabalho no [estúdio Azure Machine Learning.](https://ml.azure.com)  Ou executar o código abaixo. 

```python
run.get_metrics()
ws.get_details()
```

<!-- ## Deploy MLflow models as a web service

Deploying your MLflow experiments as an Azure Machine Learning web service allows you to leverage the Azure Machine Learning model management and data drift detection capabilities and apply them to your production models.

The following diagram demonstrates that with the MLflow deploy API you can deploy your existing MLflow models as an Azure Machine Learning web service, despite their frameworks--PyTorch, Tensorflow, scikit-learn, ONNX, etc., and manage your production models in your workspace.

![mlflow with azure machine learning diagram](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### Log your model

Before you can deploy, be sure that your model is saved so you can reference it and its path location for deployment. In your training script, there should be code similar to the following [mlflow.sklearn.log_model()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) method, that saves your model to the specified outputs directory. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Include the `conda_env` parameter to pass a dictionary representation of the dependencies and environment this model should be run in.

### Retrieve model from previous run

To retrieve the run, you need the run ID and the path in run history of where the model was saved. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### Deploy the model

Use the Azure Machine Learning SDK to deploy the model as a web service.

First, specify the deployment configuration. Azure Container Instance (ACI) is a suitable choice for a quick dev-test deployment, while Azure Kubernetes Service (AKS) is suitable for scalable production deployments.

#### Deploy to ACI

Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Then, register and deploy the model by using the Azure Machine Learning SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```
#### Deploy to AKS

To deploy to AKS, first create an AKS cluster. Create an AKS cluster using the [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) method. It may take 20-25 minutes to create a new cluster.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow' 

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')


Then, deploy the image by using the Azure Machine Learning SDK [deploy()](Then, register and deploy the model by using the Azure Machine Learning SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice
(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

The service deployment can take several minutes.

## Clean up resources

If you don't plan to use the logged metrics and artifacts in your workspace, the ability to delete them individually is currently unavailable. Instead, delete the resource group that contains the storage account and workspace, so you don't incur any charges:

1. In the Azure portal, select **Resource groups** on the far left.

   ![Delete in the Azure portal](./media/how-to-use-mlflow/delete-resources.png)

1. From the list, select the resource group you created.

1. Select **Delete resource group**.

1. Enter the resource group name. Then select **Delete**.

 -->

 ## <a name="example-notebooks"></a>Exemplo de cadernos

O [MLflow com cadernos Azure ML](https://aka.ms/azureml-mlflow-examples) demonstram e expandem sobre conceitos apresentados neste artigo.

## <a name="next-steps"></a>Passos seguintes
* [Gerencie os seus modelos.](concept-model-management-and-deployment.md)
* Monitorize os seus modelos de produção para [a deriva de dados](how-to-monitor-data-drift.md).
