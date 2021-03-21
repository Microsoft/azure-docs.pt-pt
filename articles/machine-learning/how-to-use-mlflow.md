---
title: MLflow Tracking para experiências ml
titleSuffix: Azure Machine Learning
description: Confiúdlo MLflow com Azure Machine Learning para registar métricas e artefactos de modelos ML, e implementar os seus modelos ML como um serviço web.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 12/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 02684ba91c207357e15684870a6fa0ceab3e17ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520970"
---
# <a name="train-and-track-ml-models-with-mlflow-and-azure-machine-learning-preview"></a>Modelos ML de treinamento e pista com MLflow e Azure Machine Learning (pré-visualização)

Neste artigo, aprenda a permitir que o URI de rastreio da MLflow e a API, coletivamente conhecida como [MLflow Tracking,](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)liguem a Azure Machine Learning como o backend das suas experiências de MLflow. 

As capacidades suportadas incluem: 

+ Acompanhe e regista métricas e artefactos no seu [espaço de trabalho de aprendizagem de máquinas Azure.](./concept-azure-machine-learning-architecture.md#workspace) Se já utiliza o MLflow Tracking para as suas experiências, o espaço de trabalho proporciona uma localização centralizada, segura e escalável para armazenar métricas e modelos de treino.

+ Submeta empregos de formação com [projetos MLflow](https://www.mlflow.org/docs/latest/projects.html) com suporte de backend Azure Machine Learning (pré-visualização). Pode submeter trabalhos localmente com o rastreio de Aprendizagem automática Azure ou migrar as suas corridas para a nuvem como através de um [Azure Machine Learning Compute](./how-to-create-attach-compute-cluster.md).

+ Acompanhe e gere os modelos no registo de modelos MLflow e Azure Machine Learning.

[MLflow](https://www.mlflow.org) é uma biblioteca de código aberto para gerir o ciclo de vida das suas experiências de aprendizagem automática. MLFlow Tracking é um componente do MLflow que regista e rastreia as suas métricas de treino e artefactos de modelo, independentemente do ambiente da sua experiência-- localmente no seu computador, num alvo de computação remota, numa máquina virtual ou num [cluster Azure Databricks](how-to-use-mlflow-azure-databricks.md). 

>[!NOTE]
> Como biblioteca de código aberto, o MLflow muda frequentemente. Como tal, a funcionalidade disponibilizada através da integração Azure Machine Learning e MLflow deve ser considerada como uma pré-visualização, e não totalmente suportada pela Microsoft.

O diagrama seguinte ilustra que, com o MLflow Tracking, rastreia as métricas de execução de uma experiência e armazena artefactos de modelo no seu espaço de trabalho Azure Machine Learning.

![mlflow com diagrama de aprendizagem de máquina azul](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> A informação neste documento é principalmente para cientistas de dados e desenvolvedores que querem monitorizar o processo de formação de modelos. Se você é um administrador interessado em monitorizar a utilização de recursos e eventos da Azure Machine Learning, tais como quotas, execuções de formação completas ou implementações de modelos concluídas, consulte [Monitoring Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Compare clientes MLflow e Azure Machine Learning

 A tabela seguinte resume os diferentes clientes que podem utilizar o Azure Machine Learning e as respetivas capacidades de função.

 O MLflow Tracking oferece funcionalidades de registo métrico e de armazenamento de artefactos que só estão disponíveis de outra forma através do [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro).

| Funcionalidade | Implementação de & de rastreio de fluxo mlflow | Azure Máquina aprendendo Python SDK |  CLI do Azure Machine Learning | Azure Machine Learning studio|
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

* Instale o pacote `azureml-mlflow`. 
    * Este pacote traz automaticamente `azureml-core` o [The Azure Machine Learning Python SDK,](/python/api/overview/azure/ml/install)que fornece a conectividade para o MLflow aceder ao seu espaço de trabalho.
* Criar um espaço de trabalho de [aprendizagem de máquinas Azure.](how-to-manage-workspace.md)
    * Veja quais [as permissões de acesso necessárias para realizar as suas operações de MLflow com o seu espaço de trabalho.](how-to-assign-roles.md#mlflow-operations)

## <a name="track-local-runs"></a>Acompanhe as corridas locais

O MLflow Tracking with Azure Machine Learning permite-lhe armazenar as métricas e artefactos registados a partir das suas corridas locais para o seu espaço de trabalho de Aprendizagem de Máquinas Azure.

Importe as `mlflow` e as classes para aceder ao [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29) URI de rastreio da MLflow e configure o seu espaço de trabalho.

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

As corridas remotas permitem-lhe treinar os seus modelos em computas mais potentes, como máquinas virtuais ativadas pela GPU ou clusters Machine Learning Compute. Consulte [metas de computação para formação de modelos](how-to-set-up-training-targets.md) para aprender sobre diferentes opções de computação.

O MLflow Tracking com Azure Machine Learning permite-lhe armazenar as métricas e artefactos registados a partir do seu remoto funciona no seu espaço de trabalho de Aprendizagem de Máquinas Azure. Qualquer execução com o código de rastreio MLflow nele terá métricas registadas automaticamente no espaço de trabalho. 

O ambiente conda exemplo a seguir inclui `mlflow` e `azureml-mlflow` como pacotes de pip. 


```yaml
name: sklearn-example
dependencies:
  - python=3.6.2
  - scikit-learn
  - matplotlib
  - numpy
  - pip:
    - azureml-mlflow
    - numpy
```

No seu script, configuure o seu ambiente de computação e treino com a [`Environment`](/python/api/azureml-core/azureml.core.environment.environment) classe. Em seguida, construa  [`ScriptRunConfig`](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig) com o seu cálculo remoto como o alvo do cálculo.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Com esta configuração de cálculo e treino, utilize o `Experiment.submit()` método para submeter uma execução. Este método define automaticamente o MLflow tracking URI e direciona a marcação de registo de MLflow para o seu espaço de trabalho.

```Python
run = exp.submit(src)
```

## <a name="train-with-mlflow-projects"></a>Treinar com Projetos MLflow

[MLflow Projects](https://mlflow.org/docs/latest/projects.html) permite-lhe organizar e descrever o seu código para permitir que outros cientistas de dados (ou ferramentas automatizadas) o executem. MLflow Projects com Azure Machine Learning permite-lhe acompanhar e gerir os seus percursos de formação no seu espaço de trabalho. 

Este exemplo mostra como submeter projetos de MLflow localmente com o rastreio de Aprendizagem automática Azure.

Instale a `azureml-mlflow` embalagem para utilizar o MLflow Tracking com Azure Machine Learning nas suas experiências localmente. As suas experiências podem ser executadas através de um Bloco de Notas ou editor de códigos.

```shell
pip install azureml-mlflow
```

Importe as `mlflow` e as classes para aceder ao [`Workspace`](/python/api/azureml-core/azureml.core.workspace%28class%29) URI de rastreio da MLflow e configure o seu espaço de trabalho.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

Desaça o nome da experiência MLflow `set_experiment()` e inicie a sua formação com `start_run()` . Em seguida, utilize `log_metric()` para ativar a API de registo de registo de MLflow e comece a registar as suas métricas de treino.

```Python
experiment_name = 'experiment-with-mlflow-projects'
mlflow.set_experiment(experiment_name)
```

Crie o objeto de configuração de backend para armazenar as informações necessárias para a integração, como, o alvo do cálculo e que tipo de ambiente gerido a utilizar.

```python
backend_config = {"USE_CONDA": False}
```
Adicione o `azureml-mlflow` pacote como uma dependência de pip ao seu ficheiro de configuração do ambiente de modo a rastrear métricas e artefactos chave no seu espaço de trabalho. 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```
Submeta a execução local e certifique-se de que define o parâmetro `backend = "azureml" ` . Com esta definição, pode submeter execuções localmente e obter o suporte adicional de rastreio automático de saída, ficheiros de registo, instantâneos e erros impressos no seu espaço de trabalho. 

Veja as suas corridas e métricas no [estúdio Azure Machine Learning.](overview-what-is-machine-learning-studio.md) 


```python
local_env_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    use_conda=False,
                                    backend_config = backend_config, 
                                    )

```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Veja métricas e artefactos no seu espaço de trabalho

As métricas e artefactos da exploração madeireira MLflow são mantidos no seu espaço de trabalho. Para vê-los a qualquer momento, navegue para o seu espaço de trabalho e encontre a experiência pelo nome no seu espaço de trabalho no [estúdio Azure Machine Learning](https://ml.azure.com).  Ou executar o código abaixo. 

```python
run.get_metrics()
```

## <a name="manage-models"></a>Gerir modelos 

Registe e rastreia os seus modelos com o registo do [modelo Azure Machine Learning](concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere) que suporta o registo do modelo MLflow. Os modelos Azure Machine Learning estão alinhados com o esquema do modelo MLflow, facilitando a exportação e importação destes modelos através de diferentes fluxos de trabalho. Os metadados relacionados com o MLflow, tais como, o ID executado também está marcado com o modelo registado para rastreabilidade. Os utilizadores podem submeter ensaios de formação, registar e implementar modelos produzidos a partir de execuções MLflow. 

Se pretender implantar e registar o seu modelo pronto de produção num só passo, consulte [implementar e registar os modelos MLflow](how-to-deploy-mlflow-models.md).

Para registar e ver um modelo a partir de uma corrida, utilize os seguintes passos:

1. Uma vez que a execução esteja completa, chame o `register_model()` método.

    ```python
    # the model folder produced from the run is registered. This includes the MLmodel file, model.pkl and the conda.yaml.
    run.register_model(model_name = 'my-model', model_path = 'model')
    ```

1. Veja o modelo registado no seu espaço de trabalho com [o estúdio Azure Machine Learning.](overview-what-is-machine-learning-studio.md)

    No exemplo seguinte, o modelo registado, `my-model` tem metadados de rastreio de MLflow marcados. 

    ![register-mlflow-model](./media/how-to-use-mlflow/registered-mlflow-model.png)

1. Selecione o separador Artefactos para ver todos os **ficheiros** de modelos que se alinham com o esquema do modelo MLflow (conda.yaml, MLmodel, model.pkl).

    ![modelo-schema](./media/how-to-use-mlflow/mlflow-model-schema.png)

1. Selecione MLmodel para ver o ficheiro MLmodel gerado pela execução.

    ![MLmodel-schema](./media/how-to-use-mlflow/mlmodel-view.png)


## <a name="clean-up-resources"></a>Limpar os recursos

Se não planeia utilizar as métricas e artefactos registados no seu espaço de trabalho, a capacidade de eliminá-las individualmente não está disponível. Em vez disso, elimine o grupo de recursos que contém a conta de armazenamento e o espaço de trabalho, para que não incorre em quaisquer encargos:

1. No portal do Azure, selecione **Grupos de recursos** na extremidade esquerda.

   ![Excluir no portal Azure](./media/how-to-use-mlflow/delete-resources.png)

1. Na lista, selecione o grupo de recursos que criou.

1. Selecione **Eliminar grupo de recursos**.

1. Introduza o nome do grupo de recursos. Em seguida, selecione **Eliminar**.

## <a name="example-notebooks"></a>Blocos de notas de exemplo

O [fluxo ML com os cadernos Azure ML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) demonstram e expandem-se sobre conceitos apresentados neste artigo.

> [!NOTE]
> Um repositório de exemplos orientado pela comunidade pode ser encontrado em https://github.com/Azure/azureml-examples .

## <a name="next-steps"></a>Passos seguintes

* [Implementar modelos com MLflow](how-to-deploy-mlflow-models.md).
* Monitorize os seus modelos de produção para [a deriva de dados.](./how-to-enable-data-collection.md)
* [Track Azure Databricks funciona com MLflow](how-to-use-mlflow-azure-databricks.md).
* [Gerencie os seus modelos.](concept-model-management-and-deployment.md)