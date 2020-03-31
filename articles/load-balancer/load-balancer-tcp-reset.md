---
title: Load Balancer TCP Reset on Idle in Azure
titleSuffix: Azure Load Balancer
description: Com este artigo, saiba sobre o Azure Load Balancer com pacotes RST RST TCP bidirecional no tempo de tempo inativo.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2019
ms.author: allensu
ms.openlocfilehash: d3d836ddea8d07a25ad09e6f19d9f17a680decd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294409"
---
# <a name="load-balancer-with-tcp-reset-on-idle"></a>Balancer de carga com Reset TCP em Idle

Pode utilizar o [Standard Load Balancer](load-balancer-standard-overview.md) para criar um comportamento de aplicação mais previsível para os seus cenários, permitindo o Reset TCP em Idle para uma determinada regra. O comportamento padrão do Balancer de Carga é baixar silenciosamente os fluxos quando o tempo inativo de um fluxo é atingido.  Ativar esta funcionalidade fará com que o Balancer de Carga envie resets tCP bidirecionais (pacote TCP RST) no tempo de tempo inativo.  Isto informará os pontos finais da sua aplicação que a ligação tem prazo esgotado e já não é utilizável.  Os pontos finais podem estabelecer imediatamente uma nova ligação, se necessário.

![Reset TCP do balancedor de carga](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
Altera este comportamento predefinido e permite o envio de Resets TCP no tempo inativo nas regras de NAT de entrada, regras de equilíbrio de carga e [regras de saída](https://aka.ms/lboutboundrules).  Quando ativado por regra, o Balancer load enviará o Reset TCP (TCP RST) bidirecionado para os pontos finais do cliente e do servidor no momento do tempo inativo para todos os fluxos correspondentes.

Pontos finais que recebem pacotes TCP RST fecham imediatamente a tomada correspondente. Isto fornece uma notificação imediata aos pontos finais de que a libertação da ligação ocorreu e qualquer comunicação futura sobre a mesma ligação TCP falhará.  As aplicações podem purgar as ligações quando a tomada fecha e restabelecer as ligações conforme necessário, sem esperar que a ligação TCP eventualmente se esconda.

Para muitos cenários, isto pode reduzir a necessidade de enviar TCP (ou camada de aplicação) para refrescar o tempo inativo de um fluxo. 

Se as suas durações inativas excederem as permitidas pela configuração ou pela sua aplicação mostrar um comportamento indesejável com resets TCP ativados, poderá ainda necessitar de utilizar vidas de manutenção de TCP (ou manutenção da camada de aplicação) para monitorizar a vivacidade das ligações TCP.  Além disso, as vidas de manutenção também podem permanecer úteis para quando a ligação é proxid em algum lugar do caminho, particularmente a camada de aplicação keepalives.  

Examine cuidadosamente todo o cenário final para decidir se beneficia de permitir resets tCP, ajustar o tempo inativo e se podem ser necessários passos adicionais para garantir o comportamento de aplicação desejado.

## <a name="enabling-tcp-reset-on-idle-timeout"></a>Habilitar o Reset tCP no tempo de paragem inativo

Utilizando a versão API 2018-07-01, pode permitir o envio de Resets Bidirecionais TCP em tempo de inatividade por regra:

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

- TCP RST apenas enviado durante a ligação TCP em estado ESTABELECIDO.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o Equilíbrio de [Carga Padrão.](load-balancer-standard-overview.md)
- Saiba mais sobre [as regras de saída.](load-balancer-outbound-rules-overview.md)
- [Configure TCP RST no tempo de marcha lenta](load-balancer-tcp-idle-timeout.md)
