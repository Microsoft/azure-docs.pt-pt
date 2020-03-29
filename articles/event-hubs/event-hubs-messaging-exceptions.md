---
title: Guia de resolução de problemas - Azure Event Hubs [ Azure Event Hubs ] Microsoft Docs
description: Este artigo fornece uma lista de exceções de mensagens azure Event Hubs e ações sugeridas.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: de5b95bd10bf72f60ba5d63c4b3a799556fcce33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76309783"
---
# <a name="troubleshooting-guide-for-azure-event-hubs"></a>Guia de resolução de problemas para hubs de eventos de Azure
Este artigo fornece algumas das exceções .NET geradas por Event Hubs .NET Framework APIs e também outras dicas para problemas de resolução de problemas. 

## <a name="event-hubs-messaging-exceptions---net"></a>Exceções de mensagens do Event Hubs - .NET
Esta secção enumera as exceções .NET geradas por .NET Framework APIs. 

### <a name="exception-categories"></a>Categorias de exceções

Os Hubs de Eventos .NET APIs geram exceções que podem cair nas seguintes categorias, juntamente com a ação associada que você pode tomar para tentar corrigi-los.

1. Erro de codificação do utilizador: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Ação geral: tente fixar o código antes de prosseguir.
2. Erro de configuração/configuração: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Ação geral: reveja a sua configuração e altere se necessário.
3. Exceções transitórias: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Ação geral: voltar a tentar a operação ou notificar os utilizadores.
4. Outras exceções: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Ação geral: específica do tipo de exceção; consulte a tabela na secção seguinte. 

### <a name="exception-types"></a>Tipos de exceção
A tabela seguinte lista tipos de exceções de mensagens e suas causas, e notas sugeridas ação que você pode tomar.

