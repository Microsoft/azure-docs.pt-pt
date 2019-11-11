---
title: Acompanhamento de MLflow para experimentos de ML
titleSuffix: Azure Machine Learning
description: Configure o MLflow com Azure Machine Learning para registrar métricas & artefatos e implantar modelos do databricks, do ambiente local ou do ambiente de VM.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 09/23/2019
ms.custom: seodec18
ms.openlocfilehash: 946350af0c1a4e8140fbf7f926061aae250e9969
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73716479"
---
# <a name="track-metrics-and-deploy-models-with-mlflow-and-azure-machine-learning-preview"></a>Acompanhar métricas e implantar modelos com MLflow e Azure Machine Learning (versão prévia)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este artigo demonstra como habilitar o URI de acompanhamento do MLflow e a API de log, coletivamente conhecido como [acompanhamento de MLflow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), com Azure Machine Learning. Isso permite que você:

+ Acompanhe e registre as métricas de experimento e os artefatos em seu [espaço de trabalho Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspaces). Se você já usa o acompanhamento de MLflow para seus experimentos, o espaço de trabalho fornece um local centralizado, seguro e escalonável para armazenar métricas e modelos de treinamento.

+ Implante seus experimentos do MLflow como um serviço Web Azure Machine Learning. Ao implantar como um serviço Web, você pode aplicar as funcionalidades de Azure Machine Learning monitoramento e desinstalação de descompasso de dados aos seus modelos de produção. 

