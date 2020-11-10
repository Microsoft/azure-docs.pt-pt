---
title: Azure Service Bus - exceções de mensagens / Microsoft Docs
description: Este artigo fornece uma lista de exceções de mensagens Azure Service Bus e sugeriu ações a serem tomadas quando a exceção ocorre.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e4aa6d82c20e21caabf0205d7446cf88ed8b7f34
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94409319"
---
# <a name="service-bus-messaging-exceptions"></a>Exceções de mensagens de autocarro de serviço
Este artigo enumera as exceções .NET geradas por .NET Framework APIs. 

## <a name="exception-categories"></a>Categorias de exceções
As APIs de mensagens geram exceções que podem cair nas seguintes categorias, juntamente com as ações associadas que pode tomar para tentar corrigi-las. O significado e as causas de uma exceção podem variar dependendo do tipo de entidade de mensagens:

1. Erro de codificação do utilizador[(System.ArgumentException](/dotnet/api/system.argumentexception?view=netcore-3.1&preserve-view=true), [System.InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1&preserve-view=true), [System.OperationCanceledException](/dotnet/api/system.operationcanceledexception?view=netcore-3.1&preserve-view=true), [System.Runtime.Serialization.SerializationException](/dotnet/api/system.runtime.serialization.serializationexception?view=netcore-3.1&preserve-view=true)). Ação geral: tente fixar o código antes de prosseguir.
2. Erro de configuração/configuração[(Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System.UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1&preserve-view=true). Ação geral: reveja a sua configuração e altere se necessário.
3. Exceções transitórias[(Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException).](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) Ação geral: re-tentar a operação ou notificar os utilizadores. A `RetryPolicy` classe no cliente SDK pode ser configurada para lidar com as recaídas automaticamente. Para obter mais informações, consulte [a orientação da Retry.](/azure/architecture/best-practices/retry-service-specific#service-bus)
4. Outras exceções ([System.TransactionException](/dotnet/api/system.transactions.transactionexception?view=netcore-3.1&preserve-view=true), [System.TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1&preserve-view=true), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Ação geral: específica do tipo de exceção; consulte a tabela na secção seguinte: 

## <a name="exception-types"></a>Tipos de exceções
A tabela que se segue lista tipos de exceções de mensagens e as suas causas, e notas sugeridas a medidas que pode tomar.

| **Tipo de Exceção** | **Descrição/Causas/Exemplos** | **Ação Sugerida** | **Nota sobre relagem automática/imediata** |
| --- | --- | --- | --- |
| [TimeoutExcepção](/dotnet/api/system.timeoutexception?view=netcore-3.1&preserve-view=true) |O servidor não respondeu à operação solicitada dentro do tempo especificado, que é controlado pelo [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). O servidor pode ter concluído a operação solicitada. Pode acontecer devido a atrasos na rede ou em outras infraestruturas. |Verifique se o estado do sistema é necessário para obter consistência e volte a tentar, se necessário. Ver [exceções de timeout](#timeoutexception). |Redação pode ajudar em alguns casos; adicionar lógica de retíria ao código. |
| [InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1&preserve-view=true) |A operação de utilizador solicitada não é permitida dentro do servidor ou serviço. Consulte a mensagem de exceção para mais detalhes. Por exemplo, [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) gera esta exceção se a mensagem foi recebida no modo [ReceiveAndDelete.](/dotnet/api/microsoft.azure.servicebus.receivemode) |Verifique o código e a documentação. Certifique-se de que a operação solicitada é válida. |Tentar não ajuda. |
| [OperaçãoCanceledException](/dotnet/api/system.operationcanceledexception?view=netcore-3.1&preserve-view=true) |Uma tentativa é feita para invocar uma operação num objeto que já foi fechado, abortado ou eliminado. Em casos raros, a transação ambiente já está eliminada. |Verifique o código e certifique-se de que não invoca operações num objeto eliminado. |Tentar não ajuda. |
| [Não autorizadoAccessExcepção](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1&preserve-view=true) |O objeto [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) não conseguiu adquirir um token, o token é inválido, ou o token não contém as alegações necessárias para fazer a operação. |Certifique-se de que o fornecedor de fichas é criado com os valores corretos. Verifique a configuração do Serviço de Controlo de Acesso. |Redação pode ajudar em alguns casos; adicionar lógica de retíria ao código. |
| [ArgumentoExcepção](/dotnet/api/system.argumentexception?view=netcore-3.1&preserve-view=true)<br /> [Argumentação](/dotnet/api/system.argumentnullexception?view=netcore-3.1&preserve-view=true)<br />[ArgumentOutOfRangeException](/dotnet/api/system.argumentoutofrangeexception?view=netcore-3.1&preserve-view=true) |Um ou mais argumentos fornecidos ao método são inválidos.<br /> O URI fornecido ao [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [criar](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) contém segmento de caminhos.<br /> O esquema URI fornecido ao [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) é inválido. <br />O valor da propriedade é maior que 32 KB. |Verifique o código de chamada e certifique-se de que os argumentos estão corretos. |Tentar não ajuda. |
| [Entidade de MensagensNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |A entidade associada à operação não existe ou foi eliminada. |Certifique-se de que a entidade existe. |Tentar não ajuda. |
| [MensagemNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Tente receber uma mensagem com um número de sequência particular. Esta mensagem não foi encontrada. |Certifique-se de que a mensagem ainda não foi recebida. Verifique a fila da carta para ver se a mensagem foi morta. |Tentar não ajuda. |
| [MensagensComumicoexcepção](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |O cliente não é capaz de estabelecer uma ligação com o Service Bus. |Certifique-se de que o nome do anfitrião fornecido está correto e que o hospedeiro está acessível. <p>Se o seu código funcionar num ambiente com uma firewall/proxy, certifique-se de que o tráfego para o domínio de Serviço Bus/endereço IP e portas não está bloqueado.</p>|Redatório pode ajudar se houver problemas de conectividade intermitentes. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |O serviço não é capaz de processar o pedido neste momento. |O cliente pode esperar por um período de tempo e depois voltar a tentar a operação. |O cliente pode voltar a tentar após determinado intervalo. Se uma nova mente resultar numa exceção diferente, verifique o comportamento de repetição dessa exceção. |
| [MensagensExcepção](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Exceção genérica de mensagens que podem ser lançadas nos seguintes casos:<p>Uma tentativa é feita para criar um [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) usando um nome ou caminho que pertence a um tipo de entidade diferente (por exemplo, um tópico).</p><p>Uma tentativa é feita para enviar uma mensagem maior que 256 KB. </p>O servidor ou serviço encontrou um erro durante o processamento do pedido. Consulte a mensagem de exceção para mais detalhes. Normalmente é uma exceção transitória.</p><p>O pedido foi encerrado porque a entidade está a ser estrangulada. Código de erro: 50001, 50002, 50008. </p> | Verifique o código e certifique-se de que apenas são utilizados objetos serializáveis para o corpo da mensagem (ou utilize um serializador personalizado). <p>Verifique a documentação para os tipos de valor suportados das propriedades e utilize apenas tipos suportados.</p><p> Verifique a propriedade [IsTransient.](/dotnet/api/microsoft.servicebus.messaging.messagingexception) Se for **verdade,** pode voltar a tentar a operação. </p>| Se a exceção se dever a estrangulamento, aguarde alguns segundos e volte a tentar a operação. O comportamento relemderecido é indefinido e pode não ajudar em outros cenários.|
| [Entidade de MensagensAlreadyExistsExcepção](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Tente criar uma entidade com um nome que já seja utilizado por outra entidade nesse espaço de nome de serviço. |Elimine a entidade existente ou escolha um nome diferente para a entidade a criar. |Tentar não ajuda. |
| [QuotaExceededexcepção](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |A entidade de mensagens atingiu o seu tamanho máximo admissível, ou o número máximo de ligações a um espaço de nome foi ultrapassado. |Criar espaço na entidade através da receção de mensagens da entidade ou das suas subquesidades. Ver [QuotaExceededException](#quotaexceededexception). |A recandidinha poderá ajudar se as mensagens tiverem sido removidas entretanto. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |A Service Bus devolve esta exceção se tentar criar uma ação de regra inválida. A Service Bus anexa esta exceção a uma mensagem sem letra se ocorrer um erro durante o processamento da ação de regra para essa mensagem. |Verifique a correção da regra. |Tentar não ajuda. |
| [FiltragemExcepção](/dotnet/api/microsoft.servicebus.messaging.filterexception) |A Service Bus devolve esta exceção se tentar criar um filtro inválido. A Service Bus anexa esta exceção a uma mensagem sem letra se ocorrer um erro durante o processamento do filtro para essa mensagem. |Verifique se o filtro está correto. |Tentar não ajuda. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Tente aceitar uma sessão com um ID de sessão específica, mas a sessão está atualmente bloqueada por outro cliente. |Certifique-se de que a sessão está desbloqueada por outros clientes. |A recandidinha poderá ajudar se a sessão for lançada de forma provisória. |
| [TransaçõesExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Muitas operações fazem parte da transação. |Reduza o número de operações que fazem parte desta transação. |Tentar não ajuda. |
| [Entidade de MensagensDisabledExcepção](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Pedido de operação de execução a uma entidade deficiente. |Ativar a entidade. |A recandidologia poderá ajudar se a entidade tiver sido ativada de forma provisória. |
| [NomatchingSubscriptionExcepção](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |A Service Bus devolve esta exceção se enviar uma mensagem para um tópico que tenha pré-filtragem ativada e nenhum dos filtros corresponda. |Certifique-se de que pelo menos um filtro corresponde. |Tentar não ajuda. |
| [MensagemSizeExceedExcepção](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Uma carga útil de mensagem excede o limite de 256 KB. O limite de 256 KB é o tamanho total da mensagem, que pode incluir propriedades do sistema e qualquer sobrecarga .NET. |Reduza o tamanho da carga útil da mensagem e, em seguida, relemisse a operação. |Tentar não ajuda. |
| [TransacçãoExcepção](/dotnet/api/system.transactions.transactionexception) |A transação ambiente *(Transação.Corrente)* é inválida. Pode ter sido concluído ou abortado. A exceção interior pode fornecer informações adicionais. | |Tentar não ajuda. |
| [TransaçãoInDoubtExcepção](/dotnet/api/system.transactions.transactionindoubtexception) |Uma operação é tentada numa transação que está em dúvida, ou uma tentativa de efetivo da transação e a transação fica em dúvida. |O seu pedido deve tratar desta exceção (como um caso especial), uma vez que a transação pode já ter sido cometida. |- |

## <a name="quotaexceededexception"></a>QuotaExceededexcepção
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) indica que foi excedida uma quota de uma entidade específica.

### <a name="queues-and-topics"></a>Filas e tópicos
Para filas e tópicos, é muitas vezes o tamanho da fila. A propriedade da mensagem de erro contém mais detalhes, como no exemplo seguinte:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: 'xxx-xxx-xxx'. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

A mensagem indica que o tópico excedeu o seu limite de tamanho, neste caso 1 GB (o limite de tamanho padrão). 

### <a name="namespaces"></a>Espaços de nomes

Para espaços com nomes, [o QuotaExceedededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) pode indicar que uma aplicação excedeu o número máximo de ligações a um espaço com nomes. Por exemplo:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

### <a name="common-causes"></a>Causas comuns
Há duas causas comuns para este erro: a fila das letras mortas e os recetores de mensagens não funcionais.

1. **[Fila de cartas mortas](service-bus-dead-letter-queues.md)** Um leitor não está a completar mensagens e as mensagens são devolvidas à fila/tópico quando o bloqueio expira. Pode acontecer se o leitor encontrar uma exceção que o impeça de chamar [BrokeredMessage.Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). Depois de uma mensagem ter sido lida 10 vezes, move-se para a fila da letra morta por defeito. Este comportamento é controlado pela propriedade [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) e tem um valor padrão de 10. À medida que as mensagens se acumulam na fila das cartas mortas, tomam espaço.
   
    Para resolver o problema, leia e complete as mensagens da fila das cartas mortas, como faria em qualquer outra fila. Pode utilizar o método [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) para ajudar a formatar o caminho da fila de letras mortas.
2. **Recetor parado**. Um recetor deixou de receber mensagens de uma fila ou subscrição. A forma de identificar isto é olhar para a propriedade [QueueDescription.MessageCountDetails,](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) que mostra a desagregação completa das mensagens. Se a propriedade [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) é alta ou em crescimento, então as mensagens não estão a ser lidas tão rapidamente como estão a ser escritas.

## <a name="timeoutexception"></a>TimeoutExcepção
Um [TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1&preserve-view=true) indica que uma operação iniciada pelo utilizador está a demorar mais tempo do que o tempo limite de funcionamento. 

Deve verificar o valor da propriedade [ServicePointManager.DefaultConnectionLimit,](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit?view=netcore-3.1&preserve-view=true) uma vez que bater este limite também pode causar uma [timeoutExcepção](/dotnet/api/system.timeoutexception?view=netcore-3.1&preserve-view=true).

Espera-se que os intervalos de tempo ocorram durante ou entre operações de manutenção, tais como atualizações do serviço Service Bus (ou) atualizações de SISTEMA sobre os recursos que executam o serviço. Durante as atualizações do SO, as entidades são movidas e os nós são atualizados ou reiniciados, o que pode causar intervalos de tempo. Para obter detalhes do contrato de nível de serviço (SLA) para o serviço de autocarros Azure Service, consulte [SLA for Service Bus](https://azure.microsoft.com/support/legal/sla/service-bus/).


### <a name="queues-and-topics"></a>Filas e tópicos
Para filas e tópicos, o tempo limite é especificado quer na propriedade [MessagingFactorySettings.OperationTimeout,](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) como parte da cadeia de ligação, quer através do [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). A mensagem de erro em si pode variar, mas contém sempre o valor de tempo limite especificado para a operação atual. 

## <a name="messagelocklostexception"></a>MessageLockLostException

### <a name="cause"></a>Causa

O **MessageLockLostException** é lançado quando uma mensagem é recebida usando o modo [PeekLock](message-transfers-locks-settlement.md#peeklock) Receber e o bloqueio mantido pelo cliente expira no lado de serviço.

O bloqueio de uma mensagem pode expirar devido a várias razões - 

  * O temporizador de bloqueio expirou antes de ser renovado pela aplicação do cliente.
  * A aplicação do cliente adquiriu o cadeado, guardou-o para uma loja persistente e recomeçou. Uma vez reiniciado, a aplicação do cliente olhou para as mensagens de bordo e tentou completá-las.

### <a name="resolution"></a>Resolução

No caso de uma **MessageLockLostException,** a aplicação do cliente já não pode processar a mensagem. A aplicação do cliente pode considerar opcionalmente registar a exceção para análise, mas o cliente *deve* dispor da mensagem.

Uma vez que o bloqueio da mensagem expirou, voltaria à Fila (ou Subscrição) e pode ser processado pela próxima aplicação do cliente que as chamadas recebem.

Se o **MaxDeliveryCount** tiver excedido, a mensagem poderá ser transferida para a **DeadLetterQueue**.

## <a name="sessionlocklostexception"></a>SessionLockLostException

### <a name="cause"></a>Causa

O **SessionLockLostException** é lançado quando uma sessão é aceite e o bloqueio mantido pelo cliente expira no lado do serviço.

O bloqueio de uma sessão pode expirar por várias razões - 

  * O temporizador de bloqueio expirou antes de ser renovado pela aplicação do cliente.
  * A aplicação do cliente adquiriu o cadeado, guardou-o para uma loja persistente e recomeçou. Uma vez reiniciado, a aplicação do cliente olhou para as sessões de voo e tentou processar as mensagens nessas sessões.

### <a name="resolution"></a>Resolução

No caso de uma **SessionLockLostException,** a aplicação do cliente já não pode processar as mensagens na sessão. A aplicação do cliente pode considerar registar a exceção para análise, mas o cliente *deve* dispor da mensagem.

Uma vez que o bloqueio da sessão expirou, voltaria à Fila (ou Subscrição) e pode ser bloqueado pela próxima aplicação do cliente que aceitar a sessão. Uma vez que o bloqueio de sessão é realizado por um único pedido de cliente em qualquer momento, o processamento in-order é garantido.

## <a name="socketexception"></a>SocketException

### <a name="cause"></a>Causa

Uma **socketException** é lançada nos casos abaixo -
   * Quando uma tentativa de ligação falha porque o hospedeiro não respondeu corretamente após um determinado tempo (código de erro TCP 10060).
   * Uma ligação estabelecida falhou porque o hospedeiro ligado não respondeu.
   * Houve um erro no processamento da mensagem ou o tempo limite é ultrapassado pelo anfitrião remoto.
   * Problema de recursos de rede subjacente.

### <a name="resolution"></a>Resolução

Os erros **da SocketException** indicam que o VM que hospeda as aplicações não consegue converter o nome `<mynamespace>.servicebus.windows.net` no endereço IP correspondente. 

Verifique se o comando abaixo consegue mapear para um endereço IP.

```Powershell
PS C:\> nslookup <mynamespace>.servicebus.windows.net
```

que deve fornecer uma saída como abaixo

```bash
Name:    <cloudappinstance>.cloudapp.net
Address:  XX.XX.XXX.240
Aliases:  <mynamespace>.servicebus.windows.net
```

Se o nome acima **não se resolver** para um IP e o pseudónimo namespace, verifique qual o administrador da rede para investigar mais aprofundadamente. A resolução de nomes é feita através de um servidor DNS normalmente um recurso na rede de clientes. Se a resolução dns for feita pela Azure DNS contacte o suporte da Azure.

Se a resolução **de nomes funcionar como esperado,** verifique se as ligações com o Azure Service Bus são [permitidas aqui](service-bus-troubleshooting-guide.md#connectivity-certificate-or-timeout-issues)


## <a name="messagingexception"></a>MensagensExcepção

### <a name="cause"></a>Causa

**MensagensExcepção** é uma exceção genérica que pode ser lançada por várias razões. Algumas das razões estão listadas abaixo.

   * É feita uma tentativa de criar um **Modo de Fila** num **Tópico** ou numa **Subscrição**.
   * O tamanho da mensagem enviada é maior do que o limite para o nível dado. Leia mais sobre as [quotas e limites](service-bus-quotas.md)do Service Bus.
   * O pedido específico do avião de dados (enviar, receber, completar, abandonar) foi encerrado devido a estrangulamento.
   * Problemas transitórios causados devido a atualizações de serviço e reiniciam.

> [!NOTE]
> A lista de exceções acima não é exaustiva.

### <a name="resolution"></a>Resolução

Os passos de resolução dependem do que causou o lançamento **do MessagingException.**

   * Para **questões transitórias** (onde **_éTransiente_*_ está definido para _*_verdadeiro_*_) ou para questões de* estrangulamento ,** a repetição da operação pode resolvê-la. A política de reecuração por defeito no SDK pode ser alavancada para isso.
   * Para outras questões, os detalhes da exceção indicam que as etapas de emissão e resolução podem ser deduzidas do mesmo.

## <a name="next-steps"></a>Passos seguintes
Para obter a referência completa do Service Bus .NET API, consulte a [referência API Azure .NET](/dotnet/api/overview/azure/service-bus).
Para obter dicas de resolução de problemas, consulte o [guia de resolução de problemas](service-bus-troubleshooting-guide.md)
