---
title: AMQP 1,0 em operações baseadas em solicitação-resposta do barramento de serviço do Azure | Microsoft Docs
description: Lista de Barramento de Serviço do Microsoft Azure operações baseadas em solicitação/resposta.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 4aea0a092ad836c020e23b81d38246ceead22ea0
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213291"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>AMQP 1,0 em Barramento de Serviço do Microsoft Azure: operações com base em solicitação-resposta

Este artigo define a lista de Barramento de Serviço do Microsoft Azure operações baseadas em solicitação/resposta. Essas informações se baseiam no rascunho de trabalho do AMQP Management versão 1,0.  
  
Para obter um detalhadas durante a transmissão nível AMQP 1.0 protocolo guia, que explica como o Service Bus implementa e baseia-se a especificação de técnica OASIS AMQP, consulte o [AMQP 1.0 no guia de protocolo do Service Bus do Azure e do Event Hubs][guia do protocolo amqp 1.0].  
  
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
|queue|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|topic|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|subscription|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Operações de mensagem  
  
### <a name="message-renew-lock"></a>Bloqueio de renovação de mensagem  

Estende o bloqueio de uma mensagem pelo tempo especificado na descrição da entidade.  
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|operation|Cadeia de caracteres|Sim|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
 O corpo da mensagem de solicitação deve consistir em uma seção AMQP-Value que contém um mapa com as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|matriz de UUID|Sim|Tokens de bloqueio de mensagem para renovar.|  

> [!NOTE]
> Os tokens de `DeliveryTag` bloqueio são a propriedade em mensagens recebidas. Consulte o exemplo a seguir no [SDK do .net](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) que os recupera. O token também pode aparecer no ' DeliveryAnnotations ' como ' x-opt-Lock-token ', mas isso não é garantido e deve ser `DeliveryTag` o preferencial. 
> 
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha.|  
|statusDescription|Cadeia de caracteres|Não|Descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção AMQP-Value que contém um mapa com as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|Expirações|matriz de carimbo de data/hora|Sim|Novo vencimento do token de bloqueio de mensagem correspondente aos tokens de bloqueio de solicitação.|  
  
### <a name="peek-message"></a>Inspecionar mensagem  

Inspeciona mensagens sem bloqueio.  
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|operation|Cadeia de caracteres|Sim|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|long|Sim|Número de sequência do qual iniciar a inspeção.|  
|`message-count`|int|Sim|Número máximo de mensagens a serem inspecionadas.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 204: Sem conteúdo – não há mais mensagens|  
|statusDescription|Cadeia de caracteres|Não|Descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|mensagens|lista de mapas|Sim|Lista de mensagens nas quais cada mapa representa uma mensagem.|  
  
O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|message|matriz de bytes|Sim|Mensagem codificada em fio do AMQP 1,0.|  
  
### <a name="schedule-message"></a>Agendar mensagem  

Agenda mensagens. Esta operação dá suporte à transação.
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|operation|Cadeia de caracteres|Sim|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|mensagens|lista de mapas|Sim|Lista de mensagens nas quais cada mapa representa uma mensagem.|  
  
O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|ID da mensagem|Cadeia de caracteres|Sim|`amqpMessage.Properties.MessageId`como cadeia de caracteres|  
|ID da sessão|Cadeia de caracteres|Não|`amqpMessage.Properties.GroupId as string`|  
|chave de partição|Cadeia de caracteres|Não|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|chave de partição via|Cadeia de caracteres|Não|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|message|matriz de bytes|Sim|Mensagem codificada em fio do AMQP 1,0.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha.|  
|statusDescription|Cadeia de caracteres|Não|Descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **AMQP-Value** que contém um mapa com as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|números de sequência|matriz de longo|Sim|Número de sequência de mensagens agendadas. O número de sequência é usado para cancelar.|  
  
### <a name="cancel-scheduled-message"></a>Cancelar mensagem agendada  

Cancela as mensagens agendadas.  
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|operation|Cadeia de caracteres|Sim|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|números de sequência|matriz de longo|Sim|Números de sequência de mensagens agendadas a serem canceladas.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha.|  
|statusDescription|Cadeia de caracteres|Não|Descrição do status.|   
  
## <a name="session-operations"></a>Operações de sessão  
  
### <a name="session-renew-lock"></a>Bloqueio de renovação de sessão  

Estende o bloqueio de uma mensagem pelo tempo especificado na descrição da entidade.  
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|operation|Cadeia de caracteres|Sim|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|ID da sessão|Cadeia de caracteres|Sim|ID da sessão.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 204: Sem conteúdo – não há mais mensagens|  
|statusDescription|Cadeia de caracteres|Não|Descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **AMQP-Value** que contém um mapa com as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|expiração|timestamp|Sim|Nova expiração.|  
  
### <a name="peek-session-message"></a>Inspecionar mensagem de sessão  

