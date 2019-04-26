---
title: O Azure Service Bus, exceções de mensagens | Documentos da Microsoft
description: Lista de exceções de mensagens do Service Bus e ações sugeridas.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: aschhab
ms.openlocfilehash: b90e87310bf6dec505176b7f4d4cb9e15ac57c20
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60307782"
---
# <a name="service-bus-messaging-exceptions"></a>Exceções de mensagens do Service Bus
Este artigo lista algumas exceções geradas pelo APIs de mensagens do Microsoft Azure Service Bus. Esta referência está sujeitas a alterações, por isso, verifique novamente a existência de atualizações.

## <a name="exception-categories"></a>Categorias de exceção
As APIs de mensagens geram exceções que podem ser classificados nas seguintes categorias, juntamente com a ação associada que pode tomar para tentar corrigi-los. O significado e as causas de uma exceção podem variar consoante o tipo de entidade de mensagens:

1. Erro de código de utilizador ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System. InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [ System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Ação geral: tentar corrigir o código antes de continuar.
2. Erro de configuração ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System. unauthorizedaccessexception](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Ação geral: rever a configuração e altere se necessário.
3. Exceções transitórias ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [ Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Ação geral: Repita a operação ou notificar os utilizadores. Tenha em atenção que o `RetryPolicy` classe no SDK de cliente pode ser configurado para lidar com repetições automaticamente. Ver [Repita orientações](/azure/architecture/best-practices/retry-service-specific#service-bus) para obter mais informações.
4. Outras exceções ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Ação geral: específico para o tipo de exceção; Veja a tabela na secção seguinte: 

## <a name="exception-types"></a>Tipos de exceção
A tabela seguinte lista os tipos de exceções de mensagens e suas causas e ação sugerida notas que possa realizar.

| **Tipo de exceção** | **Descrição/causa/exemplos** | **Ação sugerida** | **Tenha em atenção aquando da repetição imediata/automático** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |O servidor não respondeu para a operação pedida no período especificado, o que é controlada pela [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). O servidor de pode concluir a operação pedida. Isto pode dever-se a rede ou de outros atrasos de infraestrutura. |Verifique o estado do sistema para manter a consistência e tente novamente se necessário. Ver [exceções de tempo limite](#timeoutexception). |Repetição poderá ajudar em alguns casos; Adicione lógica de repetição ao código. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A operação de utilizador solicitada não é permitida dentro do servidor ou serviço. Ver a mensagem de exceção para obter detalhes. Por exemplo, [concluir ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) gera esta exceção se a mensagem foi recebida na [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) modo. |Verifique o código e a documentação. Certifique-se de que a operação pedida é válida. |Repetição não ajuda. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |É efetuada uma tentativa de invocar uma operação num objeto que já foi fechado, abortada ou eliminado. Em casos raros, a transação de ambiente já foi eliminada. |Verifique o código e certifique-se de que ele não invoca operações num objeto descartado. |Repetição não ajuda. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |O [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objeto não foi possível obter um token, o token é inválido ou o token não contém as declarações necessárias para efetuar a operação. |Certifique-se de que o fornecedor do token é criado com os valores corretos. Verifique a configuração do serviço de controlo de acesso. |Repetição poderá ajudar em alguns casos; Adicione lógica de repetição ao código. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Um ou mais argumentos fornecidos para o método são inválidos.<br /> O URI fornecido a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [criar](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) contém o caminho de modo.<br /> O esquema URI fornecido a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [criar](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) é inválido. <br />O valor da propriedade é maior do que 32 KB. |Verifique o código de chamada e certifique-se de que os argumentos estão corretos. |Repetição não ajuda. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |Entidade associada com a operação não existe ou foi eliminado. |Certifique-se de que a entidade existe. |Repetição não ajuda. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Tentativa de receber uma mensagem com um número de sequência específica. Esta mensagem não foi encontrada. |Certifique-se de que a mensagem já não foi recebida. Verificar a fila de mensagens não entregues para ver se a mensagem tiver sido deadlettered. |Repetição não ajuda. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Cliente não é possível estabelecer uma ligação ao Service Bus. |Certificar-se de que o nome de anfitrião fornecido está correto e o anfitrião está contactável. |Repetição pode ajudar se existem problemas de conectividade intermitente. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Serviço não é capaz de processar o pedido neste momento. |Cliente pode aguardar um período de tempo, em seguida, repita a operação. |Cliente pode voltar a tentar após determinado intervalo. Se uma repetição resulta numa exceção de diferente, verifique o comportamento de repetição dessa exceção. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Associados à mensagem de token de bloqueio expirou ou o token de bloqueio não foi encontrado. |Descarte a mensagem. |Repetição não ajuda. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |Bloqueio associado a esta sessão é perdido. |Abortar a [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) objeto. |Repetição não ajuda. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Genérico mensagens de exceção que pode ser gerada nos seguintes casos:<br /> É efetuada uma tentativa de criar uma [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) usando um nome ou o caminho que pertence a um tipo de entidade diferentes (por exemplo, um tópico).<br />  É efetuada uma tentativa para enviar uma mensagem maior do que 256 KB. O servidor ou serviço encontrou um erro durante o processamento do pedido. Ver a mensagem de exceção para obter detalhes. Isto é, normalmente, uma exceção transitória. |Verifique o código e certifique-se de que apenas os objetos serializáveis são utilizados para o corpo da mensagem (ou usar um serializador personalizado). Verifique a documentação para os tipos de valor suportado das propriedades e apenas os tipos de utilização suportado. Verifique os [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) propriedade. Se for **true**, pode repetir a operação. |Comportamento de repetição é indefinido e não poderá ajudar. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Tentativa de criar uma entidade com um nome que já está a ser utilizado por outra entidade nesse espaço de nomes de serviço. |Eliminar a entidade existente ou escolha um nome diferente para a entidade a ser criada. |Repetição não ajuda. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |A entidade de mensagens atingiu o tamanho máximo admissível, ou foi excedido o número máximo de ligações para um espaço de nomes. |Crie espaço na entidade por receber mensagens a partir da entidade ou seu subfilas. Ver [QuotaExceededException](#quotaexceededexception). |Repetição pode ajudar se as mensagens foram removidas enquanto isso. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Barramento de serviço retorna essa exceção se tentar criar uma ação de regra inválida. Do Service Bus anexa essa exceção para uma mensagem de deadlettered se ocorrer um erro ao processar a ação de regra para essa mensagem. |Verifique a ação de regra para correção. |Repetição não ajuda. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |Barramento de serviço retorna essa exceção se tentar criar um filtro inválido. Do Service Bus anexa essa exceção para uma mensagem de deadlettered se Ocorreu um erro ao processar o filtro para essa mensagem. |Verifique o filtro para correção. |Repetição não ajuda. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Tentar aceitar uma sessão com um ID de sessão específico, mas a sessão está atualmente bloqueada por outro cliente. |Certifique-se de que a sessão é desbloqueada por outros clientes. |Repetição pode ajudar se a sessão foi lançada até lá. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Demasiadas operações fazem parte da transação. |Reduza o número de operações que fazem parte desta transação. |Repetição não ajuda. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Pedido para uma operação de tempo de execução numa entidade desativada. |Ative a entidade. |Repetição pode ajudar se a entidade tiver sido ativada até lá. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Do Service Bus retorna essa exceção, se envia uma mensagem para um tópico que tenha filtragem previamente ativado e corresponder a nenhum dos filtros. |Certificar-se de que corresponde a, pelo menos, um filtro. |Repetição não ajuda. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Um payload de mensagem excede o limite de 256 KB. O limite de 256 KB é o tamanho total de mensagens, que pode incluir as propriedades do sistema e qualquer sobrecarga de .NET. |Reduzir o tamanho da carga de mensagem, em seguida, repita a operação. |Repetição não ajuda. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |A transação de ambiente (*Transaction. Current*) é inválido. Poderá ter foi concluída ou abortada. Exceção interna pode fornecer informações adicionais. | |Repetição não ajuda. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |É tentada uma operação numa transação que estiver em dúvida, ou é efetuada uma tentativa de consolidar a transação e se torna a transação em dúvida. |A aplicação tem de processar essa exceção (como um caso especial), como a transação pode já ter sido comprometida. |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) indica que foi excedida uma quota de uma entidade específica.

### <a name="queues-and-topics"></a>Filas e tópicos
Para filas e tópicos, isto é, muitas vezes, o tamanho da fila. A propriedade da mensagem de erro contém mais detalhes, como no exemplo seguinte:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: ‘xxx-xxx-xxx’. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

A mensagem indica que o tópico foi excedido o limite de tamanho, neste caso 1 GB (o limite do tamanho predefinido). 

### <a name="namespaces"></a>Espaços de nomes

Para espaços de nomes, [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) pode indicar que um aplicativo excedeu o número máximo de ligações para um espaço de nomes. Por exemplo:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>Causas comuns
Existem duas causas comuns deste erro: a fila de mensagens não entregues e não funcional recetores de mensagem.

1. **[Fila de mensagens não entregues](service-bus-dead-letter-queues.md)**  um leitor está a falhar concluir as mensagens e as mensagens são devolvidas para a fila/tópico quando o bloqueio expira. Isto pode acontecer se o leitor de encontrar uma exceção que o impede de chamar [BrokeredMessage.Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). Depois de uma mensagem foi lida 10 vezes, ele passa para a fila de mensagens não entregues por predefinição. Este comportamento é controlado pelos [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) propriedade e tem um valor predefinido de 10. Como mensagens acumuladas na fila entregues, eles ocupam espaço.
   
    Para resolver o problema, leia e conclua as mensagens da fila de mensagens não entregues, tal como faria com qualquer outra fila. Pode utilizar o [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) método para ajudar a formatar o caminho de fila de mensagens não entregues.
2. **Recetor parado**. Um recetor parou a receção de mensagens de fila ou subscrição. A forma de identificar isso é examinar o [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) propriedade, que mostra a divisão completa das mensagens. Se o [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) propriedade é alta ou cada vez maior, em seguida, as mensagens não estão a ser lidos o mais rápido que eles estão sendo gravados.

## <a name="timeoutexception"></a>TimeoutException
R [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica que uma operação iniciada pelo utilizador está a demorar mais tempo do que o tempo limite da operação. 

Deve verificar o valor do [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) propriedade, como atingir este limite também pode causar um [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

### <a name="queues-and-topics"></a>Filas e tópicos
Para filas e tópicos, o tempo limite é especificado no [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) propriedade, como parte da cadeia de ligação, ou por meio de [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). A própria mensagem de erro pode variar, mas sempre contém o valor de tempo limite especificado para a operação atual. 



## <a name="next-steps"></a>Passos Seguintes

Para obter a referência de API .NET do Service Bus completa, consulte a [referência da API de .NET do Azure](/dotnet/api/overview/azure/service-bus).

Para saber mais sobre [do Service Bus](https://azure.microsoft.com/services/service-bus/), veja os artigos seguintes:

* [Descrição geral das mensagens do Service Bus](service-bus-messaging-overview.md)
* [Arquitetura do Service Bus](service-bus-architecture.md)

