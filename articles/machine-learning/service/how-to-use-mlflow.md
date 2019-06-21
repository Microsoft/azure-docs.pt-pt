---
title: Como utilizar MLflow com o serviço Azure Machine Learning
titleSuffix: Azure Machine Learning service
description: Saiba como iniciar sessão métricas e artefactos usando a biblioteca de MLflow ao serviço Azure Machine Learning
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom: seodec18
ms.openlocfilehash: b64051a4ef7b6a816b03562fef0452cbe9ce949a
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144031"
---
# <a name="how-to-use-mlflow-with-azure-machine-learning-service-preview"></a>Como utilizar MLflow com o serviço Azure Machine Learning (pré-visualização)

Este artigo demonstra como utilizar MLflow URI de controlo e a API, o registo, coletivamente, também conhecido como MLflow de controlo, com o serviço Azure Machine Learning para controlar e inicie sessão métricas de experimentação e artefactos no seu [do Azure Machine Learning área de trabalho do serviço](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspace). Se já utilizar MLflow de controlo para suas experimentações, a área de trabalho fornece uma localização centralizada, segura e dimensionável para armazenar os seus modelos e métricas de treinamento.

[MLflow](https://www.mlflow.org) é uma biblioteca de código-fonte aberto para gerir o ciclo de vida das suas experimentações de machine learning. [Controlo de MLFlow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api) é um componente do MLflow que registra e controla seu treinamento executar a métrica e artefatos do modelo, se suas experimentações são executadas localmente, numa máquina virtual ou no remoto o cluster de cálculo.
![mlflow com o diagrama do azure machine learning](media/how-to-use-mlflow/mlflow-diagram.png)

## <a name="compare-mlflow-and-azure-machine-learning-service-clients"></a>Compare os clientes de serviço MLflow e o Azure Machine Learning

 A tabela abaixo resume os clientes de diferentes que podem utilizar o serviço Azure Machine Learning e as respetivas funcionalidades da respetiva função.

 Controlo MLflow oferece registo métrica e artefactos funcionalidades de armazenamento que apenas estão disponíveis através da [SDK de Python do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

| | Controlo de MLflow | Azure Machine Learning <br> SDK Python |  Azure Machine Learning <br> CLI | Portal do Azure|
|-|-|-|-|-|
| Gerir a área de trabalho |   | ✓ |  ✓ | ✓  |
| Utilizar arquivos de dados  |   | ✓ |  ✓ |    |
| Métricas de registo      | ✓ | ✓ |    |    |
| Carregar artefactos | ✓ | ✓ |    |    |
| Ver métricas     | ✓ | ✓ | ✓  | ✓ |
| Gerir a computação   |   | ✓ | ✓  | ✓ |
| Implementar modelos    |   | ✓ |   ✓ | ✓ |

## <a name="prerequisites"></a>Pré-requisitos

* [Instale MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Instalar o SDK de Python do Azure Machine Learning no seu computador local e criar uma área de trabalho do Azure Machine Learning](setup-create-workspace.md#sdk). O SDK fornece a conectividade para MLflow acessar sua área de trabalho.

## <a name="track-local-runs"></a>Monitorizar execuções de locais

Instalar o `azureml-contrib-run` pacote para utilizar MLflow controlo com o Azure Machine Learning em suas experimentações executadas localmente num editor de código ou bloco de notas do Jupyter.

```shell
pip install azureml-contrib-run
```

>[!NOTE]
>O espaço de nomes de azureml.contrib altera com frequência, pois estamos a trabalhar para melhorar o serviço. Como tal, qualquer coisa neste espaço de nomes deve ser considerada como uma pré-visualização e não totalmente suportada pela Microsoft.

Importar os `mlflow` e [ `Workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) classes para acessar MLflow do URI de controlo e configurar a sua área de trabalho.

No código a seguir, o `get_mlflow_tracking_uri()` método atribui um endereço URI de controlo exclusivo para a área de trabalho `ws`, e `set_tracking_uri()` aponta MLflow URI de controlo a esse endereço.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>O URI de controlo é válido até uma hora ou menos. Se reiniciar o seu script após algum tempo ocioso, utilize a API de get_mlflow_tracking_uri para obter um novo URI.

Defina o nome de experimentação MLflow com `set_experiment()` e inicie seu treinamento ser executado com `start_run()`. Em seguida, utilizar `log_metric()` para ativar a API de log MLflow e começar a seu treinamento executar a métrica de registo.

```Python
experiment_name = "experiment_with_mlflow"
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Controlar a execução remota

Execuções remotas permitem-lhe preparar seus modelos em computações mais poderosas, como máquinas virtuais com GPU ativada ou clusters de computação do Machine Learning. Ver [configurar destinos de computação de preparação de modelos](how-to-set-up-training-targets.md) para saber mais sobre as opções de computação diferentes.

Configurar a computação e o ambiente de treinamento ser executado com o [ `Environment` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) classe. Incluem `mlflow` e `azure-contrib-run` pacotes no ambiente do pip [ `CondaDependencies` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) secção. Em seguida, construir [ `ScriptRunConfig` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) com a computação remota como o destino de computação.

```Python

from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = "my_workspace",
                 name = "my_experiment")

mlflow_env = Environment(name="mlflow-env")

cd = CondaDependencies.create(pip_packages = ["mlflow", "azureml-contrib-run"])

mlflow_env.python.conda_dependencies=cd

src = ScriptRunConfig(source_directory="./my_script_location", script="my_training_script.py")

src.run_config.target = "my-remote-compute-compute"
src.run_config.environment = mlflow_env
```

No seu script de treinamento, importar `mlflow` usar MLflow APIs de registro e começa a registar as suas métricas de execução.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric("example", 1.23)
```

Com esse computação e a configuração de treinamento ser executado, utilize o `Experiment.submit("train.py")` método para submeter uma execução. Isso define o MLflow URI de controlo e automaticamente direciona o registo da MLflow à área de trabalho.

```Python
run = exp.submit(src)
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Ver métricas e artefactos na sua área de trabalho

As métricas e artefactos do registo de MLflow são mantidos na sua área de trabalho do [portal do Azure](https://portal.azure.com). Para vê-los de qualquer altura, navegue até à sua área de trabalho e localize a experimentação por nome.

## <a name="clean-up-resources"></a>Limpar recursos

Se não planeia utilizar as métricas com sessão iniciada e os artefactos na sua área de trabalho, a capacidade de eliminá-los individualmente está atualmente indisponível. Em vez disso, elimine o grupo de recursos que contém a conta de armazenamento e a área de trabalho, pelo que não existem custos:

1. No portal do Azure, selecione **Grupos de recursos** na extremidade esquerda.

   ![Eliminar no portal do Azure](media/how-to-use-mlflow/delete-resources.png)

1. Na lista, selecione o grupo de recursos que criou.

1. Selecione **Eliminar grupo de recursos**.

1. Introduza o nome do grupo de recursos. Em seguida, selecione **Eliminar**.

## <a name="example-notebooks"></a>Blocos de notas de exemplo

O [MLflow com blocos de notas do Azure ML](https://aka.ms/azureml-mlflow-examples) demonstra conceitos deste artigo.

## <a name="next-steps"></a>Passos Seguintes

* [Como implementar um modelo](how-to-deploy-and-where.md).