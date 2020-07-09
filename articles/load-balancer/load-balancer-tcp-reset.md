---
title: Reposição do reposição do balançador de carga em marcha lenta em Azure
titleSuffix: Azure Load Balancer
description: Com este artigo, aprenda sobre o Azure Load Balancer com pacotes TCP RST bidirecionais no tempo limite de marcha lenta.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2019
ms.author: allensu
ms.openlocfilehash: 68714053ac92faf8550a3e5f83a526afa1222971
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84808470"
---
# <a name="load-balancer-with-tcp-reset-on-idle"></a>Balanceador de Carga com Reposição de TCP quando Inativo

Pode utilizar o [Balancer de Carga Padrão](load-balancer-standard-overview.md) para criar um comportamento de aplicação mais previsível para os seus cenários, permitindo que o TCP Reset on Idle para uma determinada regra. O comportamento padrão do Balancer de Carga é baixar silenciosamente os fluxos quando o tempo de inatividade de um fluxo é atingido.  Ativar esta função fará com que o Balancer de Carga envie resets TCP bidirecionais (pacote TCP RST) no tempo limite de marcha lenta.  Isto informará os pontos finais da sua aplicação de que a ligação já está fora de tempo e já não é utilizável.  Os pontos finais podem estabelecer imediatamente uma nova ligação, se necessário.

![Reset TCP do balançador de carga](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
Altera este comportamento predefinido e permite o envio de Resets TCP no tempo limite de marcha lenta inativo nas regras de ACESSO NAT, regras de equilíbrio de carga e [regras de saída](https://aka.ms/lboutboundrules).  Quando ativado por regra, o Balanceador de Carga enviará o Reset TCP bidirecional (pacotes TCP RST) para os pontos finais do cliente e do servidor no momento do intervalo de marcha lenta para todos os fluxos correspondentes.

Os pontos finais que recebem pacotes TCP RST fecham imediatamente a tomada correspondente. Isto fornece uma notificação imediata aos pontos finais de que a libertação da ligação ocorreu e qualquer comunicação futura sobre a mesma ligação TCP falhará.  As aplicações podem purgar as ligações quando a tomada fecha e restabelece as ligações, se necessário, sem esperar que a ligação TCP aussa eventualmente.

Para muitos cenários, isto pode reduzir a necessidade de enviar keepalives TCP (ou camada de aplicação) para refrescar o tempo de inatividade de um fluxo. 

Se as durações inativas excederem as de permitidos pela configuração ou a sua aplicação mostrar um comportamento indesejável com resets TCP ativados, poderá ainda precisar de utilizar keepalives TCP (ou keepalives de camada de aplicação) para monitorizar a vivacidade das ligações TCP.  Além disso, os keepalives também podem permanecer úteis para quando a ligação é proxied em algum lugar no caminho, particularmente a camada de aplicação keepalives.  

Examine cuidadosamente todo o cenário final para decidir se beneficia de ativar resets TCP, ajustar o tempo limite de marcha lenta e se podem ser necessários passos adicionais para garantir o comportamento da aplicação desejada.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>Habilitar o Reset TCP no tempo limite de marcha lenta

Utilizando a versão API 2018-07-01, pode permitir o envio de resets bidirecionais de TCP em tempo de inatividade por regra:

```json
      "loadBalancingRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "inboundNatRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "outboundRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

## <a name="region-availability"></a><a name="regions"></a>Disponibilidade da região

Disponível em todas as regiões.

## <a name="limitations"></a>Limitações

- TCP RST apenas enviado durante a ligação TCP em estado estabelecido.

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [o Balanceador de Carga Padrão](load-balancer-standard-overview.md).
- Conheça as [regras de saída.](load-balancer-outbound-rules-overview.md)
- [Configure o TCP RST no tempo limite de Idle](load-balancer-tcp-idle-timeout.md)
