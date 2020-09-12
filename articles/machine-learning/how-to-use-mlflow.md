---
title: MLflow Tracking para experiências ml
titleSuffix: Azure Machine Learning
description: Confiúdlo MLflow com Azure Machine Learning para registar métricas e artefactos de modelos ML, e implementar os seus modelos ML como um serviço web.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 06/04/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: e31fdec18ab4c6135031bf21d2387585141c2735
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90018225"
---
# <a name="track-model-metrics-and-deploy-ml-models-with-mlflow-and-azure-machine-learning-preview"></a>Rastrear métricas de modelos e implementar modelos ML com MLflow e Azure Machine Learning (pré-visualização)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este artigo demonstra como permitir que o URI de rastreio da MLflow e a API, coletivamente conhecida como [MLflow Tracking,](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)liguem as suas experiências de MLflow e a Azure Machine Learning.  Fazê-lo permite-lhe,

+ Acompanhe e regista métricas e artefactos no seu [espaço de trabalho de aprendizagem de máquinas Azure.](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces) Se já utiliza o MLflow Tracking para as suas experiências, o espaço de trabalho proporciona uma localização centralizada, segura e escalável para armazenar métricas e modelos de treino.

+ Implemente as suas experiências MLflow como um serviço web Azure Machine Learning. Ao implementar como um serviço web, pode aplicar as funcionalidades de monitorização e deteção de deriva de dados do Azure Machine Learning nos seus modelos de produção. 

