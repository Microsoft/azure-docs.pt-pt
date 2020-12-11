---
title: Rede Virtual Azure - Conceitos e boas práticas
description: Saiba mais sobre os conceitos e as melhores práticas da Rede Virtual Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2020
ms.author: kumud
ms.openlocfilehash: d279516c1c9c08512c850a0f70eb84c0c1f63166
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111349"
---
# <a name="azure-virtual-network-concepts-and-best-practices"></a>Conceitos e boas práticas da Rede Virtual Azure

Este artigo descreve conceitos-chave e boas práticas para a Azure Virtual Network (VNet) .

## <a name="vnet-concepts"></a>Conceitos VNet

- **Espaço de endereço:** Ao criar um VNet, deve especificar um espaço de endereço IP privado personalizado utilizando endereços públicos e privados (RFC 1918). O Azure atribui aos recursos numa rede virtual um endereço IP privado a partir do espaço de endereços que atribuir. Por exemplo, se colocar um VM num VNet com espaço de endereço, 10.0.0.0/16, o VM será atribuído a um IP privado como 10.0.0.4.
- **Sub-redes:** As sub-redes permitem segmentar a rede virtual em uma ou mais sub-redes e atribuir uma parte do espaço de endereço da rede virtual a cada sub-rede. Em seguida, pode implantar recursos Azure numa sub-rede específica. Tal como numa rede tradicional, as sub-redes permitem segmentar o seu espaço de endereço VNet em segmentos apropriados para a rede interna da organização. Isto também melhora a eficiência da atribuição de endereços. Pode proteger recursos dentro de sub-redes utilizando grupos de segurança de rede. Para obter mais informações, consulte [os grupos de segurança da Rede.](security-overview.md)
- **Regiões**: O VNet é traçado para uma única região/localização; no entanto, várias redes virtuais de diferentes regiões podem ser conectadas em conjunto usando o Virtual Network Peering.
- **Assinatura:** O VNet é traçado para uma subscrição. Pode implementar várias redes virtuais dentro de cada [subscrição](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) do Azure e [região](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) do Azure.

## <a name="best-practices"></a>Melhores práticas

Ao construir a sua rede em Azure, é importante ter em mente os seguintes princípios de design universal:

- Certifique-se de espaços de endereços não sobrepostos. Certifique-se de que o seu espaço de endereço VNet (bloco CIDR) não se sobrepõe às outras gamas de rede da sua organização.
- As suas sub-redes não devem cobrir todo o espaço de endereço do VNet. Planeie com antecedência e reserve algum espaço de endereço para o futuro.
- Recomenda-se que tenha menos VNets grandes do que múltiplos VNets pequenos. Isto evitará a gestão.
- Proteja os seus VNet's atribuindo Grupos de Segurança de Rede (NSGs) às sub-redes por baixo delas.

## <a name="next-steps"></a>Passos seguintes

 Para começar a utilizar uma rede virtual, crie uma, implemente algumas VMs na mesma e comunique entre as VMs. Para saber como, veja o início rápido [Criar uma rede virtual](quick-create-portal.md).
