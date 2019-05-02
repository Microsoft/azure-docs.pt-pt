---
title: Sessões de mensagens do Service Bus do Azure | Documentos da Microsoft
description: Lidar com seqüências de mensagens do Service bus do Azure com sessões.
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
ms.openlocfilehash: 2c206d42e220534225cfef0415a65c1f9494f761
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64569789"
---
# <a name="message-sessions-first-in-first-out-fifo"></a>Sessões de mensagens: primeiro, primeiro out (FIFO) 

Sessões do Microsoft Azure Service Bus permitem a manipulação de conjunta e ordenada de sequências não vinculadas de mensagens relacionadas. Para obter uma garantia de FIFO no Service Bus, utilize sessões. Barramento de serviço não é prescritivo sobre a natureza da relação entre as mensagens e também não define um modelo específico para determinar em que uma sequência de mensagem inicia ou termina.

> [!NOTE]
> O escalão básico do Service Bus não suporta a sessões. Os escalões standard e premium suportam sessões. Para obter mais informações, consulte [preços do Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

Qualquer remetente pode criar uma sessão quando enviar mensagens para um tópico ou fila definindo a [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) propriedade para algum identificador definida pelo aplicativo que seja exclusivo para a sessão. Ao nível do protocolo AMQP 1.0, este valor é mapeado para o *id de grupo* propriedade.

Com reconhecimento de sessão filas ou subscrições, sessões entram em existência quando existe pelo menos uma mensagem com a sessão [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId). Uma vez que existe uma sessão, não existe tempo definido ou API para quando a sessão expira ou desaparece. Teoricamente, pode ser recebida uma mensagem para uma sessão de hoje em dia, a mensagem seguinte no tempo de um ano e se o **SessionId** corresponder, a sessão é o mesmo do ponto de vista do Service Bus.

Normalmente, no entanto, um aplicativo tem uma noção clara de onde um conjunto de mensagens relacionadas começa e termina. Do Service Bus não define quaisquer regras específicas.

Um exemplo de como delinear uma seqüência para transferir um ficheiro é definir o **etiqueta** propriedade para a primeira mensagem **iniciar**, para que as mensagens intermediárias **conteúdo**, e para a última mensagem **final**. A posição relativa das mensagens de conteúdo pode ser calculada como a mensagem atual *SequenceNumber* delta a partir do **iniciar** mensagem *SequenceNumber*.

A funcionalidade de sessão ativa do Service Bus específico receber operação, na forma de [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) no c# e APIs de Java. Ativar a funcionalidade definindo a [Requersessão](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) propriedade na fila ou subscrição através do Azure Resource Manager, ou ao definir o sinalizador no portal. Isto é necessário antes de tentar usar as operações de API relacionadas.

No portal, defina o sinalizador com a caixa de verificação seguinte:

![][2]

As APIs para sessões existem em clientes de fila e subscrição. Existe um modelo de imperativo que controla quando as sessões e as mensagens são recebidas e um manipulador de modelo baseado em, semelhante à *OnMessage*, que oculta a complexidade de gerir a receção de loop.

## <a name="session-features"></a>Funcionalidades de sessão

Sessões fornecem simultâneas da demultiplexação dos fluxos de mensagens intercalada enquanto preserva e garantindo a entrega ordenada.

![][1]

R [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) recetor é criado pelo cliente abertos ao recebimento de uma sessão. As chamadas de cliente [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) ou [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) em c#. No modelo de retorno de chamada reativo, ele registra um manipulador de sessão.

Quando o [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) objeto é aceite e enquanto ele é mantido por um cliente, esse cliente mantém um bloqueio exclusivo em todas as mensagens com essa sessão [SessionId](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) que existe na fila ou subscrição, e também em todas as mensagens com isso **SessionId** que chegam ainda enquanto a sessão é mantida.

O bloqueio seja liberado quando **feche** ou **CloseAsync** são chamados, ou quando o bloqueio expira em casos em que o aplicativo é não é possível efetuar a operação de fecho. O bloqueio de sessão deve ser tratado como um bloqueio exclusivo num ficheiro, que significa que o aplicativo deve fechar a sessão assim que ele já não precisa dele e/ou não espera que as mensagens adicionais.

Quando vários recetores simultâneos retirará da fila, as mensagens que pertencem a uma sessão em particular são expedidas para o recetor específico que atualmente possui o bloqueio da sessão. Com essa operação, um fluxo de mensagens intercalada que reside numa fila ou subscrição corretamente anular multiplexado a recetores diferentes e os recetores também podem residir nos computadores de cliente diferentes, uma vez que o gerenciamento de bloqueio acontece lado do serviço, dentro de O Service Bus.

A ilustração anterior mostra três recetores de sessão simultâneos. Uma sessão com `SessionId` = 4 não tem nenhum cliente Active Directory, proprietário, o que significa que nenhuma mensagem sejam entregues desta sessão específica. Uma sessão atua em diversos aspectos, como uma fila de sub-rotina.

O bloqueio de sessão mantido pelo recetor de sessão é uma abrangência para os bloqueios de mensagem utilizado pelas *bloqueio de pré-visualização* modo de liquidação. Um recetor não pode ter duas mensagens ao mesmo tempo "em trânsito", mas as mensagens têm de ser processadas por ordem. Só pode ser obtida uma nova mensagem quando a mensagem anterior foi concluída ou lettered em papel já era. Abandonar um causas de mensagem a mesma mensagem que sejam disponibilizados novamente com a próxima receber operação.

## <a name="message-session-state"></a>Estado da sessão de mensagem

Quando os fluxos de trabalho são processados em grande escala, sistemas na cloud de elevada disponibilidade, o manipulador de fluxo de trabalho associado a uma determinada sessão tem de ser capaz de recuperar de falhas inesperadas e também ter a capacidade de retoma o trabalho parcialmente concluído no outro processo ou máquina a partir de onde o trabalho começou.

O recurso de estado de sessão permite uma anotação definida pelo aplicativo de uma sessão de mensagem dentro do Mediador, para que o estado de processamento gravado em relação ao sessão fica instantaneamente disponível quando a sessão é obtida por um processador de novo.

Da perspectiva do Service Bus, o estado da sessão de mensagem é um objeto opaco binário que pode conter dados do tamanho de uma mensagem, que é 256 KB para o Service Bus Standard e de 1 MB para Premium do Service Bus. O estado de processamento em relação uma sessão pode ser mantido dentro do Estado da sessão ou o estado da sessão pode apontar para alguns localização de armazenamento ou o registo de base de dados que contém essas informações.

As APIs para o gerenciamento de estado de sessão, [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) e [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), pode ser encontrado no [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) objeto no c# e APIs de Java. Uma sessão que não tinha anteriormente nenhum Estado de sessão definido devolve um **nulo** fazem referência para **GetState**. Limpar o estado da sessão definidas anteriormente é feito com [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

Tenha em atenção que o estado da sessão permanece, desde que ele não será eliminado (retornando **nulo**), mesmo se todas as mensagens numa sessão são consumidas.

Todas as sessões existentes numa fila ou subscrição podem ser enumeradas com o **SessionBrowser** método na API de Java e com [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) sobre o [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) e [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) no cliente do .NET.

O estado da sessão contidas numa fila ou em que contagens de uma subscrição para a quota de armazenamento dessa entidade. Quando a aplicação é concluída com uma sessão, recomenda-se, por conseguinte, para a aplicação limpar o seu estado retido para evitar o custo de gestão externo.

## <a name="next-steps"></a>Passos Seguintes

- Consulte a [Microsoft.Azure.ServiceBus exemplos](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) ou [Microsoft.ServiceBus.Messaging exemplos](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) para obter um exemplo que utiliza o cliente do .NET Framework para lidar com mensagens com reconhecimento de sessão. 

Para saber mais sobre mensagens do Service Bus, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