[MLflow](https://www.mlflow.org) é uma biblioteca de código aberto para gerir o ciclo de vida das suas experiências de aprendizagem automática. MLFlow Tracking é um componente do MLflow que regista e rastreia as suas métricas de treino e artefactos de modelo, independentemente do ambiente da sua experiência-- localmente no seu computador, num alvo de computação remota, numa máquina virtual ou num cluster Azure Databricks. 

>[!NOTE]
> Como biblioteca de código aberto, o MLflow muda frequentemente. Como tal, a funcionalidade disponibilizada através da integração Azure Machine Learning e MLflow deve ser considerada como uma pré-visualização, e não totalmente suportada pela Microsoft.

O diagrama seguinte ilustra que, com o MLflow Tracking, rastreia as métricas de execução de uma experiência e armazena artefactos de modelo no seu espaço de trabalho Azure Machine Learning.

![mlflow com diagrama de aprendizagem de máquina azul](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> A informação neste documento é principalmente para cientistas de dados e desenvolvedores que querem monitorizar o processo de formação de modelos. Se você é um administrador interessado em monitorizar a utilização de recursos e eventos da Azure Machine Learning, tais como quotas, execuções de formação completas ou implementações de modelos concluídas, consulte [Monitoring Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Compare clientes MLflow e Azure Machine Learning

 A tabela abaixo resume os diferentes clientes que podem utilizar o Azure Machine Learning e as respetivas capacidades de função.

 O MLflow Tracking oferece funcionalidades de registo métrico e de armazenamento de artefactos que só estão disponíveis de outra forma através do [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true).


| Funcionalidade | Implementação de & de rastreio de fluxo mlflow | Azure Máquina aprendendo Python SDK |  Azure Machine Learning CLI | Azure Machine Learning studio|
|---|---|---|---|---|
| Gerir espaço de trabalho |   | ✓ | ✓ | ✓ |
| Utilizar lojas de dados  |   | ✓ | ✓ | |
| Métricas de registo      | ✓ | ✓ |   | |
| Carregar artefactos | ✓ | ✓ |   | |
| Ver métricas     | ✓ | ✓ | ✓ | ✓ |
| Gerir a computação   |   | ✓ | ✓ | ✓ |
| Implementar modelos    | ✓ | ✓ | ✓ | ✓ |
|Monitorar desempenho do modelo||✓|  |   |
| Detetar desvios de dados |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>Pré-requisitos

* [Instale o MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Instale o Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) no seu computador local O SDK fornece a conectividade para o MLflow aceder ao seu espaço de trabalho.
* Criar um espaço de trabalho de [aprendizagem de máquinas Azure.](how-to-manage-workspace.md)

## <a name="track-local-runs"></a>Acompanhe as corridas locais

O MLflow Tracking with Azure Machine Learning permite-lhe armazenar as métricas e artefactos registados a partir das suas corridas locais para o seu espaço de trabalho de Aprendizagem de Máquinas Azure.

Instale o `azureml-mlflow` pacote para utilizar o MLflow Tracking com Azure Machine Learning nas suas experiências realizadas localmente num Bloco de Notas ou editor de códigos do Jupyter.

```shell
pip install azureml-mlflow
```

Importe as `mlflow` e as classes para aceder ao [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py&preserve-view=true) URI de rastreio da MLflow e configure o seu espaço de trabalho.

No código seguinte, o `get_mlflow_tracking_uri()` método atribui um endereço URI de rastreio único ao espaço de `ws` trabalho, e aponta o `set_tracking_uri()` MLflow tracking URI para esse endereço.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>O URI de rastreio é válido até uma hora ou menos. Se reiniciar o seu script após algum tempo inativo, use a API get_mlflow_tracking_uri para obter um novo URI.

Desaça o nome da experiência MLflow `set_experiment()` e inicie a sua formação com `start_run()` . Em seguida, utilize `log_metric()` para ativar a API de registo de registo de MLflow e comece a registar as suas métricas de treino.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Faixa remota

O MLflow Tracking com Azure Machine Learning permite-lhe armazenar as métricas e artefactos registados a partir do seu remoto funciona no seu espaço de trabalho de Aprendizagem de Máquinas Azure.

As corridas remotas permitem-lhe treinar os seus modelos em computas mais potentes, como máquinas virtuais ativadas pela GPU ou clusters Machine Learning Compute. Consulte [metas de computação para formação de modelos](how-to-set-up-training-targets.md) para aprender sobre diferentes opções de computação.

Configure o seu ambiente de corrida de computação e treino com a [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true) classe. Inclua `mlflow` e `azureml-mlflow` pip pacotes na secção do [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py&preserve-view=true) ambiente. Em seguida, construa  [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py&preserve-view=true) com o seu cálculo remoto como o alvo do cálculo.

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

No seu script de treino, importe `mlflow` usar as APIs de registo de MLflow e comece a registar as suas métricas de execução.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Com esta configuração de cálculo e treino, utilize o `Experiment.submit('train.py')` método para submeter uma execução. Este método define automaticamente o MLflow tracking URI e direciona a marcação de registo de MLflow para o seu espaço de trabalho.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Track Azure Databricks corre

O MLflow Tracking with Azure Machine Learning permite-lhe armazenar as métricas e artefactos registados a partir dos seus Dados Azure que funcionam no seu espaço de trabalho de Aprendizagem de Máquinas Azure.

Para executar as suas experiências do Mlflow com a Azure Databricks, é necessário criar primeiro um [espaço de trabalho e um cluster Azure Databricks.](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) No seu cluster, certifique-se de instalar a biblioteca *azureml-mlflow* a partir de PyPi, para garantir que o seu cluster tem acesso às funções e classes necessárias.

A partir daqui, importe o seu caderno de experiências, prenda-o ao seu cluster Azure Databricks e execute a sua experiência. 

### <a name="install-libraries"></a>Instalar bibliotecas

Para instalar bibliotecas no seu cluster, navegue no separador **Bibliotecas** e clique em **Instalar Novo**

 ![mlflow com diagrama de aprendizagem de máquina azul](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

No campo **Pacote,** escreva azureml-mlflow e clique na instalação. Repita este passo se necessário para instalar outros pacotes adicionais no seu cluster para a sua experiência.

 ![mlflow com diagrama de aprendizagem de máquina azul](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>Configurar o seu caderno e espaço de trabalho

Uma vez configurado o seu cluster, importe o seu caderno de experiências, abra-o e prenda o seu cluster a ele.

O seguinte código deve estar no seu caderno de experiências. Este código obtém os detalhes da sua subscrição Azure para instantaneaizar o seu espaço de trabalho. Este código pressupõe que você tem um grupo de recursos existente e espaço de trabalho Azure Machine Learning, caso contrário poderá [criá-los](how-to-manage-workspace.md). 

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

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Ligue os seus databricks Azure e espaços de aprendizagem de máquinas Azure

No [portal Azure,](https://ms.portal.azure.com)pode ligar o seu espaço de trabalho Azure Databricks (ADB) a um novo espaço de trabalho de Aprendizagem automática Azure ou existente. Para tal, navegue para o seu espaço de trabalho ADB e selecione o botão **de espaço de trabalho Link Azure Machine Learning** no canto inferior direito. A ligação dos seus espaços de trabalho permite-lhe rastrear os seus dados experimentais no espaço de trabalho Azure Machine Learning. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>Link MLflow rastreio para o seu espaço de trabalho

Depois de instantaneamente o seu espaço de trabalho, desapasse o MLflow tracking URI. Ao fazê-lo, liga o rastreio do MLflow ao espaço de trabalho Azure Machine Learning. Após a ligação, todas as suas experiências aterrarão no serviço de rastreio de machine learning gerido Azure Machine Learning.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>Definir diretamente MLflow Tracking no seu caderno

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

No seu script de treino, importe mlflow para usar as APIs de registo de MLflow e comece a registar as suas métricas de execução. O exemplo seguinte, regista a métrica da perda de época. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>Automatizar o rastreio do fluxo MLflow

Em vez de definir manualmente o URI de rastreio em todas as sessãos de cadernos experimentais subsequentes nos seus clusters, faça-o automaticamente utilizando este [script Azure Machine Learning Tracking Cluster Init](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md).

Quando configurado corretamente, é possível ver os seus dados de rastreio MLflow na API API de Aprendizagem de Máquinas Azure e em todos os clientes, e em Azure Databricks através da interface de utilizador MLflow ou utilizando o cliente MLflow.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Veja métricas e artefactos no seu espaço de trabalho

As métricas e artefactos da exploração madeireira MLflow são mantidos no seu espaço de trabalho. Para vê-los a qualquer momento, navegue para o seu espaço de trabalho e encontre a experiência pelo nome no seu espaço de trabalho no [estúdio Azure Machine Learning](https://ml.azure.com).  Ou executar o código abaixo. 

```python
run.get_metrics()
ws.get_details()
```

## <a name="deploy-mlflow-models-as-a-web-service"></a>Implementar os modelos MLflow como um serviço web

Implementar as suas experiências MLflow como um serviço web Azure Machine Learning permite-lhe alavancar as capacidades de gestão do modelo Azure Machine Learning e de deteção de deriva de dados e aplicá-las aos seus modelos de produção.

O diagrama seguinte demonstra que com a API de implementação de MLflow pode implementar os seus modelos MLflow existentes como um serviço web Azure Machine Learning, apesar das suas estruturas-- PyTorch, Tensorflow, scikit-learn, ONNX, etc., e gerir os seus modelos de produção no seu espaço de trabalho.

![mlflow com diagrama de aprendizagem de máquina azul](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### <a name="log-your-model"></a>Registar o seu modelo

Antes de poder ser implantado, certifique-se de que o seu modelo está guardado para que possa remá-lo e a sua localização de caminho para a sua implantação. No seu script de treino, deve haver um código semelhante ao seguinte método [mlflow.sklearn.log_model()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) que guarda o seu modelo para o diretório de saídas especificado. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Incluir o `conda_env` parâmetro para passar uma representação do dicionário das dependências e do ambiente em que este modelo deve ser executado.

### <a name="retrieve-model-from-previous-run"></a>Recuperar modelo de execução anterior

Para recuperar a corrida, precisa da identificação de execução e do caminho na história da execução de onde o modelo foi salvo. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### <a name="deploy-the-model"></a>Implementar o modelo

Utilize o Azure Machine Learning SDK para implementar o modelo como um serviço web.

Primeiro, especifique a configuração de implementação. Azure Container Instance (ACI) é uma escolha adequada para uma implantação rápida de teste de dev, enquanto o Serviço Azure Kubernetes (AKS) é adequado para implantações de produção escaláveis.

#### <a name="deploy-to-aci"></a>Implementar para ACI

Configurar a sua configuração de implementação com o método [deploy_configuration().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) Também pode adicionar tags e descrições para ajudar a acompanhar o seu serviço web.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Em seguida, registe e implemente o modelo utilizando o método de [implementação](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) Azure Machine Learning SDK. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```
#### <a name="deploy-to-aks"></a>Implementar para AKS

Para implantar para aKS, primeiro crie um cluster AKS. Crie um cluster AKS utilizando o método [ComputeTarget.create().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#&preserve-view=truecreate-workspace--name--provisioning-configuration-) Pode levar 20 a 25 minutos para criar um novo cluster.

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
Configurar a sua configuração de implementação com o método [deploy_configuration().](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) Também pode adicionar tags e descrições para ajudar a acompanhar o seu serviço web.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

Em seguida, implemente a imagem utilizando o Azure Machine Learning SDK [implementar()](Em seguida, registe e implemente o modelo utilizando o método [de implementação](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) Azure Machine Learning SDK. 

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

A colocação de serviço pode demorar vários minutos.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não planeia utilizar as métricas e artefactos registados no seu espaço de trabalho, a capacidade de eliminá-las individualmente não está disponível. Em vez disso, elimine o grupo de recursos que contém a conta de armazenamento e o espaço de trabalho, para que não incorre em quaisquer encargos:

1. No portal do Azure, selecione **Grupos de recursos** na extremidade esquerda.

   ![Excluir no portal Azure](./media/how-to-use-mlflow/delete-resources.png)

1. Na lista, selecione o grupo de recursos que criou.

1. Selecione **Eliminar grupo de recursos**.

1. Insira o nome do grupo de recursos. Em seguida, selecione **Eliminar**.

## <a name="example-notebooks"></a>Blocos de notas de exemplo

O [fluxo ML com os cadernos Azure ML](https://aka.ms/azureml-mlflow-examples) demonstram e expandem-se sobre conceitos apresentados neste artigo.

## <a name="next-steps"></a>Próximos passos
* [Gerencie os seus modelos.](concept-model-management-and-deployment.md)
* Monitorize os seus modelos de produção para [a deriva de dados.](how-to-monitor-data-drift.md)
