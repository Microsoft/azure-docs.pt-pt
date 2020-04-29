---
title: Permitir que o criador de laboratório escolha a localização nos Serviços de Laboratório Azure
description: Este artigo descreve como um administrador de conta de laboratório pode permitir que os criadores de laboratório escolham locais para os seus laboratórios.
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 52d5628698d1f945a7f672595ee7ce4739b6d13c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77444371"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>Permitir que o criador de laboratório escolha o local para o laboratório nos Serviços de Laboratório Azure
Na Azure Lab Services, um dono de uma conta de laboratório pode permitir que os criadores de laboratório (educadores) escolham um local para o laboratório que criam. Esta localização pode ser diferente da localização da conta do laboratório. Uma localização é um grupo de regiões de Azure. Por exemplo, a localização dos Estados Unidos é um grupo de regiões como os EUA Orientais, Os EUA Ocidentais, e assim por diante. 

Você, como dono de uma conta de laboratório, pode selecionar o criador do **laboratório para escolher a** opção de localização do laboratório quando criar uma conta de laboratório e depois de criar a conta de laboratório (ou uma conta de laboratório existente). 

## <a name="at-the-time-of-lab-account-creation"></a>No momento da criação de conta de laboratório
Quando cria uma conta de laboratório, vê-se esta opção no primeiro ecrã (separador**Basics).** 

![Ativar a opção no momento da criação de laboratório](../media/allow-lab-creator-pick-lab-location/create-lab-account.png)

Esta opção é desativada se selecionar uma rede virtual de pares para a sua conta de laboratório no separador **Advanced.**  

![Quando a rede virtual peer está ativada](../media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>Depois que a conta de laboratório é criada
Depois de criar a conta de laboratório, pode ativar ou desativar esta opção seguindo estes passos: 

1. Na página **da Conta lab,** selecione **as definições** do Laboratório no menu esquerdo.
2. Selecione o criador do **laboratório permitir que** escolha a opção de localização do laboratório se quiser permitir que o criador do laboratório selecione um local para o laboratório. Se for desativado, os laboratórios são automaticamente criados no mesmo local onde existe a conta do laboratório. 
    
    Este campo é desativado quando seleciona uma rede virtual para o campo de **rede virtual Peer.** É porque os laboratórios na conta do laboratório devem estar na mesma região que o laboratório conta para eles acederem a recursos na rede virtual de pares. 
1. Selecione **Guardar** na barra de ferramentas. 

    ![Configurações de laboratório](../media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>Nenhuma rede virtual e seleção de localização não é permitida
Neste cenário, não permitiu que o criador do laboratório escolhesse a opção de localização do **laboratório.** 

![Sem localização de laboratório](../media/allow-lab-creator-pick-lab-location/lab-no-location.png)

Então, os criadores de laboratório (educadores) não vêem uma opção para escolher um local para o laboratório. Verão o preço por hora para cada opção de tamanho disponível para eles. Quando criarem um laboratório, será criado numa região de Azure que fica no mesmo local que a região de Azure onde está a sua conta de laboratório. Por exemplo, se a conta de laboratório estiver no **Oeste dos EUA,** então o laboratório pode ser criado no **Centro-Sul dos EUA,** mas não seria criado no **Canadá Leste.** Não garantimos nada sobre a região que escolhemos, além de que está no local. Se um tamanho está atualmente limitado, então o criador do laboratório verá uma caixa de verificação onde eles podem ver os tamanhos que normalmente apoiamos, mas que estão atualmente indisponíveis. 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>Na rede virtual e a seleção de localização não é permitida
Neste cenário, o criador do **laboratório permitir que o criador** do laboratório escolha a opção de localização do laboratório é desativado porque selecionou uma rede virtual para a conta de laboratório. Em seguida, os criadores de laboratório verão o mesmo ecrã que com a opção anterior. Como todos os VMs têm de estar na mesma região do Azure que a rede virtual, o laboratório será criado na mesma região azure em que a rede virtual se encontra. Se essa região em particular estiver limitada a um tamanho, o tamanho aparecerá como indisponível. 

## <a name="location-selection-is-enabled"></a>A seleção de localização está ativada
Quando selecionar Permitir que o criador do laboratório escolha a localização do **laboratório,** os criadores de laboratório (educadores) vêem uma opção para selecionar um local ao criar um laboratório. 

![Escolha uma localização de laboratório](../media/allow-lab-creator-pick-lab-location/location-selection.png)

Os criadores de laboratório supõem a gama de preços para todos os locais em que o tamanho está, e podem escolher um Local. O laboratório será criado em qualquer região de Azure que mapeie para aquele local.

Se um local estiver limitado, não está na lista por defeito. Expanda a lista de drop-down e selecione **Locais indisponíveis para este tamanho**. 

![Mostrar locais indisponíveis](../media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>Custo
Mais cedo, o preço baseava-se no tamanho vm que escolheste para o laboratório. Agora, o preço baseia-se na combinação de Sistema Operativo (OS), Tamanho e localização. 

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Ligue a rede do seu laboratório com uma rede virtual de pares](how-to-connect-peer-virtual-network.md)
- [Anexar uma galeria de imagens partilhadas a um laboratório](how-to-attach-detach-shared-image-gallery.md)
- [Adicione um utilizador como proprietário de laboratório](how-to-add-user-lab-owner.md)
- [Ver configurações de firewall para um laboratório](how-to-configure-firewall-settings.md)
- [Configure outras definições para um laboratório](how-to-configure-lab-accounts.md)