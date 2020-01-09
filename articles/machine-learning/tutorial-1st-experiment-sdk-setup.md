---
title: 'Tutorial: criar seu primeiro experimento do ML'
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
ms.openlocfilehash: 84fa6b1d7f48ac7aea44e693a7df8945ba180d29
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75658649"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Tutorial: introdução à criação de seu primeiro experimento do ML com o SDK do Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste tutorial, você concluirá as etapas de ponta a ponta para começar a usar o SDK Azure Machine Learning Python em execução em notebooks Jupyter. Este tutorial é a **parte um de uma série de tutoriais de duas partes**e aborda a configuração e instalação do ambiente Python, bem como a criação de um espaço de trabalho para gerenciar seus experimentos e modelos de aprendizado de máquina. A [**parte dois**](tutorial-1st-experiment-sdk-train.md) baseia isso para treinar vários modelos de aprendizado de máquina e apresentar o processo de gerenciamento de modelos usando o Azure Machine Learning Studio e o SDK.

Neste tutorial:

> [!div class="checklist"]
> * Crie um [Workspace do Azure Machine Learning](concept-workspace.md) a ser usado no próximo tutorial.
> * Clone o notebook tutoriais em sua pasta no espaço de trabalho.
> * Crie uma instância de computação baseada em nuvem com Azure Machine Learning SDK do Python instalado e pré-configurado.


Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje.

## <a name="create-a-workspace"></a>Criar áreas de trabalho

Um espaço de trabalho Azure Machine Learning é um recurso fundamental na nuvem que você usa para experimentar, treinar e implantar modelos de aprendizado de máquina. Ele vincula sua assinatura do Azure e o grupo de recursos a um objeto facilmente consumido no serviço. 

Você cria um espaço de trabalho por meio do portal do Azure, um console baseado na Web para gerenciar seus recursos do Azure. 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Anote seu espaço de **trabalho** e sua **assinatura**. Você precisará delas para garantir que crie seu experimento no lugar certo. 

## <a name="azure"></a>Executar o bloco de anotações em seu espaço de trabalho

Este tutorial usa o servidor de bloco de anotações de nuvem em seu espaço de trabalho para uma experiência de instalação desconfigurada e gratuita. Use [seu próprio ambiente](how-to-configure-environment.md#local) se preferir ter controle sobre seu ambiente, pacotes e dependências.

Acompanhe este vídeo ou use as etapas detalhadas abaixo para clonar e executar o tutorial do seu espaço de trabalho. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]



### <a name="clone-a-notebook-folder"></a>Clonar uma pasta do bloco de anotações

Você conclui as seguintes etapas de configuração e execução do experimento no Azure Machine Learning Studio, uma interface consolidada que inclui ferramentas de Machine Learning para executar cenários de ciência de dados para profissionais de ciência de dados de todos os níveis de habilidade.

1. Entre no [Azure Machine Learning Studio](https://ml.azure.com/).

1. Selecione sua assinatura e o espaço de trabalho que você criou.

1. Selecione **blocos de anotações** à esquerda.

1. Abra a pasta de **exemplos** .

1. Abra a pasta **Python** .

1. Abra a pasta com um número de versão.  Esse número representa a versão atual do SDK do Python.

1. Selecione o **"..."** à direita da pasta **tutoriais** e, em seguida, selecione **clonar**.

    ![Clonar pasta](./media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png)

1. Uma lista de pastas exibe a exibição de cada usuário que acessa o espaço de trabalho.  Selecione sua pasta para clonar a pasta **tutoriais** .

### <a name="a-nameopenopen-the-cloned-notebook"></a><a name="open">abrir o bloco de anotações clonado

1. Em **arquivos de usuário** , abra a pasta e abra a pasta **tutoriais** clonados.

    ![Abrir pasta de tutoriais](./media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png)

    > [!IMPORTANT]
    > Você pode exibir blocos de anotações na pasta de **exemplos** , mas não pode executar um bloco de anotações a partir daí.  Para executar um notebook, abra a versão clonada do bloco de anotações na seção **arquivos do usuário** .
    
1. Selecione o arquivo **tutorial-1º-experimento-SDK-Train. ipynb** na sua pasta **tutoriais** .

1. Na barra superior, selecione uma instância de computação a ser usada para executar o bloco de anotações. Essas VMs são pré-configuradas com tudo o [que você precisa para executar Azure Machine Learning](concept-compute-instance.md#contents). Você pode selecionar uma VM criada por qualquer usuário do seu espaço de trabalho. 

1. Se nenhuma máquina virtual for encontrada, selecione **+ Adicionar** para criar a VM da instância de computação. 

    1. Ao criar uma VM, forneça um nome.  O nome deve ter entre 2 e 16 caracteres. Os caracteres válidos são letras, dígitos e caractere e também devem ser exclusivos em sua assinatura do Azure.

    1.  Selecione o tamanho da máquina virtual nas opções disponíveis.

    1. Em seguida, selecione **Criar**. Pode levar aproximadamente 5 minutos para configurar sua VM.

1. Depois que a VM estiver disponível, ela será exibida na barra de ferramentas superior.  Agora você pode executar o bloco de anotações usando **executar tudo** na barra de ferramentas ou usando **Shift + Enter** nas células de código do bloco de anotações.

Se você tiver widgets personalizados ou preferir usar Jupyter/JupyterLab, selecione o menu suspenso **Jupyter** na extrema direita e, em seguida, selecione **Jupyter** ou **JupyterLab**. A nova janela do navegador será aberta.

> [!NOTE]
> As instâncias de computação (versão prévia) estão disponíveis no momento somente para espaços de trabalho com uma região de **EUA Central norte** ou **sul do Reino Unido**, com suporte para outras regiões em breve.
>Se o seu espaço de trabalho estiver em qualquer outra região, você poderá continuar a criar e usar uma [VM do bloco de anotações](concept-compute-instance.md#notebookvm) .  Você pode usar uma VM de bloco de anotações ou uma instância de computação para executar o bloco de anotações. Uma VM continuará em execução e incorrerá em custo, a menos que você a interrompa. Você pode interrompê-lo manualmente na guia **computação** no estúdio ou conferir a [postagem no blog](https://techcommunity.microsoft.com/t5/educator-developer-blog/azure-virtual-machine-auto-shutdown/ba-p/379342) sobre como habilitar o desligamento automático agendado para VMs do Azure.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você concluiu estas tarefas:

* Criou um espaço de trabalho Azure Machine Learning.
* Criou e configurou um servidor de bloco de anotações de nuvem em seu espaço de trabalho.

Na **parte dois** do tutorial, você executa o código em `tutorial-1st-experiment-sdk-train.ipynb` para treinar um modelo de aprendizado de máquina. 

> [!div class="nextstepaction"]
> [Tutorial: treinar seu primeiro modelo](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Se você não planejar a seguinte parte 2 deste tutorial ou quaisquer outros tutoriais, deverá [interromper a VM do servidor do bloco de anotações de nuvem](tutorial-1st-experiment-sdk-train.md#clean-up-resources) quando não estiver usando-a para reduzir o custo.


