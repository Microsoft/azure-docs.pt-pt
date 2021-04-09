---
title: Reposição do reposição do balançador de carga e tempo limite de marcha lenta em Azure
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
ms.date: 10/07/2020
ms.author: allensu
ms.openlocfilehash: 0d02b46345af13770f77a7dac452127a665e01fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94696749"
---
# <a name="load-balancer-tcp-reset-and-idle-timeout"></a>Reposição do reposição do balançador de carga e tempo de marcha lenta

Pode utilizar o [Balancer de Carga Padrão](./load-balancer-overview.md) para criar um comportamento de aplicação mais previsível para os seus cenários, permitindo que o TCP Reset on Idle para uma determinada regra. O comportamento padrão do Balancer de Carga é baixar silenciosamente os fluxos quando o tempo de inatividade de um fluxo é atingido.  Ativar esta função fará com que o Balancer de Carga envie resets TCP bidirecionais (pacote TCP RST) no tempo limite de marcha lenta.  Isto informará os pontos finais da sua aplicação de que a ligação já está fora de tempo e já não é utilizável.  Os pontos finais podem estabelecer imediatamente uma nova ligação, se necessário.

![Reset TCP do balançador de carga](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
## <a name="tcp-reset"></a>Reset TCP

Altera este comportamento predefinido e permite o envio de Resets TCP no tempo limite de marcha lenta inativo nas regras de ACESSO NAT, regras de equilíbrio de carga e [regras de saída](./load-balancer-outbound-connections.md#outboundrules).  Quando ativado por regra, o Balanceador de Carga enviará o Reset TCP bidirecional (pacotes TCP RST) para os pontos finais do cliente e do servidor no momento do intervalo de marcha lenta para todos os fluxos correspondentes.

Os pontos finais que recebem pacotes TCP RST fecham imediatamente a tomada correspondente. Isto fornece uma notificação imediata aos pontos finais de que a libertação da ligação ocorreu e qualquer comunicação futura sobre a mesma ligação TCP falhará.  As aplicações podem purgar as ligações quando a tomada fecha e restabelece as ligações, se necessário, sem esperar que a ligação TCP aussa eventualmente.

Para muitos cenários, isto pode reduzir a necessidade de enviar keepalives TCP (ou camada de aplicação) para refrescar o tempo de inatividade de um fluxo. 

Se as durações inativas excederem as de permitidos pela configuração ou a sua aplicação mostrar um comportamento indesejável com resets TCP ativados, poderá ainda precisar de utilizar keepalives TCP (ou keepalives de camada de aplicação) para monitorizar a vivacidade das ligações TCP.  Além disso, os keepalives também podem permanecer úteis para quando a ligação é proxied em algum lugar no caminho, particularmente a camada de aplicação keepalives.  

Examine cuidadosamente todo o cenário final para decidir se beneficia de ativar resets TCP, ajustar o tempo limite de marcha lenta e se podem ser necessários passos adicionais para garantir o comportamento da aplicação desejada.

## <a name="configurable-tcp-idle-timeout"></a>Tempo limite de marcha lenta ção do TCP configurável

O Azure Load Balancer tem a seguinte gama de tempo limite de marcha lenta:
-  4 minutos a 100 minutos para regras de saída
-  4 minutos a 30 minutos para as regras do Balanceador de Carga e regras NAT de entrada

Por predefinição, está programado para 4 minutos. Se um período de inatividade for maior do que o valor de tempo limite, não há garantias de que a sessão TCP ou HTTP seja mantida entre o cliente e o seu serviço na nuvem.

Quando a ligação estiver fechada, a aplicação do seu cliente poderá receber a seguinte mensagem de erro: "A ligação subjacente foi encerrada: Uma ligação que se esperava que fosse mantida viva foi fechada pelo servidor."

Uma prática comum é usar um TCP de vida. Esta prática mantém a ligação ativa por um período mais longo. Para obter mais informações, consulte estes [exemplos .NET](/dotnet/api/system.net.servicepoint.settcpkeepalive). Com o tempo de vida ativo, os pacotes são enviados durante períodos de inatividade na ligação. Os pacotes de manutenção vivos asseguram que o valor de tempo de saída não é atingido e a ligação é mantida por um longo período de tempo.

A regulação funciona apenas para ligações de entrada. Para evitar perder a ligação, configufique a fixação do TCP com um intervalo inferior ao da definição de tempo de marcha lenta inativa ou aumente o valor de tempo de marcha lenta inativo. Para apoiar estes cenários, foi adicionado o apoio a um tempo limite configurável.

A TCP mantém-se viva para cenários em que a vida útil da bateria não é um constrangimento. Não é recomendado para aplicações móveis. A utilização de um TCP de vida numa aplicação móvel pode drenar a bateria do dispositivo mais rapidamente.


## <a name="limitations"></a>Limitações

- Reposição de TCP apenas enviada durante a ligação TCP em estado estabelecido.
- O reset TCP não é enviado para balançadores de carga internos com portas HA configuradas.
- O tempo limite de inatividade da TCP não afeta as regras de equilíbrio de carga no protocolo UDP.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o Balanceador de Carga Padrão](./load-balancer-overview.md).
- Conheça as [regras de saída.](./load-balancer-outbound-connections.md#outboundrules)
- [Configure o TCP RST no tempo limite de Idle](load-balancer-tcp-idle-timeout.md)