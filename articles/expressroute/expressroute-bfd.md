---
title: Configurar BFD através do ExpressRoute - Azure | Documentos da Microsoft
description: Este artigo fornece instruções sobre como configurar BFD (deteção de reencaminhamento de bidirecional) ao longo do peering privado de um circuito do ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 8/17/2018
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 14f65851e50ed25024524f6d988ba2b2f2b3aeba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60367684"
---
# <a name="configure-bfd-over-expressroute"></a>Configurar BFD através do ExpressRoute

O ExpressRoute suporta a deteção de reencaminhamento bidirecional (BFD) através do peering privado. Ao ativar BFD através do ExpressRoute, pode agilizar a deteção de falha de ligação entre dispositivos de ponta (MSEE) do Microsoft Enterprise e os routers no qual terminar o circuito do ExpressRoute (PE). Pode cessar o ExpressRoute através de dispositivos de encaminhamento do Edge de cliente ou dispositivos de encaminhamento de limite de parceiro (se saiu com o serviço de ligação de camada 3 gerida). Este documento explica-lhe a necessidade de BFD e como ativar BFD através do ExpressRoute.

## <a name="need-for-bfd"></a>Necessidade de BFD

O diagrama seguinte mostra o benefício de ativação BFD através de circuito do ExpressRoute: [![1]][1]

Pode ativar o circuito do ExpressRoute, por ligações de camada 2 ou geridos ligações de camada 3. Em ambos os casos, se houver um ou mais dispositivos de camada 2 no caminho de ligação do ExpressRoute, a responsabilidade de detetar quaisquer falhas de ligação no caminho se encontra com o BGP overlying.

Nos dispositivos MSEE, keepalive BGP e o tempo de espera são normalmente configurados como 60 e 180 segundos respectivamente. Portanto, após uma falha de ligação que demoraria até três minutos para detetar qualquer falha de ligação e mude o tráfego para ligação alternativa.

Pode controlar os temporizadores BGP ao configurar mais baixo keepalive BGP e o tempo de espera no dispositivo de peering do edge de cliente. Se os temporizadores BGP são sem correspondência entre os dois dispositivos de peering, a sessão de BGP entre os elementos de rede utilizar o valor mais baixo do temporizador. O keepalive do BGP pode ser definido como tão baixo como três segundos e o tempo de espera por ordem de dezenas de segundos. No entanto, definir timers BGP agressivamente menos preferível porque o protocolo é exigente em termos de processo.

Neste cenário, pode ajudar a BFD. BFD fornece detecção de falhas de ligação de baixa sobrecarga num intervalo de tempo subsecond. 


## <a name="enabling-bfd"></a>Ativar BFD

BFD está configurado por predefinição em todas as recém-criado ExpressRoute privadas peering interfaces nos MSEEs. Portanto, para ativar BFD, precisa apenas configurar o BFD sua PEs. A configuração de BFD é o processo de dois passos: tem de configurar o BFD na interface e, em seguida, ligá-lo para a sessão de BGP.

Um exemplo de configuração de PE (com o Cisco IOS XE) é mostrado abaixo. 

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
>Para ativar BFD sob um já existente peering privado; terá de repor o peering. Consulte [peerings do ExpressRoute de reposição][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>Negociação de temporizador BFD

Entre itens de mesmo nível BFD, mais lenta os dois protocolos de mesmo nível determina a taxa de transmissão. Intervalos de transmissão/receção do MSEEs BFD estão definidos para 300 milissegundos. Em determinados cenários, é possível definir o intervalo de cada um valor mais alto de 750 milissegundos. Ao configurar os valores mais altos, pode forçar estes intervalos para ter mais; No entanto, não mais curto.

>[!NOTE]
>Se tiver configurado os circuitos peering privados do ExpressRoute com redundância geográfica ou utilize o IPSec de Site-Site VPN conectividade como cópia de segurança para o peering privado do ExpressRoute; Ativar BFD por peering privado ajudaria a ativação pós-falha mais rápida de após uma falha de conectividade do ExpressRoute. 
>

## <a name="next-steps"></a>Próximos Passos

Para obter mais informações ou ajuda, veja as ligações seguintes:

- [Criar e modificar um circuito do ExpressRoute][CreateCircuit]
- [Criar e modificar o encaminhamento de um circuito do ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD agiliza a hora de dedução de falha de ligação"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






