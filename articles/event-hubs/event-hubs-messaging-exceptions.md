---
title: Guia de solução de problemas – hubs de eventos do Azure | Microsoft Docs
description: Este artigo fornece uma lista de exceções de mensagens de Hubs de eventos e ações sugeridas.
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
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76309783"
---
# <a name="troubleshooting-guide-for-azure-event-hubs"></a>Guia de solução de problemas para hubs de eventos do Azure
Este artigo fornece algumas das exceções .NET geradas pelos hubs de eventos .NET Framework APIs e também outras dicas para solucionar problemas. 

## <a name="event-hubs-messaging-exceptions---net"></a>Exceções de mensagens dos hubs de eventos-.NET
Esta seção lista as exceções .NET geradas por .NET Framework APIs. 

### <a name="exception-categories"></a>Categorias de exceção

As APIs do .NET dos hubs de eventos geram exceções que podem se enquadrar nas categorias a seguir, juntamente com a ação associada que você pode executar para tentar corrigi-las.

1. Erro de código de utilizador: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System. InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [ System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Ação geral: tentar corrigir o código antes de continuar.
2. Erro de configuração: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [ System. unauthorizedaccessexception](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Ação geral: rever a configuração e altere se necessário.
3. Exceções transitórias: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [ Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Ação geral: Repita a operação ou notificar os utilizadores.
4. Outras exceções: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Ação geral: específico para o tipo de exceção; consulte a tabela na secção seguinte. 

### <a name="exception-types"></a>Tipos de exceção
A tabela seguinte lista os tipos de exceções de mensagens e suas causas e ação sugerida notas que possa realizar.

| Tipo de Excepção | Descrição/causa/exemplos | Ação sugerida | Tenha em atenção aquando da repetição imediata/automático |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |O servidor não respondeu à operação solicitada dentro do tempo especificado, que é controlado por [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). O servidor de pode concluir a operação pedida. Essa exceção pode ocorrer devido à rede ou a outros atrasos de infraestrutura. |Verifique o estado do sistema para manter a consistência e tente novamente se necessário.<br /> Ver [TimeoutException](#timeoutexception). | Repetição poderá ajudar em alguns casos; Adicione lógica de repetição ao código. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A operação de usuário solicitada não é permitida no servidor ou serviço. Ver a mensagem de exceção para obter detalhes. Por exemplo, [concluída](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) gera esta exceção se a mensagem foi recebida na [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) modo. | Verifique o código e a documentação. Certifique-se de que a operação pedida é válida. | A repetição não ajudará. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | É efetuada uma tentativa de invocar uma operação num objeto que já foi fechado, abortada ou eliminado. Em casos raros, a transação de ambiente já foi eliminada. | Verifique o código e certifique-se de que ele não invoque operações em um objeto Descartado. | A repetição não ajudará. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | O objeto [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) não pôde adquirir um token, o token é inválido ou o token não contém as declarações necessárias para realizar a operação. | Certifique-se de que o fornecedor do token é criado com os valores corretos. Verifique a configuração do serviço de controle de acesso. | Repetição poderá ajudar em alguns casos; Adicione lógica de repetição ao código. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Um ou mais argumentos fornecidos para o método são inválidos. O URI fornecido a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [criar](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) contém o caminho de modo. O esquema URI fornecido a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [criar](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) é inválido. O valor da propriedade é maior do que 32 KB. | Verifique o código de chamada e certifique-se de que os argumentos estão corretos. | Repetição não ajudará. |
| [Microsoft.ServiceBus.Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | Entidade associada com a operação não existe ou foi eliminado. | Certifique-se de que a entidade existe. | Repetição não ajudará. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | Cliente não é capaz de estabelecer uma ligação ao Hub de eventos. |Certificar-se de que o nome de anfitrião fornecido está correto e o anfitrião está contactável. | Repetição pode ajudar se existem problemas de conectividade intermitente. |
| [Microsoft. ServiceBus. Messaging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | Serviço não é capaz de processar o pedido neste momento. | Cliente pode aguardar um período de tempo, em seguida, repita a operação. <br /> Ver [ServerBusyException](#serverbusyexception). | Cliente pode voltar a tentar após determinado intervalo. Se uma repetição resulta numa exceção de diferente, verifique o comportamento de repetição dessa exceção. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Genérico mensagens de exceção que pode ser gerada nos seguintes casos: é efetuada uma tentativa de criar uma [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) usando um nome ou o caminho que pertence a um tipo de entidade diferentes (por exemplo, um tópico). É feita uma tentativa de enviar uma mensagem maior que 1 MB. O servidor ou serviço encontrou um erro durante o processamento do pedido. Ver a mensagem de exceção para obter detalhes. Essa exceção é, normalmente, uma exceção transitória. | Verifique o código e certifique-se de que apenas os objetos serializáveis são utilizados para o corpo da mensagem (ou usar um serializador personalizado). Verifique a documentação para os tipos de valor suportado das propriedades e apenas os tipos de utilização suportado. Verifique os [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) propriedade. Se for **true**, pode repetir a operação. | Comportamento de repetição é indefinido e não poderá ajudar. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Tentativa de criar uma entidade com um nome que já está a ser utilizado por outra entidade nesse espaço de nomes de serviço. | Eliminar a entidade existente ou escolha um nome diferente para a entidade a ser criada. | Repetição não ajudará. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | A entidade de mensagens foi atingido o tamanho máximo admissível. Essa exceção pode acontecer se o número máximo de recetores (que é 5) já foi aberto num nível de grupo por consumidor. | Crie espaço na entidade por receber mensagens a partir da entidade ou seu subfilas. <br /> Consulte [QuotaExceededException](#quotaexceededexception) | Repetição pode ajudar se as mensagens foram removidas enquanto isso. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Pedido para uma operação de tempo de execução numa entidade desativada. |Ative a entidade. | Repetição pode ajudar se a entidade tiver sido ativada até lá. |
| [Microsoft.ServiceBus.Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Uma carga de mensagem excede o limite de 1 MB. Esse limite de 1 MB é para a mensagem total, que pode incluir propriedades do sistema e qualquer sobrecarga do .NET. | Reduzir o tamanho da carga de mensagem, em seguida, repita a operação. |Repetição não ajudará. |

### <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indica que foi excedida uma quota de uma entidade específica.

Essa exceção pode acontecer se o número máximo de recetores (5) já foi aberto num nível de grupo por consumidor.

#### <a name="event-hubs"></a>Event Hubs
Os Hubs de eventos tem um limite de 20 grupos de consumidores do Hub de eventos. Quando está tentando criar mais, receberá um [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

### <a name="timeoutexception"></a>TimeoutException
R [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica que uma operação iniciada pelo utilizador está a demorar mais tempo do que o tempo limite da operação. 

Hubs de eventos, o tempo limite é especificado como parte da cadeia de ligação, ou por meio [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). A própria mensagem de erro pode variar, mas sempre contém o valor de tempo limite especificado para a operação atual. 

#### <a name="common-causes"></a>Causas comuns
Existem duas causas comuns deste erro: configuração incorreta ou a um erro de serviço transitório.

1. **Configuração incorreta** o tempo limite da operação poderá ser demasiado pequeno para a condição operacional. O valor predefinido para o tempo limite da operação no SDK de cliente é 60 segundos. Verifique se o seu código tem o valor definido para algo demasiado pequena. A condição de uso de CPU e de rede pode afetar o tempo que leva para a conclusão de uma determinada operação, de modo que o tempo limite da operação não deve ser definido como um valor pequeno.
2. **Erro de serviço transitório** , às vezes, o serviço de Hubs de eventos pode passar por atrasos no processamento de pedidos; por exemplo, durante os períodos de tráfego elevado. Nesses casos, pode tentar novamente a operação após um atraso, até que a operação é efetuada com êxito. Se a mesma operação continuar a falhar após várias tentativas, visite o [site do Estado do serviço do Azure](https://azure.microsoft.com/status/) para ver se existem quaisquer falhas conhecidas do serviço.

### <a name="serverbusyexception"></a>ServerBusyException

R [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) ou [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) indica que um servidor está sobrecarregado. Existem dois códigos de erro relevantes para esta exceção.

#### <a name="error-code-50002"></a>Código de erro 50002

Este erro pode ocorrer para um dos dois motivos:

1. A carga não é distribuída uniformemente entre todas as partições no Hub de eventos e uma partição atinge a limitação da unidade de produtividade local.
    
    Resolução: Rever a estratégia de distribuição de partição ou tentar [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) poderá ajudar.

2. O namespace de hubs de eventos não tem unidades de produtividade suficientes (você pode verificar a tela de **métricas** na janela de namespace de hubs de eventos no [portal do Azure](https://portal.azure.com) para confirmar). O portal mostra informações agregadas (1 minuto), mas medimos a taxa de transferência em tempo real – portanto, é apenas uma estimativa.

    Resolução: Aumentar as unidades de débito no espaço de nomes pode ajudar. Pode fazer esta operação no portal, além do **dimensionamento** janela do ecrã de espaço de nomes dos Hubs de eventos. Em alternativa, pode utilizar [ampliação automática](event-hubs-auto-inflate.md).

#### <a name="error-code-50001"></a>Código de erro 50001

Este erro raramente deve ocorrer. Isso acontece quando o contentor em execução de código para o espaço de nomes tem pouco CPU – não mais do que alguns segundos antes do Balanceador de carga de Hubs de eventos começa.

#### <a name="limit-on-calls-to-the-getruntimeinformation-method"></a>Limite em chamadas para o método GetRuntimeInformation
Os hubs de eventos do Azure dão suporte a até 50 chamadas por segundo para o GetRuntimeInfo por segundo. Você pode receber uma exceção semelhante à seguinte uma vez que o limite for atingido:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemas de conectividade, certificado ou tempo limite
As etapas a seguir podem ajudá-lo a solucionar problemas de conectividade/certificado/tempo limite para todos os serviços em *. servicebus.windows.net. 

- Navegue até ou [wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/`. Ele ajuda a verificar se você tem problemas de filtragem de IP ou de rede virtual ou de cadeia de certificados (mais comuns ao usar o SDK do Java).

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
- Execute o comando a seguir para verificar se alguma porta está bloqueada no firewall. As portas usadas são 443 (HTTPS), 5671 (AMQP) e 9093 (Kafka). Dependendo da biblioteca que você usa, outras portas também são usadas. Aqui está o comando de exemplo que verifica se a porta 5671 está bloqueada.

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    No Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Quando houver problemas intermitentes de conectividade, execute o comando a seguir para verificar se há algum pacote Descartado. Esse comando tentará estabelecer 25 conexões TCP diferentes a cada 1 segundo com o serviço. Em seguida, você pode verificar quantos deles foram bem-sucedidos/com falha e também ver a latência de conexão TCP. Você pode baixar a ferramenta de `psping` [aqui](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
    ```
    Você pode usar comandos equivalentes se estiver usando outras ferramentas, como `tnc`, `ping`e assim por diante. 
- Obtenha um rastreamento de rede se as etapas anteriores não ajudarem e a analisarem usando ferramentas como o [Wireshark](https://www.wireshark.org/). Entre em contato com [suporte da Microsoft](https://support.microsoft.com/) se necessário. 

## <a name="next-steps"></a>Passos seguintes

Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Event Hubs Overview (Descrição Geral dos Hubs de Eventos)](event-hubs-what-is-event-hubs.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
