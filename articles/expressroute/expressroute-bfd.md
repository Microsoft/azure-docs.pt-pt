---
title: 'Azure ExpressRoute: Configure BFD'
description: Este artigo fornece instruções sobre como configurar o BFD (Deteção bidirecional de encaminhamento) sobre o espremiamento privado de um circuito ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 254f5909e7ed8db4dc18ade2677a3213b268cf41
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97511268"
---
# <a name="configure-bfd-over-expressroute"></a>Configure BFD sobre ExpressRoute

O ExpressRoute suporta a Deteção bidirecional de encaminhamento (BFD) tanto sobre o espreício privado como para a Microsoft. Quando ativa o BFD em vez do ExpressRoute, pode acelerar a deteção de falhas de ligação entre os dispositivos microsoft Enterprise edge (MSEE) e os routers que o seu circuito ExpressRoute fica configurado (CE/PE). Pode configurar o ExpressRoute sobre os seus dispositivos de encaminhamento de arestas ou os seus dispositivos de encaminhamento Partner Edge (se for com o serviço de ligação da Camada 3 gerido). Este documento acompanha-o através da necessidade de BFD, e como ativar o BFD em vez do ExpressRoute.

## <a name="need-for-bfd"></a>Necessidade de BFD

O seguinte diagrama mostra o benefício de permitir o BFD sobre o circuito ExpressRoute: [![1]][1]

Pode ativar o circuito ExpressRoute através das ligações da Camada 2 ou das ligações geridas da Camada 3. Em ambos os casos, se houver mais de um dispositivo Layer-2 na rota de ligação ExpressRoute, a responsabilidade de detetar quaisquer falhas de ligação no caminho reside na sessão de BGP sobressaída.

Nos dispositivos MSEE, o BGP mantém-se vivo e o tempo de espera são tipicamente configurados como 60 e 180 segundos, respectivamente. Por esta razão, quando uma falha de ligação acontece, pode levar até três minutos para detetar qualquer falha de ligação e mudar o tráfego para a ligação alternativa.

Pode controlar os temporizadores BGP configurando um BGP inferior mantendo-se vivo e tempo de espera no seu dispositivo de observação de borda. Se os temporizadores BGP não forem os mesmos entre os dois dispositivos de observação, a sessão de BGP estabelecerá utilizando o valor de tempo mais baixo. A continuação do BGP pode ser definida até três segundos e o tempo de espera até 10 segundos. No entanto, a definição de um temporizador BGP muito agressivo não é recomendada porque o protocolo é intensivo em processos.

Neste cenário, o BFD pode ajudar. O BFD fornece uma deteção de falha de ligação de baixa sobrecarga num intervalo de tempo de subsegundo. 


## <a name="enabling-bfd"></a>Habilitação BFD

O BFD é configurado por padrão em todas as interfaces de observação privadas ExpressRoute recentemente criadas nos MSEEs. Como tal, para ativar o BFD, basta configurar o BFD nos seus dispositivos primários e secundários. Configurar o BFD é um processo em duas etapas. Configura o BFD na interface e, em seguida, ligue-o à sessão de BGP.

Uma configuração CE/PE (utilizando Cisco IOS XE) é mostrada abaixo. 

```console
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
```

>[!NOTE]
>Para permitir o BFD sob um espreitamento privado já existente; é necessário reiniciar o espreitamento. Ver [reset ExpressRoute][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>Negociação do Temporizador BFD

Entre os pares do BFD, o mais lento dos dois pares determina a taxa de transmissão. Os intervalos de transmissão/receção MSEEs BFD são definidos em 300 milissegundos. Em certos cenários, o intervalo pode ser definido com um valor mais elevado de 750 milissegundos. Ao configurar um valor mais elevado, pode forçar estes intervalos a serem mais longos, mas não é possível torná-los mais curtos.

>[!NOTE]
>Se tiver circuitos ExpressRoute Geo redundantes configurados ou utilizar a conectividade IPSec VPN local como cópia de segurança. Permitir o BFD ajudaria a falhar mais rapidamente após uma falha de conectividade ExpressRoute. 
>

## <a name="next-steps"></a>Passos Seguintes

Para mais informações ou ajuda, confira os seguintes links:

- [Criar e modificar um circuito ExpressRoute][CreateCircuit]
- [Criar e modificar o encaminhamento de um circuito ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD acelera o tempo de dedução de falhas de ligação"

<!--Link References-->
[CreateCircuit]: ./expressroute-howto-circuit-portal-resource-manager.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[ResetPeering]: ./expressroute-howto-reset-peering.md