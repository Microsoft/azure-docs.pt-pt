---
title: Resolver erros AMQP no Service bus do Azure | Documentos da Microsoft
description: Fornece uma lista de erros AMQP pode receber quando utilizar o Azure Service Bus e fazer com que esses erros.
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
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58910049"
---
# <a name="amqp-errors-in-azure-service-bus"></a>Erros AMQP no Service bus do Azure
Este artigo fornece alguns dos erros que recebe quando utilizar o AMQP com o Azure Service Bus. Eles estão todos os comportamentos padrão do serviço. Pode evitá-los por meio de chamadas de envio/receção na ligação/ligação, que recria automaticamente a ligação/de ligação.

## <a name="link-is-closed"></a>Ligação está fechada 
Consulte o seguinte erro quando a ligação AMQP e ligação são Active Directory, mas nenhuma chamada (por exemplo, enviar ou receber) são feitas através da ligação durante 10 minutos. Por isso, a ligação está fechada. A ligação ainda está aberta.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>A ligação foi fechada
Consulte o seguinte erro na ligação AMQP quando todas as ligações na ligação foram fechadas porque não ocorreu nenhuma atividade (inativo) e não tiver sido criado um novo link em 5 minutos.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>Não é criada a ligação 
Se este erro ocorrer quando é criada uma nova ligação AMQP, mas não é criada uma ligação dentro de 1 minuto da criação da ligação AMQP.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre o AMQP e o Service Bus, visite as seguintes ligações:

* [Descrição geral do AMQP no Service Bus]
* [Guia do protocolo AMQP 1.0]
* [AMQP no Service Bus para o Windows Server]

[Descrição geral do AMQP no Service Bus]: service-bus-amqp-overview.md
[Guia do protocolo AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP no Service Bus para o Windows Server]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
