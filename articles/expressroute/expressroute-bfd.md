---
title: 'Azure ExpressRoute: Configure BFD'
description: Este artigo fornece instruções sobre como configurar o BFD (Deteção de Reencaminhamento Bidirecional) sobre o peering privado de um circuito ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 11/1/2018
ms.author: rambala
ms.openlocfilehash: 378b639e89ffd46f6b32d7004f934104dd4b5407
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80064846"
---
# <a name="configure-bfd-over-expressroute"></a>Configure BFD sobre ExpressRoute

O ExpressRoute suporta a Deteção de Reencaminhamento Bidirecional (BFD) tanto em termos privados como de espreitamento da Microsoft. Ao ativar o BFD através do ExpressRoute, pode acelerar a deteção de falhas de ligação entre dispositivos microsoft Enterprise edge (MSEE) e os routers em que termina o circuito ExpressRoute (CE/PE). Pode encerrar o ExpressRoute sobre dispositivos de encaminhamento do Customer Edge ou dispositivos de encaminhamento partner Edge (se for com o serviço de ligação de camada 3 gerido). Este documento acompanha-o através da necessidade de BFD e como permitir o BFD através do ExpressRoute.

## <a name="need-for-bfd"></a>Necessidade de BFD

O diagrama seguinte mostra o benefício de permitir o BFD sobre o circuito ExpressRoute: [![1]][1]

Pode ativar o circuito ExpressRoute através de ligações camada 2 ou ligações geridas pela Camada 3. Em qualquer dos casos, se houver um ou mais dispositivos Layer-2 no caminho de ligação ExpressRoute, a responsabilidade de detetar quaisquer falhas de ligação no caminho reside no BGP sobrepor-se.

Nos dispositivos MSEE, o BGP mantém-se vivo e o tempo de espera são normalmente configurados como 60 e 180 segundos, respectivamente. Portanto, na sequência de uma falha de ligação, levaria até três minutos para detetar qualquer falha de ligação e mudar o tráfego para uma ligação alternativa.

Pode controlar os temporizadores BGP configurando o bGP inferior manter-se vivo e manter o tempo de espera no dispositivo de observação de borda do cliente. Se os tempos de BGP forem incompatíveis entre os dois dispositivos de observação, a sessão de BGP entre os pares utilizaria o valor do temporizador mais baixo. O BGP mantém-se vivo até três segundos, e o tempo de espera na ordem de dezenas de segundos. No entanto, a fixação de temporadores De GP de forma agressiva é menos preferível porque o protocolo é intensivo.

Neste cenário, o BFD pode ajudar. O BFD fornece uma deteção de falha de ligação de baixa sobrecarga num intervalo de tempo de subsegundo. 


## <a name="enabling-bfd"></a>Habilitar o BFD

O BFD é configurado por padrão sob todas as interfaces privadas de peering expressRoute recém-criadas nos MSEEs. Portanto, para ativar o BFD, é necessário configurar apenas o BFD nos seus CEs/PEs (tanto nos seus dispositivos primários como secundários). Configurar o BFD é um processo em duas etapas: é necessário configurar o BFD na interface e, em seguida, ligá-lo à sessão bGP.

Um exemplo de configuração CE/PE (utilizando cisco IOS XE) é mostrado abaixo. 

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
>Permitir o BFD sob um epeering privado já existente; precisa repor o olhar. Ver [Reset ExpressRoute peerings][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>Negociação do Temporizador bfd

Entre os pares da BFD, o mais lento dos dois pares determina a taxa de transmissão. Os intervalos de transmissão/receção de MSEEs BFD estão definidos para 300 milissegundos. Em certos cenários, o intervalo pode ser fixado a um valor superior de 750 milissegundos. Ao configurar valores mais elevados, pode forçar estes intervalos a serem mais longos; mas, não mais curto.

>[!NOTE]
>Se tiver configurado circuitos Geo-redundantes da ExpressRoute ou utilizar a conectividade VPN IPSec do Site-a-Site como cópia de segurança; permitir o BFD ajudaria a falhar mais rapidamente na sequência de uma falha de conectividade ExpressRoute. 
>

## <a name="next-steps"></a>Passos Seguintes

Para mais informações ou ajuda, consulte os seguintes links:

- [Criar e modificar um circuito ExpressRoute][CreateCircuit]
- [Criar e modificar o encaminhamento de um circuito ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.pngBFD acelera tempo de "dedução de falha" de ligação

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






