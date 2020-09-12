---
title: Submeter uma corrida de formação a um alvo de computação
titleSuffix: Azure Machine Learning
description: Treine o seu modelo de aprendizagem automática em vários ambientes de treino (metas de computação). Pode facilmente alternar entre ambientes de treino. Comece a treinar localmente. Se precisar de escalar, mude para um alvo de computação baseado na nuvem.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 08/28/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperfq1
ms.openlocfilehash: da48b593b8f645566b2f9775fabc5d8e62e625b6
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661564"
---
# <a name="submit-a-training-run-to-a-compute-target"></a>Submeter uma corrida de formação a um alvo de computação

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a usar vários ambientes de formação[(metas de computação)](concept-compute-target.md)para treinar o seu modelo de aprendizagem automática.

Ao treinar, é comum começar no computador local e, mais tarde, executar esse script de treino num alvo de computação diferente. Com o Azure Machine Learning, pode executar o seu script em vários alvos de computação sem ter de alterar o seu script de treino.

Tudo o que precisa de fazer é definir o ambiente para cada alvo de cálculo dentro de uma **configuração de execução de script**.  Em seguida, quando quiser executar a sua experiência de treino num alvo de computação diferente, especifique a configuração de execução para esse cálculo.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente hoje a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree)
* [O Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)
* [Um espaço de trabalho de aprendizagem automática Azure,](how-to-manage-workspace.md)`ws`
* Um alvo computacional, `my_compute_target` .  Criar um alvo de computação com:
  * [Python SDK](how-to-create-attach-compute-sdk.md) 
  * [Azure Machine Learning studio](how-to-create-attach-compute-studio.md)

## <a name="whats-a-script-run-configuration"></a><a name="whats-a-run-configuration"></a>O que é uma configuração de execução de script?

Submetes a tua experiência de treino com um objeto [ScriptRunConfig.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py&preserve-view=true)  Este objeto inclui:

* **source_directory**: O diretório de origem que contém o seu roteiro de treino
* **Script**: Identifique o roteiro de treino
* **run_config**: A [configuração de execução,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py&preserve-view=true)que por sua vez define onde o treino ocorrerá. No `run_config` seu especifique o alvo do cálculo e o ambiente a utilizar ao executar o script de treino.  

## <a name="whats-an-environment"></a>O que é um ambiente?

Os [ambientes](concept-environments.md) de aprendizagem automática Azure são uma encapsulação do ambiente onde o seu treino de aprendizagem automática acontece. Especificam os pacotes Python, variáveis ambientais e configurações de software em torno dos seus scripts de treino e pontuação. Também especificam os tempos de execução (Python, Spark ou Docker).  

Os ambientes são especificados no  `run_config` objeto dentro de um `ScriptRunConfig` .

## <a name="train-your-model"></a><a id="submit"></a>Preparar o seu modelo

O código padrão para submeter uma corrida de treino é o mesmo para todos os tipos de metas de computação:

1. Criar uma experiência para correr
1. Criar um ambiente onde o script será executado
1. Crie uma configuração de execução de script, que faz referência ao alvo e ao ambiente do cálculo
1. Submeter a corrida
1. Espere que a corrida termine.

Ou pode:

* Submeta a experiência com um `Estimator` objeto como mostrado nos [modelos de ML do comboio com estimadores](how-to-train-ml-models.md).
* Submeta uma execução HyperDrive para [sintonização hiperparítnica](how-to-tune-hyperparameters.md).
* Submeter uma experiência através da extensão do [Código VS.](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model)

## <a name="create-an-experiment"></a>Criar uma experimentação

Crie uma experiência no seu espaço de trabalho.

```python
from azureml.core import Experiment

experiment_name = 'my_experiment'

experiment = Experiment(workspace=ws, name=experiment_name)
```

## <a name="create-an-environment"></a>Criar um ambiente

Ambientes com curadoria contêm coleções de pacotes Python e estão disponíveis no seu espaço de trabalho por padrão. Estes ambientes são apoiados por imagens estivas em cache que reduzem o custo de preparação da execução. Para um alvo de computação remota, você pode usar um desses ambientes populares curados para começar com:

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
my_environment = Environment.get(workspace=ws, name="AzureML-Minimal")
```

Para obter mais informações e detalhes sobre ambientes, consulte [Criar & utilizar ambientes de software em Azure Machine Learning](how-to-use-environments.md).
  
### <a name="local-compute-target"></a>Alvo de computação local

Se o seu alvo de cálculo for a sua **máquina local,** é responsável por garantir que todos os pacotes necessários estão disponíveis no ambiente Python onde o script é executado.  Utilize `python.user_managed_dependencies` para utilizar o seu ambiente python atual (ou o Python no caminho que especificar).

```python
from azureml.core import Environment

# Editing a run configuration property on-fly.
my_environment = Environment("user-managed-env")

my_environment.python.user_managed_dependencies = True

# You can choose a specific Python environment by pointing to a Python path 
#my_environment.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
```

## <a name="create-script-run-configuration"></a>Criar configuração de execução de script

Agora que tem um alvo de cálculo `compute_target` () e ambiente `my_environment` (), crie uma configuração de execução de script que executa o seu script de treino `train.py` () localizado no seu `project_folder` diretório:

```python
from azureml.core import ScriptRunConfig

script_run_config = ScriptRunConfig(source_directory=project_folder, script='train.py')

# Set compute target
script_run_config.run_config.target = my_compute_target

# Set environment.   If you don't do this, a default environment will be created.
script_run_config.run_config.environment = my_environment
```

Também pode querer definir o quadro para a sua execução.

* Para um cluster HDI:
    ```python
    src.run_config.framework = "pyspark"
    ```

* Para uma máquina virtual remota:
    ```python
    src.run_config.framework = "python"
    ```

## <a name="submit-the-experiment"></a>Submeter a experimentação

```python
run = experiment.submit(config=script_run_config)
```

> [!IMPORTANT]
> Quando submete a formação, uma imagem do diretório que contém os seus scripts de treino é criada e enviada para o alvo do cálculo. Também é armazenado como parte da experiência no seu espaço de trabalho. Se alterar ficheiros e submeter a execução novamente, apenas os ficheiros alterados serão carregados.
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> Para obter mais informações sobre instantâneos, consulte [Snapshots](concept-azure-machine-learning-architecture.md#snapshots).


<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Rastreio e integração de Git

Quando se inicia uma corrida de treinamento onde o diretório de origem é um repositório local de Git, a informação sobre o repositório é armazenada na história da execução. Para obter mais informações, consulte [a integração do Git para a Azure Machine Learning.](concept-train-model-git-integration.md)

## <a name="notebook-examples"></a>Exemplos de cadernos

Consulte estes cadernos para exemplos de formação com vários alvos de computação:
* [como usar-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutoriais/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Próximos passos

* [Tutorial: Treinar um modelo](tutorial-train-models-with-aml.md) usa um alvo de computação gerido para treinar um modelo.
* Aprender a [sintonizar eficientemente os hiperparímetros](how-to-tune-hyperparameters.md) para construir modelos melhores.? view=azure-ml-py&preserve-view=true)
* Uma vez treinado, aprenda [como e onde implementar modelos.](how-to-deploy-and-where.md)
* Consulte a referência [SDK da classe RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py&preserve-view=true)
* [Use a azure machine learning com redes virtuais Azure](how-to-enable-virtual-network.md)