---
title: Problemas de resolução de erros da AMQP no Autocarro de Serviço saquedeiro do Azure Microsoft Docs
description: Fornece uma lista de erros AMQP que pode receber ao utilizar o Azure Service Bus e a causa desses erros.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2019
ms.author: aschhab
ms.openlocfilehash: 85d24a9e7c753ec5dba80c4f259dd3fb51d9c14b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "60402790"
---
# <a name="amqp-errors-in-azure-service-bus"></a>Erros da AMQP no Ônibus de Serviço Azure
Este artigo fornece alguns dos erros que recebe ao utilizar a AMQP com o Azure Service Bus. São todos comportamentos padrão do serviço. Pode evitá-las fazendo chamadas de envio/receção na ligação/ligação, que recria automaticamente a ligação/ligação.

## <a name="link-is-closed"></a>O link está fechado 
Vê o seguinte erro quando a ligação e ligação AMQP estão ativas, mas não são feitas chamadas (por exemplo, enviar ou receber) utilizando o link durante 10 minutos. Então, a ligação está fechada. A ligação ainda está aberta.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>A ligação está fechada
Vê-se o seguinte erro na ligação AMQP quando todas as ligações na ligação foram fechadas porque não houve atividade (inativa) e não foi criada uma nova ligação em 5 minutos.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>Link não é criado 
Você vê este erro quando uma nova ligação AMQP é criada, mas uma ligação não é criada dentro de 1 minuto da criação da Ligação AMQP.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre amqp e ônibus de serviço, visite os seguintes links:

* [Visão geral do ônibus de serviço AMQP]
* [Guia do protocolo AMQP 1.0]
* [AMQP em ônibus de serviço para servidor windows]

[Visão geral do ônibus de serviço AMQP]: service-bus-amqp-overview.md
[Guia do protocolo AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP em ônibus de serviço para servidor windows]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
