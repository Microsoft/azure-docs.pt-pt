---
title: Azure Event Hubs - exceções
description: Este artigo fornece uma lista de exceções de mensagens Azure Event Hubs e ações sugeridas.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a93daa88c468a22838a6f9012f0c4622447f5555
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512372"
---
# <a name="event-hubs-messaging-exceptions---net"></a>Event Hubs exceções de mensagens - .NET
Esta secção enumera as exceções .NET geradas por .NET Framework APIs. 

## <a name="exception-categories"></a>Categorias de exceções

As APIs de Eventos .NET geram exceções que podem enquadrar-se nas seguintes categorias, juntamente com as ações associadas que pode tomar para tentar corrigi-las:

 - Erro de codificação do utilizador: 
 
   - [System.ArgumentException](/dotnet/api/system.argumentexception?view=netcore-3.1)
   - [Sistema.InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1)
   - [Sistema.OperationCanceledException](/dotnet/api/system.operationcanceledexception?view=netcore-3.1)
   - [System.Runtime.Serialization.SerializationException](/dotnet/api/system.runtime.serialization.serializationexception?view=netcore-3.1)
   
   Ação geral: Tente fixar o código antes de prosseguir.
 
 - Erro de configuração/configuração: 
 
   - [Microsoft.ServiceBus.messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception)
   - [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception)
   - [Sistema.União DesacessExcepção](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1)
   
   Ação geral: Reveja a sua configuração e altere se necessário.
   
 - Exceções transitórias: 
 
   - [Microsoft.ServiceBus.messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception)
   - [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception)
   - [Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception)
   - [Microsoft.ServiceBus.messaging.messagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)
   
   Ação geral: Recandid no funcionamento ou notifique os utilizadores.
 
 - Outras exceções: 
 
   - [System.TransactionException](/dotnet/api/system.transactions.transactionexception?view=netcore-3.1)
   - [Sistema.TimeoutExcepção](#timeoutexception)
   - [Microsoft.ServiceBus.messaging.messageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception)
   - [Microsoft.ServiceBus.messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception)
   
   Ação geral: Específica ao tipo de exceção; consulte a tabela na secção seguinte. 

## <a name="exception-types"></a>Tipos de exceções
A tabela que se segue lista tipos de exceções de mensagens e as suas causas, e notas sugeridas a medidas que pode tomar.

| Tipo de Exceção | Descrição/Causas/Exemplos | Ação Sugerida | Nota sobre relagem automática/imediata |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutExcepção](/dotnet/api/system.timeoutexception?view=netcore-3.1) |O servidor não respondeu à operação solicitada dentro do tempo especificado, que é controlado pelo [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). O servidor pode ter concluído a operação solicitada. Esta exceção pode acontecer devido a atrasos na rede ou em outras infraestruturas. |Verifique se o estado do sistema é necessário para obter consistência e volte a tentar, se necessário.<br /> Ver [TimeoutException](#timeoutexception). | Redação pode ajudar em alguns casos; adicionar lógica de retíria ao código. |
| [InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1) |A operação de utilizador solicitada não é permitida dentro do servidor ou serviço. Consulte a mensagem de exceção para mais detalhes. Por exemplo, [a Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) gera esta exceção se a mensagem tiver sido recebida no modo [ReceiveAndDelete.](/dotnet/api/microsoft.servicebus.messaging.receivemode) | Verifique o código e a documentação. Certifique-se de que a operação solicitada é válida. | Tentar não vai ajudar. |
| [OperaçãoCanceledException](/dotnet/api/system.operationcanceledexception?view=netcore-3.1) | Uma tentativa é feita para invocar uma operação num objeto que já foi fechado, abortado ou eliminado. Em casos raros, a transação ambiente já está eliminada. | Verifique o código e certifique-se de que não invoca operações num objeto eliminado. | Tentar não vai ajudar. |
| [Não autorizadoAccessExcepção](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1) | O objeto [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) não conseguiu adquirir um token, o token é inválido, ou o token não contém as alegações necessárias para fazer a operação. | Certifique-se de que o fornecedor de fichas é criado com os valores corretos. Verifique a configuração do Serviço de Controlo de Acesso. | Redação pode ajudar em alguns casos; adicionar lógica de retíria ao código. |
| [ArgumentoExcepção](/dotnet/api/system.argumentexception?view=netcore-3.1)<br /> [Argumentação](/dotnet/api/system.argumentnullexception?view=netcore-3.1)<br />[ArgumentOutOfRangeException](/dotnet/api/system.argumentoutofrangeexception?view=netcore-3.1) | Um ou mais argumentos fornecidos ao método são inválidos. O URI fornecido ao [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [criar](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) contém segmento de caminhos. O esquema URI fornecido ao [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) é inválido. O valor da propriedade é maior que 32 KB. | Verifique o código de chamada e certifique-se de que os argumentos estão corretos. | Tentar novamente não vai ajudar. |
| [Microsoft.ServiceBus.Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MensagensEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | A entidade associada à operação não existe ou foi eliminada. | Certifique-se de que a entidade existe. | Tentar novamente não vai ajudar. |
| [MensagensComumicoexcepção](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | O cliente não é capaz de estabelecer uma ligação ao Event Hub. |Certifique-se de que o nome do anfitrião fornecido está correto e que o hospedeiro está acessível. | Redatório pode ajudar se houver problemas de conectividade intermitentes. |
| [Microsoft.ServiceBus.Messaging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | O serviço não é capaz de processar o pedido neste momento. | O cliente pode esperar por um período de tempo e depois voltar a tentar a operação. <br /> Ver [ServerBusyException](#serverbusyexception). | O cliente pode voltar a tentar após determinado intervalo. Se uma nova mente resultar numa exceção diferente, verifique o comportamento de repetição dessa exceção. |
| [MensagensExcepção](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Exceção genérica de mensagens que podem ser lançadas nos seguintes casos: Uma tentativa é feita para criar um [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) usando um nome ou caminho que pertence a um tipo de entidade diferente (por exemplo, um tópico). Uma tentativa é feita para enviar uma mensagem maior que 1 MB. O servidor ou serviço encontrou um erro durante o processamento do pedido. Consulte a mensagem de exceção para mais detalhes. Esta exceção é geralmente uma exceção transitória. | Verifique o código e certifique-se de que apenas são utilizados objetos serializáveis para o corpo da mensagem (ou utilize um serializador personalizado). Verifique a documentação para os tipos de valor suportados das propriedades e utilize apenas tipos suportados. Verifique a propriedade [IsTransient.](/dotnet/api/microsoft.servicebus.messaging.messagingexception) Se for **verdade,** pode voltar a tentar a operação. | O comportamento relemderecido é indefinido e pode não ajudar. |
| [Entidade de MensagensAlreadyExistsExcepção](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Tente criar uma entidade com um nome que já seja utilizado por outra entidade nesse espaço de nome de serviço. | Elimine a entidade existente ou escolha um nome diferente para a entidade a criar. | Tentar novamente não vai ajudar. |
| [QuotaExceededexcepção](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | A entidade de mensagens atingiu o seu tamanho máximo admissível. Esta exceção pode acontecer se o número máximo de recetores (que é 5) já tiver sido aberto a nível de grupo por consumidor. | Criar espaço na entidade através da receção de mensagens da entidade ou das suas subquesidades. <br /> Ver [QuotaExceededException](#quotaexceededexception) | A recandidinha poderá ajudar se as mensagens tiverem sido removidas entretanto. |
| [Entidade de MensagensDisabledExcepção](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Pedido de operação de execução a uma entidade deficiente. |Ativar a entidade. | A recandidologia poderá ajudar se a entidade tiver sido ativada de forma provisória. |
| [Microsoft.ServiceBus.messagingSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceededEdEdException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Uma carga útil de mensagem excede o limite de 1-MB. Este limite de 1-MB é para a mensagem total, que pode incluir propriedades do sistema e qualquer sobrecarga .NET. | Reduza o tamanho da carga útil da mensagem e, em seguida, relemisse a operação. |Tentar novamente não vai ajudar. |

## <a name="quotaexceededexception"></a>QuotaExceededexcepção
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indica que foi excedida uma quota de uma entidade específica.

Esta exceção pode acontecer se o número máximo de recetores (5) já tiver sido aberto a nível de grupo por consumidor.

### <a name="event-hubs"></a>Hubs de Eventos
O Event Hubs tem um limite de 20 grupos de consumidores por Event Hub. Quando tenta criar mais, recebe uma [QuotaExceedededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutExcepção
Um [TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1) indica que uma operação iniciada pelo utilizador está a demorar mais tempo do que o tempo limite de funcionamento. 

Para os Centros de Eventos, o tempo limite é especificado quer como parte da cadeia de ligação, quer através do [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). A mensagem de erro em si pode variar, mas contém sempre o valor de tempo limite especificado para a operação atual. 

### <a name="common-causes"></a>Causas comuns
Existem duas causas comuns para este erro: configuração incorreta ou um erro de serviço transitório.

- **Configuração incorreta** O tempo limite de funcionamento pode ser muito pequeno para a condição operacional. O valor predefinido para o tempo limite de funcionamento no cliente SDK é de 60 segundos. Verifique se o seu código tem o valor definido para algo demasiado pequeno. O estado de utilização da rede e da CPU pode afetar o tempo que uma determinada operação demora a ser concluída, pelo que o tempo limite de funcionamento não deve ser definido para um pequeno valor.
- **Erro de serviço transitório** Por vezes, o serviço Event Hubs pode sofrer atrasos no processamento de pedidos; por exemplo, durante períodos de tráfego elevado. Nesses casos, pode voltar a tentar a sua operação após um atraso, até que a operação seja bem sucedida. Se a mesma operação continuar a falhar após várias tentativas, visite o [site de estado de serviço da Azure](https://azure.microsoft.com/status/) para ver se existem falhas de serviço conhecidas.

## <a name="serverbusyexception"></a>ServerBusyException

Um [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) ou [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) indica que um servidor está sobrecarregado. Existem dois códigos de erro relevantes para esta exceção.

### <a name="error-code-50002"></a>Código de erro 50002
Este erro pode ocorrer por uma de duas razões:

- A carga não é distribuída uniformemente em todas as divisórias do centro do evento, e uma partição atinge a limitação da unidade de produção local.
    
    **Resolução**: Rever a estratégia de distribuição de divisórias ou experimentar [o EventHubClient.Send (eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) pode ajudar.

- O espaço de nomes Do Event Hubs não tem unidades de produção suficientes (pode verificar o ecrã **Métricas** na janela de espaço de nomes Do Event Hubs no [portal Azure](https://portal.azure.com) para confirmar). O portal mostra informação agregada (1 minuto), mas medimos a produção em tempo real – por isso é apenas uma estimativa.

    **Resolução**: Aumentar as unidades de produção no espaço de nomes pode ajudar. Pode fazer esta operação no portal, na janela **escala** do ecrã do espaço de nomes Do Event Hubs. Ou pode utilizar [auto-insuflado.](event-hubs-auto-inflate.md)

### <a name="error-code-50001"></a>Código de erro 50001

Este erro raramente deve ocorrer. Acontece quando o código de funcionamento do contentor para o seu espaço de identificação é baixo na CPU – não mais do que alguns segundos antes do início do balançor de carga do Event Hubs.

**Resolução**: Limite das chamadas para o método GetRuntimeInformation. O Azure Event Hubs suporta até 50 chamadas por segundo para o GetRuntimeInfo por segundo. Poderá receber uma exceção semelhante à seguinte assim que o limite for atingido:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```


## <a name="next-steps"></a>Próximos passos

Pode saber mais sobre os Hubs de Eventos ao aceder às seguintes ligações:

* [Descrição geral dos Event Hubs](./event-hubs-about.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
