---
title: Exceções do Azure Relay e como resolvê-las | Microsoft Docs
description: Lista de exceções do Azure Relay e ações sugeridas que pode tomar para ajudar a resolvê-las.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 44eeba6eb7b8cfd4e81a923c2d9a3155f1709f2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98625153"
---
# <a name="azure-relay-exceptions"></a>Exceções do Azure Relay

Este artigo enumera algumas exceções que podem ser geradas pelas APIs do Azure Relay. Esta referência está sujeita a alterações, por isso verifique se há atualizações.

## <a name="exception-categories"></a>Categorias de exceções

As APIs do Relé geram exceções que podem cair nas seguintes categorias. Também estão listadas as ações sugeridas que pode tomar para ajudar a resolver as exceções.

*   **Erro de codificação do utilizador**: [System.ArgumentException,](/dotnet/api/system.argumentexception) [System.InvalidOperationException,](/dotnet/api/system.invalidoperationexception) [System.OperationCanceledException](/dotnet/api/system.operationcanceledexception), [System.Runtime.Serialization.SerializationException](/dotnet/api/system.runtime.serialization.serializationexception). 

    **Ação geral**: Tente fixar o código antes de prosseguir.
*   **Erro de configuração/configuração**: [System.UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception). 

    **Ação geral**: Reveja a sua configuração. Se necessário, altere a configuração.
*   **Exceções transitórias**: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Ação geral**: Recandid no funcionamento ou notifique os utilizadores.
*   **Outras exceções**: [System.TransactionException,](/dotnet/api/system.transactions.transactionexception) [System.TimeoutException](/dotnet/api/system.timeoutexception). 

    **Ação geral**: Específico do tipo de exceção. Consulte a tabela na secção seguinte. 

## <a name="exception-types"></a>Tipos de exceções

A tabela que se segue lista os tipos de exceções de mensagens e as suas causas. Também observa ações sugeridas que pode tomar para ajudar a resolver as exceções.

