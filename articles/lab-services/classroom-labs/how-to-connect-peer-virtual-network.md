---
title: Ligue-se a uma rede de pares nos Serviços de Laboratório Azure [ Microsoft Docs
description: Aprenda a ligar a sua rede de laboratório a outra rede como par. Por exemplo, ligue a sua rede de escolas/universidades no local com a rede virtual do Lab em Azure.
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
ms.openlocfilehash: d2115b1dc7e9f3150e44eb5ee9417e88ebeaa279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80370839"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Conecte a rede do seu laboratório com uma rede virtual de pares nos Serviços de Laboratório Azure 
Este artigo fornece informações sobre o peering da sua rede de laboratórios com outra rede. 

## <a name="overview"></a>Descrição geral
O epeering de rede virtual permite-lhe ligar perfeitamente as redes virtuais Azure. Uma vez executado o peering, as redes virtuais aparecem como uma única, para fins de conectividade. O tráfego entre máquinas virtuais nas redes virtuais espreitadas é encaminhado através da infraestrutura de espinha dorsal da Microsoft, tal como o tráfego é encaminhado entre máquinas virtuais na mesma rede virtual, apenas através de endereços IP privados. Para mais informações, consulte o peering da [rede Virtual.](../../virtual-network/virtual-network-peering-overview.md)

Poderá ter de ligar a rede do seu laboratório a uma rede virtual de pares em alguns cenários, incluindo os seguintes:

- As máquinas virtuais no laboratório têm software que se conecta a servidores de licença supérum para adquirir licença
- As máquinas virtuais no laboratório precisam de acesso a conjuntos de dados (ou quaisquer outros ficheiros) nas partilhas da rede da universidade. 

Algumas redes no local estão ligadas à Rede Virtual Azure, quer através do [ExpressRoute](../../expressroute/expressroute-introduction.md) quer [do Portal da Rede Virtual.](../../vpn-gateway/vpn-gateway-about-vpngateways.md) Estes serviços devem ser criados fora dos Serviços de Laboratório Azure. Para saber mais sobre a ligação de uma rede no local ao Azure utilizando o ExpressRoute, consulte a visão geral da [ExpressRoute](../../expressroute/expressroute-introduction.md). Para a conectividade no local utilizando um Portal de Rede Virtual, o gateway, a rede virtual especificada, e a conta de laboratório devem estar todos na mesma região.

> [!NOTE]
> Ao criar uma Rede Virtual Azure que será observada com uma conta de laboratório, é importante entender como a região da rede virtual impacta onde os laboratórios de sala de aula são criados.  Para mais informações, consulte a secção do guia do administrador sobre [as regiões\locais](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regions-or-locations).

## <a name="configure-at-the-time-of-lab-account-creation"></a>Configure no momento da criação de conta de laboratório
Durante a criação de uma nova conta de laboratório, você pode escolher uma rede virtual existente que mostra na lista de dropdown da **rede virtual Peer** no separador **Advanced.** A rede virtual selecionada está conectada (peered) a laboratórios criados sob a conta de laboratório. Todas as máquinas virtuais em laboratórios que são criadas após a realização desta mudança teriam acesso aos recursos na rede virtual. 

![Selecione VNet para peer](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> Para instruções detalhadas passo a passo para criar uma conta de laboratório, consulte [Configurar uma conta](tutorial-setup-lab-account.md) de laboratório


## <a name="configure-after-the-lab-is-created"></a>Configurar depois que o laboratório é criado
A mesma propriedade pode ser ativada a partir do separador de **configuração labs** da página **Lab Account** se não tiver configurado uma rede de pares no momento da criação de conta de laboratório. A alteração feita a esta definição aplica-se apenas aos laboratórios que são criados após a mudança. Como pode ver na imagem, pode ativar ou desativar a **rede virtual peer** para laboratórios na conta de laboratório. 

![Ativar ou desativar o vnet espreitando após a criação do laboratório](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

Ao selecionar uma rede virtual para o campo de **rede virtual Peer,** o criador do **laboratório permitir escolher a** opção de localização do laboratório é desativado. É porque os laboratórios na conta do laboratório devem estar na mesma região que o laboratório conta para eles se conectarem com os recursos na rede virtual de pares. 

> [!IMPORTANT]
> Esta alteração de definição aplica-se apenas aos laboratórios que são criados após a mudança, e não aos laboratórios existentes. 


## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Permitir que o criador do laboratório escolha a localização do laboratório](allow-lab-creator-pick-lab-location.md)
- [Anexar uma galeria de imagens partilhadas a um laboratório](how-to-attach-detach-shared-image-gallery.md)
- [Adicione um utilizador como proprietário de laboratório](how-to-add-user-lab-owner.md)
- [Ver configurações de firewall para um laboratório](how-to-configure-firewall-settings.md)
- [Configure outras definições para um laboratório](how-to-configure-lab-accounts.md)