---
title: 'Tutorial: criar seu primeiro experimento do ML: instalação'
titleSuffix: Azure Machine Learning
description: Neste tutorial, você começará a usar o SDK Azure Machine Learning Python em execução em notebooks Jupyter.  Na parte 1, você cria um espaço de trabalho no qual você gerenciará experimentos e modelos de ML.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/25/2019
ms.openlocfilehash: fc26b224a2af6ab4f1f6bf5551381d4739831351
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053874"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Tutorial: introdução à criação de seu primeiro experimento do ML com o SDK do Python

Neste tutorial, você concluirá as etapas de ponta a ponta para começar a usar o SDK Azure Machine Learning Python em execução em notebooks Jupyter. Este tutorial é a **parte um de uma série de tutoriais de duas partes**e aborda a configuração e instalação do ambiente Python, bem como a criação de um espaço de trabalho para gerenciar seus experimentos e modelos de aprendizado de máquina. A [**parte dois**](tutorial-1st-experiment-sdk-train.md) baseia isso para treinar vários modelos de aprendizado de máquina e apresentar o processo de gerenciamento de modelos usando o portal do Azure e o SDK.

Neste tutorial:

> [!div class="checklist"]
> * Crie um [Workspace do Azure Machine Learning](concept-workspace.md) a ser usado no próximo tutorial.
> * Clone o notebook tutoriais em sua pasta no espaço de trabalho.
> * Crie uma VM do Jupyter Notebook baseada em nuvem com o SDK do Python Azure Machine Learning instalado e pré-configurado.

Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje.

## <a name="create-a-workspace"></a>Criar áreas de trabalho

Um espaço de trabalho Azure Machine Learning é um recurso fundamental na nuvem que você usa para experimentar, treinar e implantar modelos de aprendizado de máquina. Ele vincula sua assinatura do Azure e o grupo de recursos a um objeto facilmente consumido no serviço. 

Você cria um espaço de trabalho por meio do portal do Azure, um console baseado na Web para gerenciar seus recursos do Azure. 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Anote seu espaço de **trabalho** e sua **assinatura**. Você precisará delas para garantir que crie seu experimento no lugar certo. 


## <a name="azure"></a>Clonar uma pasta do bloco de anotações

Este exemplo usa o servidor de bloco de anotações de nuvem em seu espaço de trabalho para uma experiência de instalação desconfigurada e gratuita. Use [seu próprio ambiente](how-to-configure-environment.md#local) se preferir ter controle sobre seu ambiente, pacotes e dependências.

Você conclui as seguintes etapas de configuração e execução de experimento na página de aterrissagem do espaço de trabalho (versão prévia), uma interface consolidada que inclui ferramentas de aprendizado de máquina para executar cenários de ciência de dados para os profissionais de ciência de dados de todos os níveis de habilidade.

1. Entre na página de [aterrissagem do espaço de trabalho](https://ml.azure.com/).

1. Selecione sua assinatura e o espaço de trabalho que você criou.

1. Selecione **blocos de anotações e arquivos** à esquerda.

1. Abra a pasta de **exemplos** .

1. Selecione o **"..."** à direita da pasta **tutoriais** e, em seguida, selecione **clonar**.

    ![Clonar pasta](media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png)

1. Há uma pasta exibida para cada usuário que acessa o espaço de trabalho.  Selecione a pasta para clonar a pasta do **tutorial** .

## <a name="a-nameopenselect-a-vm-to-run-the-notebook"></a><a name="open">selecionar uma VM para executar o bloco de anotações

1. Em **arquivos de usuário** , abra a pasta e abra a pasta **tutoriais** clonados.

    ![Abrir pasta de tutoriais](media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png)

    > [!IMPORTANT]
    > Você pode exibir blocos de anotações na pasta de **exemplos** , mas não pode executar um bloco de anotações a partir daí.  Para executar um notebook, abra a versão clonada do bloco de anotações na seção **arquivos do usuário** .
    
1. Selecione o arquivo **tutorial-1º-experimento-SDK-Train. ipynb** na sua pasta **tutoriais** .

1. Na barra superior, selecione uma VM de notebook a ser usada para executar o bloco de anotações. Essas VMs são pré-configuradas com tudo o que você precisa para executar Azure Machine Learning. Você pode selecionar uma VM criada por qualquer usuário do seu espaço de trabalho. 

1. Se nenhuma máquina virtual for encontrada, selecione **+ nova VM** para criar a VM.

    ![Criar uma VM](media/tutorial-1st-experiment-sdk-setup/no-vm.png)

    1. Ao criar uma VM, forneça um nome.  O nome deve ter entre 2 e 16 caracteres. Os caracteres válidos são letras, dígitos e caractere e também devem ser exclusivos em sua assinatura do Azure.

    1. Em seguida, selecione **Criar**. Pode levar aproximadamente 5 minutos para configurar sua VM.

1. Depois que a VM estiver disponível, ela será exibida na barra de ferramentas superior.  Agora você pode executar o bloco de anotações usando **executar tudo** na barra de ferramentas ou usando **Shift + Enter** nas células de código do bloco de anotações.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você concluiu estas tarefas:

* Criou um espaço de trabalho Azure Machine Learning.
* Criou e configurou um servidor de bloco de anotações de nuvem em seu espaço de trabalho.

Na **parte dois** do tutorial, você executa o código em `tutorial-1st-experiment-sdk-train.ipynb` para treinar um modelo de aprendizado de máquina. 

> [!div class="nextstepaction"]
> [Tutorial: treinar seu primeiro modelo](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Se você não planejar a seguinte parte 2 deste tutorial ou quaisquer outros tutoriais, deverá [interromper a VM do servidor do bloco de anotações de nuvem](tutorial-1st-experiment-sdk-train.md#clean-up-resources) quando não estiver usando-a para reduzir o custo.