| **Tipo de exceção** | **Descrição** | **Ação sugerida** | **Nota sobre relagem automática ou imediata** |
| --- | --- | --- | --- |
| [Intervalo de tempo](/dotnet/api/system.timeoutexception) |O servidor não respondeu à operação solicitada dentro do tempo especificado, que é controlado pelo [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). O servidor pode ter concluído a operação solicitada. Isto pode acontecer devido a atrasos de rede ou de outras infraestruturas. |Verifique se o estado do sistema é de consistência e, em seguida, volte a tentar, se necessário. Ver [TimeoutException](#timeoutexception). |Redação pode ajudar em alguns casos; adicionar lógica de retíria ao código. |
| [Operação inválida](/dotnet/api/system.invalidoperationexception) |A operação do utilizador solicitado não é permitida dentro do servidor ou serviço. Consulte a mensagem de exceção para mais detalhes. |Verifique o código e a documentação. Certifique-se de que a operação solicitada é válida. |Tentar novamente não vai ajudar. |
| [Operação Cancelada](/dotnet/api/system.operationcanceledexception) |Uma tentativa é feita para invocar uma operação num objeto que já foi fechado, abortado ou eliminado. Em casos raros, a transação ambiente já está eliminada. |Verifique o código e certifique-se de que não invoca operações num objeto eliminado. |Tentar novamente não vai ajudar. |
| [Acesso não autorizado](/dotnet/api/system.unauthorizedaccessexception) |O objeto [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) não conseguiu adquirir um token, o token é inválido, ou o token não contém as reclamações necessárias para a realização da operação. |Certifique-se de que o fornecedor de fichas é criado com os valores corretos. Verifique a configuração do serviço Access Control. |Redação pode ajudar em alguns casos; adicionar lógica de retíria ao código. |
| [Exceção de argumentos,](/dotnet/api/system.argumentexception)<br /> [Argumento Nulo,](/dotnet/api/system.argumentnullexception)<br />[Argumento fora do alcance](/dotnet/api/system.argumentoutofrangeexception) |Ocorreu um ou mais dos seguintes factos:<br />Um ou mais argumentos fornecidos ao método são inválidos.<br /> O URI fornecido ao [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Criar](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) contém um ou mais segmentos de caminhos.<br />O esquema URI fornecido ao [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) é inválido. <br />O valor da propriedade é maior que 32 KB. |Verifique o código de chamada e certifique-se de que os argumentos estão corretos. |Tentar novamente não vai ajudar. |
| [Servidor Ocupado](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |O serviço não é capaz de processar o pedido neste momento. |O cliente pode esperar por um período de tempo, e depois voltar a tentar a operação. |O cliente pode voltar a tentar depois de um intervalo específico. Se uma nova mente resultar numa exceção diferente, verifique o comportamento redatório dessa exceção. |
| [Quota Excedida](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |A entidade de mensagens atingiu o seu tamanho máximo admissível. |Criar espaço na entidade através da receção de mensagens da entidade ou das suas subquesidades. Ver [QuotaExceededException](#quotaexceededexception). |A recandidinha poderá ajudar se as mensagens tiverem sido removidas entretanto. |
| [Tamanho da mensagem excedido](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Uma carga útil de mensagem excede o limite de 256 KB. Note que o limite de 256 KB é o tamanho total da mensagem. O tamanho total da mensagem pode incluir propriedades do sistema e qualquer sobrecarga Microsoft .NET. |Reduza o tamanho da carga útil da mensagem e, em seguida, relemisse a operação. |Tentar novamente não vai ajudar. |

## <a name="quotaexceededexception"></a>QuotaExceededexcepção

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indica que foi excedida uma quota de uma entidade específica.

Para a Relay, esta exceção envolve o [System.ServiceModel.QuotaExceedededException,](/dotnet/api/system.servicemodel.quotaexceededexception)o que indica que o número máximo de ouvintes foi ultrapassado para este ponto final. Isto é indicado no valor **máximo do Ponto Desponto** do Ponto de Verificação da exceção.

## <a name="timeoutexception"></a>TimeoutExcepção
Um [TimeoutException](/dotnet/api/system.timeoutexception) indica que uma operação iniciada pelo utilizador está a demorar mais tempo do que o tempo limite de funcionamento. 

Verifique o valor da propriedade [ServicePointManager.DefaultConnectionLimit.](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) Atingir este limite também pode causar uma [timeoutExcepção](/dotnet/api/system.timeoutexception).

Para o Relay, poderá receber exceções de tempo limite quando abrir pela primeira vez uma ligação de reencaminhadores. Há duas causas comuns para esta exceção:

*   O valor [OpenTimeout](/previous-versions/) pode ser demasiado pequeno (mesmo que seja por uma fração de segundo).
* Um ouvinte de retransmissão no local pode não responder (ou pode encontrar problemas com regras de firewall que proíbem os ouvintes de aceitar novas ligações com o cliente), e o valor [OpenTimeout](/previous-versions/) é inferior a 20 segundos.

Exemplo:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Causas comuns
Há duas causas comuns para este erro:

*   **Configuração incorreta**
    
    O tempo limite de funcionamento pode ser muito pequeno para a condição operacional. O valor predefinido para o tempo limite de funcionamento no cliente SDK é de 60 segundos. Verifique se o valor do seu código está definido para algo demasiado pequeno. Note que a utilização do CPU e o estado da rede podem afetar o tempo que uma operação demora a ser concluída. É uma boa ideia não definir o tempo limite da operação para um valor muito pequeno.
*   **Erro de serviço transitório**

    Ocasionalmente, o serviço Relay pode sofrer atrasos no processamento de pedidos. Isto pode acontecer, por exemplo, durante períodos de elevado tráfego. Se isto ocorrer, recorra à sua operação após um atraso, até que a operação seja bem sucedida. Se a mesma operação continuar a falhar após várias tentativas, verifique o [site de estado de serviço Azure](https://azure.microsoft.com/status/) para ver se existem falhas de serviço conhecidas.

## <a name="next-steps"></a>Passos seguintes
* [Perguntas frequentes de retransmissão de Azure](relay-faq.md)
* [Criar um espaço de nome de retransmissão](relay-create-namespace-portal.md)
* [Começa com o Azure Relay e .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Começa com o Azure Relay e o Nó](relay-hybrid-connections-node-get-started.md)
