---
title: 'Tutorial: Começar com machine learning - Python'
titleSuffix: Azure Machine Learning
description: Neste tutorial, você vai começar com o Azure Machine Learning SDK para Python correndo em seu ambiente de desenvolvimento pessoal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: c0fe3c3808709de732bec8ce0599d380094405e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91368486"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-your-development-environment-part-1-of-4"></a>Tutorial: Começa com a Azure Machine Learning no teu ambiente de desenvolvimento (parte 1 de 4)

Nesta *série tutorial de quatro partes,* você vai aprender os fundamentos do Azure Machine Learning e completar tarefas de aprendizagem de máquinas Python baseadas em empregos na plataforma cloud Azure. Estas tarefas incluem:

1. Crie um espaço de trabalho e o seu ambiente de desenvolvimento local para machine learning.
2. Executar o código na nuvem utilizando o Azure Machine Learning SDK para Python.
3. Gerencie o ambiente Python que usa para o treino de modelos.
4. Faça upload de dados para a Azure e consuma esses dados em formação.

Na primeira parte desta série tutorial, você:

> [!div class="checklist"]
> * Instale o Azure Machine Learning SDK.
> * Configurar a estrutura do diretório para código.
> * Criar um espaço de trabalho de aprendizagem automática Azure.
> * Configure o seu ambiente de desenvolvimento local.
> * Crie um cluster de computação.

>[!NOTE]
> Esta série tutorial centra-se nos conceitos de Aprendizagem automática Azure, adequados a tarefas de aprendizagem automática *baseadas em trabalhos* python que são computacionalmente intensivas e/ou requerem reprodutibilidade. Se as suas tarefas de aprendizagem automática não encaixarem neste perfil, utilize a [funcionalidade Jupyter ou RStudio numa instância de aprendizagem automática Azure](tutorial-1st-experiment-sdk-setup.md) para se mudar para a Azure Machine Learning.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente [a aprendizagem automática Azure.](https://aka.ms/AMLFree)
- Familiaridade com [conceitos](concept-azure-machine-learning-architecture.md)de Python e Machine Learning. Exemplos incluem ambientes, treino e pontuação.
- Um ambiente de desenvolvimento local: um portátil com Python instalado e o seu IDE favorito (por exemplo, Visual Studio Code, PyCharm ou Jupyter).

## <a name="install-the-azure-machine-learning-sdk"></a>Instalar o Azure Machine Learning SDK

Ao longo deste tutorial, fazemos uso do Azure Machine Learning SDK para Python.

Pode utilizar as ferramentas mais familiares para si (por exemplo, Conda e pip) para configurar um ambiente para utilizar ao longo deste tutorial. Instale no ambiente o Azure Machine Learning SDK para Python via pip:

```bash
pip install azureml-sdk
```

## <a name="create-a-directory-structure-for-code"></a>Criar uma estrutura de diretório para código
Recomendamos que crie a seguinte estrutura de diretório simples para este tutorial:

```markdown
tutorial
└──.azureml
```

- `tutorial`: Diretório de alto nível do projeto.
- `.azureml`: Subdiretório oculto para armazenar ficheiros de configuração de aprendizagem automática Azure.

## <a name="create-an-azure-machine-learning-workspace"></a>Criar uma área de trabalho do Azure Machine Learning

Um espaço de trabalho é um recurso de alto nível para a Azure Machine Learning e é um lugar centralizado para:

- Gerir recursos como o cálculo.
- Armazenar ativos como cadernos, ambientes, conjuntos de dados, oleodutos, modelos e pontos finais.
- Colaborar com outros membros da equipa.

No diretório de alto nível, `tutorial` adicione um novo ficheiro Python chamado `01-create-workspace.py` usando o seguinte código. Adapte os parâmetros (nome, ID de subscrição, grupo de recursos e [localização)](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)com as suas preferências.

Pode executar o código numa sessão interativa ou como um ficheiro Python.

>[!NOTE]
> Quando estiver a utilizar um ambiente de desenvolvimento local (por exemplo, portátil), será solicitado que autente para autenticar no seu espaço de trabalho utilizando um código de *dispositivo* na primeira vez que executar o seguinte código. Siga as instruções no ecrã.

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

Depois de executar o corte de código anterior, a sua estrutura de pasta será:

```markdown
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
```

O ficheiro `.azureml/config.json` contém os metadados necessários para ligar ao seu espaço de trabalho de aprendizagem de máquinas Azure. Ou seja, contém o seu ID de subscrição, grupo de recursos e nome do espaço de trabalho. 

> [!NOTE]
> O conteúdo `config.json` não são segredos. É bom partilhar estes detalhes.
>
> A autenticação ainda é necessária para interagir com o seu espaço de trabalho de Aprendizagem automática Azure.

## <a name="create-an-azure-machine-learning-compute-cluster"></a>Criar um cluster de cálculo de aprendizagem automática Azure

Crie um script Python no `tutorial` diretório de alto nível chamado `02-create-compute.py` . Preencha-o com o seguinte código para criar um cluster de cálculo Azure Machine Learning que irá autoescalizar entre zero e quatro nós:

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # This automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that the cluster does not exist already
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

Executar o ficheiro Python:

```bash
python ./02-create-compute.py
```


> [!NOTE]
> Quando o cluster for criado, terá 0 nós a provisionados. O cluster *não* incorre em custos até que você apresente um emprego. Este cluster diminuirá quando estiver inativo durante 2.400 segundos (40 minutos).

A sua estrutura de pastas será agora a seguinte:

```bash
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
└──02-create-compute.py
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial de configuração, você tem:

- Criou um espaço de trabalho de Aprendizagem automática Azure.
- Crie o seu ambiente de desenvolvimento local.
- Criei um cluster de cálculo de aprendizagem automática Azure.

No próximo tutorial, você anda através da submissão de um script para o cluster de cálculo de aprendizagem automática Azure.

> [!div class="nextstepaction"]
> [Tutorial: Executar um "Olá mundo!" Escrita python em Azure](tutorial-1st-experiment-hello-world.md)
