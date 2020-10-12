---
title: Permitir que o criador de laboratório escolha a localização nos Serviços do Laboratório Azure
description: Este artigo descreve como um administrador de conta de laboratório pode permitir que os criadores de laboratório escolham locais para os seus laboratórios.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 3b6c2d83414b1abc763755fbf15402b122c2186b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85444204"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>Permitir que o criador do laboratório escolha a localização para o laboratório nos Serviços de Laboratório Azure
Nos Serviços Azure Lab, um dono de uma conta de laboratório pode permitir que os criadores de laboratório (educadores) escolham um local para o laboratório que criam. Este local pode ser diferente da localização da conta do laboratório. Uma localização é um grupo de regiões de Azure. Por exemplo, a localização dos Estados Unidos é um grupo de regiões como os EUA Orientais, Os Estados Unidos, e assim por diante. 

Você, como dono de uma conta de laboratório, pode selecionar o criador do laboratório Allow para escolher a opção **de localização do laboratório** quando criar uma conta de laboratório e depois de criar a conta de laboratório (ou uma conta de laboratório existente). 

## <a name="at-the-time-of-lab-account-creation"></a>No momento da criação de conta de laboratório
Quando cria uma conta de laboratório, vê esta opção no primeiro ecrã **(Separador Básico).** 

![Ativar a opção no momento da criação de laboratório](./media/allow-lab-creator-pick-lab-location/create-lab-account.png)

Esta opção é desativada se selecionar uma rede virtual de pares para a sua conta de laboratório no separador **Avançado.**  

![Quando a rede virtual de pares está ativada](./media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>Depois que a conta de laboratório é criada
Depois de criar a conta de laboratório, pode ativar ou desativar esta opção seguindo estes passos: 

1. Na página **'Contas de Laboratório',** selecione **as definições de Laboratório** no menu esquerdo.
2. Selecione o criador do laboratório para escolher a opção **de localização** do laboratório se quiser permitir que o criador do laboratório selecione um local para o laboratório. Se for desativado, os laboratórios são automaticamente criados no mesmo local onde a conta do laboratório existe. 
    
    Este campo é desativado quando seleciona uma rede virtual para o campo **de rede virtual Peer.** É porque os laboratórios na conta do laboratório devem estar na mesma região que o laboratório para que eles acedam a recursos na rede virtual de pares. 
1. Selecione **Guardar** na barra de ferramentas. 

    ![Configurações de laboratório](./media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>Não é permitida a seleção de rede virtual e localização
Neste cenário, não permitiu que o criador do laboratório permitisse escolher a opção **de localização** do laboratório. 

![Sem localização de laboratório](./media/allow-lab-creator-pick-lab-location/lab-no-location.png)

Depois, os criadores de laboratório (educadores) não vêem uma opção para escolher um local para o laboratório. Eles verão o preço por hora para cada opção de tamanho disponível para eles. Quando criarem um laboratório, será criado numa região do Azure que está no mesmo local que a região de Azure em que a sua conta de laboratório está. Por exemplo, se a conta de laboratório estiver nos **EUA**ocidentais, então o laboratório pode ser criado no **Centro Sul dos EUA,** mas não seria criado no **Canadá Leste.** Não garantimos nada sobre a região que escolhemos, além de estar no local. Se um tamanho está atualmente limitado, então o criador do laboratório verá uma caixa de verificação onde eles podem ver os tamanhos que normalmente suportamos, mas estão atualmente indisponíveis. 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>Na rede virtual e na seleção de localização não é permitido
Neste cenário, o criador do laboratório allow para escolher a opção **de localização do laboratório** é desativado porque selecionou uma rede virtual de pares para a conta de laboratório. Em seguida, os criadores de laboratório verão o mesmo ecrã que com a opção anterior. Como todos os VMs têm de estar na mesma região do Azure que a rede virtual, o laboratório será criado na mesma região do Azure em que a rede virtual se encontra. Se essa região em particular for limitada por um tamanho, o tamanho aparecerá como indisponível. 

## <a name="location-selection-is-enabled"></a>A seleção de localização está ativada
Quando selecionar, permita **ao criador do laboratório escolher a localização do laboratório,** os criadores de laboratório (educadores) vêem uma opção para selecionar um local ao criar um laboratório. 

![Escolha uma localização de laboratório](./media/allow-lab-creator-pick-lab-location/location-selection.png)

Os criadores de laboratório vêem a gama de preços para todos os locais em que o tamanho está, e podem escolher uma localização. O laboratório será criado em qualquer região de Azure que mapear para aquele local.

Se uma localização é limitada, não é mostrada na lista por defeito. Expanda a lista de drop-down e selecione **Mostrar locais indisponíveis para este tamanho**. 

![Mostrar locais indisponíveis](./media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>Cost
Anteriormente, o preço baseava-se no tamanho de VM que escolheste para o laboratório. Agora, o preço baseia-se na combinação do Sistema Operativo (SISTEMA), Tamanho e localização. 

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Ligue a rede do seu laboratório a uma rede virtual de pares](how-to-connect-peer-virtual-network.md)
- [Anexar uma galeria de imagens partilhada a um laboratório](how-to-attach-detach-shared-image-gallery.md)
- [Adicione um utilizador como dono de laboratório](how-to-add-user-lab-owner.md)
- [Ver definições de firewall para um laboratório](how-to-configure-firewall-settings.md)
- [Configure outras configurações para um laboratório](how-to-configure-lab-accounts.md)