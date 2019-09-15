---
title: 'Tutorial: Crie seu primeiro experimento de ML: Configurar'
titleSuffix: Azure Machine Learning
description: Nesta série de tutoriais, você conclui as etapas de ponta a ponta para começar a usar o SDK Azure Machine Learning Python em execução em notebooks Jupyter.  A parte 1 aborda a criação de um ambiente de servidor de notebook de nuvem, bem como a criação de um espaço de trabalho para gerenciar seus experimentos e modelos de Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 08/28/2019
ms.openlocfilehash: 9bc5b9688a8cd568b47fe2dad88d6d007ceca0c4
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71004065"
---
# <a name="tutorial-get-started-creating-your-first-ml-experiment-with-the-python-sdk"></a>Tutorial: Introdução à criação de seu primeiro experimento do ML com o SDK do Python

Neste tutorial, você concluirá as etapas de ponta a ponta para começar a usar o SDK Azure Machine Learning Python em execução em notebooks Jupyter. Este tutorial é a **parte um de uma série de tutoriais de duas partes**e aborda a configuração e instalação do ambiente Python, bem como a criação de um espaço de trabalho para gerenciar seus experimentos e modelos de aprendizado de máquina. A [**parte dois**](tutorial-1st-experiment-sdk-train.md) baseia isso para treinar vários modelos de aprendizado de máquina e apresentar o processo de gerenciamento de modelos usando o portal do Azure e o SDK.

Neste tutorial:

> [!div class="checklist"]
> * Crie um [Workspace do Azure Machine Learning](concept-workspace.md) a ser usado no próximo tutorial.
> * Crie uma VM do Jupyter Notebook baseada em nuvem com o SDK do Python Azure Machine Learning instalado e pré-configurado.

Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree) hoje.

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Um espaço de trabalho Azure Machine Learning é um recurso fundamental na nuvem que você usa para experimentar, treinar e implantar modelos de aprendizado de máquina. Ele vincula sua assinatura do Azure e o grupo de recursos a um objeto facilmente consumido no SDK. Se você já tiver um espaço de trabalho Azure Machine Learning, pule para a [próxima seção](#azure). Caso contrário, crie um agora.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="azure"></a>Criar um servidor de notebook de nuvem

Este exemplo usa o servidor de bloco de anotações de nuvem em seu espaço de trabalho para uma experiência de instalação desconfigurada e gratuita. Use [seu próprio ambiente](how-to-configure-environment.md#local) se preferir ter controle sobre seu ambiente, pacotes e dependências.

No seu espaço de trabalho, você cria um recurso de nuvem para começar a usar o Jupyter notebooks. Esse recurso é uma máquina virtual Linux baseada em nuvem pré-configurada com tudo o que você precisa para executar Azure Machine Learning.

1. Abra seu espaço de trabalho no [portal do Azure](https://portal.azure.com/).  Se você não tiver certeza de como localizar seu espaço de trabalho no portal, consulte como [localizar seu espaço de trabalho](how-to-manage-workspace.md#view).

1. Na página do seu espaço de trabalho no portal do Azure, selecione **VMs do notebook** à esquerda.

1. Selecione **+ novo** para criar uma VM de notebook.

     ![Selecionar nova VM](./media/tutorial-1st-experiment-sdk-setup/add-workstation.png)

1. Forneça um nome para sua VM. 
   + O nome da VM do seu bloco de anotações deve ter entre 2 e 16 caracteres. Os caracteres válidos são letras, dígitos e caractere.  
   + O nome também deve ser exclusivo em toda a sua assinatura do Azure.

1. Em seguida, selecione **Criar**. Pode levar um tempo para configurar sua VM.

1. Aguarde até que o status seja alterado para **em execução**.
   Depois que a VM estiver em execução, use a seção de **VMs do notebook** para iniciar a interface da Web do Jupyter.

1. Selecione **Jupyter** na coluna **URI** para sua VM.

    ![Iniciar o servidor do Jupyter Notebook](./media/tutorial-1st-experiment-sdk-setup/start-server.png)

   O link inicia o servidor do bloco de anotações e abre a página da Web do Jupyter notebook em uma nova guia do navegador.  Esse link funcionará apenas para a pessoa que cria a VM. Cada usuário do espaço de trabalho deve criar sua própria VM.


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você concluiu estas tarefas:

* Criou um espaço de trabalho Azure Machine Learning.
* Criou e configurou um servidor de bloco de anotações de nuvem em seu espaço de trabalho.

Na **parte dois** do tutorial, você executa o código `tutorial-1st-experiment-sdk-train.ipynb` para treinar um modelo de aprendizado de máquina. 

> [!div class="nextstepaction"]
> [Tutorial: Treinar seu primeiro modelo](tutorial-1st-experiment-sdk-train.md)

> [!IMPORTANT]
> Se você não planejar a seguinte parte 2 deste tutorial ou quaisquer outros tutoriais, deverá [interromper a VM do servidor do bloco de anotações de nuvem](tutorial-1st-experiment-sdk-train.md#clean-up-resources) quando não estiver usando-a para reduzir o custo.


