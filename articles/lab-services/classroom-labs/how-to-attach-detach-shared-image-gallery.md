---
title: Anexar ou separar uma galeria de imagens partilhadas nos Serviços de Laboratório Azure [ Microsoft Docs
description: Este artigo descreve como anexar uma galeria de imagens partilhada a um laboratório de sala de aula em Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2020
ms.author: spelluru
ms.openlocfilehash: aef5cd13742c0265851f5ba2918d557b4e1026d0
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592649"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Anexar ou separar uma galeria de imagens partilhadas nos Serviços de Laboratório Azure
Este artigo mostra-lhe como anexar ou separar uma galeria de imagens partilhadas a uma conta de laboratório. 

## <a name="scenarios"></a>Cenários
Aqui estão os dois cenários suportados por esta funcionalidade: 

- Um administrador de uma conta de laboratório anexa uma galeria de imagens partilhada à conta do laboratório, e envia uma imagem para a galeria de imagens partilhada fora do contexto de um laboratório. Então, os criadores de laboratório podem usar essa imagem da galeria de imagens partilhadas para criar laboratórios. 
- Um administrador de uma conta de laboratório anexa uma galeria de imagens partilhada à conta do laboratório. Um criador de laboratório (instrutor) guarda a imagem personalizada do seu laboratório para a galeria de imagens partilhadas. Em seguida, outros criadores de laboratório podem selecionar esta imagem a partir da galeria de imagens partilhadas para criar um modelo para os seus laboratórios. 

    Quando uma imagem é guardada numa galeria de imagens partilhada, a Azure Lab Services replica a imagem guardada para outras regiões disponíveis na mesma [geografia.](https://azure.microsoft.com/global-infrastructure/geographies/) Garante que a imagem está disponível para laboratórios criados noutras regiões na mesma geografia. Guardar imagens para uma galeria de imagens partilhadas incorre num custo adicional, que inclui o custo para todas as imagens replicadas. Este custo é separado do custo de utilização dos Serviços De Laboratório Azure. Para obter mais informações sobre os preços da Galeria de Imagem Partilhada, consulte [a Galeria de Imagem Partilhada – Faturação]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).

    > [!NOTE]
    > A Azure Lab Services apoia a criação de VMs de modelo baseados em imagens **generalizadas** e **especializadas** numa galeria de imagens partilhadas. 


## <a name="configure-at-the-time-of-lab-account-creation"></a>Configure no momento da criação de conta de laboratório
Quando estiver a criar uma conta de laboratório, pode anexar uma galeria de imagens partilhada à conta do laboratório. Pode selecionar uma galeria de imagens partilhadas existente a partir da lista de lançamentos ou criar uma nova. Para criar e anexar uma galeria de imagem partilhada à conta de laboratório, selecione **Criar novo,** insira um nome para a galeria e introduza **OK**. 

![Configure galeria de imagens partilhadas no momento da criação de conta de laboratório](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Configurar depois da conta de laboratório ser criada
Após a criação da conta de laboratório, pode fazer as seguintes tarefas:

- Criar e anexar uma galeria de imagens partilhadas
- Anexar uma galeria de imagem partilhada à conta de laboratório
- Desaloque uma galeria de imagens partilhada da conta do laboratório

## <a name="create-and-attach-a-shared-image-gallery"></a>Criar e anexar uma galeria de imagens partilhadas
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços** no menu esquerdo. Selecione **Serviços de Laboratório** na secção **DEVOPS.** Se selecionar estrela ( ) ao lado dos Serviços de `*` **Laboratório,** é adicionado à secção **FAVORITES** no menu esquerdo. A partir da próxima vez, selecione **Serviços** de Laboratório sob **OS FAVORITOS**.

    ![Todos os Serviços -> Serviços laboratoriais](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Selecione a sua conta de laboratório para ver a página da **Conta lab.** 
4. Selecione galeria de **imagem partilhada** no menu esquerdo e selecione **+ Criar** na barra de ferramentas.  

    ![Criar botão de galeria de imagem partilhada](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. Na janela da galeria de **imagens partilhadas Create,** insira um **nome** para a galeria e introduza **OK**. 

    ![Criar janela de galeria de imagem partilhada](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    A Azure Lab Services cria a galeria de imagens partilhadas e anexa-a à conta do laboratório. Todos os laboratórios criados nesta conta de laboratório têm acesso à galeria de imagens partilhadas anexada. 

    ![Galeria de imagem anexada](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    No painel de baixo, vê-se imagens na galeria de imagens partilhadas. Nesta nova galeria, não há imagens. Quando envias imagens para a galeria, vê-las nesta página.     

    Todas as imagens na galeria de imagens partilhadas anexadas são ativadas por defeito. Pode ativar ou desativar as imagens selecionadas selecionando-as na lista e utilizando o botão **de imagens selecionadas enable** ou **Desativar imagens selecionadas.**

## <a name="attach-an-existing-shared-image-gallery"></a>Anexar uma galeria de imagem partilhada existente
O seguinte procedimento mostra-lhe como anexar uma galeria de imagens partilhadas existente a uma conta de laboratório. 

1. Na página **da Conta Lab,** selecione galeria de **imagem partilhada** no menu esquerdo e selecione **Anexar** na barra de ferramentas. 

    ![Galeria de imagens partilhadas - Adicionar botão](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Na **página anexar uma** página existente da Galeria de Imagem Partilhada, selecione a sua galeria de imagens partilhadas e selecione **OK**.

    ![Selecione uma galeria existente](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Veja o seguinte ecrã: 

    ![A minha galeria na lista](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    Neste exemplo, ainda não há imagens na galeria de imagens partilhadas.

    A identidade da Azure Lab Services é adicionada como um contribuinte para a galeria de imagens partilhadas que está anexada ao laboratório. Permite aos educadores/administradores de TI guardar imagens de máquinas virtuais para a galeria de imagens partilhadas. Todos os laboratórios criados nesta conta de laboratório têm acesso à galeria de imagens partilhadas anexada. 

    Todas as imagens na galeria de imagens partilhadas anexadas são ativadas por defeito. Pode ativar ou desativar as imagens selecionadas selecionando-as na lista e utilizando o botão **de imagens selecionadas enable** ou **Desativar imagens selecionadas.** 

## <a name="detach-a-shared-image-gallery"></a>Desmontar uma galeria de imagens partilhada
Apenas uma galeria de imagens partilhada pode ser anexada a um laboratório. Se quiser anexar outra galeria de imagens partilhadas, desaque a atual antes de anexar a nova. Para separar uma galeria de imagens partilhada do seu laboratório, selecione **Detach** na barra de ferramentas e confirme a operação de desapego. 

![Desaque a galeria de imagens partilhadas da conta do laboratório](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Passos seguintes
Para saber como guardar uma imagem de laboratório na galeria de imagens partilhadas ou usar uma imagem da galeria de imagens partilhadas para criar um VM, veja como usar a galeria de [imagens partilhadas.](how-to-use-shared-image-gallery.md)

Para obter mais informações sobre galerias de imagem partilhadas em geral, consulte a galeria de [imagens partilhadas.](../../virtual-machines/windows/shared-image-galleries.md)
