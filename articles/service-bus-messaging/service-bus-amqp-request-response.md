---
title: Operações de solicitação/resposta do AMQP 1,0 no barramento de serviço do Azure
description: Lista de Barramento de Serviço do Microsoft Azure operações baseadas em solicitação/resposta.
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/22/2019
ms.author: aschhab
ms.openlocfilehash: b549aee197f35df29a982a1a86644c46b9061c63
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72785284"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>AMQP 1,0 em Barramento de Serviço do Microsoft Azure: operações com base em solicitação-resposta

Este artigo define a lista de Barramento de Serviço do Microsoft Azure operações baseadas em solicitação/resposta. Essas informações se baseiam no rascunho de trabalho do AMQP Management versão 1,0.  
  
Para obter um guia de protocolo AMQP 1,0 de nível de transmissão detalhado, que explica como o barramento de serviço implementa e se baseia na especificação técnica do OASIS AMQP, consulte o [AMQP 1,0 no barramento de serviço do Azure e no guia do protocolo de hubs de eventos][guia do protocolo amqp 1.0].  
  
## <a name="concepts"></a>Conceitos  
  
### <a name="entity-description"></a>Descrição da entidade  

Uma descrição de entidade refere-se a uma classe [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription)do barramento de serviço, [classe TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription)ou objeto de [classe SubscriptionDescription](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) .  
  
### <a name="brokered-message"></a>Mensagem orientada  

Representa uma mensagem no barramento de serviço, que é mapeada para uma mensagem AMQP. O mapeamento é definido no [Guia do protocolo AMQP do barramento de serviço](service-bus-amqp-protocol-guide.md).  
  
## <a name="attach-to-entity-management-node"></a>Anexar ao nó de gerenciamento de entidades  

Todas as operações descritas neste documento seguem um padrão de solicitação/resposta, estão no escopo de uma entidade e exigem a anexação a um nó de gerenciamento de entidade.  
  
### <a name="create-link-for-sending-requests"></a>Criar link para enviar solicitações  

Cria um link para o nó de gerenciamento para enviar solicitações.  
  
```
requestLink = session.attach(
role: SENDER,
    target: { address: "<entity address>/$management" },
    source: { address: ""<my request link unique address>" }
)

```
  
### <a name="create-link-for-receiving-responses"></a>Criar link para receber respostas  

Cria um link para receber respostas do nó de gerenciamento.  
  
```
responseLink = session.attach(
role: RECEIVER,
    source: { address: "<entity address>/$management" }
    target: { address: "<my response link unique address>" }
)

```
  
### <a name="transfer-a-request-message"></a>Transferir uma mensagem de solicitação  

Transfere uma mensagem de solicitação.  
Um estado de transação pode ser adicionado opcionalmente para operações que dão suporte à transação.

```
requestLink.sendTransfer(
        Message(
                properties: {
                        message-id: <request id>,
                        reply-to: "<my response link unique address>"
                },
                application-properties: {
                        "operation" -> "<operation>",
                }
        ),
        [Optional] State = transactional-state: {
                txn-id: <txn-id>
        }
)
```
  
### <a name="receive-a-response-message"></a>Receber uma mensagem de resposta  

Recebe a mensagem de resposta do link de resposta.  
  
```
responseMessage = responseLink.receiveTransfer()
```
  
A mensagem de resposta está no seguinte formato:
  
```
Message(
properties: {
        correlation-id: <request id>
    },
    application-properties: {
            "statusCode" -> <status code>,
            "statusDescription" -> <status description>,
           },
)

```
  
### <a name="service-bus-entity-address"></a>Endereço da entidade do barramento de serviço  

As entidades do barramento de serviço devem ser abordadas da seguinte maneira:  
  
|Tipo de entidade|Endereço|Exemplo|  
|-----------------|-------------|-------------|  
|fila|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|tópico|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|subscrição|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Operações de mensagem  
  
### <a name="message-renew-lock"></a>Bloqueio de renovação de mensagem  

