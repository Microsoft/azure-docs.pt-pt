---
title: 'Tutorial: Começar a aprendizagem automática - Python'
titleSuffix: Azure Machine Learning
description: Neste tutorial, você vai começar com o Azure Machine Learning Python SDK correndo no seu ambiente de desenvolvimento pessoal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 48edc138e7ab16e712339d6291db52a4a2b36b32
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90946641"
---
# <a name="tutorial-get-started-with-azure-machine-learning-on-your-development-environment-part-1-of-4"></a>Tutorial: Começa com a Azure Machine Learning no teu ambiente de desenvolvimento (Parte 1 de 4)

Nesta **série tutorial de quatro partes,** você vai aprender os fundamentos do Azure Machine Learning e completar tarefas python ml baseadas em empregos na nuvem Azure, incluindo:

1. Crie um espaço de trabalho e o seu ambiente de desenvolvimento de aprendizagem automática local.
2. Executar código na nuvem usando O Python SDK da Azure Machine Learning.
3. Gerencie o ambiente Python que usa para o treino de modelos.
4. Faça upload de dados para a Azure e consuma esses dados em formação.

Na **1 parte desta série tutorial,** você:

> [!div class="checklist"]
> * Instalar o Azure Machine Learning SDK
> * Configurar estrutura de diretório para código
> * Criar uma área de trabalho do Azure Machine Learning
> * Configure o seu ambiente de desenvolvimento local
> * Criar um cluster de computação

>[!NOTE]
> Esta série tutorial centra-se nos conceitos de Aprendizagem automática Azure, adequados a tarefas de aprendizagem automática __baseadas em trabalhos__ python que são computacionalmente intensivas e/ou requerem reprodutibilidade. Se as suas tarefas de aprendizagem automática não encaixarem neste perfil, utilize a [funcionalidade Jupyter ou RStudio num caso de aprendizagem automática Azure](tutorial-1st-experiment-sdk-setup.md) para embarcar no Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente [a Azure Machine Learning](https://aka.ms/AMLFree) hoje.
- Familiaridade com [conceitos](concept-azure-machine-learning-architecture.md)de Python e Machine Learning. Por exemplo, ambientes, treinos, pontuação, e assim por diante.
- Um ambiente de desenvolvimento local - um portátil com Python instalado e o seu IDE favorito (por exemplo: VSCode, Pycharm, Jupyter, etc.

## <a name="install-the-azure-machine-learning-sdk"></a>Instalar o Azure Machine Learning SDK

Ao longo deste tutorial, fazemos uso do Azure Machine Learning Python SDK.

Você pode usar as ferramentas mais familiares para você - por exemplo: conda, pip, e assim por diante - para configurar um ambiente para usar durante todo este tutorial. Instale no ambiente o Azure Machine Learning Python SDK via pip:

```bash
pip install azureml-sdk
```

## <a name="create-directory-structure-for-code"></a>Criar estrutura de diretório para código
Recomendamos que tenha o seguinte diretório simples configurado para este tutorial:

```markdown
tutorial
└──.azureml
```

- **tutorial** (diretório de alto nível do projeto)
- **.azureml** (subdireorito oculto do tutorial): O `.azureml` diretório é utilizado para armazenar ficheiros de configuração de aprendizagem automática Azure.

## <a name="create-an-azure-machine-learning-workspace"></a>Criar um espaço de trabalho para aprendizagem de máquinas Azure

Um espaço de trabalho é um recurso de alto nível para a Azure Machine Learning e é um lugar centralizado para:

- Gerir recursos como o cálculo
- Armazenar ativos como Cadernos, Ambientes, Conjuntos de Dados, Pipelines, Modelos, Pontos Finais, e assim por diante
- Colaborar com outros membros da equipa

No diretório dos pais de topo - `tutorial` adicione um novo ficheiro Python chamado com o código `01-create-workspace.py` abaixo. Adapte os parâmetros (nome, ID de subscrição, grupo de recursos e [localização](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service) com as suas preferências.

Pode executar o código numa sessão interativa ou como um ficheiro python.

>[!NOTE]
> Ao utilizar um ambiente de desenvolvimento local (por exemplo, portátil) será solicitado que autente para autenticar no seu espaço de trabalho utilizando um código de *dispositivo* na primeira vez que executar o código abaixo. Siga as instruções no ecrã.

```python
# tutorial/01-create-workspace.py
from azureml.core import Workspace

ws = Workspace.create(name='<my_workspace_name>', # provide a name for your workspace
                      subscription_id='<azure-subscription-id>', # provide your subscription ID
                      resource_group='<myresourcegroup>', # provide a resource group name
                      create_resource_group=True,
                      location='<NAME_OF_REGION>') # For example: 'westeurope' or 'eastus2' or 'westus2' or 'southeastasia'.

# write out the workspace details to a configuration file: .azureml/config.json
ws.write_config(path='.azureml')
```

Executar este código a partir do `tutorial` diretório:

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

Uma vez executado o corte de código acima, a sua estrutura de pasta será semelhante:

```markdown
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
```

O ficheiro `.azureml/config.json` contém os metadados necessários para ligar ao seu espaço de trabalho Azure Machine Learning - nomeadamente o seu ID de subscrição, grupo de recursos e nome do espaço de trabalho. 

> [!NOTE]
> O conteúdo `config.json` de não são segredos - é perfeitamente bom partilhar estes detalhes.
> A autenticação ainda é necessária para interagir com o seu espaço de trabalho de Aprendizagem automática Azure.

## <a name="create-an-azure-machine-learning-compute-cluster"></a>Criar um cluster de cálculo de aprendizagem automática Azure

Crie uma escrita python no `tutorial` diretório de nível superior chamado `02-create-compute.py` e povoe com o seguinte código para criar um cluster de cálculo Azure Machine Learning que irá escalar automaticamente entre zero e quatro nós:

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # this automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=4, 
                                                            idle_seconds_before_scaledown=2400)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

Executar o ficheiro python:

```bash
python ./02-create-compute.py
```


> [!NOTE]
> Quando o cluster for criado, terá 0 nós a provisionados. Portanto, o cluster **não** incorre em custos até que você submeta um emprego. Este cluster diminuirá quando estiver inativo durante 2400 segundos (40 minutos).

A sua estrutura de pastas será agora a seguinte:

```bash
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
└──02-create-compute.py
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial de configuração tem:

- Criou um espaço de trabalho para aprendizagem de máquinas Azure
- Configurar o seu ambiente de desenvolvimento local
- Criei um cluster de cálculo de aprendizagem automática Azure.

No próximo tutorial, você anda através da submissão de um script para o cluster de cálculo de aprendizagem automática Azure.

> [!div class="nextstepaction"]
> [Tutorial: Executar "Hello World" Python Script em Azure](tutorial-1st-experiment-hello-world.md)