| Tipo de exceção | Descrição/Causa/Exemplos | Ação Sugerida | Nota sobre retry automático/imediato |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |O servidor não respondeu à operação solicitada dentro do tempo especificado, que é controlado pela [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). O servidor pode ter concluído a operação solicitada. Esta exceção pode acontecer devido a atrasos na rede ou outros atrasos nas infraestruturas. |Verifique se o estado do sistema tem consistência e retenta, se necessário.<br /> Ver [TimeoutException](#timeoutexception). | A retentativa pode ajudar em alguns casos; adicionar lógica de retry ao código. |
| [Invalidação de Operação](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A operação de utilizador solicitada não é permitida dentro do servidor ou serviço. Consulte a mensagem de exceção para mais detalhes. Por exemplo, [complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) gera esta exceção se a mensagem foi recebida no modo [ReceiveAndDelete.](/dotnet/api/microsoft.servicebus.messaging.receivemode) | Verifique o código e a documentação. Certifique-se de que a operação solicitada é válida. | Retry não vai ajudar. |
| [Operação Cancelamentode Exceção](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | É feita uma tentativa de invocar uma operação a um objeto que já foi fechado, abortado ou eliminado. Em casos raros, a transação ambiente já está alienada. | Verifique o código e certifique-se de que não invoca operações num objeto eliminado. | Retry não vai ajudar. |
| [Exceção não autorizada de Acesso](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | O objeto [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) não conseguiu adquirir um símbolo, o símbolo é inválido, ou o símbolo não contém as alegações necessárias para fazer a operação. | Certifique-se de que o fornecedor simbólico é criado com os valores corretos. Verifique a configuração do Serviço de Controlo de Acesso. | A retentativa pode ajudar em alguns casos; adicionar lógica de retry ao código. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutoutRangeexception](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Um ou mais argumentos fornecidos ao método são inválidos. O URI fornecido ao [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) contém segmentos de caminhos. O regime URI fornecido ao [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) é inválido. O valor da propriedade é superior a 32 KB. | Verifique o código de chamada e certifique-se de que os argumentos estão corretos. | Retry não vai ajudar. |
| [Microsoft.ServiceBus.Messaging Messaging EntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | A entidade associada à operação não existe ou foi eliminada. | Certifique-se de que a entidade existe. | Retry não vai ajudar. |
| [Falta de comunicação de mensagens](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | O cliente não é capaz de estabelecer uma ligação ao Event Hub. |Certifique-se de que o nome do anfitrião fornecido está correto e que o hospedeiro está acessível. | A retry pode ajudar se houver problemas de conectividade intermitentes. |
| [Microsoft.ServiceBus.Messaging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | O serviço não pode processar o pedido neste momento. | O cliente pode esperar por um período de tempo, e depois voltar a tentar a operação. <br /> Ver [ServerBusyException](#serverbusyexception). | O cliente pode voltar a tentar após determinado intervalo. Se uma nova tentativa resultar numa exceção diferente, verifique o comportamento de novo de tentativa dessa exceção. |
| [Reexceção de mensagens](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Exceção genérica de mensagens que podem ser lançadas nos seguintes casos: É feita uma tentativa de criar um [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) usando um nome ou caminho que pertença a um tipo de entidade diferente (por exemplo, um tópico). É feita uma tentativa de enviar uma mensagem maior que 1 MB. O servidor ou serviço encontrou um erro durante o processamento do pedido. Consulte a mensagem de exceção para mais detalhes. Esta exceção é geralmente uma exceção transitória. | Verifique o código e certifique-se de que apenas são utilizados objetos serlizáveis para o corpo da mensagem (ou utilize um serializador personalizado). Verifique a documentação dos tipos de valor suportados das propriedades e utilize apenas tipos suportados. Verifique a propriedade [IsTransient.](/dotnet/api/microsoft.servicebus.messaging.messagingexception) Se for **verdade,** pode voltar a tentar a operação. | O comportamento de retry é indefinido e pode não ajudar. |
| [Entidade de Mensagens Já Existe Exceção](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Tente criar uma entidade com um nome que já é usado por outra entidade nesse espaço de nome de serviço. | Eliminar a entidade existente ou escolher um nome diferente para a entidade a criar. | Retry não vai ajudar. |
| [QuotaExceedException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | A entidade de mensagens atingiu o seu tamanho máximo permitido. Esta exceção pode acontecer se o número máximo de recetores (que é 5) já tiver sido aberto a nível de grupo por consumidor. | Criar espaço na entidade recebendo mensagens da entidade ou das suas subfilas. <br /> Ver [QuotaExceededException](#quotaexceededexception) | A retry pode ajudar se as mensagens tiverem sido removidas entretanto. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Pedido de operação em tempo de funcionamento numa entidade com deficiência. |Ativar a entidade. | A retentativa pode ajudar se a entidade tiver sido ativada interinamente. |
| [Microsoft.ServiceBus.MessagesizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Uma carga útil de mensagem excede o limite de 1-MB. Este limite de 1-MB destina-se à mensagem total, que pode incluir propriedades do sistema e qualquer sobrecarga .NET. | Reduza o tamanho da carga útil da mensagem e, em seguida, tente novamente a operação. |Retry não vai ajudar. |

### <a name="quotaexceededexception"></a>QuotaExceedException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indica que foi excedida uma quota de uma entidade específica.

Esta exceção pode acontecer se o número máximo de recetores (5) já tiver sido aberto a nível de grupo por consumidor.

#### <a name="event-hubs"></a>Event Hubs
O Event Hubs tem um limite de 20 grupos de consumidores por Event Hub. Quando tenta criar mais, recebe uma [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

### <a name="timeoutexception"></a>TimeoutException
Um [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica que uma operação iniciada pelo utilizador está a demorar mais tempo do que o tempo de funcionamento. 

Para os Hubs de Eventos, o tempo limite é especificado quer como parte da cadeia de ligação, quer através do [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). A mensagem de erro em si pode variar, mas contém sempre o valor limite especificado para o funcionamento atual. 

#### <a name="common-causes"></a>Causas comuns
Existem duas causas comuns para este erro: configuração incorreta ou erro de serviço transitório.

1. **Configuração incorreta** O tempo de funcionamento pode ser muito pequeno para a condição operacional. O valor padrão para o tempo de funcionamento no SDK cliente é de 60 segundos. Verifique se o seu código tem o valor definido para algo demasiado pequeno. O estado da rede e da utilização do CPU pode afetar o tempo necessário para que uma determinada operação seja concluída, pelo que o tempo limite de funcionamento não deve ser fixado num pequeno valor.
2. **Erro de serviço transitório** Por vezes, o serviço Event Hubs pode experimentar atrasos no processamento de pedidos; por exemplo, durante períodos de tráfego elevado. Nesses casos, pode voltar a tentar a sua operação após um atraso, até que a operação seja bem sucedida. Se a mesma operação continuar a falhar após várias tentativas, visite o site de estado do [serviço Azure](https://azure.microsoft.com/status/) para ver se existem alguma paragem de serviço conhecida.

### <a name="serverbusyexception"></a>ServerBusyException

A [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) ou [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) indica que um servidor está sobrecarregado. Há dois códigos de erro relevantes para esta exceção.

#### <a name="error-code-50002"></a>Código de erro 50002

Este erro pode ocorrer por uma de duas razões:

1. A carga não é distribuída uniformemente por todas as divisórias no centro do evento, e uma divisória atinge a limitação da unidade de produção local.
    
    Resolução: Rever a estratégia de distribuição de divisórias ou tentar [EventHubClient.Enviar (eventoDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) pode ajudar.

2. O espaço de nome sinuoso do Event Hubs não tem unidades de entrada suficientes (pode verificar o ecrã **Métricas** na janela espaço de nome do Event Hubs no [portal Azure](https://portal.azure.com) para confirmar). O portal mostra informação agregada (1 minuto), mas medimos a produção em tempo real – por isso é apenas uma estimativa.

    Resolução: Aumentar as unidades de entrada no espaço de nome pode ajudar. Pode fazer esta operação no portal, na janela **Escala** do ecrã de espaço de nome Do Event Hubs. Ou pode usar [auto-insuflado.](event-hubs-auto-inflate.md)

#### <a name="error-code-50001"></a>Código de erro 50001

Este erro raramente deve ocorrer. Acontece quando o código de funcionamento do recipiente para o seu espaço de nome é baixo em CPU – não mais do que alguns segundos antes do início do equilíbriode carga dos Centros de Eventos.

#### <a name="limit-on-calls-to-the-getruntimeinformation-method"></a>Limite às chamadas para o método GetRuntimeInformation
O Azure Event Hubs suporta até 50 chamadas por segundo para o GetRuntimeInfo por segundo. Pode receber uma exceção semelhante à seguinte, uma vez atingido o limite:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemas de conectividade, certificado ou timeout
Os seguintes passos podem ajudá-lo com problemas de conectividade/certificado/timeout para todos os serviços em *.servicebus.windows.net. 

- Navegue para ou [paraque.](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/` Ajuda a verificar se tem problemas de filtragem IP ou de rede virtual ou cadeia de certificados (mais comuns quando se utiliza java SDK).

    Um exemplo de mensagem bem sucedida:
    
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
- Execute o seguinte comando para verificar se alguma porta está bloqueada na firewall. As portas utilizadas são 443 (HTTPS), 5671 (AMQP) e 9093 (Kafka). Dependendo da biblioteca que utiliza, outras portas também são utilizadas. Aqui está o comando da amostra que verifica se a porta 5671 está bloqueada.

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Em Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Quando houver problemas de conectividade intermitentes, faça o seguinte comando para verificar se existem pacotes caídos. Este comando tentará estabelecer 25 ligações TCP diferentes a cada 1 segundo com o serviço. Em seguida, pode verificar quantos deles conseguiram/falharam e também ver a latência da ligação tCP. Pode descarregar `psping` a ferramenta a partir [daqui](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
    ```
    Pode utilizar comandos equivalentes se estiver a `tnc` `ping`utilizar outras ferramentas, tais como , e assim por diante. 
- Obtenha um rastreio de rede se os passos anteriores não ajudarem e analisá-lo usando ferramentas como [wireshark](https://www.wireshark.org/). Contacte o Suporte da [Microsoft](https://support.microsoft.com/) se necessário. 

## <a name="next-steps"></a>Passos seguintes

Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Event Hubs](event-hubs-what-is-event-hubs.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
