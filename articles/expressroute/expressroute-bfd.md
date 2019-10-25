---
title: Configurar o BFD no ExpressRoute – Azure | Microsoft Docs
description: Este artigo fornece instruções sobre como configurar o BFD (detecção de encaminhamento bidirecional) por meio do emparelhamento privado de um circuito do ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 8/17/2018
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: e33e90d988251afde630401bed165a4d3614d2cd
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881453"
---
# <a name="configure-bfd-over-expressroute"></a>Configurar o BFD no ExpressRoute

O ExpressRoute dá suporte à BFD (detecção de encaminhamento bidirecional) sobre o emparelhamento privado. Ao habilitar o BFD no ExpressRoute, você pode acelerar a detecção de falhas de link entre os dispositivos do Microsoft Enterprise Edge (MSEE) e os roteadores nos quais você encerra o circuito do ExpressRoute (PE/CE). Você pode encerrar o ExpressRoute em dispositivos de roteamento de borda de cliente ou dispositivos de roteamento de borda de parceiro (se você tiver feito com o serviço de conexão de camada 3 gerenciado). Este documento orienta você pela necessidade de BFD e como habilitar o BFD no ExpressRoute.

## <a name="need-for-bfd"></a>Necessidade de BFD

O diagrama a seguir mostra a vantagem de habilitar o BFD no circuito do ExpressRoute: [![1]][1]

Você pode habilitar o circuito do ExpressRoute por conexões de camada 2 ou conexões de camada 3 gerenciadas. Em ambos os casos, se houver um ou mais dispositivos de camada 2 no caminho de conexão do ExpressRoute, a responsabilidade de detectar quaisquer falhas de link no caminho está no BGP subjacente.

Nos dispositivos MSEE, o BGP KeepAlive e o tempo de espera são normalmente configurados como 60 e 180 segundos, respectivamente. Portanto, após uma falha de link, levaria até três minutos para detectar qualquer falha de link e alternar o tráfego para conexão alternativa.

Você pode controlar os temporizadores BGP Configurando o BGP KeepAlive e o tempo de espera inferiores no dispositivo de emparelhamento de borda do cliente. Se os temporizadores BGP forem incompatíveis entre os dois dispositivos de emparelhamento, a sessão BGP entre os pares usará o valor de timer inferior. O BGP KeepAlive pode ser definido como baixo em três segundos e o tempo de espera na ordem de dezenas de segundos. No entanto, a definição de temporizadores BGP é agressivamente menos preferível, pois o protocolo tem uso intensivo de processo.

Nesse cenário, o BFD pode ajudar. O BFD fornece detecção de falha de link de baixa sobrecarga em um intervalo de tempo de subsegundo. 


## <a name="enabling-bfd"></a>Habilitando BFD

O BFD é configurado por padrão em todas as interfaces de emparelhamento privado do ExpressRoute criadas recentemente no MSEEs. Portanto, para habilitar o BFD, você precisa apenas configurar o BFD no PEs/CEs (ambos em seus dispositivos primários e secundários). A configuração do BFD é um processo de duas etapas: você precisa configurar o BFD na interface e, em seguida, vinculá-lo à sessão BGP.

Um exemplo de configuração PE/CE (usando o Cisco IOS XE) é mostrado abaixo. 

    interface TenGigabitEthernet2/0/0.150
      description private peering to Azure
      encapsulation dot1Q 15 second-dot1q 150
      ip vrf forwarding 15
      ip address 192.168.15.17 255.255.255.252
      bfd interval 300 min_rx 300 multiplier 3


    router bgp 65020
      address-family ipv4 vrf 15
        network 10.1.15.0 mask 255.255.255.128
        neighbor 192.168.15.18 remote-as 12076
        neighbor 192.168.15.18 fall-over bfd
        neighbor 192.168.15.18 activate
        neighbor 192.168.15.18 soft-reconfiguration inbound
      exit-address-family

>[!NOTE]
>Para habilitar o BFD em um emparelhamento privado já existente; Você precisa redefinir o emparelhamento. Consulte [Redefinir emparelhamentos de ExpressRoute][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>Negociação de timer BFD

Entre os pares de BFD, os dois pontos mais lentos determinam a taxa de transmissão. Os intervalos de transmissão/recepção MSEEs BFD são definidos como 300 milissegundos. Em determinados cenários, o intervalo pode ser definido com um valor mais alto de 750 milissegundos. Ao configurar valores mais altos, você pode forçar esses intervalos a serem maiores; Mas não mais curto.

>[!NOTE]
>Se você tiver configurado circuitos de emparelhamento privado do ExpressRoute com redundância geográfica ou usar a conectividade VPN IPSec site a site como backup para o emparelhamento privado do ExpressRoute; habilitar o BFD sobre o emparelhamento privado ajudará o failover mais rápido após uma falha de conectividade do ExpressRoute. 
>

## <a name="next-steps"></a>Próximos Passos

Para obter mais informações ou ajuda, confira os links a seguir:

- [Criar e modificar um circuito ExpressRoute][CreateCircuit]
- [Criar e modificar o roteamento de um circuito do ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD acelera o tempo de dedução da falha de link"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






