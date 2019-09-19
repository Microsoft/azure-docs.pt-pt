---
title: Sessões de mensagens do barramento de serviço do Azure | Microsoft Docs
description: Manipule sequências de mensagens do barramento de serviço do Azure com sessões.
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
ms.openlocfilehash: 7264b8e5a536c90d106b3bf4a5e26093744327d6
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091820"
---
# <a name="message-sessions-first-in-first-out-fifo"></a>Sessões de mensagens: PEPS (primeiro a entrar, primeiro a sair) 

As sessões de Barramento de Serviço do Microsoft Azure permitem o tratamento em conjunto e ordenado de sequências não associadas de mensagens relacionadas. Para obter uma garantia FIFO no barramento de serviço, use sessões. O barramento de serviço não é prescritiva sobre a natureza da relação entre as mensagens e também não define um modelo específico para determinar onde uma sequência de mensagens inicia ou termina.

> [!NOTE]
> A camada básica do barramento de serviço não oferece suporte a sessões. As camadas Standard e Premium dão suporte a sessões. Para obter mais informações, consulte [preços do barramento de serviço](https://azure.microsoft.com/pricing/details/service-bus/).

Qualquer remetente pode criar uma sessão ao enviar mensagens para um tópico ou fila definindo a propriedade [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) como um identificador definido pelo aplicativo que é exclusivo para a sessão. No nível do protocolo AMQP 1,0, esse valor é mapeado para a propriedade *Group-ID* .

Em filas ou assinaturas com reconhecimento de sessão, as sessões entram em existência quando há pelo menos uma mensagem com a [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId)da sessão. Quando uma sessão existe, não há tempo definido ou API para quando a sessão expira ou desaparece. Teoricamente, uma mensagem pode ser recebida para uma sessão hoje, a próxima mensagem no horário de um ano e, se a **SessionID** corresponder, a sessão será a mesma da perspectiva do barramento de serviço.

Normalmente, no entanto, um aplicativo tem uma noção clara de onde um conjunto de mensagens relacionadas começa e termina. O barramento de serviço não define nenhuma regra específica.

Um exemplo de como delinear uma sequência para transferir um arquivo é definir a propriedade **Label** para a primeira mensagem como **Start**, para mensagens intermediárias para **Content**e para a última mensagem como **end**. A posição relativa das mensagens de conteúdo pode ser computada como a mensagem atual *SequenceNumber* Delta da mensagem de **início** *SequenceNumber*.

O recurso de sessão no barramento de serviço habilita uma operação de recebimento específica, na [](/dotnet/api/microsoft.servicebus.messaging.messagesession) forma de MessageSession C# nas APIs do e do Java. Habilite o recurso definindo a propriedade [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) na fila ou assinatura por meio de Azure Resource Manager ou definindo o sinalizador no Portal. Isso é necessário antes de tentar usar as operações de API relacionadas.

No portal, defina o sinalizador com a caixa de seleção a seguir:

![][2]

> [!NOTE]
> Quando as sessões são habilitadas em uma fila ou em uma assinatura, os aplicativos cliente ***não podem mais*** enviar/receber mensagens regulares. Todas as mensagens devem ser enviadas como parte de uma sessão (definindo a ID de sessão) e recebidas recebendo a sessão.

As APIs para sessões existem em clientes de fila e de assinatura. Há um modelo imperativo que controla quando as sessões e mensagens são recebidas e um modelo baseado em manipulador, semelhante ao *onMessage*, que oculta a complexidade do gerenciamento do loop de recebimento.

## <a name="session-features"></a>Recursos de sessão

As sessões fornecem a Desmultiplexação simultânea de fluxos de mensagens intercaladas enquanto preservam e garantem a entrega ordenada.

![][1]

Um receptor de [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) é criado pelo cliente aceitando uma sessão. O cliente chama [QueueClient. AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) ou [QueueClient. AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) no C#. No modelo de retorno de chamada reativo, ele registra um manipulador de sessão.

Quando o objeto [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) é aceito e enquanto é mantido por um cliente, esse cliente mantém um bloqueio exclusivo em todas as mensagens com a [SessionID](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) da sessão que existe na fila ou assinatura e também em todas as mensagens com essa **SessionID** isso ainda chega enquanto a sessão é mantida.

O bloqueio é liberado quando **Close** ou **CloseAsync** são chamados ou quando o bloqueio expira em casos em que o aplicativo não consegue executar a operação de fechamento. O bloqueio de sessão deve ser tratado como um bloqueio exclusivo em um arquivo, o que significa que o aplicativo deve fechar a sessão assim que não precisar mais dela e/ou não esperar nenhuma mensagem adicional.

Quando vários destinatários simultâneos efetuam pull da fila, as mensagens que pertencem a uma sessão específica são expedidas para o receptor específico que atualmente mantém o bloqueio para essa sessão. Com essa operação, um fluxo de mensagens intercaladas que reside em uma fila ou assinatura é limpo de forma limpa para destinatários diferentes e esses receptores também podem residir em computadores cliente diferentes, pois o gerenciamento de bloqueios ocorre no lado do serviço, dentro Barramento de serviço.

A ilustração anterior mostra três receptores de sessão simultâneos. Uma sessão com `SessionId` = 4 não tem nenhum cliente proprietário ativo, o que significa que nenhuma mensagem é entregue dessa sessão específica. Uma sessão atua de várias maneiras, como uma subfila.

O bloqueio de sessão mantido pelo receptor de sessão é uma proteção para os bloqueios de mensagem usados pelo modo de liquidação de *bloqueio de Peek* . Um receptor não pode ter duas mensagens simultaneamente "em trânsito", mas as mensagens devem ser processadas na ordem. Uma nova mensagem só pode ser obtida quando a mensagem anterior tiver sido concluída ou estiver inativa. Abandonar uma mensagem faz com que a mesma mensagem seja servida novamente com a próxima operação de recebimento.

## <a name="message-session-state"></a>Estado da sessão da mensagem

Quando os fluxos de trabalho são processados em sistemas de nuvem de alta disponibilidade e de grande escala, o manipulador de fluxo de trabalho associado a uma sessão específica deve ser capaz de se recuperar de falhas inesperadas e também ser capaz de retomar trabalhos parcialmente concluídos em um outro processo ou máquina de onde o trabalho começou.

O recurso de estado de sessão permite uma anotação definida pelo aplicativo de uma sessão de mensagem dentro do agente, para que o estado de processamento registrado relativo a essa sessão se torne instantaneamente disponível quando a sessão for adquirida por um novo processador.

Da perspectiva do barramento de serviço, o estado de sessão da mensagem é um objeto binário opaco que pode conter dados do tamanho de uma mensagem, que é 256 KB para o barramento de serviço Standard e 1 MB para o barramento de serviço Premium. O estado de processamento relativo a uma sessão pode ser mantido dentro do estado da sessão ou o estado da sessão pode apontar para algum local de armazenamento ou registro de banco de dados que contém essas informações.

As APIs para gerenciar o estado da sessão, [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) e [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), podem ser encontradas no objeto [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) nas APIs C# do e do Java. Uma sessão que anteriormente não tinha nenhum estado de sessão definido retorna uma referência **nula** para **GetState**. Limpar o estado de sessão definido anteriormente é feito com [SetState (NULL)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

Observe que o estado da sessão permanece desde que não seja apagado (retornando **NULL**), mesmo que todas as mensagens em uma sessão sejam consumidas.

Todas as sessões existentes em uma fila ou assinatura podem ser enumeradas com o método **SessionBrowser** na API do Java e com [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) no [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) e no [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) no cliente .net.

O estado de sessão mantido em uma fila ou em uma assinatura conta com relação à cota de armazenamento da entidade. Quando o aplicativo é concluído com uma sessão, é recomendável que o aplicativo Limpe seu estado retido para evitar custo de gerenciamento externo.

## <a name="impact-of-delivery-count"></a>Impacto da contagem de entrega

A definição de contagem de entrega por mensagem no contexto de sessões varia um pouco da definição no a ausência de sessões. Aqui está uma tabela Resumindo quando a contagem de entrega é incrementada.

| Cenário | A contagem de entrega da mensagem é incrementada |
|----------|---------------------------------------------|
| A sessão é aceita, mas o bloqueio de sessão expira (devido ao tempo limite) | Sim |
| A sessão é aceita, as mensagens dentro da sessão não são concluídas (mesmo se estiverem bloqueadas) e a sessão será fechada | Não |
| A sessão é aceita, as mensagens são concluídas e, em seguida, a sessão é fechada explicitamente | N/A (esse é o fluxo padrão. Estas mensagens são removidas da sessão) |

## <a name="next-steps"></a>Passos Seguintes

- Consulte os exemplos de [Microsoft. Azure. ServiceBus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) ou [Microsoft. ServiceBus. Messaging](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) para obter um exemplo que usa o cliente .NET Framework para lidar com mensagens com reconhecimento de sessão. 

Para saber mais sobre as mensagens do barramento de serviço, consulte os seguintes tópicos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
