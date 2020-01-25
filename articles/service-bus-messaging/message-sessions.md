---
title: Sessões de mensagens azure service bus / Microsoft Docs
description: Manuseie sequências de mensagens de ônibus de serviço Azure com sessões.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 7da3c3de5074df80c676238e4d43dbd677b0a3b4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720236"
---
# <a name="message-sessions-first-in-first-out-fifo"></a>Sessões de mensagem: primeiro a entrar, primeiro fora (FIFO) 

As sessões de ônibus de serviço do Microsoft Azure permitem o manuseamento conjunto e ordenado de sequências não limitadas de mensagens relacionadas. Para realizar uma garantia FIFO no Ônibus de Serviço, utilize as Sessões. O Service Bus não é prescritivo sobre a natureza da relação entre as mensagens, e também não define um modelo específico para determinar onde uma sequência de mensagens começa ou termina.

> [!NOTE]
> O nível básico de Ônibus de Serviço não suporta sessões. As sessões de suporte de níveis standard e premium. Para mais informações, consulte os preços dos [autocarros de serviço.](https://azure.microsoft.com/pricing/details/service-bus/)

Qualquer remetente pode criar uma sessão ao submeter mensagens num tópico ou fila, definindo a propriedade [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) para um identificador definido pela aplicação que seja único na sessão. Ao nível do protocolo AMQP 1.0, este valor mapeia para a propriedade *group-id.*

Nas filas ou subscrições conscientes da sessão, surgem sessões quando há pelo menos uma mensagem com o [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId)da sessão. Uma vez que existe uma sessão, não há tempo definido ou API para quando a sessão expirar ou desaparecer. Teoricamente, uma mensagem pode ser recebida para uma sessão hoje, a próxima mensagem dentro de um ano, e se o **SessionId** corresponder, a sessão é a mesma do ponto de vista do Bus de Serviço.

Normalmente, no entanto, uma aplicação tem uma noção clara de onde um conjunto de mensagens relacionadas começa e termina. O Ônibus de Serviço não estabelece regras específicas.

Um exemplo de como delinear uma sequência para transferir um ficheiro é definir a propriedade **Label** para a primeira mensagem a **iniciar,** para mensagens intermédias para **conteúdo**, e para que a última mensagem **termine**. A posição relativa das mensagens de conteúdo pode ser calculada como a mensagem atual *SequenceNumber* delta a partir da mensagem **inicial** *SequenceNumber*.

A funcionalidade de sessão no Service Bus permite uma operação C# de receção específica, sob a forma de [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) nas APIs e Java. Ativa a funcionalidade definindo a propriedade [necessáriaSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) na fila ou subscrição via Azure Resource Manager, ou colocando a bandeira no portal. Isto é necessário antes de tentar utilizar as operações relacionadas com a API.

No portal, coloque a bandeira com a seguinte caixa de verificação:

![][2]

> [!NOTE]
> Quando as Sessões são ativadas numa fila ou numa subscrição, as aplicações do cliente ***já não*** podem enviar/receber mensagens regulares. Todas as mensagens devem ser enviadas como parte de uma sessão (definindo o id da sessão) e recebidas recebendo a sessão.

As APIs para sessões existem em clientes de fila e subscrição. Existe um modelo imperativo que controla quando as sessões e as mensagens são recebidas, e um modelo baseado em manipulador, semelhante ao *OnMessage,* que esconde a complexidade da gestão do loop de receção.

## <a name="session-features"></a>Características da sessão

As sessões proporcionam um desmúltiplo simultâneo de fluxos de mensagens intercaladas, preservando e garantindo a entrega ordenada.

![][1]

Um recetor [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) é criado pelo cliente que aceita uma sessão. O cliente chama [queueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) ou [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) em C#. No modelo de chamada reativa, regista um manipulador de sessão.

Quando o objeto [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) é aceite e enquanto é detido por um cliente, esse cliente detém um bloqueio exclusivo em todas as mensagens com o [SessionId](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) dessa sessão que existem na fila ou subscrição, e também em todas as mensagens com o **SessionId** que ainda chegam enquanto a sessão é realizada.

O bloqueio é libertado quando o **Close** ou **o CloseAsync** são chamados, ou quando o bloqueio expira nos casos em que a aplicação não é capaz de executar o funcionamento próximo. O bloqueio da sessão deve ser tratado como um bloqueio exclusivo num ficheiro, o que significa que a aplicação deve encerrar a sessão assim que deixar de precisar e/ou não esperar mais mensagens.

Quando vários recetores simultâneos retiram da fila, as mensagens pertencentes a uma determinada sessão são enviadas para o recetor específico que atualmente detém o cadeado para essa sessão. Com esta operação, um fluxo de mensagens intercalada sintetizado que reside numa fila ou subscrição é limpo desmultiplexado para diferentes recetores e esses recetores também podem viver em diferentes máquinas de cliente, uma vez que a gestão do bloqueio acontece lado de serviço, no interior Autocarro de serviço.

A ilustração anterior mostra três recetores simultâneos de sessão. One Session with `SessionId` = 4 não tem cliente ativo e possuidor, o que significa que nenhuma mensagem é entregue a partir desta sessão específica. Uma sessão funciona de muitas maneiras como uma sub fila.

O bloqueio de sessão mantido pelo recetor da sessão é um guarda-chuva para os bloqueios de mensagem utilizados pelo modo de liquidação *de bloqueio de peek..* Um recetor não pode ter duas mensagens em simultâneo "em voo", mas as mensagens devem ser processadas em ordem. Uma nova mensagem só pode ser obtida quando a mensagem prévia tiver sido completada ou com letras mortas. O abandono de uma mensagem faz com que a mesma mensagem seja novamente servida com a próxima operação de receção.

## <a name="message-session-state"></a>Estado da sessão de mensagens

Quando os fluxos de trabalho são processados em sistemas de nuvem de alta escala e de alta disponibilidade, o manipulador de fluxode trabalho associado a uma determinada sessão deve ser capaz de recuperar de falhas inesperadas e também ser capaz de retomar os trabalhos parcialmente concluídos em um diferente processo ou máquina de onde o trabalho começou.

A instalação do estado da sessão permite uma anotação definida pela aplicação de uma sessão de mensagem dentro do corretor, de modo que o estado de processamento registado relativo a essa sessão fica instantaneamente disponível quando a sessão é adquirida por um novo processador.

Do ponto de vista do Ônibus de serviço, o estado da sessão de mensagens é um objeto binário opaco que pode conter dados do tamanho de uma mensagem, que é 256 KB para service bus standard, e 1 MB para Service Bus Premium. O estado de processamento relativo a uma sessão pode ser realizado dentro do estado da sessão, ou o estado da sessão pode apontar para algum local de armazenamento ou registo de base de dados que detenha tais informações.

As APIs para gestão do estado de sessão, [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) e C# [GetState,](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)podem ser encontradas no objeto [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) tanto nas APIs como em Java. Uma sessão que anteriormente não tinha estado definido devolve uma referência **nula** para **o GetState**. A limpeza do estado de sessão previamente definido é feita com [setState (nulo)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

Note que o estado da sessão permanece enquanto não estiver esclarecido (retornando **nulo),** mesmo que todas as mensagens de uma sessão sejam consumidas.

Todas as sessões existentes numa fila ou subscrição podem ser enumeradas com o método **SessionBrowser** no API java e com [O GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) no [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) e [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) no cliente .NET.

O estado da sessão realizado numa fila ou numa subscrição conta para a quota de armazenamento daquela entidade. Quando o pedido estiver concluído com uma sessão, recomenda-se, por isso, que a aplicação limpe o seu estado retido para evitar custos de gestão externos.

## <a name="impact-of-delivery-count"></a>Impacto da contagem de entregas

A definição de contagem de entregas por mensagem no contexto das sessões varia ligeiramente em coma da definição na ausência de sessões. Aqui está uma tabela resumindo quando a contagem de entrega é incrementada.

| Cenário | A contagem de entrega da mensagem é incrementada |
|----------|---------------------------------------------|
| A sessão é aceite, mas o bloqueio da sessão expira (devido ao intervalo) | Sim |
| A sessão é aceite, as mensagens dentro da sessão não estão completas (mesmo que estejam bloqueadas), e a sessão está fechada | Não |
| A sessão é aceite, as mensagens são completadas, e então a sessão é explicitamente fechada | N/A (este é o fluxo padrão. Aqui as mensagens são removidas da sessão) |

## <a name="next-steps"></a>Passos seguintes

- Consulte as [amostras Microsoft.Azure.ServiceBus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) ou [Microsoft.ServiceBus.Mensagens,](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) por exemplo, que utiliza o cliente .NET Framework para lidar com mensagens conscientes da sessão. 

Para saber mais sobre as mensagens do barramento de serviço, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
