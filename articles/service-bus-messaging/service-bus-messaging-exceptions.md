---
title: Guia de solução de problemas do barramento de serviço do Azure | Microsoft Docs
description: Este artigo fornece uma lista de exceções de mensagens Azure Service Bus e sugeriu ações a tomar quando a exceção ocorre.
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 37f316af68bc0b20f21eb606e2abc8232f29ce32
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759368"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Guia de solução de problemas do barramento de serviço do Azure
Este artigo fornece algumas das exceções .NET geradas pelo barramento de serviço .NET Framework APIs e também outras dicas para solucionar problemas. 

## <a name="service-bus-messaging-exceptions"></a>Exceções de mensagens do Service Bus
Esta seção lista as exceções .NET geradas por .NET Framework APIs. 

### <a name="exception-categories"></a>Categorias de exceção
As APIs de mensagens geram exceções que podem se enquadrar nas categorias a seguir, juntamente com a ação associada que você pode executar para tentar corrigi-las. O significado e as causas de uma exceção podem variar dependendo do tipo de entidade de mensagens:

1. Erro de codificação do usuário ([System. ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System. InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System. OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System. Runtime. Serialization. serializaexception](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Ação geral: tentar corrigir o código antes de continuar.
2. Erro de instalação/configuração ([Microsoft. ServiceBus. Messaging. MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System. UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Ação geral: rever a configuração e altere se necessário.
3. Exceções transitórias ([Microsoft. ServiceBus. Messaging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft. ServiceBus. Messaging. ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft. ServiceBus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Ação geral: Repita a operação ou notificar os utilizadores. A classe `RetryPolicy` no SDK do cliente pode ser configurada para tratar repetições automaticamente. Para obter mais informações, consulte [diretrizes de repetição](/azure/architecture/best-practices/retry-service-specific#service-bus).
4. Outras exceções ([System. Transactions. TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System. tempo_limiteexception](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft. ServiceBus. Messaging. MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft. ServiceBus. Messaging. SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Ação geral: específica para o tipo de exceção; consulte a tabela na seção a seguir: 

### <a name="exception-types"></a>Tipos de exceção
A tabela seguinte lista os tipos de exceções de mensagens e suas causas e ação sugerida notas que possa realizar.

| **Tipo de exceção** | **Descrição/causa/exemplos** | **Ação sugerida** | **Observação sobre repetição automática/imediata** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |O servidor não respondeu à operação solicitada dentro do tempo especificado, que é controlado por [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). O servidor de pode concluir a operação pedida. Isso pode acontecer devido à rede ou a outros atrasos de infraestrutura. |Verifique o estado do sistema para manter a consistência e tente novamente se necessário. Consulte [exceções de tempo limite](#timeoutexception). |Repetição poderá ajudar em alguns casos; Adicione lógica de repetição ao código. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A operação de usuário solicitada não é permitida no servidor ou serviço. Ver a mensagem de exceção para obter detalhes. Por exemplo, [Complete ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) gera essa exceção se a mensagem foi recebida no modo [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) . |Verifique o código e a documentação. Certifique-se de que a operação pedida é válida. |A repetição não ajuda. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |É efetuada uma tentativa de invocar uma operação num objeto que já foi fechado, abortada ou eliminado. Em casos raros, a transação de ambiente já foi eliminada. |Verifique o código e certifique-se de que ele não invoque operações em um objeto Descartado. |A repetição não ajuda. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |O objeto [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) não pôde adquirir um token, o token é inválido ou o token não contém as declarações necessárias para realizar a operação. |Certifique-se de que o fornecedor do token é criado com os valores corretos. Verifique a configuração do serviço de controle de acesso. |Repetição poderá ajudar em alguns casos; Adicione lógica de repetição ao código. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Um ou mais argumentos fornecidos para o método são inválidos.<br /> O URI fornecido a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [criar](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) contém o caminho de modo.<br /> O esquema URI fornecido a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [criar](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) é inválido. <br />O valor da propriedade é maior do que 32 KB. |Verifique o código de chamada e certifique-se de que os argumentos estão corretos. |A repetição não ajuda. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |A entidade associada à operação não existe ou foi excluída. |Certifique-se de que a entidade existe. |A repetição não ajuda. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Tentativa de receber uma mensagem com um número de sequência específico. Esta mensagem não foi encontrada. |Certifique-se de que a mensagem ainda não tenha sido recebida. Verifique a fila de mensagens mortas para ver se a mensagem foi morto. |A repetição não ajuda. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |O cliente não é capaz de estabelecer uma conexão com o barramento de serviço. |Certificar-se de que o nome de anfitrião fornecido está correto e o anfitrião está contactável. |Repetição pode ajudar se existem problemas de conectividade intermitente. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |O serviço não é capaz de processar a solicitação no momento. |Cliente pode aguardar um período de tempo, em seguida, repita a operação. |Cliente pode voltar a tentar após determinado intervalo. Se uma repetição resulta numa exceção de diferente, verifique o comportamento de repetição dessa exceção. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |O token de bloqueio associado à mensagem expirou ou o token de bloqueio não foi encontrado. |Descartar a mensagem. |A repetição não ajuda. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |O bloqueio associado a esta sessão foi perdido. |Anule o objeto [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) . |A repetição não ajuda. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Exceção de mensagens genéricas que pode ser lançada nos seguintes casos:<br /> É feita uma tentativa de criar um [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) usando um nome ou caminho que pertence a um tipo de entidade diferente (por exemplo, um tópico).<br />  É efetuada uma tentativa para enviar uma mensagem maior do que 256 KB. O servidor ou serviço encontrou um erro durante o processamento do pedido. Ver a mensagem de exceção para obter detalhes. Geralmente é uma exceção transitória. |Verifique o código e certifique-se de que apenas os objetos serializáveis são utilizados para o corpo da mensagem (ou usar um serializador personalizado). Verifique a documentação para os tipos de valor suportado das propriedades e apenas os tipos de utilização suportado. Verifique os [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) propriedade. Se for **true**, você poderá repetir a operação. |Comportamento de repetição é indefinido e não poderá ajudar. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Tentativa de criar uma entidade com um nome que já está a ser utilizado por outra entidade nesse espaço de nomes de serviço. |Eliminar a entidade existente ou escolha um nome diferente para a entidade a ser criada. |A repetição não ajuda. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |A entidade de mensagens atingiu seu tamanho máximo permitido ou o número máximo de conexões com um namespace foi excedido. |Crie espaço na entidade por receber mensagens a partir da entidade ou seu subfilas. Consulte [QuotaExceededException](#quotaexceededexception). |Repetição pode ajudar se as mensagens foram removidas enquanto isso. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |O barramento de serviço retornará essa exceção se você tentar criar uma ação de regra inválida. O barramento de serviço anexa essa exceção a uma mensagem morto se ocorrer um erro durante o processamento da ação de regra para essa mensagem. |Verifique se a ação da regra está correta. |A repetição não ajuda. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |O barramento de serviço retornará essa exceção se você tentar criar um filtro inválido. O barramento de serviço anexa essa exceção a uma mensagem morto se ocorreu um erro ao processar o filtro para essa mensagem. |Verifique se o filtro está correto. |A repetição não ajuda. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Tentativa de aceitar uma sessão com uma ID de sessão específica, mas a sessão está atualmente bloqueada por outro cliente. |Verifique se a sessão está desbloqueada por outros clientes. |Tentar novamente pode ajudar se a sessão foi liberada no interim. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Muitas operações fazem parte da transação. |Reduza o número de operações que fazem parte desta transação. |A repetição não ajuda. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Pedido para uma operação de tempo de execução numa entidade desativada. |Ative a entidade. |Repetição pode ajudar se a entidade tiver sido ativada até lá. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |O barramento de serviço retornará essa exceção se você enviar uma mensagem para um tópico com filtragem prévia habilitada e nenhum dos filtros corresponder. |Verifique se pelo menos um filtro corresponde. |A repetição não ajuda. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Uma carga de mensagem excede o limite de 256 KB. O limite de 256-KB é o tamanho total da mensagem, que pode incluir propriedades do sistema e qualquer sobrecarga do .NET. |Reduzir o tamanho da carga de mensagem, em seguida, repita a operação. |A repetição não ajuda. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |A transação de ambiente (*Transaction. Current*) é inválida. Ele pode ter sido concluído ou anulado. A exceção interna pode fornecer informações adicionais. | |A repetição não ajuda. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Uma operação é tentada em uma transação que está em dúvida, ou é feita uma tentativa de confirmar a transação e a transação se torna em dúvida. |Seu aplicativo deve tratar essa exceção (como um caso especial), pois a transação pode já ter sido confirmada. |- |

### <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) indica que foi excedida uma quota de uma entidade específica.

#### <a name="queues-and-topics"></a>Filas e tópicos
Para filas e tópicos, geralmente é o tamanho da fila. A propriedade mensagem de erro contém mais detalhes, como no exemplo a seguir:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: ‘xxx-xxx-xxx’. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

A mensagem informa que o tópico excedeu seu limite de tamanho, neste caso, 1 GB (o limite de tamanho padrão). 

#### <a name="namespaces"></a>Espaços de nomes

Para namespaces, [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) pode indicar que um aplicativo excedeu o número máximo de conexões com um namespace. Por exemplo:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>Causas comuns
Há duas causas comuns para esse erro: a fila de mensagens mortas e os receptores de mensagens não funcionais.

1. **[Fila de mensagens mortas](service-bus-dead-letter-queues.md)** Um leitor está falhando em concluir mensagens e as mensagens são retornadas para a fila/tópico quando o bloqueio expira. Isso pode acontecer se o leitor encontrar uma exceção que impeça a chamada de [BrokeredMessage. Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). Depois que uma mensagem for lida 10 vezes, ela passará para a fila de mensagens mortas por padrão. Esse comportamento é controlado pela propriedade [QueueDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) e tem um valor padrão de 10. À medida que as mensagens são acumuladas na fila, elas ocupam espaço.
   
    Para resolver o problema, leia e conclua as mensagens da fila de mensagens mortas, como faria em qualquer outra fila. Você pode usar o método [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) para ajudar a formatar o caminho da fila de mensagens mortas.
2. **Receptor parado**. Um receptor parou de receber mensagens de uma fila ou assinatura. A maneira de identificar isso é examinar a propriedade [QueueDescription. MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) , que mostra a divisão completa das mensagens. Se a propriedade [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) for alta ou em crescimento, as mensagens não serão lidas tão rapidamente quanto estão sendo gravadas.

### <a name="timeoutexception"></a>TimeoutException
R [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica que uma operação iniciada pelo utilizador está a demorar mais tempo do que o tempo limite da operação. 

Você deve verificar o valor da propriedade [ServicePointManager. DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) , uma vez que atingir esse limite também pode causar um [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

#### <a name="queues-and-topics"></a>Filas e tópicos
Para filas e tópicos, o tempo limite é especificado na propriedade [MessagingFactorySettings. OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) , como parte da cadeia de conexão, ou por meio de [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). A própria mensagem de erro pode variar, mas sempre contém o valor de tempo limite especificado para a operação atual. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemas de conectividade, certificado ou tempo limite
As etapas a seguir podem ajudá-lo a solucionar problemas de conectividade/certificado/tempo limite para todos os serviços em *. servicebus.windows.net. 

- Navegue até ou [wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/`. Ele ajuda a verificar se você tem problemas de filtragem de IP ou de rede virtual ou de cadeia de certificados (mais comuns ao usar o SDK do Java).

    Um exemplo de mensagem bem-sucedida:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Um exemplo de mensagem de erro de falha:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Execute o comando a seguir para verificar se alguma porta está bloqueada no firewall. As portas usadas são 443 (HTTPS), 5671 (AMQP) e 9354 (net Messaging/SBMP). Dependendo da biblioteca que você usa, outras portas também são usadas. Aqui está o comando de exemplo que verifica se a porta 5671 está bloqueada. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    No Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Quando houver problemas intermitentes de conectividade, execute o comando a seguir para verificar se há algum pacote Descartado. Esse comando tentará estabelecer 25 conexões TCP diferentes a cada 1 segundo com o serviço. Em seguida, você pode verificar quantos deles foram bem-sucedidos/com falha e também ver a latência de conexão TCP. Você pode baixar a ferramenta de `psping` [aqui](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    Você pode usar comandos equivalentes se estiver usando outras ferramentas, como `tnc`, `ping`e assim por diante. 
- Obtenha um rastreamento de rede se as etapas anteriores não ajudarem e a analisarem usando ferramentas como o [Wireshark](https://www.wireshark.org/). Entre em contato com [suporte da Microsoft](https://support.microsoft.com/) se necessário. 


## <a name="next-steps"></a>Passos seguintes

Para obter a referência completa da API .NET do barramento de serviço, consulte a [referência da API .net do Azure](/dotnet/api/overview/azure/service-bus).

Para saber mais sobre o [barramento de serviço](https://azure.microsoft.com/services/service-bus/), confira os seguintes artigos:

* [Descrição geral das mensagens do Service Bus](service-bus-messaging-overview.md)
* [Arquitetura do Service Bus](service-bus-architecture.md)

