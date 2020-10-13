---
title: Especifique imagens do mercado para um laboratório em Azure Lab Services
description: Este artigo mostra-lhe como especificar as imagens do Marketplace que o criador de laboratório pode usar para criar laboratórios numa conta de laboratório nos Serviços do Laboratório Azure.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5a353394fec3dadf0f808a25ec66f6e9028820a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85445615"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>Especifique as imagens do Marketplace disponíveis para os criadores de laboratório
Enquanto proprietário de uma conta de laboratório, pode especificar as imagens do Marketplace que os criadores de laboratórios podem utilizar para criar laboratórios nessa conta. 

## <a name="select-images-available-for-labs"></a>Selecione imagens disponíveis para laboratórios
Selecione **Imagens do Marketplace** no menu à esquerda. Por predefinição, verá a lista completa de imagens (ativadas e desativadas). Pode filtrar a lista para ver apenas imagens **Enabled only**ativadas/desativadas selecionando a / única opção**desativada ativada** a partir da lista de drop-down no topo. 
    
![Página de imagens do Marketplace](./media/tutorial-setup-lab-account/marketplace-images-page.png)

As imagens do Marketplace que aparecem na lista são as que satisfazem as seguintes condições:
    
- Cria uma VM individual.
- Utiliza o Azure Resource Manager para aprovisionar VMs
- Não requer a compra de planos de licenciamento extra

## <a name="disable-images-for-a-lab"></a>Desativar imagens para um laboratório 
Para desativar uma única imagem para um laboratório, selecione **... (elipse)** na última coluna e selecione **Imagem de desativação**. 

![Desativar uma imagem](./media/tutorial-setup-lab-account/disable-one-image.png) 

Em alternativa, selecione a caixa de verificação antes do nome da imagem e selecione **Desativar as imagens selecionadas** na barra de ferramentas. 

Para desativar várias imagens ao mesmo tempo, selecione caixas de verificação antes dos nomes de imagem e selecione **Desativar as imagens selecionadas** na barra de ferramentas. 

![Desativar várias imagens](./media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>Ativar imagens para um laboratório
Para ativar uma imagem desativada, selecione **... (elipse)** na última coluna e selecione **Ativar a imagem**. Em alternativa, selecione a caixa de verificação antes do nome da imagem e selecione **Ative imagens selecionadas** na barra de ferramentas. 

Para desativar várias imagens ao mesmo tempo, selecione caixas de verificação antes dos nomes de imagem e selecione **Ative Imagens selecionadas** na barra de ferramentas. 

## <a name="enable-images-at-the-time-of-lab-creation"></a>Ativar imagens no momento da criação de laboratório
Pode ativar mais imagens no momento da criação do laboratório: 

1. Inscreva-se no site da [Azure Lab Services](https://labs.azure.com) usando credenciais **do proprietário de conta de laboratório**
2. Selecione a imagem da máquina virtual padrão ou a seta para baixo. 
3. **Selecione Ative mais opções de imagem**. 

    ![Ativar mais opções de imagem](./media/specify-marketplace-images/enable-more-images-menu.png)
4. Siga as instruções da secção anterior para ativar as imagens selecionadas. 
5. Poderá ter de fechar a janela do **novo laboratório** e reabri-la para ver as imagens que selecionou no passo anterior. 



## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Como dono de laboratório, crie e gere laboratórios](how-to-manage-classroom-labs.md)
- [Como dono de laboratório, crie e publique modelos](how-to-create-manage-template.md)
- [Como dono de laboratório, configurar e controlar o uso de um laboratório](how-to-configure-student-usage.md)
- [Como utilizador de laboratório, aceda aos laboratórios de sala de aula](how-to-use-classroom-lab.md)