Estende o bloqueio de uma mensagem pelo tempo especificado na descrição da entidade.  
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|Operacional|string|Sim|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
 O corpo da mensagem de solicitação deve consistir em uma seção AMQP-Value que contém um mapa com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|matriz de UUID|Sim|Tokens de bloqueio de mensagem para renovar.|  

> [!NOTE]
> Os tokens de bloqueio são a propriedade `DeliveryTag` nas mensagens recebidas. Consulte o exemplo a seguir no [SDK do .net](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) que os recupera. O token também pode aparecer no ' DeliveryAnnotations ' como ' x-opt-Lock-token ', mas isso não é garantido e o `DeliveryTag` deve ser preferencial. 
> 
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha.|  
|statusDescription|string|Não|Descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção AMQP-Value que contém um mapa com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|Expirações|matriz de carimbo de data/hora|Sim|Novo vencimento do token de bloqueio de mensagem correspondente aos tokens de bloqueio de solicitação.|  
  
### <a name="peek-message"></a>Inspecionar mensagem  

Inspeciona mensagens sem bloqueio.  
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|Operacional|string|Sim|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|Longas|Sim|Número de sequência do qual iniciar a inspeção.|  
|`message-count`|int|Sim|Número máximo de mensagens a serem inspecionadas.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 204: nenhum conteúdo – não há mais mensagens|  
|statusDescription|string|Não|Descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|mensagens|lista de mapas|Sim|Lista de mensagens nas quais cada mapa representa uma mensagem.|  
  
O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|message|matriz de bytes|Sim|Mensagem codificada em fio do AMQP 1,0.|  
  
### <a name="schedule-message"></a>Agendar mensagem  

Agenda mensagens. Esta operação dá suporte à transação.
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|Operacional|string|Sim|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|mensagens|lista de mapas|Sim|Lista de mensagens nas quais cada mapa representa uma mensagem.|  
  
O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|ID da mensagem|string|Sim|`amqpMessage.Properties.MessageId` como cadeia de caracteres|  
|ID da sessão|string|Não|`amqpMessage.Properties.GroupId as string`|  
|chave de partição|string|Não|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|chave de partição via|string|Não|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|message|matriz de bytes|Sim|Mensagem codificada em fio do AMQP 1,0.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha.|  
|statusDescription|string|Não|Descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **AMQP-Value** que contém um mapa com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|números de sequência|matriz de longo|Sim|Número de sequência de mensagens agendadas. O número de sequência é usado para cancelar.|  
  
### <a name="cancel-scheduled-message"></a>Cancelar mensagem agendada  

Cancela as mensagens agendadas.  
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|Operacional|string|Sim|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|números de sequência|matriz de longo|Sim|Números de sequência de mensagens agendadas a serem canceladas.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha.|  
|statusDescription|string|Não|Descrição do status.|   
  
## <a name="session-operations"></a>Operações de sessão  
  
### <a name="session-renew-lock"></a>Bloqueio de renovação de sessão  

Estende o bloqueio de uma mensagem pelo tempo especificado na descrição da entidade.  
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|Operacional|string|Sim|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|ID da sessão|string|Sim|ID da sessão.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 204: nenhum conteúdo – não há mais mensagens|  
|statusDescription|string|Não|Descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **AMQP-Value** que contém um mapa com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|Ela|carimbo de data/hora|Sim|Nova expiração.|  
  
### <a name="peek-session-message"></a>Inspecionar mensagem de sessão  

Inspeciona mensagens de sessão sem bloqueio.  
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|Operacional|string|Sim|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|número de sequência de|Longas|Sim|Número de sequência do qual iniciar a inspeção.|  
|contagem de mensagens|int|Sim|Número máximo de mensagens a serem inspecionadas.|  
|ID da sessão|string|Sim|ID da sessão.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 204: nenhum conteúdo – não há mais mensagens|  
|statusDescription|string|Não|Descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **AMQP-Value** que contém um mapa com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|mensagens|lista de mapas|Sim|Lista de mensagens nas quais cada mapa representa uma mensagem.|  
  
 O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|message|matriz de bytes|Sim|Mensagem codificada em fio do AMQP 1,0.|  
  