Inspeciona mensagens de sessão sem bloqueio.  
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|operation|Cadeia de caracteres|Sim|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|from-sequence-number|long|Sim|Número de sequência do qual iniciar a inspeção.|  
|contagem de mensagens|int|Sim|Número máximo de mensagens a serem inspecionadas.|  
|ID da sessão|Cadeia de caracteres|Sim|ID da sessão.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 204: Sem conteúdo – não há mais mensagens|  
|statusDescription|Cadeia de caracteres|Não|Descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **AMQP-Value** que contém um mapa com as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|mensagens|lista de mapas|Sim|Lista de mensagens nas quais cada mapa representa uma mensagem.|  
  
 O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|message|matriz de bytes|Sim|Mensagem codificada em fio do AMQP 1,0.|  
  
### <a name="set-session-state"></a>Definir estado da sessão  

Define o estado de uma sessão.  
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|operation|Cadeia de caracteres|Sim|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|ID da sessão|Cadeia de caracteres|Sim|ID da sessão.|  
|estado da sessão|matriz de bytes|Sim|Dados binários opacos.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|Cadeia de caracteres|Não|Descrição do status.|  
  
### <a name="get-session-state"></a>Obter estado da sessão  

Obtém o estado de uma sessão.  
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|operation|Cadeia de caracteres|Sim|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|ID da sessão|Cadeia de caracteres|Sim|ID da sessão.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|Cadeia de caracteres|Não|Descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|estado da sessão|matriz de bytes|Sim|Dados binários opacos.|  
  
### <a name="enumerate-sessions"></a>Enumerar sessões  

Enumera sessões em uma entidade de mensagens.  
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|operation|Cadeia de caracteres|Sim|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|hora da última atualização|timestamp|Sim|Filtre para incluir somente as sessões atualizadas após um determinado tempo.|  
|ignorar|int|Sim|Ignorar várias sessões.|  
|Início|int|Sim|Número máximo de sessões.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 204: Sem conteúdo – não há mais mensagens|  
|statusDescription|Cadeia de caracteres|Não|Descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|ignorar|int|Sim|Número de sessões ignoradas se o código de status for 200.|  
|sessões-IDs|Matriz de cadeias de caracteres|Sim|Matriz de IDs de sessão se o código de status for 200.|  
  
## <a name="rule-operations"></a>Operações de regra  
  
### <a name="add-rule"></a>Adicionar Regra  
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|operation|Cadeia de caracteres|Sim|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|nome da regra|Cadeia de caracteres|Sim|Nome da regra, sem incluir nomes de tópicos e de assinatura.|  
|regra-descrição|map|Sim|Descrição da regra, conforme especificado na próxima seção.|  
  
O mapa de **Descrição de regra** deve incluir as entradas a seguir, onde **SQL-Filter** e **correlação-Filter** são mutuamente exclusivos:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|sql-filter|map|Sim|`sql-filter`, conforme especificado na próxima seção.|  
|correlação-filtro|map|Sim|`correlation-filter`, conforme especificado na próxima seção.|  
|sql-rule-action|map|Sim|`sql-rule-action`, conforme especificado na próxima seção.|  
  
O mapa do filtro SQL deve incluir as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|expression|Cadeia de caracteres|Sim|Expressão de filtro SQL.|  
  
O mapa **correlação-filtro** deve incluir pelo menos uma das seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|ID de correlação|Cadeia de caracteres|Não||  
|ID da mensagem|Cadeia de caracteres|Não||  
|para|Cadeia de caracteres|Não||  
|responder para|Cadeia de caracteres|Não||  
|label|Cadeia de caracteres|Não||  
|ID da sessão|Cadeia de caracteres|Não||  
|reply-to-session-ID|Cadeia de caracteres|Não||  
|Tipo de conteúdo|Cadeia de caracteres|Não||  
|properties|map|Não|Mapeia para o barramento de serviço [BrokeredMessage. Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).|  
  
O mapa **SQL-Rule-Action** deve incluir as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|expression|Cadeia de caracteres|Sim|Expressão de ação SQL.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|Cadeia de caracteres|Não|Descrição do status.|  
  
### <a name="remove-rule"></a>Remover Regra  
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|operation|Cadeia de caracteres|Sim|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|nome da regra|Cadeia de caracteres|Sim|Nome da regra, sem incluir nomes de tópicos e de assinatura.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|Cadeia de caracteres|Não|Descrição do status.|  
  
### <a name="get-rules"></a>Obter regras

#### <a name="request"></a>Pedir

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:

|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|operation|Cadeia de caracteres|Sim|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  

O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|Início|int|Sim|O número de regras a serem buscadas na página.|  
|ignorar|int|Sim|O número de regras a serem ignoradas. Define o índice inicial (+ 1) na lista de regras. | 

#### <a name="response"></a>Resposta

A mensagem de resposta inclui as seguintes propriedades:

|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|rules| matriz de mapa|Sim|Matriz de regras. Cada regra é representada por um mapa.|

Cada entrada de mapa na matriz inclui as seguintes propriedades:

