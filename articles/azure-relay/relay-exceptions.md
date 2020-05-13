---
title: As exceções do Azure Relay e como resolvê-las Microsoft Docs
description: Lista de exceções à Azure Relay e ações sugeridas que pode tomar para ajudar a resolvê-las.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 5f9dd02c-cce0-43b3-8eb8-744f0c27f38c
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2017
ms.author: spelluru
ms.openlocfilehash: fe8f057443b978e70e7cdd2591affd455fefdca8
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210872"
---
# <a name="azure-relay-exceptions"></a>Exceções azure relay

Este artigo enumera algumas exceções que podem ser geradas pelas APIs de Retransmissão Azure. Esta referência está sujeita a alterações, por isso, verifique se há atualizações.

## <a name="exception-categories"></a>Categorias de exceções

As APIs de retransmissão geram exceções que podem cair nas seguintes categorias. Também estão listadas as ações sugeridas que pode tomar para ajudar a resolver as exceções.

*   **Erro de codificação**do utilizador : [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). 

    **Ação geral**: Tente corrigir o código antes de prosseguir.
*   **Erro de configuração/configuração**: [Sistema.Acesso não autorizadoExcepção](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). 

    **Ação geral**: Reveja a sua configuração. Se necessário, altere a configuração.
*   **Exceções transitórias**: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Ação geral**: Tente a operação ou notifique os utilizadores.
*   **Outras exceções**: [System.Transactions.TransactionsException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx). 

    **Ação geral**: Específico do tipo de exceção. Consulte a tabela na secção seguinte. 

## <a name="exception-types"></a>Tipos de exceção

A tabela seguinte lista os tipos de exceção de mensagens e as suas causas. Também nota que podem tomar medidas sugeridas para ajudar a resolver as exceções.

| **Tipo de exceção** | **Descrição** | **Ação sugerida** | **Nota sobre retry automático ou imediato** |
| --- | --- | --- | --- |
| [Intervalo](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |O servidor não respondeu à operação solicitada dentro do tempo especificado, que é controlado pela [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). O servidor pode ter concluído a operação solicitada. Isto pode acontecer devido a atrasos na rede ou outros atrasos na infraestrutura. |Verifique se o estado do sistema tem consistência e, se necessário, volte a tentar. Ver [TimeoutException](#timeoutexception). |A retentativa pode ajudar em alguns casos; adicionar lógica de retry ao código. |
| [Operação Inválida](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A operação de utilizador solicitada não é permitida dentro do servidor ou serviço. Consulte a mensagem de exceção para mais detalhes. |Verifique o código e a documentação. Certifique-se de que a operação solicitada é válida. |Retry não vai ajudar. |
| [Operação Cancelada](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |É feita uma tentativa de invocar uma operação a um objeto que já foi fechado, abortado ou eliminado. Em casos raros, a transação ambiente já está alienada. |Verifique o código e certifique-se de que não invoca operações num objeto eliminado. |Retry não vai ajudar. |
| [Acesso não autorizado](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |O objeto [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) não conseguiu adquirir um símbolo, o símbolo é inválido ou o símbolo não contém as alegações necessárias para a realização da operação. |Certifique-se de que o fornecedor simbólico é criado com os valores corretos. Verifique a configuração do serviço de Controlo de Acesso. |A retentativa pode ajudar em alguns casos; adicionar lógica de retry ao código. |
| [Exceção do argumento,](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [Argumento Nulo,](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[Argumento fora de alcance](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Um ou mais dos seguintes ocorreram:<br />Um ou mais argumentos fornecidos ao método são inválidos.<br /> O URI fornecido ao [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) contém um ou mais segmentos de caminho.<br />O regime URI fornecido ao [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) é inválido. <br />O valor da propriedade é superior a 32 KB. |Verifique o código de chamada e certifique-se de que os argumentos estão corretos. |Retry não vai ajudar. |
| [Servidor Ocupado](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |O serviço não pode processar o pedido neste momento. |O cliente pode esperar por um período de tempo, e depois voltar a tentar a operação. |O cliente pode voltar a tentar depois de um intervalo específico. Se uma nova tentativa resultar numa exceção diferente, verifique o comportamento de retry dessa exceção. |
| [Quota Ultrapassada](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |A entidade de mensagens atingiu o seu tamanho máximo permitido. |Criar espaço na entidade recebendo mensagens da entidade ou das suas subfilas. Ver [QuotaExceededException](#quotaexceededexception). |A retry pode ajudar se as mensagens tiverem sido removidas entretanto. |
| [Tamanho da mensagem ultrapassado](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Uma carga útil de mensagem excede o limite de 256 KB. Note que o limite de 256 KB é o tamanho total da mensagem. O tamanho total da mensagem pode incluir propriedades do sistema e qualquer sobrecarga da Microsoft .NET. |Reduza o tamanho da carga útil da mensagem e, em seguida, tente novamente a operação. |Retry não vai ajudar. |

## <a name="quotaexceededexception"></a>QuotaExceedException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indica que foi excedida uma quota de uma entidade específica.

Para a Retransmissão, esta exceção envolve o [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx), que indica que o número máximo de ouvintes foi ultrapassado para este ponto final. Isto é indicado no valor **máximo de ouvintesPerEndpoint** da mensagem de exceção.

## <a name="timeoutexception"></a>TimeoutException
Um [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica que uma operação iniciada pelo utilizador está a demorar mais tempo do que o tempo de funcionamento. 

Verifique o valor da propriedade [ServicePointManager.DefaultConnectionLimit.](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) Atingir este limite também pode causar uma Exceção do [Tempo.](https://msdn.microsoft.com/library/system.timeoutexception.aspx)

Para o Relay, poderá receber exceções no tempo de paragem quando abrir pela primeira vez uma ligação de remetente. Há duas causas comuns para esta exceção:

*   O valor [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) pode ser muito pequeno (mesmo que por uma fração de segundo).
* Um ouvinte de retransmissão no local pode não responder (ou pode encontrar problemas de regras de firewall que proíbem os ouvintes de aceitar novas ligações com o cliente), e o valor [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) é inferior a cerca de 20 segundos.

Exemplo:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Causas comuns
Existem duas causas comuns para este erro:

*   **Configuração incorreta**
    
    O tempo de funcionamento pode ser muito pequeno para a condição operacional. O valor padrão para o tempo de funcionamento no SDK cliente é de 60 segundos. Verifique se o valor do seu código está definido para algo demasiado pequeno. Note que o uso do CPU e o estado da rede podem afetar o tempo que uma operação leva para completar. É uma boa ideia não definir o tempo de funcionamento para um valor muito pequeno.
*   **Erro de serviço transitório**

    Ocasionalmente, o serviço Relay pode experimentar atrasos nos pedidos de processamento. Isto pode acontecer, por exemplo, durante períodos de tráfego elevado. Se isto ocorrer, tente novamente a sua operação após um atraso, até que a operação seja bem sucedida. Se a mesma operação continuar a falhar após várias tentativas, verifique o local de estado do [serviço Azure](https://azure.microsoft.com/status/) para ver se existem falhas de serviço conhecidas.

## <a name="next-steps"></a>Passos seguintes
* [FaQs de retransmissão azure](relay-faq.md)
* [Criar um espaço de nome de retransmissão](relay-create-namespace-portal.md)
* [Começa com o Azure Relay e .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Começar com O Relé Azure e Nó](relay-hybrid-connections-node-get-started.md)

