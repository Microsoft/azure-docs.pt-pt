---
title: Guia de solução de problemas – hubs de eventos do Azure | Microsoft Docs
description: Este artigo fornece uma lista de exceções de mensagens de hubs de eventos do Azure e ações sugeridas.
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
ms.date: 12/03/2019
ms.author: shvija
ms.openlocfilehash: bea59ff29579c5d009a87c8d1564db4c0baf6e69
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793262"
---
# <a name="troubleshooting-guide-for-azure-event-hubs"></a>Guia de solução de problemas para hubs de eventos do Azure
Este artigo fornece algumas das exceções .NET geradas pelos hubs de eventos .NET Framework APIs e também outras dicas para solucionar problemas. 

## <a name="event-hubs-messaging-exceptions---net"></a>Exceções de mensagens dos hubs de eventos-.NET
Esta seção lista as exceções .NET geradas por .NET Framework APIs. 

### <a name="exception-categories"></a>Categorias de exceção

As APIs do .NET dos hubs de eventos geram exceções que podem se enquadrar nas categorias a seguir, juntamente com a ação associada que você pode executar para tentar corrigi-las.

1. Erro de codificação do usuário: [System. ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System. InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System. OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System. Runtime. Serialization. serializaexception](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Ação geral: tente corrigir o código antes de continuar.
2. Erro de instalação/configuração: [Microsoft. ServiceBus. Messaging. MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft. Azure. EventHubs. MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [System. UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Ação geral: revise sua configuração e altere se necessário.
3. Exceções transitórias: [Microsoft. ServiceBus. Messaging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft. ServiceBus. Messaging. ServerBusyException](#serverbusyexception), [Microsoft. Azure. EventHubs. ServerBusyException](#serverbusyexception), [Microsoft. ServiceBus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Ação geral: repita a operação ou notifique os usuários.
4. Outras exceções: [System. Reactions. TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System. tempo_limiteexception](#timeoutexception), [Microsoft. ServiceBus. Messaging. MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft. ServiceBus. Messaging. SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Ação geral: específica para o tipo de exceção; consulte a tabela na seção a seguir. 

### <a name="exception-types"></a>Tipos de exceção
A tabela a seguir lista os tipos de exceção de mensagens e suas causas e as observações sugeridas que você pode tomar.

| Tipo de exceção | Descrição/causa/exemplos | Ação sugerida | Observação sobre repetição automática/imediata |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |O servidor não respondeu à operação solicitada dentro do tempo especificado, que é controlado por [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). O servidor pode ter concluído a operação solicitada. Essa exceção pode ocorrer devido à rede ou a outros atrasos de infraestrutura. |Verifique o estado do sistema quanto à consistência e tente novamente, se necessário.<br /> Consulte [TimeoutException](#timeoutexception). | Tentar novamente pode ajudar em alguns casos; Adicione a lógica de repetição ao código. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A operação de usuário solicitada não é permitida no servidor ou serviço. Consulte a mensagem de exceção para obter detalhes. Por exemplo, [concluir](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) gera essa exceção se a mensagem foi recebida no modo [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) . | Verifique o código e a documentação. Verifique se a operação solicitada é válida. | A repetição não ajudará. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | É feita uma tentativa de invocar uma operação em um objeto que já foi fechado, anulado ou descartado. Em casos raros, a transação de ambiente já foi descartada. | Verifique o código e certifique-se de que ele não invoque operações em um objeto Descartado. | A repetição não ajudará. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | O objeto [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) não pôde adquirir um token, o token é inválido ou o token não contém as declarações necessárias para realizar a operação. | Verifique se o provedor de token foi criado com os valores corretos. Verifique a configuração do serviço de controle de acesso. | Tentar novamente pode ajudar em alguns casos; Adicione a lógica de repetição ao código. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Um ou mais argumentos fornecidos ao método são inválidos. O URI fornecido para [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) contém segmento (s) de caminho. O esquema de URI fornecido para [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) é inválido. O valor da propriedade é maior que 32 KB. | Verifique o código de chamada e certifique-se de que os argumentos estejam corretos. | A repetição não ajudará. |
| [Microsoft. ServiceBus. Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft. Azure. EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | A entidade associada à operação não existe ou foi excluída. | Verifique se a entidade existe. | A repetição não ajudará. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | O cliente não é capaz de estabelecer uma conexão com o Hub de eventos. |Verifique se o nome de host fornecido está correto e se o host está acessível. | Tentar novamente pode ajudar se houver problemas intermitentes de conectividade. |
| [Microsoft. ServiceBus. Messaging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft. Azure. EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | O serviço não pode processar a solicitação no momento. | O cliente pode aguardar um período de tempo e, em seguida, repetir a operação. <br /> Consulte [ServerBusyException](#serverbusyexception). | O cliente pode tentar novamente após determinado intervalo. Se uma nova tentativa resultar em uma exceção diferente, verifique o comportamento de repetição dessa exceção. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Exceção de mensagens genéricas que podem ser geradas nos seguintes casos: uma tentativa é feita para criar um [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) usando um nome ou caminho que pertença a um tipo de entidade diferente (por exemplo, um tópico). É feita uma tentativa de enviar uma mensagem maior que 1 MB. O servidor ou serviço encontrou um erro durante o processamento da solicitação. Consulte a mensagem de exceção para obter detalhes. Essa exceção é geralmente uma exceção transitória. | Verifique o código e certifique-se de que apenas objetos serializáveis sejam usados para o corpo da mensagem (ou use um serializador personalizado). Verifique a documentação para obter os tipos de valor com suporte das propriedades e use apenas os tipos com suporte. Verifique a propriedade [istransitória](/dotnet/api/microsoft.servicebus.messaging.messagingexception) . Se for **true**, você poderá repetir a operação. | O comportamento de repetição é indefinido e pode não ajudar. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Tentativa de criar uma entidade com um nome que já está sendo usado por outra entidade nesse namespace de serviço. | Exclua a entidade existente ou escolha um nome diferente para a entidade a ser criada. | A repetição não ajudará. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | A entidade de mensagens atingiu seu tamanho máximo permitido. Essa exceção pode ocorrer se o número máximo de receptores (que é 5) já tiver sido aberto em um nível de grupo por consumidor. | Crie espaço na entidade, recebendo mensagens da entidade ou de suas subfilas. <br /> Consulte [QuotaExceededException](#quotaexceededexception) | Tentar novamente pode ajudar se as mensagens foram removidas enquanto isso ocorre. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Solicitação de uma operação de tempo de execução em uma entidade desabilitada. |Ative a entidade. | Tentar novamente poderá ajudar se a entidade tiver sido ativada no interim. |
| [Microsoft. ServiceBus. Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft. Azure. EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Uma carga de mensagem excede o limite de 1 MB. Esse limite de 1 MB é para a mensagem total, que pode incluir propriedades do sistema e qualquer sobrecarga do .NET. | Reduza o tamanho da carga da mensagem e repita a operação. |A repetição não ajudará. |

### <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indica que foi excedida uma quota de uma entidade específica.

Essa exceção pode ocorrer se o número máximo de receptores (5) já tiver sido aberto em um nível de grupo por consumidor.

#### <a name="event-hubs"></a>Hubs de Eventos
Os hubs de eventos têm um limite de 20 grupos de consumidores por Hub de eventos. Ao tentar criar mais, você receberá um [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

### <a name="timeoutexception"></a>TimeoutException
Um [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica que uma operação iniciada pelo usuário está demorando mais do que o tempo limite da operação. 

Para os hubs de eventos, o tempo limite é especificado como parte da cadeia de conexão ou por meio de [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). A própria mensagem de erro pode variar, mas sempre contém o valor de tempo limite especificado para a operação atual. 

#### <a name="common-causes"></a>Causas comuns
Há duas causas comuns para esse erro: configuração incorreta ou um erro de serviço transitório.

1. **Configuração incorreta** O tempo limite da operação pode ser muito pequeno para a condição operacional. O valor padrão para o tempo limite da operação no SDK do cliente é de 60 segundos. Verifique se o seu código tem o valor definido como algo muito pequeno. A condição de uso de CPU e de rede pode afetar o tempo que leva para a conclusão de uma determinada operação, de modo que o tempo limite da operação não deve ser definido como um valor pequeno.
2. **Erro de serviço transitório** Às vezes, o serviço de hubs de eventos pode enfrentar atrasos no processamento de solicitações; por exemplo, durante períodos de tráfego alto. Nesses casos, você pode repetir a operação após um atraso, até que a operação seja bem-sucedida. Se a mesma operação ainda falhar após várias tentativas, visite o [site de status do serviço do Azure](https://azure.microsoft.com/status/) para ver se há interrupções de serviço conhecidas.

### <a name="serverbusyexception"></a>ServerBusyException

Um [Microsoft. ServiceBus. Messaging. ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) ou [Microsoft. Azure. EventHubs. ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) indica que um servidor está sobrecarregado. Há dois códigos de erro relevantes para essa exceção.

#### <a name="error-code-50002"></a>Código de erro 50002

Esse erro pode ocorrer por um destes dois motivos:

1. A carga não é distribuída uniformemente entre todas as partições no Hub de eventos e uma partição atinge a limitação da unidade de produtividade local.
    
    Resolução: a revisão da estratégia de distribuição de partição ou a tentativa de [EventHubClient. Send (eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) pode ajudar.

2. O namespace de hubs de eventos não tem unidades de produtividade suficientes (você pode verificar a tela de **métricas** na janela de namespace de hubs de eventos no [portal do Azure](https://portal.azure.com) para confirmar). O portal mostra informações agregadas (1 minuto), mas medimos a taxa de transferência em tempo real – portanto, é apenas uma estimativa.

    Resolução: aumentar as unidades de produtividade no namespace pode ajudar. Você pode executar essa operação no portal, na janela **escala** da tela namespace de hubs de eventos. Ou, você pode usar o [inflar automaticamente](event-hubs-auto-inflate.md).

#### <a name="error-code-50001"></a>Código de erro 50001

Esse erro raramente deve ocorrer. Acontece quando o contêiner que executa o código para seu namespace está com pouca CPU – não mais do que alguns segundos antes de o balanceador de carga dos hubs de eventos começar.

#### <a name="limit-on-calls-to-the-getruntimeinformation-method"></a>Limite em chamadas para o método GetRuntimeInformation
Os hubs de eventos do Azure dão suporte a até 50 chamadas por segundo para o GetRuntimeInfo por segundo. Você pode receber uma exceção semelhante à seguinte uma vez que o limite for atingido:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemas de conectividade, certificado ou tempo limite
As etapas a seguir podem ajudá-lo a solucionar problemas de conectividade/certificado/tempo limite para todos os serviços em *. servicebus.windows.net. 

- Navegue até ou [wget](https://www.gnu.org/software/wget/) `https://sbwagn2.servicebus.windows.net/`. Ele ajuda a verificar se você tem problemas de filtragem de IP ou de rede virtual ou de cadeia de certificados (mais comuns ao usar o SDK do Java).
- Execute o comando a seguir para verificar se alguma porta está bloqueada no firewall. Dependendo da biblioteca que você usa, outras portas também são usadas. Por exemplo: 443, 5672, 9354.

    ```powershell
    tnc sbwagn2.servicebus.windows.net -port 5671
    ```

    No Linux:

    ```shell
    telnet sbwagn2.servicebus.windows.net 5671
    ```
- Quando houver problemas intermitentes de conectividade, execute o comando a seguir para verificar se há algum pacote Descartado. Mantenha-o em execução por aproximadamente 1 minuto para saber se as conexões estão parcialmente bloqueadas. Você pode baixar a ferramenta de `psping` [aqui](/sysinternals/downloads/psping).

    ```shell
    psping.exe -t -q ehedhdev.servicebus.windows.net:9354 -nobanner     
    ```
    Você pode usar comandos equivalentes se estiver usando outras ferramentas, como `tnc`, `ping`e assim por diante. 
- Obtenha um rastreamento de rede se as etapas anteriores não ajudarem e as analisarem ou entrem em contato com a [suporte da Microsoft](https://support.microsoft.com/). 

## <a name="next-steps"></a>Passos seguintes

Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Event Hubs Overview (Descrição Geral dos Hubs de Eventos)](event-hubs-what-is-event-hubs.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
