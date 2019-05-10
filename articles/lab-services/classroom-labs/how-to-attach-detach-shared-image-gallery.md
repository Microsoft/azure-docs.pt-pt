---
title: Anexar ou desanexar uma galeria de imagem partilhada do Azure Lab Services | Documentos da Microsoft
description: Saiba como anexar uma galeria de imagens partilhado a um laboratório no Azure Lab Services.
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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: de4e9fb4b15f4c346926fe46f23255c668204c2e
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413898"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Anexar ou desanexar uma galeria de imagem partilhada do Azure Lab Services
Administrador de professores/laboratório pode guardar uma imagem de modelo VM no Azure [Galeria de imagem partilhada](../../virtual-machines/windows/shared-image-galleries.md) para sua reutilização por outras pessoas. Como primeiro passo, o administrador de laboratório anexa uma galeria de imagem partilhada existente para a conta de laboratório. Depois da Galeria de imagem partilhada é anexada, laboratórios criados da conta de laboratório podem guardar imagens na Galeria de imagem partilhada. Outros professores podem selecionar esta imagem a partir da Galeria de imagem partilhada para criar um modelo para suas classes. 

Este artigo mostra-lhe como anexar ou desanexar uma galeria de imagem partilhada para uma conta de laboratório. 

## <a name="configure-at-the-time-of-lab-account-creation"></a>Configurar o momento da criação de contas de laboratório
Quando estiver a criar uma conta de laboratório, pode anexar uma galeria de imagem partilhada para a conta de laboratório. Pode selecionar uma galeria de imagem partilhada existente na lista pendente ou criar um novo. Para criar e anexar uma galeria de imagem partilhada para a conta de laboratório, selecione **criar novo**, introduza um nome para a Galeria e introduza **OK**. 

![Configurar a Galeria de imagens partilhado no momento da criação de contas de laboratório](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Configurar depois de criar a conta de laboratório
Depois de criar a conta de laboratório, pode fazer as seguintes tarefas:

- Criar e anexar uma galeria de imagem partilhada
- Anexar uma galeria de imagem partilhada para a conta de laboratório
- Anular a exposição de uma galeria de imagem partilhada da conta de laboratório

## <a name="create-and-attach-a-shared-image-gallery"></a>Criar e anexar uma galeria de imagem partilhada
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** no menu da esquerda. Selecione **Lab Services** no **DEVOPS** secção. Se selecionar asterisco (`*`) junto a **Lab Services**, é adicionado para o **Favoritos** secção no menu da esquerda. A partir da próxima vez em diante, pode selecionar **Lab Services** sob **Favoritos**.

    ![Todos os serviços -> Serviços de laboratório](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Selecione a sua conta de laboratório para ver os **conta de laboratório** página. 
4. Selecione **Galeria de imagens de partilhado** no menu à esquerda e selecione **+ criar** na barra de ferramentas.  

    ![Crie um botão de Galeria de imagem partilhada](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. Na **Galeria de imagem partilhada Create** janela, introduza um **nome** da Galeria e introduza **OK**. 

    ![Criar a janela de Galeria de imagem partilhada](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    O Azure Lab Services cria a Galeria de imagem partilhada e anexados-lo para a conta de laboratório. Todos os laboratórios criados nesta conta de laboratório tem acesso à Galeria de imagens partilhado anexado. 

    ![Galeria da imagem anexada](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    No painel inferior, verá as imagens na Galeria de imagens partilhado. Esta nova galeria, existem sem imagens. Ao carregar imagens para a galeria, vê-los nesta página.     

    Todas as imagens na Galeria de imagens partilhado anexado estão ativadas por predefinição. Pode ativar ou desativar imagens selecionadas ao selecioná-los na lista e utilizar o **ativar imagens selecionadas** ou **desativar imagens selecionadas** botão.

## <a name="attach-an-existing-shared-image-gallery"></a>Anexar uma galeria de imagem partilhada existente
O procedimento seguinte mostra-lhe como ligar uma galeria de imagem partilhada existente a uma conta de laboratório. 

1. Sobre o **conta de laboratório** página, selecione **Galeria de imagens de partilhado** no menu à esquerda e selecione **Attach** na barra de ferramentas. 

    ![Partilhado Galeria de imagens: adicionar o botão](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Sobre o **anexar uma galeria de imagens existente partilhado** página, selecione a Galeria de imagens compartilhadas e selecione **OK**.

    ![Selecione uma galeria existente](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Veja o ecrã seguinte: 

    ![Minha Galeria na lista](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    Neste exemplo, existem não existem imagens na Galeria de imagens partilhado ainda.

    Identidade do Lab Services do Azure é adicionada como contribuidor para a Galeria de imagem partilhada que está ligada ao laboratório. Ele permite aos professores / administrador de TI para guardar a máquina virtual imagens na Galeria de imagem partilhada. Todos os laboratórios criados nesta conta de laboratório tem acesso à Galeria de imagens partilhado anexado. 

    Todas as imagens na Galeria de imagens partilhado anexado estão ativadas por predefinição. Pode ativar ou desativar imagens selecionadas ao selecioná-los na lista e utilizar o **ativar imagens selecionadas** ou **desativar imagens selecionadas** botão. 

## <a name="save-an-image-to-the-shared-image-gallery"></a>Guardar uma imagem na Galeria de imagem partilhada
Depois de uma galeria de imagem partilhada é anexada, um administrador de conta de laboratório ou um professor pode salvar ou carregar uma imagem na Galeria de imagem partilhada para que possa ser reutilizado por outros professores. Para obter instruções para carregar uma imagem à Galeria de imagens partilhado, consulte [descrição geral da Galeria de imagens de partilhado](../../virtual-machines/windows/shared-images.md). 

> [!NOTE]
> Curently, a interface de utilizador (IU) de laboratórios de sala de aula não suporta a guardar uma imagem de laboratório para a Galeria de imagem partilhada. 

## <a name="detach-a-shared-image-gallery"></a>Anular a exposição de uma galeria de imagem partilhada
Apenas uma galeria de imagens partilhados pode ser anexada a um laboratório. Se quiser anexar outro Galeria de imagem partilhada, anular a exposição aquele atual antes de anexar novo. Para anular a exposição de uma galeria de imagem partilhada a partir do seu laboratório, selecione **anulação de exposições** na barra de ferramentas e confirme a operação de anulação de exposições. 

![Anular a exposição a Galeria de imagens partilhado da conta de laboratório](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre como guardar uma imagem de laboratório para a Galeria de imagem partilhada ou utilizar uma imagem a partir da Galeria de imagem partilhada para criar uma VM, veja [como utilizar a Galeria de imagem partilhada](how-to-use-shared-image-gallery.md).

Para obter mais informações sobre partilhados galerias de imagem em geral, consulte [Galeria de imagem partilhada](../../virtual-machines/windows/shared-image-galleries.md).
