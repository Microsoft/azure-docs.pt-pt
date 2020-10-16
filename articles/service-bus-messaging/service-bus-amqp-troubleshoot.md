---
title: Resolução de problemas erros amQP no Azure Service Bus / Microsoft Docs
description: Fornece uma lista de erros amqp que pode receber ao utilizar o Azure Service Bus e causar esses erros.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 88b10940e0b910f50e6ccf7f8c53134fa7f0ba2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88064354"
---
# <a name="amqp-errors-in-azure-service-bus"></a>Erros da AMQP no Azure Service Bus
Este artigo fornece alguns dos erros que recebe ao utilizar amQP com a Azure Service Bus. São todos comportamentos padrão do serviço. Pode evitá-las fazendo chamadas de envio/receção na ligação/ligação, que recria automaticamente a ligação/ligação.

## <a name="link-is-closed"></a>Link está fechado 
Vê o seguinte erro quando a ligação e ligação AMQP estão ativas, mas nenhuma chamada (por exemplo, enviar ou receber) é feita usando o link durante 10 minutos. Então, a ligação está fechada. A ligação ainda está aberta.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>A ligação está fechada
Você vê o seguinte erro na ligação AMQP quando todas as ligações na ligação foram fechadas porque não havia atividade (inativa) e um novo link não foi criado em 5 minutos.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>Link não é criado 
Vê este erro quando é criada uma nova ligação AMQP, mas não é criado um link no prazo de 1 minuto após a criação da AmQP Connection.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre AMQP e Service Bus, visite os seguintes links:

* [Visão geral do AMQP do autocarro de serviço]
* [Guia do protocolo AMQP 1.0]
* [AMQP no ônibus de serviço para windows server]

[Visão geral do AMQP do autocarro de serviço]: service-bus-amqp-overview.md
[Guia do protocolo AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP no ônibus de serviço para windows server]: /previous-versions/service-bus-archive/dn282144(v=azure.100)