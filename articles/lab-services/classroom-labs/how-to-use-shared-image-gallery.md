---
title: Utilizar uma galeria de imagem partilhada do Azure Lab Services | Documentos da Microsoft
description: Saiba como configurar uma conta de laboratório para utilizar uma galeria de imagem partilhada para que um utilizador pode partilhar uma imagem com os outros e outro utilizador pode utilizar a imagem para criar um modelo de VM no laboratório.
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
ms.date: 03/28/2019
ms.author: spelluru
ms.openlocfilehash: 93136c7d685bd9fc8ec4bcdea3a900b28029059b
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58653214"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Utilizar uma galeria de imagem partilhada do Azure Lab Services
Este artigo mostra como o administrador de professores/laboratório pode economizar uma imagem de máquina virtual do modelo para sua reutilização por outras pessoas. Estas imagens são guardadas no Azure [Galeria de imagem partilhada](../../virtual-machines/windows/shared-image-galleries.md). Como primeiro passo, o administrador de laboratório anexa uma galeria de imagem partilhada existente para a conta de laboratório. Depois da Galeria de imagem partilhada é anexada, laboratórios criados da conta de laboratório podem guardar imagens na Galeria de imagem partilhada. Outros professores podem selecionar esta imagem a partir da Galeria de imagem partilhada para criar um modelo para suas classes. 

## <a name="prerequisites"></a>Pré-requisitos
Criar uma galeria de imagem partilhada utilizando [do Azure PowerShell](../../virtual-machines/windows/shared-images.md) ou [CLI do Azure](../../virtual-machines/linux/shared-images.md).

## <a name="attach-a-shared-image-gallery-to-a-lab-account"></a>Anexar uma galeria de imagem partilhada para uma conta de laboratório
O procedimento seguinte mostra como anexar uma galeria de imagem partilhada para uma conta de laboratório. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** no menu da esquerda. Selecione **Lab Services** no **DEVOPS** secção. Se selecionar asterisco (`*`) junto a **Lab Services**, é adicionado para o **Favoritos** secção no menu da esquerda. A partir da próxima vez em diante, pode selecionar **Lab Services** sob **Favoritos**.

    ![Todos os serviços -> Serviços de laboratório](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Selecione a sua conta de laboratório para ver os **conta de laboratório** página. 
4. Selecione **Galeria de imagens de partilhado** no menu à esquerda e selecione **Attach** na barra de ferramentas. 

    ![Partilhado Galeria de imagens: adicionar o botão](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Sobre o **anexar uma galeria de imagens existente partilhado** página, selecione a Galeria de imagens compartilhadas e selecione **OK**.

    ![Selecione uma galeria existente](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Veja o ecrã seguinte: 

    ![Minha Galeria na lista](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    Neste exemplo, existem não existem imagens na Galeria de imagens partilhado ainda.

Identidade do Lab Services do Azure é adicionada como contribuidor para a Galeria de imagem partilhada que está ligada ao laboratório. Ele permite aos professores / administrador de TI para guardar a máquina virtual imagens na Galeria de imagem partilhada. Todos os laboratórios criados nesta conta de laboratório tem acesso à Galeria de imagens partilhado anexado. 

Todas as imagens na Galeria de imagens partilhado anexado estão ativadas por predefinição. Pode ativar ou desativar imagens selecionadas ao selecioná-los na lista e utilizar o **ativar imagens selecionadas** ou **desativar imagens selecionadas** botão. 

## <a name="detach-a-shared-image-gallery"></a>Anular a exposição de uma galeria de imagem partilhada
Apenas uma galeria de imagens partilhados pode ser anexada a um laboratório. Se quiser anexar outro Galeria de imagem partilhada, anular a exposição aquele atual antes de anexar novo. Para anular a exposição de uma galeria de imagem partilhada a partir do seu laboratório, selecione **anulação de exposições** na barra de ferramentas e confirme a operação de anulação de exposições. 

## <a name="save-an-image-to-the-shared-image-gallery"></a>Guardar uma imagem na Galeria de imagem partilhada
Depois de uma galeria de imagem partilhada é anexada, um professor pode guardar uma imagem de modelo na Galeria de imagem partilhada para que possa ser reutilizado por outros professores.

![Guardar imagem de máquina virtual na Galeria](../media/how-to-use-shared-image-gallery/save-virtual-machine.png)

## <a name="use-an-image-from-the-shared-image-gallery"></a>Utilizar uma imagem a partir da Galeria de imagem partilhada
Professor/professora podem escolher uma imagem personalizada, disponível na Galeria de imagens partilhado para o modelo durante a criação do novo laboratório.

![Utilizar imagem de máquina virtual a partir da Galeria](../media/how-to-use-shared-image-gallery/use-shared-image.png)

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre as galerias de imagem partilhada, consulte [Galeria de imagem partilhada](../../virtual-machines/windows/shared-image-galleries.md).