### <a name="set-session-state"></a>Definir estado da sessão  

Define o estado de uma sessão.  
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|Operacional|string|Sim|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|ID da sessão|string|Sim|ID da sessão.|  
|estado da sessão|matriz de bytes|Sim|Dados binários opacos.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|string|Não|Descrição do status.|  
  
### <a name="get-session-state"></a>Obter estado da sessão  

Obtém o estado de uma sessão.  
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|Operacional|string|Sim|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|ID da sessão|string|Sim|ID da sessão.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|string|Não|Descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|estado da sessão|matriz de bytes|Sim|Dados binários opacos.|  
  
### <a name="enumerate-sessions"></a>Enumerar sessões  

Enumera sessões em uma entidade de mensagens.  
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|Operacional|string|Sim|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|hora da última atualização|carimbo de data/hora|Sim|Filtre para incluir somente as sessões atualizadas após um determinado tempo.|  
|saltar|int|Sim|Ignorar várias sessões.|  
|Início|int|Sim|Número máximo de sessões.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 204: nenhum conteúdo – não há mais mensagens|  
|statusDescription|string|Não|Descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|saltar|int|Sim|Número de sessões ignoradas se o código de status for 200.|  
|sessões-IDs|Matriz de cadeias de caracteres|Sim|Matriz de IDs de sessão se o código de status for 200.|  
  
## <a name="rule-operations"></a>Operações de regra  
  
### <a name="add-rule"></a>Adicionar regra  
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|Operacional|string|Sim|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|nome da regra|string|Sim|Nome da regra, sem incluir nomes de tópicos e de assinatura.|  
|regra-descrição|map|Sim|Descrição da regra, conforme especificado na próxima seção.|  
  
O mapa de **Descrição de regra** deve incluir as entradas a seguir, onde **SQL-Filter** e **correlação-Filter** são mutuamente exclusivos:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|filtro SQL|map|Sim|`sql-filter`, conforme especificado na próxima seção.|  
|correlação-filtro|map|Sim|`correlation-filter`, conforme especificado na próxima seção.|  
|SQL-regra-ação|map|Sim|`sql-rule-action`, conforme especificado na próxima seção.|  
  
O mapa do filtro SQL deve incluir as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|expressão|string|Sim|Expressão de filtro SQL.|  
  
O mapa **correlação-filtro** deve incluir pelo menos uma das seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|ID de correlação|string|Não||  
|ID da mensagem|string|Não||  
|para|string|Não||  
|responder para|string|Não||  
|label|string|Não||  
|ID da sessão|string|Não||  
|reply-to-session-ID|string|Não||  
|tipo de conteúdo|string|Não||  
|propriedades|map|Não|Mapeia para o barramento de serviço [BrokeredMessage. Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).|  
  
O mapa **SQL-Rule-Action** deve incluir as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|expressão|string|Sim|Expressão de ação SQL.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|string|Não|Descrição do status.|  
  
### <a name="remove-rule"></a>Remover regra  
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|Operacional|string|Sim|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|nome da regra|string|Sim|Nome da regra, sem incluir nomes de tópicos e de assinatura.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|string|Não|Descrição do status.|  
  
### <a name="get-rules"></a>Obter regras

#### <a name="request"></a>Pedir

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:

|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|Operacional|string|Sim|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  

O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|Início|int|Sim|O número de regras a serem buscadas na página.|  
|saltar|int|Sim|O número de regras a serem ignoradas. Define o índice inicial (+ 1) na lista de regras. | 

#### <a name="response"></a>Resposta

A mensagem de resposta inclui as seguintes propriedades:

|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|regras| matriz de mapa|Sim|Matriz de regras. Cada regra é representada por um mapa.|

Cada entrada de mapa na matriz inclui as seguintes propriedades:

|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|regra-descrição|matriz de objetos descritos|Sim|`com.microsoft:rule-description:list` com o código AMQP descrito 0x0000013700000004| 