|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|regra-descrição|matriz de objetos descritos|Sim|`com.microsoft:rule-description:list`com código AMQP descrito 0x0000013700000004| 

`com.microsoft.rule-description:list`é uma matriz de objetos descritos. A matriz inclui o seguinte:

|Índice|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
| 0 | matriz de objetos descritos | Sim | `filter`conforme especificado abaixo. |
| 1 | matriz de objeto descrito | Sim | `ruleAction`conforme especificado abaixo. |
| 2 | Cadeia de caracteres | Sim | nome da regra. |

`filter`pode ser de qualquer um dos seguintes tipos:

| Nome do descritor | Código do descritor | Value |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | Filtro SQL |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | Filtro de correlação |
| `com.microsoft:true-filter:list` | 0x000001370000007 | Filtro verdadeiro que representa 1 = 1 |
| `com.microsoft:false-filter:list` | 0x000001370000008 | Filtro falso representando 1 = 0 |

`com.microsoft:sql-filter:list`é uma matriz descrita que inclui:

|Índice|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
| 0 | Cadeia de caracteres | Sim | Expressão de filtro SQL |

`com.microsoft:correlation-filter:list`é uma matriz descrita que inclui:

|Índice (se existir)|Tipo de valor|Conteúdo do valor|  
|---------|----------------|--------------|
| 0 | Cadeia de caracteres | ID de Correlação |
| 1 | Cadeia de caracteres | ID da mensagem |
| 2 | Cadeia de caracteres | Para |
| 3 | Cadeia de caracteres | Responder a |
| 4 | Cadeia de caracteres | Etiqueta |
| 5 | Cadeia de caracteres | ID de Sessão |
| 6 | Cadeia de caracteres | Responder a ID da Sessão|
| 7 | Cadeia de caracteres | Tipo de Conteúdo |
| 8 | Mapa | Mapa de propriedades definidas pelo aplicativo |

`ruleAction`pode ser um dos seguintes tipos:

| Nome do descritor | Código do descritor | Value |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | Ação de regra vazia-nenhuma ação de regra presente |
| `com.microsoft:sql-rule-action:list` | 0x0000013700000006 | Ação de regra SQL |

`com.microsoft:sql-rule-action:list`é uma matriz de objetos descritos cuja primeira entrada é uma cadeia de caracteres que contém a expressão da ação da regra SQL.

## <a name="deferred-message-operations"></a>Operações de mensagens adiadas  
  
### <a name="receive-by-sequence-number"></a>Receber por número de sequência  

Recebe mensagens adiadas por número de sequência.  
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|operation|Cadeia de caracteres|Sim|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|números de sequência|matriz de longo|Sim|Números de sequência.|  
|receiver-settle-mode|ubyte|Sim|Modo de **liquidação do destinatário** , conforme especificado no AMQP Core v 1.0.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|Cadeia de caracteres|Não|Descrição do status.|  
  
O corpo da mensagem de resposta deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|mensagens|lista de mapas|Sim|Lista de mensagens em que cada mapa representa uma mensagem.|  
  
O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|token de bloqueio|uuid|Sim|Bloquear token se `receiver-settle-mode` for 1.|  
|message|matriz de bytes|Sim|Mensagem codificada em fio do AMQP 1,0.|  
  
### <a name="update-disposition-status"></a>Status de disposição da atualização  

Atualiza o status de disposição de mensagens adiadas. Esta operação dá suporte a transações.
  
#### <a name="request"></a>Pedir  

A mensagem de solicitação deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|operation|Cadeia de caracteres|Sim|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Não|Tempo limite do servidor de operação em milissegundos.|  
  
O corpo da mensagem de solicitação deve consistir em uma seção **AMQP-Value** que contém um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|disposição-status|Cadeia de caracteres|Sim|concluída<br /><br /> Abandon<br /><br /> suspenso|  
|tokens de bloqueio|matriz de UUID|Sim|Tokens de bloqueio de mensagem para atualizar o status de disposição.|  
|deadletter-reason|Cadeia de caracteres|Não|Poderá ser definido se o status de disposição estiver definido como **suspenso**.|  
|deadletter-description|Cadeia de caracteres|Não|Poderá ser definido se o status de disposição estiver definido como **suspenso**.|  
|Propriedades para modificar|map|Não|Lista de propriedades de mensagens orientadas do barramento de serviço a serem modificadas.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicativo:  
  
|Chave|Tipo de valor|Requerido|Conteúdo do valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – êxito; caso contrário, falha|  
|statusDescription|Cadeia de caracteres|Não|Descrição do status.|

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o AMQP e o barramento de serviço, visite os links a seguir:

* [Visão geral do AMQP do barramento de serviço]
* [Guia do protocolo AMQP 1.0]
* [AMQP no barramento de serviço para Windows Server]

[Visão geral do AMQP do barramento de serviço]: service-bus-amqp-overview.md
[Guia do protocolo AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP no barramento de serviço para Windows Server]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
