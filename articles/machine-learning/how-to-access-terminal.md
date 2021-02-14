---
title: Como aceder a um terminal de instância computacional no seu espaço de trabalho
titleSuffix: Azure Machine Learning
description: Utilize o terminal numa instância de cálculo para operações de Git, para instalar pacotes e adicionar núcleos.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 02/05/2021
ms.openlocfilehash: e42d6d53e4d06e5ec7a33ea4b8ca18233dcf1dd7
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100101566"
---
# <a name="access-a-compute-instance-terminal-in-your-workspace"></a>Aceda a um terminal de instância computacional no seu espaço de trabalho

Aceda ao terminal de uma instância computacional no seu espaço de trabalho para:

* Utilize ficheiros de Git e ficheiros de versão. Estes ficheiros são armazenados no seu sistema de ficheiros do espaço de trabalho, não se limitando a uma única instância de computação.
* Instale pacotes na instância de cálculo.
* Crie núcleos extras na instância de computação.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.
* Um espaço de trabalho de aprendizagem automática. Ver [Criar um espaço de trabalho de aprendizagem de máquinas Azure](how-to-manage-workspace.md).

## <a name="access-a-terminal"></a>Aceda a um terminal

Para aceder ao terminal:

1. Abra o seu espaço de trabalho no [estúdio Azure Machine Learning.](https://ml.azure.com)
1. Do lado esquerdo, selecione **Cadernos**.
1. Selecione a imagem **do terminal Open.**

    :::image type="content" source="media/how-to-use-terminal/open-terminal-window.png" alt-text="Janela de terminais aberto":::

1. Quando uma instância computacional está em funcionamento, aparece a janela do terminal para essa instância de computação.
1. Quando não estiver a decorrer nenhuma instância de computação, utilize a secção **Compute** no direito de iniciar ou criar uma instância de computação.
    :::image type="content" source="media/how-to-use-terminal/start-or-create-compute.png" alt-text="Iniciar ou criar uma instância computacional":::

Além dos passos acima, também pode aceder ao terminal a partir de:

* RStudio: Selecione o **separador Terminal** em cima à esquerda.
* Jupyter Lab: Selecione o **azulejo terminal** sob o **outro** título no separador Launcher.
* Jupyter: Selecione **Novo Terminal de>** no topo direito no separador Ficheiros.
* SSH para a máquina, se ativar o acesso ao SSH quando a instância de computação foi criada.

## <a name="copy-and-paste-in-the-terminal"></a>Copiar e colar no terminal

> * Janelas: `Ctrl-Insert` copiar e utilizar ou `Ctrl-Shift-v` `Shift-Insert` colar.
> * Mac OS: `Cmd-c` copiar e `Cmd-v` colar.
> * O FireFox/IE pode não suportar adequadamente as permissões de prancheta.

## <a name="use-files-from-git-and-version-files"></a><a name=git></a> Utilize ficheiros de Git e ficheiros de versão

Aceda a todas as operações do Git a partir do terminal. Todos os ficheiros e pastas Git serão armazenados no seu sistema de ficheiros do espaço de trabalho. Este armazenamento permite-lhe utilizar estes ficheiros a partir de qualquer instância de computação no seu espaço de trabalho.

> [!NOTE]
> Adicione os seus ficheiros e pastas em qualquer lugar sob a pasta **~/cloudfiles/código/Utilizadores** para que fiquem visíveis em todos os seus ambientes Jupyter.

Saiba mais sobre [a clonagem de repositórios de Git no seu sistema de ficheiros de espaço de trabalho.](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system)

## <a name="install-packages"></a>Instalar pacotes

 Instale pacotes a partir de uma janela do terminal. Instale pacotes Python no ambiente **Python 3.6 - AzureML.**  Instale as embalagens R no ambiente **R.**

Ou pode instalar pacotes diretamente no Jupyter Notebook ou no RStudio:

* RStudio Utilize o **separador Pacotes** no canto inferior direito ou o **separador Consola** na parte superior esquerda.  
* Python: Adicione código de instalação e execute numa célula Jupyter Notebook.

> [!NOTE]
> Para a gestão de pacotes dentro de um caderno, utilize funções mágicas **%pip** ou **%conda** para instalar automaticamente pacotes no **núcleo atualmente em funcionamento**, em vez de **!pip** ou **!conda,** que se refere a todas as embalagens (incluindo pacotes fora do núcleo atualmente em funcionamento)

## <a name="add-new-kernels"></a>Adicione novos núcleos

> [!WARNING]
>  Ao personalizar a instância computacional, certifique-se de que não apaga o ambiente **conda azureml_py36** ou o núcleo **Python 3.6 - AzureML.** Isto é necessário para a funcionalidade Jupyter/JupyterLab

Para adicionar um novo núcleo Jupyter à instância computacional:

1. Utilize a janela do terminal para criar um novo ambiente.  Por exemplo, o código abaixo `newenv` cria:

    ```shell
    conda create --name newenv
    ```

1. Ative o ambiente.  Por exemplo, depois de `newenv` criar:

    ```shell
    conda activate newenv
    ```

1. Instale o pacote pip e ipykernel para o novo ambiente e crie um núcleo para esse conda env

    ```shell
    conda install pip
    conda install ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

Qualquer um dos [Jupyter Kernels disponíveis](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) pode ser instalado.

## <a name="manage-terminal-sessions"></a>Gerir sessões terminais

 Selecione **Ver sessões ativas** na barra de ferramentas do terminal para ver uma lista de todas as sessões de terminais ativas. Quando não houver sessões ativas, este separador será desativado.

Feche quaisquer sessões não usedusadas para preservar os recursos do seu caso de computação.