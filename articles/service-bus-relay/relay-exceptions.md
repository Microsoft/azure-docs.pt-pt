---
title: Exceções de reencaminhamento do Azure e como resolvê-los | Documentos da Microsoft
description: Lista de exceções de reencaminhamento do Azure e ações sugeridas, que pode tomar para ajudar a resolvê-los.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60749041"
---
# <a name="azure-relay-exceptions"></a>Exceções de reencaminhamento do Azure

Este artigo lista algumas exceções que podem ser geradas pelas APIs de reencaminhamento do Azure. Esta referência está sujeitas a alterações, por isso, verifique novamente a existência de atualizações.

## <a name="exception-categories"></a>Categorias de exceção

As APIs de reencaminhamento gerar exceções que podem se enquadrar em categorias a seguir. Também incluídos estão ações sugeridas, que pode tomar para ajudar a resolver as exceções.

*   **Erro de código de utilizador**: System.ArgumentException, [System.InvalidOperationException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). 

    **Ação geral**: Tente corrigir o código antes de continuar.
*   **Erro de configuração**: [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). 

    **Ação geral**: Reveja a configuração. Se necessário, altere a configuração.
*   **Exceções transitórias**: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Ação geral**: Repita a operação ou notificar os utilizadores.
*   **Outras exceções**: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx). 

    **Ação geral**: Específicas para o tipo de exceção. Consulte a tabela na secção seguinte. 

## <a name="exception-types"></a>Tipos de exceção

A tabela seguinte lista os tipos de exceções de mensagens e suas causas. Ele anota também ações sugeridas, que pode tomar para ajudar a resolver as exceções.

| **Tipo de exceção** | **Descrição** | **Ação sugerida** | **Tenha em atenção aquando da repetição imediata ou automática** |
| --- | --- | --- | --- |
| [Tempo limite](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |O servidor não respondeu para a operação pedida no período especificado, o que é controlada pela [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). O servidor de pode concluir a operação pedida. Isto pode dever-se a rede ou de outros atrasos de infraestrutura. |Verifique o estado do sistema para manter a consistência e, em seguida, repita, se necessário. Ver [TimeoutException](#timeoutexception). |Repetição poderá ajudar em alguns casos; Adicione lógica de repetição ao código. |
| [Operação inválida](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |A operação de utilizador solicitada não é permitida dentro do servidor ou serviço. Ver a mensagem de exceção para obter detalhes. |Verifique o código e a documentação. Certifique-se de que a operação pedida é válida. |Repetição não ajudará. |
| [Operação cancelada](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |É efetuada uma tentativa de invocar uma operação num objeto que já foi fechado, abortada ou eliminado. Em casos raros, a transação de ambiente já foi eliminada. |Verifique o código e certifique-se de que ele não invoca operações num objeto descartado. |Repetição não ajudará. |
| [Acesso não autorizado](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |O [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objeto não foi possível obter um token, o token é inválido ou o token não contém as declarações necessárias para efetuar a operação. |Certifique-se de que o fornecedor do token é criado com os valores corretos. Verifique a configuração do serviço de controlo de acesso. |Repetição poderá ajudar em alguns casos; Adicione lógica de repetição ao código. |
| [Exceção de argumento](https://msdn.microsoft.com/library/system.argumentexception.aspx),<br /> [Argumento nulo](https://msdn.microsoft.com/library/system.argumentnullexception.aspx),<br />[Argumento fora do intervalo](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Ocorreram um ou mais dos seguintes procedimentos:<br />Um ou mais argumentos fornecidos para o método são inválidos.<br /> O URI fornecido a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [criar](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) contém um ou mais segmentos de caminho.<br />O esquema URI fornecido a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [criar](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) é inválido. <br />O valor da propriedade é maior do que 32 KB. |Verifique o código de chamada e certifique-se de que os argumentos estão corretos. |Repetição não ajudará. |
| [Servidor ocupado](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |Serviço não é capaz de processar o pedido neste momento. |O cliente pode aguardar um período de tempo, em seguida, repita a operação. |O cliente poderá repetir após um intervalo específico. Se uma repetição os resultados numa exceção diferente, verifique o comportamento de repetição dessa exceção. |
| [Quota excedida](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |A entidade de mensagens foi atingido o tamanho máximo admissível. |Crie espaço na entidade por receber mensagens a partir da entidade ou seu subfilas. Ver [QuotaExceededException](#quotaexceededexception). |Repetição pode ajudar se as mensagens foram removidas enquanto isso. |
| [Foi excedido o tamanho de mensagem](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Um payload de mensagem excede o limite de 256 KB. Observe que o limite de 256 KB é o tamanho total de mensagens. O tamanho total de mensagens pode incluir propriedades do sistema e qualquer sobrecarga de Microsoft .NET. |Reduzir o tamanho da carga de mensagem, em seguida, repita a operação. |Repetição não ajudará. |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indica que foi excedida uma quota de uma entidade específica.

Para o reencaminhamento, essa exceção encapsula a [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx), que indica que o número máximo de serviços de escuta foi excedido para este ponto final. Isso é indicado no **MaximumListenersPerEndpoint** valor da mensagem de exceção.

## <a name="timeoutexception"></a>TimeoutException
R [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica que uma operação iniciada pelo utilizador está a demorar mais tempo do que o tempo limite da operação. 

Verificar o valor do [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) propriedade. Atingir este limite também pode causar uma [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

Para o reencaminhamento, poderá receber exceções de tempo limite quando a primeira vez que abrir uma ligação de remetente do reencaminhamento. Existem duas causas comuns para esta exceção:

*   O [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) valor poderá ser demasiado pequeno (se ainda por uma fração de segundo).
* Um serviço de escuta de reencaminhamento no local pode estar sem resposta (ou pode surgir problemas de regras de firewall que proíbem a serviços de escuta de aceitar novas ligações de cliente) e o [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) valor é menor do que cerca de 20 segundos.

Exemplo:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Causas comuns
Existem duas causas comuns deste erro:

*   **Configuração incorreta**
    
    O tempo limite da operação poderá ser demasiado pequeno para a condição operacional. O valor predefinido para o tempo limite da operação no SDK de cliente é 60 segundos. Verifique se o valor em seu código está definido como algo demasiado pequena. Tenha em atenção que a utilização da CPU e a condição da rede podem afetar o tempo necessário para uma operação seja concluída. É uma boa idéia não definir o tempo limite da operação para um valor muito pequeno.
*   **Erro de serviço transitório**

    Ocasionalmente, o serviço de reencaminhamento pode ocorrer atrasos no processamento de pedidos. Isto pode acontecer, por exemplo, durante os períodos de tráfego elevado. Se isto ocorrer, repita a operação após um atraso, até que a operação é efetuada com êxito. Verifique se a mesma operação continuar a falhar após várias tentativas, o [site do Estado do serviço do Azure](https://azure.microsoft.com/status/) para ver se existem são conhecidos interrupções de serviço.

## <a name="next-steps"></a>Passos Seguintes
* [FAQ de reencaminhamento do Azure](relay-faq.md)
* [Criar um espaço de nomes do reencaminhamento](relay-create-namespace-portal.md)
* [Introdução ao reencaminhamento do Azure e o .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Introdução ao reencaminhamento do Azure e o nó](relay-hybrid-connections-node-get-started.md)