`com.microsoft.rule-description:list` é uma matriz de objetos descritos. A matriz inclui o seguinte:

|Índice|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
| 0 | matriz de objetos descritos | Sim | `filter` conforme especificado abaixo. |
| 1 | matriz de objeto descrito | Sim | `ruleAction` conforme especificado abaixo. |
| 2 | string | Sim | nome da regra. |

`filter` pode ser de qualquer um dos seguintes tipos:

| Nome do descritor | Código do descritor | Valor |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | Filtro SQL |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | Filtro de correlação |
| `com.microsoft:true-filter:list` | 0x000001370000007 | Filtro verdadeiro que representa 1 = 1 |
| `com.microsoft:false-filter:list` | 0x000001370000008 | Filtro falso representando 1 = 0 |

`com.microsoft:sql-filter:list` é uma matriz descrita que inclui:

|Índice|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
| 0 | string | Sim | Expressão de filtro SQL |

`com.microsoft:correlation-filter:list` é uma matriz descrita que inclui:

|Índice (se existir)|Tipo de valor|Conteúdo do valor|  
|---------|----------------|--------------|
| 0 | string | ID de Correlação |
| 1 | string | ID da mensagem |
| 2 | string | a |
| 3 | string | Responder a |
| 4 | string | Label |
| 5 | string | ID de Sessão |
| 6 | string | Responder à ID da sessão|
| 7 | string | Tipo de conteúdo |
| 8 | Mapa | Mapa de propriedades definidas pelo aplicativo |

`ruleAction` pode ser um dos seguintes tipos:

| Nome do descritor | Código do descritor | Valor |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | Ação de regra vazia-nenhuma ação de regra presente |
| `com.microsoft:sql-rule-action:list` | 0x0000013700000006 | Ação de regra SQL |

`com.microsoft:sql-rule-action:list` é uma matriz de objetos descritos cuja primeira entrada é uma cadeia de caracteres que contém a expressão da ação da regra SQL.

## <a name="deferred-message-operations"></a>Operações de mensagens adiadas  
  
### <a name="receive-by-sequence-number"></a>Receber por número de sequência  

Recebe mensagens adiadas por número de sequência.  
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|Operacional|string|Sim|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|números de sequência|matriz de longo|Sim|Números de sequência.|  
|receptor-modo de liquidação|ubyte|Sim|Modo de **liquidação do destinatário** , conforme especificado no AMQP Core v 1.0.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|string|Não|Descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|mensagens|lista de mapas|Sim|Lista de mensagens em que cada mapa representa uma mensagem.|  
  
O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|token de bloqueio|uuid|Sim|Bloquear token se `receiver-settle-mode` for 1.|  
|message|matriz de bytes|Sim|Mensagem codificada em fio do AMQP 1,0.|  
  
### <a name="update-disposition-status"></a>Status de disposição da atualização  

Atualiza o status de disposição de mensagens adiadas. Esta operação dá suporte a transações.
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|Operacional|string|Sim|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|disposição-status|string|Sim|conclusão<br /><br /> Abandon<br /><br /> Suspenso|  
|tokens de bloqueio|matriz de UUID|Sim|Tokens de bloqueio de mensagem para atualizar o status de disposição.|  
|motivo de mensagens mortas|string|Não|Poderá ser definido se o status de disposição estiver definido como **suspenso**.|  
|Descrição de mensagens mortas|string|Não|Poderá ser definido se o status de disposição estiver definido como **suspenso**.|  
|Propriedades para modificar|map|Não|Lista de propriedades de mensagens orientadas do barramento de serviço a serem modificadas.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Obrigatório|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|string|Não|Descrição do status.|

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o AMQP e o barramento de serviço, visite os links a seguir:

* [Visão geral do AMQP do barramento de serviço]
* [Guia do protocolo AMQP 1.0]
* [AMQP no barramento de serviço para Windows Server]

[Visão geral do AMQP do barramento de serviço]: service-bus-amqp-overview.md
[Guia do protocolo AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP no barramento de serviço para Windows Server]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