O [MLflow](https://www.mlflow.org) é uma biblioteca de software livre para gerenciar o ciclo de vida de seus experimentos de aprendizado de máquina. O acompanhamento de MLFlow é um componente de MLflow que registra e rastreia suas métricas de execução de treinamento e artefatos de modelo, independentemente do ambiente de seu experimento, em um destino de computação remota, em uma máquina virtual, localmente em seu computador ou em um cluster Azure Databricks.

O diagrama a seguir ilustra isso com o acompanhamento de MLflow, você controla as métricas de execução de um experimento e armazena artefatos de modelo em seu espaço de trabalho Azure Machine Learning.

![mlflow com o diagrama do Azure Machine Learning](media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> As informações neste documento são basicamente para cientistas de dados e desenvolvedores que desejam monitorar o processo de treinamento de modelo. Se você for um administrador interessado em monitorar o uso de recursos e eventos do Azure Machine Learning, como cotas, execuções de treinamento concluídas ou implantações de modelo concluídas, consulte [monitoramento Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Comparar clientes MLflow e Azure Machine Learning

 A tabela abaixo resume os diferentes clientes que podem usar Azure Machine Learning e seus respectivos recursos de função.

 O acompanhamento de MLflow oferece funcionalidades de log de métricas e armazenamento de artefatos que estão disponíveis apenas de outra forma por meio do [SDK Azure Machine Learning Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).


| | Implantação de & de rastreamento de MLflow | Azure Machine Learning SDK do Python |  CLI do Azure Machine Learning | Azure Machine Learning studio|
|---|---|---|---|---|
| Gerenciar espaço de trabalho |   | ✓ | ✓ | ✓ |
| Usar armazenamentos de dados  |   | ✓ | ✓ | |
| Métricas de log      | ✓ | ✓ |   | |
| Carregar artefatos | ✓ | ✓ |   | |
| Ver métricas     | ✓ | ✓ | ✓ | ✓ |
| Gerir a computação   |   | ✓ | ✓ | ✓ |
| Implementar modelos    | ✓ | ✓ | ✓ | ✓ |
|Monitorar o desempenho do modelo||✓|  |   |
| Detetar desvios de dados |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>Pré-requisitos

* [Instale o MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Instalar o SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) em seu computador local o SDK fornece a conectividade para MLflow acessar seu espaço de trabalho.
* [Crie um Workspace do Azure Machine Learning](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Rastrear execuções locais

O acompanhamento de MLflow com o Azure Machine Learning permite armazenar as métricas registradas e os artefatos de suas execuções locais em seu espaço de trabalho Azure Machine Learning.

Instale o pacote de `azureml-contrib-run` para usar o acompanhamento de MLflow com Azure Machine Learning em seus experimentos executados localmente em um Jupyter Notebook ou editor de código.

```shell
pip install azureml-contrib-run
```

>[!NOTE]
>O namespace azureml. contrib é alterado com frequência, à medida que trabalhamos para melhorar o serviço. Assim, qualquer coisa neste namespace deve ser considerada como uma versão prévia e não tem suporte total da Microsoft.

Importe as classes `mlflow` e [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) para acessar o URI de acompanhamento do MLflow e configurar seu espaço de trabalho.

No código a seguir, o método `get_mlflow_tracking_uri()` atribui um endereço URI de acompanhamento exclusivo ao espaço de trabalho, `ws`e `set_tracking_uri()` aponta o URI de acompanhamento de MLflow para esse endereço.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>O URI de rastreamento é válido até uma hora ou menos. Se você reiniciar o script após algum tempo ocioso, use a API get_mlflow_tracking_uri para obter um novo URI.

Defina o nome do experimento do MLflow com `set_experiment()` e inicie a execução do treinamento com `start_run()`. Em seguida, use `log_metric()` para ativar a API de log MLflow e começar a registrar suas métricas de execução de treinamento.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Rastrear execuções remotas

O acompanhamento de MLflow com o Azure Machine Learning permite armazenar as métricas registradas e os artefatos de suas execuções remotas em seu espaço de trabalho Azure Machine Learning.

As execuções remotas permitem treinar seus modelos em computações mais poderosas, como máquinas virtuais habilitadas para GPU ou clusters Computação do Machine Learning. Consulte [Configurar destinos de computação para treinamento de modelo](how-to-set-up-training-targets.md) para saber mais sobre diferentes opções de computação.

Configure seu ambiente de computação e de execução de treinamento com a classe [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) . Inclua `mlflow` e `azure-contrib-run` pacotes Pip na seção de [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) do ambiente. Em seguida, construa [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) com a computação remota como o destino de computação.

```Python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-contrib-run'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

No script de treinamento, importe `mlflow` para usar as APIs de log MLflow e comece a registrar suas métricas de execução.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Com essa configuração de execução de computação e treinamento, use o método `Experiment.submit('train.py')` para enviar uma execução. Esse método define automaticamente o URI de acompanhamento MLflow e direciona o log de MLflow para seu espaço de trabalho.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Rastrear Azure Databricks execuções

O acompanhamento de MLflow com o Azure Machine Learning permite armazenar as métricas registradas e os artefatos de suas execuções do databricks em seu espaço de trabalho Azure Machine Learning.

Para executar os experimentos do Mlflow com Azure Databricks, você precisa primeiro criar um [espaço de trabalho e um cluster do Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)

No cluster, certifique-se de instalar a biblioteca *azureml-mlflow* de PyPi, para garantir que o cluster tenha acesso às funções e classes necessárias.
Aqui, importe seu notebook de experimento, anexe o cluster a ele e execute o experimento. 

### <a name="install-libraries"></a>Instalar bibliotecas

Para instalar bibliotecas em seu cluster, navegue até a guia **bibliotecas** e clique em **Instalar nova**

 ![mlflow com o diagrama do Azure Machine Learning](media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

No campo **pacote** , digite azureml-mlflow e clique em instalar. Repita essa etapa conforme necessário para instalar outros pacotes adicionais em seu cluster para seu experimento.

 ![mlflow com o diagrama do Azure Machine Learning](media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>Configurar seu bloco de anotações e o espaço de trabalho

Depois que o cluster estiver configurado, importe seu notebook de experimento, abra-o e anexe o cluster a ele.

O código a seguir deve estar no seu notebook de experimento. Esse código obtém os detalhes de sua assinatura do Azure para criar uma instância do seu espaço de trabalho. Esse código pressupõe que você tenha um grupo de recursos existente e Azure Machine Learning espaço de trabalho, caso contrário, você pode [criá-los](how-to-manage-workspace.md). 

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

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Conectar seus espaços de trabalho do Azure Databricks e do Azure Machine Learning

No [portal do Azure](https://ms.portal.azure.com), você pode vincular seu espaço de trabalho Azure DATABRICKS (ADB) a um espaço de trabalho Azure Machine Learning novo ou existente. Para fazer isso, navegue até o espaço de trabalho do ADB e selecione o botão **vincular Azure Machine Learning espaço de trabalho** no canto inferior direito. Vincular seus espaços de trabalho permite que você acompanhe seus dados de teste no espaço de trabalho Azure Machine Learning. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>Vincular o rastreamento de MLflow ao seu espaço de trabalho

Depois de instanciar seu espaço de trabalho, defina o URI de acompanhamento MLflow. Ao fazer isso, você vincula o rastreamento MLflow ao espaço de trabalho Azure Machine Learning. Após a vinculação, todos os experimentos vão para o serviço de controle de Azure Machine Learning gerenciado.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>Definir diretamente o controle de MLflow em seu bloco de anotações

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

No script de treinamento, importe mlflow para usar as APIs de log MLflow e comece a registrar suas métricas de execução. O exemplo a seguir registra a métrica de perda de época. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>Automatizar a configuração do acompanhamento de MLflow

Em vez de definir manualmente o URI de acompanhamento em cada sessão de notebook de experimento subsequente em seus clusters, faça isso automaticamente usando esse [Azure Machine Learning script de inicialização de cluster](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md).

Quando configurado corretamente, você poderá ver seus dados de acompanhamento de MLflow na API REST do Azure Machine Learning e em todos os clientes e em Azure Databricks por meio da interface do usuário do MLflow ou usando o cliente MLflow.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Exibir métricas e artefatos em seu espaço de trabalho

As métricas e os artefatos do log MLflow são mantidos em seu espaço de trabalho. Para exibi-las a qualquer momento, navegue até seu espaço de trabalho e localize o experimento por nome em seu espaço de trabalho no [Azure Machine Learning Studio](https://ml.azure.com).  Ou execute o código abaixo. 

```python
run.get_metrics()
ws.get_details()
```

## <a name="deploy-mlflow-models-as-a-web-service"></a>Implantar modelos MLflow como um serviço Web

Implantar seus experimentos MLflow como um serviço Web Azure Machine Learning permite aproveitar os recursos de detecção de Azure Machine Learning de gerenciamento de modelos e descompasso de dados e aplicá-los aos seus modelos de produção.

O diagrama a seguir demonstra que, com a API de implantação do MLflow, você pode implantar seus modelos existentes do MLflow como um serviço Web Azure Machine Learning, apesar de suas estruturas--PyTorch, Tensorflow, scikit-learn, ONNX, etc. e gerenciar seus modelos de produção no seu espaço de trabalho.

![mlflow com o diagrama do Azure Machine Learning](media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### <a name="log-your-model"></a>Registrar seu modelo

Antes de implantar o, certifique-se de que seu modelo seja salvo para que você possa referenciá-lo e seu local de caminho para implantação. No script de treinamento, deve haver um código semelhante ao seguinte método [mlflow. sklearn. log_model ()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) , que salva o modelo no diretório de saídas especificado. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Inclua o parâmetro `conda_env` para passar uma representação de dicionário das dependências e do ambiente em que esse modelo deve ser executado.

### <a name="retrieve-model-from-previous-run"></a>Recuperar modelo da execução anterior

Para recuperar a execução, você precisa da ID de execução e do caminho no histórico de execução de onde o modelo foi salvo. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### <a name="create-docker-image"></a>Criar imagem do Docker

A função `mlflow.azureml.build_image()` cria uma imagem do Docker do modelo salvo em uma maneira com reconhecimento de estrutura. Ele cria automaticamente o código de wrapper inferência específico da estrutura e especifica as dependências do pacote para você. Especifique o caminho do modelo, o espaço de trabalho, a ID de execução e outros parâmetros.

O código a seguir cria uma imagem do Docker usando execute *:/< Run. id >/Model* como o caminho de model_uri para um experimento Scikit-learn.

```python
import mlflow.azureml

azure_image, azure_model = mlflow.azureml.build_image(model_uri='runs:/{}/{}'.format(runid, model_save_path),
                                                      workspace=ws,
                                                      model_name='sklearn-model',
                                                      image_name='sklearn-image',
                                                      synchronous=True)
```
A criação da imagem do Docker pode levar vários minutos. 

### <a name="deploy-the-docker-image"></a>Implantar a imagem do Docker 

Depois que a imagem for criada, use o SDK do Azure Machine Learning para implantar a imagem como um serviço Web.

Primeiro, especifique a configuração de implantação. A ACI (instância de contêiner do Azure) é uma opção adequada para uma implantação rápida de desenvolvimento/teste, enquanto o AKS (serviço kubernetes do Azure) é adequado para implantações de produção escalonáveis.

#### <a name="deploy-to-aci"></a>Implementar para ACI

Configure sua configuração de implantação com o método [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Você também pode adicionar marcas e descrições para ajudar a manter o controle do seu serviço Web.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Em seguida, implante a imagem usando o método [deploy_from_image ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-) do SDK do Azure Machine Learning. 

```python
webservice = Webservice.deploy_from_image( image=azure_image, 
                                           workspace=ws, 
                                           name='diabetes-model-1', 
                                           deployment_config=aci_config)

webservice.wait_for_deployment(show_output=True)
```
#### <a name="deploy-to-aks"></a>Implementar para AKS

Para implantar no AKS, primeiro crie um cluster AKS e coloque a imagem do Docker que você deseja implantar. Para este exemplo, traga a imagem criada anteriormente da implantação do ACI.

Para obter a imagem da implantação ACI anterior, use a classe [Image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image.image?view=azure-ml-py) . 

```python
from azureml.core.image import Image

# Get the image by name, you can change this based on the image you want to deploy
myimage = Image(workspace=ws, name='sklearn-image') 
```

Crie um cluster AKS usando o método [ComputeTarget. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) . Pode levar de 20-25 minutos para criar um novo cluster.

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
Configure sua configuração de implantação com o método [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Você também pode adicionar marcas e descrições para ajudar a manter o controle do seu serviço Web.

```python
from azureml.core.webservice import Webservice, AksWebservice
from azureml.core.image import ContainerImage

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)

# Unique service name
service_name ='aks-service'
```

Em seguida, implante a imagem usando o método [deploy_from_image ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-) do SDK do Azure Machine Learning. 

```python
# Webservice creation using single command
aks_service = Webservice.deploy_from_image( workspace=ws, 
                                            name=service_name,
                                            deployment_config = aks_config
                                            image = myimage,
                                            deployment_target = aks_target)

aks_service.wait_for_deployment(show_output=True)
```

A implantação do serviço pode levar vários minutos.

## <a name="clean-up-resources"></a>Limpar recursos

Se você não planeja usar as métricas registradas e os artefatos em seu espaço de trabalho, a capacidade de excluí-los individualmente não estará disponível no momento. Em vez disso, exclua o grupo de recursos que contém a conta de armazenamento e o espaço de trabalho, para que você não incorra nenhum encargo:

1. No portal do Azure, selecione **Grupos de recursos** na extremidade esquerda.

   ![Excluir no portal do Azure](media/how-to-use-mlflow/delete-resources.png)

1. Na lista, selecione o grupo de recursos que criou.

1. Selecione **Eliminar grupo de recursos**.

1. Insira o nome do grupo de recursos. Em seguida, selecione **Eliminar**.


## <a name="example-notebooks"></a>Blocos de anotações de exemplo

O [MLflow com os notebooks do Azure ml](https://aka.ms/azureml-mlflow-examples) demonstram e se expandem sobre os conceitos apresentados neste artigo.

## <a name="next-steps"></a>Passos seguintes
* [Gerencie seus modelos](concept-model-management-and-deployment.md).
* Monitore seus modelos de produção para [descompasso de dados](how-to-monitor-data-drift.md).
