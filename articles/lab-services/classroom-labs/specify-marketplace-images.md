---
title: Especifique imagens do mercado para um laboratório em Serviços de Laboratório Azure
description: Este artigo mostra como especificar as imagens do Marketplace que o criador de laboratório pode usar para criar laboratórios numa conta de laboratório em Azure Lab Services.
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
ms.date: 04/10/2020
ms.author: spelluru
ms.openlocfilehash: a64dee6da521764a38fc60bee06545f6a561c297
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81257708"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>Especificar imagens do Marketplace disponíveis para criadores de laboratório
Enquanto proprietário de uma conta de laboratório, pode especificar as imagens do Marketplace que os criadores de laboratórios podem utilizar para criar laboratórios nessa conta. 

## <a name="select-images-available-for-labs"></a>Selecione imagens disponíveis para laboratórios
Selecione **Imagens do Marketplace** no menu à esquerda. Por predefinição, verá a lista completa de imagens (ativadas e desativadas). Pode filtrar a lista para ver apenas imagens ativadas/desativadas selecionando a única opção **Ativada**/**apenas desativada** a partir da lista de drop-down na parte superior. 
    
![Página de imagens do Marketplace](../media/tutorial-setup-lab-account/marketplace-images-page.png)

As imagens do Marketplace que aparecem na lista são as que satisfazem as seguintes condições:
    
- Cria uma VM individual.
- Utiliza o Azure Resource Manager para aprovisionar VMs
- Não requer a compra de planos de licenciamento extra

## <a name="disable-images-for-a-lab"></a>Desativar imagens para um laboratório 
Para desativar uma única imagem para um laboratório, **selecione... (elipse)** na última coluna e selecione **Desativar a imagem**. 

![Desativar uma imagem](../media/tutorial-setup-lab-account/disable-one-image.png) 

Em alternativa, selecione a caixa de verificação antes do nome da imagem e selecione **Desativar imagens selecionadas** na barra de ferramentas. 

Para desativar várias imagens ao mesmo tempo, selecione caixas de verificação antes dos nomes de imagem e selecione **Desativar imagens selecionadas** na barra de ferramentas. 

![Desativar várias imagens](../media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>Ativar imagens para um laboratório
Para ativar uma imagem desativada, **selecione... (elipse)** na última coluna e selecione **Imagem Ativar**. Em alternativa, selecione a caixa de verificação antes do nome da imagem e selecione **Ativar imagens selecionadas** na barra de ferramentas. 

Para desativar várias imagens ao mesmo tempo, selecione caixas de verificação antes dos nomes de imagem e selecione **Ativar imagens selecionadas** na barra de ferramentas. 

## <a name="enable-images-at-the-time-of-lab-creation"></a>Ativar imagens no momento da criação de laboratório
Pode permitir mais imagens na criação do laboratório do momento: 

1. Inscreva-se no site da [Azure Lab Services](https://labs.azure.com) usando credenciais do proprietário de conta de **laboratório**
2. Selecione a imagem da máquina virtual padrão ou a seta para baixo. 
3. **Selecione Ativar mais opções**de imagem . 

    ![Ativar mais opções de imagem](../media/specify-marketplace-images/enable-more-images-menu.png)
4. Siga as instruções da secção anterior para ativar as imagens selecionadas. 
5. Poderá ter de fechar a janela do **novo laboratório** e reabri-la para ver as imagens que selecionou no passo anterior. 



## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Como dono de laboratório, crie e gere a gestão de laboratórios](how-to-manage-classroom-labs.md)
- [Como proprietário de laboratório, configura risa e publica modelos](how-to-create-manage-template.md)
- [Como dono de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
- [Como utilizador de laboratório, aceda a laboratórios de sala de aula](how-to-use-classroom-lab.md)
