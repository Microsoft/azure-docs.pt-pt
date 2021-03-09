---
title: Perguntas frequentes sobre O Servidor de Rota Azure
description: Encontre respostas para perguntas frequentes sobre o Azure Route Server.
services: route-server
author: duongau
ms.service: route-server
ms.topic: article
ms.date: 03/08/2021
ms.author: duau
ms.openlocfilehash: 6eed0ed3e936b0e9a534c82a3105c2ed37cab3d5
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485424"
---
# <a name="azure-route-server-preview-faq"></a>Azure Route Server (Pré-visualização) FAQ

> [!IMPORTANT]
> O Azure Route Server (Preview) encontra-se atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="what-is-azure-route-server"></a>O que é O Servidor de Rota Azure?

O Azure Route Server é um serviço totalmente gerido que lhe permite gerir facilmente o encaminhamento entre o seu aparelho virtual de rede (NVA) e a sua rede virtual.

### <a name="is-azure-route-server-just-a-vm"></a>O Azure Route Server é apenas um VM?

N.º Azure Route Server é um serviço projetado com alta disponibilidade. Se for implantado numa região do Azure que suporta [Zonas de Disponibilidade,](../availability-zones/az-overview.md)terá redundância ao nível da zona.

### <a name="what-routing-protocols-does-azure-route-server-support"></a><a name = "protocol"></a>Que protocolos de encaminhamento suportam o Azure Route Server?

O Azure Route Server suporta apenas o Protocolo de Gateway de Fronteira (BGP). O seu NVA precisa de suportar o BGP externo multi-hop porque terá de implantar o Azure Route Server numa sub-rede dedicada na sua rede virtual. A [ASN](https://en.wikipedia.org/wiki/Autonomous_system_(Internet)) que escolhe deve ser diferente daquela que o Azure Route Server utiliza quando configura o BGP no seu NVA.

### <a name="does-azure-route-server-route-data-traffic-between-my-nva-and-my-vms"></a>O Azure Route Server encaminha o tráfego de dados entre o meu NVA e os meus VMs?

N.º O Azure Route Server apenas troca rotas BGP com o seu NVA. O tráfego de dados vai diretamente da NVA para o VM escolhido e diretamente do VM para o NVA.

### <a name="if-azure-route-server-receives-the-same-route-from-more-than-one-nva-will-it-program-all-copies-of-the-route-but-each-with-a-different-next-hop-to-the-vms-in-the-virtual-network"></a>Se o Azure Route Server receber a mesma rota de mais de um NVA, irá programar todas as cópias da rota (mas cada uma com um próximo salto diferente) para os VMs na rede virtual?

Sim, só se a rota tiver o mesmo comprimento do percurso AS. Quando os VMs enviarem tráfego para o destino desta rota, os anfitriões VM farão Equal-Cost encaminhamento multi-caminho (ECMP). No entanto, se um NVA enviar a rota com um comprimento de caminho AS mais curto do que outros NVAs. O Azure Route Server irá programar apenas a rota que tem o próximo lúpulo definido para este NVA para os VMs na rede virtual.

### <a name="does-azure-route-server-support-vnet-peering"></a>O Azure Route Server suporta o VNet Peering?

Sim. Se espreitar um VNet que hospeda o Azure Route Server para outro VNet e ativar o Use Remote Gateway nesse VNet. O Azure Route Server irá aprender os espaços de endereço desse VNet e enviá-los para todos os NVAs espreitados.

### <a name="what-autonomous-system-numbers-asns-can-i-use"></a>Que Números de Sistema Autónomo (ASNs) posso usar?

Pode utilizar as suas próprias ASNs públicas ou ASNs privadas no seu aparelho virtual de rede. Não pode utilizar as gamas reservadas pela Azure ou pela IANA.
As seguintes ASNs são reservadas pela Azure ou pela IANA:

* ASNs reservadas por Azure:
    * Public ASNs: 8074, 8075, 12076
    * ASNs privados: 65515 65517, 65518, 65519, 65520
* ASNs [reservadas pela IANA:](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml)
    * 23456, 64496-64511, 65535-65551

### <a name="can-i-use-32-bit-4-byte-asns"></a>Posso usar ASNs de 32 bits (4 bytes) ?

Não, o Azure Route Server suporta apenas 16 bits (2 bytes) ASNs.

## <a name="route-server-limits"></a><a name = "limitations"></a>Limites do servidor de rota

O Azure Route Server tem os seguintes limites (por implementação).

| Recurso | Limite |
|----------|-------|
| Número de pares BGP apoiados | 8 |
| Número de rotas que cada par de BGP pode anunciar ao Azure Route Server | 200 |
| Número de rotas que o Azure Route Server pode anunciar ao Portal ExpressRoute ou VPN | 200 |

Se a sua NVA anunciar mais rotas do que o limite, a sessão de BGP será abandonada. Se isto acontecer ao gateway e ao Azure Route Server, perderá a conectividade da sua rede no local para o Azure. Para obter mais informações, consulte diagnosticar um problema de [encaminhamento de máquinas virtuais Azure](../virtual-network/diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Passos seguintes

Saiba como [configurar o Azure Route Server](quickstart-configure-route-server-powershell.md).
