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
ms.date: 02/11/2021
ms.custom: devx-track-python
adobe-target: true
ms.openlocfilehash: 5d61f330c823aff755a25e637bb58c132aa58100
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369145"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-your-development-environment-part-1-of-4"></a>Tutorial: Começa com a Azure Machine Learning no teu ambiente de desenvolvimento (parte 1 de 4)

Nesta *série tutorial de quatro partes,* você vai aprender os fundamentos do Azure Machine Learning e completar tarefas de aprendizagem de máquinas Python baseadas em empregos na plataforma cloud Azure. 

Na primeira parte desta série tutorial, você:

> [!div class="checklist"]
> * Instale o Azure Machine Learning SDK.
> * Configurar a estrutura do diretório para código.
> * Criar um espaço de trabalho de aprendizagem automática Azure.
> * Configure o seu ambiente de desenvolvimento local.
> * Crie um cluster de computação.

> [!NOTE]
> Esta série tutorial centra-se nos conceitos de Aprendizagem automática Azure necessários para submeter **trabalhos** em lote - é aqui que o código é submetido à nuvem para ser executado em segundo plano sem qualquer interação do utilizador. Isto é útil para scripts ou códigos acabados que deseja executar repetidamente, ou para tarefas de aprendizagem automática intensiva de computação. Se estiver mais interessado num fluxo de trabalho exploratório, pode utilizar [o Jupyter ou o RStudio num caso de aprendizagem automática Azure](tutorial-1st-experiment-sdk-setup.md).

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma conta gratuita antes de começar. Experimente [a aprendizagem automática Azure.](https://aka.ms/AMLFree)
- [Anaconda](https://www.anaconda.com/download/) ou [Miniconda](https://www.anaconda.com/download/) para gerir ambientes virtuais Python e instalar pacotes.  
- Se não está familiarizado com o uso de conda, veja [Como começa com conda.](https://conda.io/projects/conda/en/latest/user-guide/getting-started.html)

## <a name="install-the-azure-machine-learning-sdk"></a>Instalar o Azure Machine Learning SDK

Ao longo deste tutorial, você usará o Azure Machine Learning SDK para Python. Para evitar problemas de dependência de Python, criará um ambiente isolado. Esta série tutorial usa conda para criar este ambiente. Se preferir utilizar outras soluções, tais como `venv` `virtualenv` , ou estivador, certifique-se de que utiliza uma versão Python >=3,5 e < 3.9.

Verifique se tem conda instalada no seu sistema:
    
```bash
conda --version
```
    
Se este comando retornar um `conda not found` erro, [descarregue e instale Miniconda](https://docs.conda.io/en/latest/miniconda.html). 

Depois de instalar a Conda, utilize um terminal ou uma janela De imediato Anaconda para criar um novo ambiente:

```bash
conda create -n tutorial python=3.8
```

Em seguida, instale o Azure Machine Learning SDK no ambiente conda que criou:

```bash
conda activate tutorial
pip install azureml-core
```
    
> [!NOTE]
> Leva aproximadamente 2 minutos para a instalação Azure Machine Learning SDK para ser concluída.
>
> Se tiver um erro de tempo limite, tente `pip install --default-timeout=100 azureml-core` em vez disso.


> [!div class="nextstepaction"]
> [Instalei o SDK](?success=install-sdk#dir) [e dei conta de um problema.](https://www.research.net/r/7C8Z3DN?issue=install-sdk)

## <a name="create-a-directory-structure-for-code"></a><a name="dir"></a>Criar uma estrutura de diretório para código

Recomendamos que crie a seguinte estrutura de diretório simples para este tutorial:

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-1.png" alt-text="estrutura de diretório: tutorial de nível superior com subdireção .azureml":::


- `tutorial`: Diretório de alto nível do projeto.
- `.azureml`: Subdiretório oculto para armazenar ficheiros de configuração de aprendizagem automática Azure.

Por exemplo, para criar isto numa janela de festa:

```bash
mkdir tutorial
cd tutorial
mkdir .azureml
```

> [!TIP]
> Para criar ou ver a estrutura numa janela gráfica, primeiro ativar a capacidade de ver e criar ficheiros e pastas ocultos:
>
> * Numa janela Mac Finder use **Command + Shift + .** para alternar a exibição de ficheiros/pastas ocultos.  
> * Num Explorador de Ficheiros Windows 10, veja [como visualizar ficheiros e pastas ocultos](https://support.microsoft.com/en-us/windows/view-hidden-files-and-folders-in-windows-10-97fbc472-c603-9d90-91d0-1166d1d9f4b5). 
> * Na Interface Gráfica Linux, utilize **ctrl + h** ou o menu **Ver** e verifique a caixa para **mostrar ficheiros ocultos**.




> [!div class="nextstepaction"]
> [Criei um diretório](?success=create-dir#workspace) [que encontrei um problema.](https://www.research.net/r/7C8Z3DN?issue=create-dir)

## <a name="create-an-azure-machine-learning-workspace"></a><a name="workspace"></a>Criar uma área de trabalho do Azure Machine Learning

Um espaço de trabalho é um recurso de alto nível para a Azure Machine Learning e é um lugar centralizado para:

- Gerir recursos como o cálculo.
- Armazenar ativos como cadernos, ambientes, conjuntos de dados, oleodutos, modelos e pontos finais.
- Colaborar com outros membros da equipa.

No diretório de alto nível, `tutorial` adicione um novo ficheiro Python chamado `01-create-workspace.py` usando o seguinte código. Adapte os parâmetros (nome, ID de subscrição, grupo de recursos e [localização)](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)com as suas preferências.

Pode executar o código numa sessão interativa ou como um ficheiro Python.

>[!NOTE]
> Quando estiver a utilizar um ambiente de desenvolvimento local (por exemplo, o seu computador), será solicitado que autente para autenticar no seu espaço de trabalho utilizando um *código de dispositivo* na primeira vez que executar o seguinte código. Siga as instruções no ecrã.

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

Na janela que tem o ambiente *tutorial1 conda* ativado, execute este código a partir do `tutorial` diretório.

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

> [!TIP]
> Se executar este código lhe dá um erro que não tem acesso à subscrição, consulte [Criar um espaço de trabalho](how-to-manage-workspace.md?tab=python#create-multi-tenant) para obter informações sobre opções de autenticação.


Depois de ter executado com sucesso *01-create-workspace.py,* a sua estrutura de pasta será:

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-2.png" alt-text="O ficheiro config.jsaparece na subdiretório .azureml após a execução 01-create-workspace.py":::

O ficheiro `.azureml/config.json` contém os metadados necessários para ligar ao seu espaço de trabalho de aprendizagem de máquinas Azure. Ou seja, contém o seu ID de subscrição, grupo de recursos e nome do espaço de trabalho. 

> [!NOTE]
> O conteúdo `config.json` não são segredos. É bom partilhar estes detalhes.
>
> A autenticação ainda é necessária para interagir com o seu espaço de trabalho de Aprendizagem automática Azure.

> [!div class="nextstepaction"]
> [Criei um espaço de trabalho](?success=create-workspace#cluster) que [deparou-me com um problema.](https://www.research.net/r/7C8Z3DN?issue=create-workspace)

## <a name="create-an-azure-machine-learning-compute-cluster"></a><a name="cluster"></a> Criar um cluster de cálculo de aprendizagem automática Azure

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
                                                           idle_seconds_before_scaledown=2400,
                                                           min_nodes=0,
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

Na janela que tem o ambiente *de conda tutorial1* ativado, execute o ficheiro Python:

```bash
python ./02-create-compute.py
```


> [!NOTE]
> Quando o cluster for criado, terá 0 nós a provisionados. O cluster *não* incorre em custos até que você apresente um emprego. Este cluster diminuirá quando estiver inativo durante 2.400 segundos (40 minutos).

A sua estrutura de pastas será agora a seguinte:

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-3.png" alt-text="Adicione 02-create-compute.py ao diretório tutorial":::

> [!div class="nextstepaction"]
> [Criei um cluster de computação](?success=create-compute-cluster#next-steps) [que deparei com um problema](https://www.research.net/r/7C8Z3DN?issue=create-compute-cluster)

## <a name="view-in-the-studio"></a>Vista no estúdio

Inscreva-se no [estúdio Azure Machine Learning](https://ml.azure.com) para ver o espaço de trabalho e a instância de computação que criou.

1. Selecione a **Subscrição** utilizada para criar o espaço de trabalho.
1. Selecione o **espaço de trabalho machine learning** que criou, *tutorial-ws*.
1. Uma vez que o espaço de trabalho se carregue, no lado esquerdo, **selecione Compute**.
1. No topo, selecione o **separador de clusters Compute.**

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/compute-instance-in-studio.png" alt-text="Screenshot: Veja a instância de cálculo no seu espaço de trabalho.":::

Esta vista mostra o aglomerado de computação aprovisionado, juntamente com o número de nós ociosos, nós ocupados e nós não protegidos.  Como ainda não usaste o cluster, todos os nós não estão atualmente não divulgados.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial de configuração, você tem:

- Criou um espaço de trabalho de Aprendizagem automática Azure.
- Crie o seu ambiente de desenvolvimento local.
- Criei um cluster de cálculo de aprendizagem automática Azure.

Nas outras partes deste tutorial aprenderá:

* Parte 2. Executar o código na nuvem utilizando o Azure Machine Learning SDK para Python.
* Parte 3. Gerencie o ambiente Python que usa para o treino de modelos.
* Parte 4. Faça upload de dados para a Azure e consuma esses dados em formação.

Continue até ao próximo tutorial, para passar por submeter um script ao cluster de cálculo de Aprendizagem automática Azure.

> [!div class="nextstepaction"]
> [Tutorial: Executar um "Olá mundo!" Escrita python em Azure](tutorial-1st-experiment-hello-world.md)
