---
title: 'Tutorial: Configure o ambiente e o espaço de trabalho.'
titleSuffix: Azure Machine Learning service
description: Nesta série de tutoriais, você conclui as etapas de ponta a ponta para começar a usar o SDK Azure Machine Learning Python em execução em notebooks Jupyter.  A parte 1 aborda a criação de um ambiente de servidor de notebook de nuvem, bem como a criação de um espaço de trabalho para gerenciar seus experimentos e modelos de Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 07/20/2019
ms.openlocfilehash: 70fee90fb82618a409d2566a3235ad8ca42e1760
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934421"
---
# <a name="tutorial-set-up-environment-and-workspace"></a>Tutorial: Configurar ambiente e espaço de trabalho

Neste tutorial, você concluirá as etapas de ponta a ponta para começar a usar o SDK Azure Machine Learning Python em execução em notebooks Jupyter. Este tutorial é a **parte um de uma série de tutoriais de duas partes**e aborda a configuração e instalação do ambiente Python, bem como a criação de um espaço de trabalho para gerenciar seus experimentos e modelos de aprendizado de máquina. A [**parte dois**](tutorial-1st-experiment-sdk-train.md) baseia isso para treinar vários modelos de aprendizado de máquina e apresentar o processo de gerenciamento de modelos usando o portal do Azure e o SDK.

Neste tutorial:

> [!div class="checklist"]
> * Crie um espaço de trabalho do Machine Learning para usar no próximo tutorial.
> * Crie uma VM do Jupyter Notebook baseada em nuvem com o SDK do Python Azure Machine Learning instalado e pré-configurado.

## <a name="prerequisites"></a>Pré-requisitos

O único pré-requisito para este tutorial é uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do serviço de Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

## <a name="create-a-workspace"></a>Criar uma área de trabalho

Um espaço de trabalho é um recurso fundamental na nuvem que você usa para experimentar, treinar e implantar modelos de aprendizado de máquina. Ele vincula sua assinatura do Azure e o grupo de recursos a um objeto facilmente consumido no SDK. Se você já tiver um espaço de trabalho de serviço do Azure Machine Learning, pule para a [próxima seção](#azure). Caso contrário, crie um agora.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="azure"></a>Criar um servidor de notebook de nuvem

Este exemplo usa o servidor de bloco de anotações de nuvem em seu espaço de trabalho para uma experiência de instalação desconfigurada e gratuita. Use [seu próprio ambiente](how-to-configure-environment.md#local) se preferir ter controle sobre seu ambiente, pacotes e dependências.

No seu espaço de trabalho, você cria um recurso de nuvem para começar a usar o Jupyter notebooks. Esse recurso é uma máquina virtual Linux baseada em nuvem pré-configurada com tudo o que você precisa para executar Azure Machine Learning serviço.

1. Abra seu espaço de trabalho no [portal do Azure](https://portal.azure.com/).  Se você não tiver certeza de como localizar seu espaço de trabalho no portal, consulte como [localizar seu espaço de trabalho](how-to-manage-workspace.md#view).

1. Na página do seu espaço de trabalho no portal do Azure, selecione **VMs do notebook** à esquerda.

1. Selecione **+ novo** para criar uma VM de notebook.

     ![Selecionar nova VM](./media/tutorial-1st-experiment-sdk-setup/add-workstation.png)

1. Forneça um nome para sua VM. Em seguida, selecione **Criar**.

    > [!NOTE]
    > O nome da VM do seu bloco de anotações deve ter entre 2 e 16 caracteres. Os caracteres válidos são letras, dígitos e caractere.  O nome também deve ser exclusivo em toda a sua assinatura do Azure.

1. Aguarde até que o status seja alterado para **em execução**.

### <a name="launch-jupyter-web-interface"></a>Iniciar interface da Web do Jupyter

Depois que a VM estiver em execução, use a seção de **VMs do notebook** para iniciar a interface da Web do Jupyter.

1. Selecione **Jupyter** na coluna **URI** para sua VM.

    ![Iniciar o servidor do Jupyter Notebook](./media/tutorial-1st-experiment-sdk-setup/start-server.png)

    O link inicia o servidor do bloco de anotações e abre a página da Web do Jupyter notebook em uma nova guia do navegador.  Esse link funcionará apenas para a pessoa que cria a VM. Cada usuário do espaço de trabalho deve criar sua própria VM.

1. Na página da Web do bloco de anotações do Jupyter, o Top nome_da_pasta é seu nome de usuário.  Selecione esta pasta.

    > [!TIP]
    > Essa pasta está localizada no [contêiner de armazenamento](concept-workspace.md#resources) em seu espaço de trabalho em vez de na própria VM do bloco de anotações.  Você pode excluir a VM do notebook e ainda manter todo o seu trabalho.  Quando você criar uma nova VM de bloco de anotações mais tarde, ela carregará essa mesma pasta. Se você compartilhar seu espaço de trabalho com outras pessoas, ele verá sua pasta e você verá seus.

1. Abra o `samples-*` subdiretório e, em seguida `tutorials/tutorial-1st-experiment-sdk-train.ipynb` , abra para executar a **parte dois** do tutorial.

## <a name="end"></a>Limpar recursos

Não conclua esta seção se você planeja continuar na **parte 2** do tutorial.

### <a name="stop-the-notebook-vm"></a>Parar a VM do notebook

Se você usou um servidor de bloco de anotações de nuvem, pare a VM quando não a estiver usando para reduzir o custo.

1. Em seu espaço de trabalho, selecione **máquinas virtuais do notebook**.

   ![Parar o servidor de VM](./media/tutorial-1st-experiment-sdk-setup/stop-server.png)

1. Na lista, selecione a VM.

1. Selecione **parar**.

1. Quando você estiver pronto para usar o servidor novamente, selecione **Iniciar**.

### <a name="delete-everything"></a>Excluir tudo

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Você também pode manter o grupo de recursos, mas excluir um único espaço de trabalho. Exiba as propriedades do espaço de trabalho e selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você concluiu estas tarefas:

* Criou um espaço de trabalho de serviço Azure Machine Learning.
* Criou e configurou um servidor de bloco de anotações de nuvem em seu espaço de trabalho.

Continue com a **parte 2** deste tutorial para treinar um modelo de aprendizado de máquina simples.

> [!div class="nextstepaction"]
> [Tutorial: Treinar seu primeiro modelo](tutorial-1st-experiment-sdk-train.md)
