---
title: Ligar a uma rede de ponto a ponto no Azure Lab Services | Documentos da Microsoft
description: Saiba como ligar a sua rede de laboratório com outra rede como um elemento de rede. Por exemplo, liga a sua rede de escola/Universidade de no local com a rede virtual do laboratório no Azure.
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
ms.openlocfilehash: 465352af52cbc84773e52782233065b3000921e7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60695412"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Ligar a rede do seu laboratório com um elemento da rede virtual no Azure Lab Services 
Este artigo fornece informações sobre o peering da rede de laboratórios com outra rede. 

## <a name="overview"></a>Descrição geral
Peering de rede virtual permite que se conecte a redes virtuais do Azure. Uma vez executado o peering, as redes virtuais aparecem como uma única, para fins de conectividade. O tráfego entre máquinas virtuais nas redes virtuais em modo de peering será encaminhado através da infraestrutura principal da Microsoft, tal como o tráfego é encaminhado entre máquinas virtuais na mesma rede virtual, através de endereços IP privados apenas. Para obter mais informações, consulte [peering de rede Virtual](../../virtual-network/virtual-network-peering-overview.md).

Poderá ter de ligar a rede do seu laboratório com um elemento da rede virtual em alguns cenários, incluindo os seguintes:

- As máquinas virtuais no laboratório têm software que se liga a servidores de licenças no local para adquirir licença
- As máquinas virtuais no laboratório tem acesso a conjuntos de dados (ou outros arquivos) em compartilhamentos de rede da universidade. 

Determinadas redes no local estão ligados à rede Virtual do Azure ou através de [ExpressRoute](../../expressroute/expressroute-introduction.md) ou [Gateway de rede Virtual](../../vpn-gateway/vpn-gateway-about-vpngateways.md). Estes serviços tem de ser definidos fora do Azure Lab Services. Para saber mais sobre como ligar uma rede no local ao Azure através do ExpressRoute, veja [descrição geral do ExpressRoute]) (... /expressroute/expressroute-Introduction.MD). Para conectividade no local através de um Gateway de rede Virtual, o gateway, rede virtual especificada e a conta de laboratório têm de estar todos na mesma região.


## <a name="configure-at-the-time-of-lab-account-creation"></a>Configurar o momento da criação de contas de laboratório
Durante a criação de conta de laboratório novo, pode escolher uma rede virtual existente que mostra a **elemento da rede virtual** lista suspensa. A rede virtual selecionada é connected(peered) aos laboratórios criado sob a conta de laboratório. Todas as máquinas virtuais em laboratórios que são criados após a criação desta alteração teria acesso aos recursos na rede virtual em modo de peering. 

![Selecione o VNet peering entre](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> Para obter instruções detalhadas passo a passo para criar uma conta de laboratório, consulte [configurar uma conta de laboratório](tutorial-setup-lab-account.md)


## <a name="configure-after-the-lab-is-created"></a>Configurar depois de criar o laboratório
A mesma propriedade pode ser ativada a partir do **configuração de laboratórios** separador da **conta de laboratório** página se não tiver configurado uma rede de mesmo nível no momento da criação de contas de laboratório. Alterações efetuadas a esta definição aplica-se apenas aos laboratórios que são criados após a alteração.

![Ativar ou desativar o VNet peering depois de criar o laboratório](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

Como pode ver na imagem, pode ativar ou desativar **elemento da rede virtual** para laboratórios na conta de laboratório. 

> [!IMPORTANT]
> Esta alteração da definição só se aplica a laboratórios que são criados após a alteração é feita, não para os laboratórios existentes. 



## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos:

- [Como administrador, criar e gerir contas de laboratório](how-to-manage-lab-accounts.md)
- [Como proprietário de um laboratório, criar e gerir laboratórios](how-to-manage-classroom-labs.md)
- [Como proprietário de um laboratório, configurar e publicar modelos](how-to-create-manage-template.md)
- [Como um utilizador de laboratório, acessar os laboratórios de sala de aula](how-to-use-classroom-lab.md)

