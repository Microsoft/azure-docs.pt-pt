---
title: Resolução de problemas erros amQP em Azure Event Hubs | Microsoft Docs
description: Fornece uma lista de erros amqp que pode receber ao utilizar os Hubs de Eventos Azure e causar esses erros.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 51b96792f6921bae9364212c6e5f9c987ff05e2a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103466070"
---
# <a name="amqp-errors-in-azure-event-hubs"></a>Erros amqp em Azure Event Hubs
Este artigo fornece alguns dos erros que recebe ao utilizar AMQP com Azure Event Hubs. São todos comportamentos padrão do serviço. Pode evitá-las fazendo chamadas de envio/receção na ligação/ligação, que recria automaticamente a ligação/ligação.

## <a name="link-is-closed"></a>Link está fechado 
Vê o seguinte erro quando a ligação e ligação AMQP estão ativas, mas nenhuma chamada (por exemplo, enviar ou receber) é feita usando o link durante 30 minutos. Então, a ligação está fechada. A ligação ainda está aberta.

" AMQP:link: desprendedo-forçado: A ligação 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000-0000000000000000000000 Origem detache: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:30:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp: 2/16/2018 11:10:40 PM "

## <a name="connection-is-closed"></a>A ligação está fechada
Você vê o seguinte erro na ligação AMQP quando todas as ligações na ligação foram fechadas porque não havia atividade (inativa) e um novo link não foi criado em 5 minutos.

" Error{condition=amqp:connection:forced, description='The connection was inative for more the allowed 300000 milissegundos and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null} "

## <a name="link-isnt-created"></a>Link não é criado 
Você vê este erro quando uma nova ligação AMQP é criada, mas um link não é criado dentro de 1 minuto após a criação da Conexão AMQP.

" Error{condition=amqp:connection:forced, description='The connection was inative for more the allowed 60000 milissegundos and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null} "

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre AMQP, consulte o [guia de protocolo AMQP 1.0.](../service-bus-messaging/service-bus-amqp-protocol-guide.md)
