---
title: Operações de pedido/resposta AMQP 1.0 no Ônibus de Serviço Azure
description: Este artigo define a lista de operações baseadas em pedidos/resposta seleções amqp no Microsoft Azure Service Bus.
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
ms.date: 01/23/2020
ms.author: aschhab
ms.openlocfilehash: 338b411eade35fa357f64131c64b8ef2034c92b0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76761088"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>AMQP 1.0 no Microsoft Azure Service Bus: operações baseadas em pedidos de resposta

Este artigo define a lista de operações de pedido/resposta do Microsoft Azure Service Bus. Esta informação baseia-se no projeto de funcionamento da VERSÃO 1.0 da AMQP Management Version 1.0.  
  
Para um guia de protocolo seletiva AMQP 1.0 detalhado, que explica como o Service Bus implementa e baseia-se na especificação técnica da AmQP AmQP, consulte o guia de [protocolo AMQP 1.0 da AMQP 1.0 no]guia de[protocolo amqp 1.0.]  
  
## <a name="concepts"></a>Conceitos  
  
### <a name="entity-description"></a>Descrição da entidade  

Uma descrição da entidade refere-se a uma classe Service Bus [QueueDescription,](/dotnet/api/microsoft.servicebus.messaging.queuedescription) [classe TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription), ou objeto de [classe SubscriçãoDescription.](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription)  
  
### <a name="brokered-message"></a>Mensagem intermediada  

Representa uma mensagem no Service Bus, que é mapeada para uma mensagem AMQP. O mapeamento é definido no guia de [protocolo saque tÁrpto](service-bus-amqp-protocol-guide.md)de autocarro de serviço .  
  
## <a name="attach-to-entity-management-node"></a>Anexar ao nó de gestão de entidades  

Todas as operações descritas neste documento seguem um padrão de pedido/resposta, são anexadas a uma entidade e exigem a fixação de um nó de gestão de entidades.  
  
### <a name="create-link-for-sending-requests"></a>Criar link para envio de pedidos  

Cria uma ligação ao nó de gestão para envio de pedidos.  
  
```
requestLink = session.attach(
role: SENDER,
    target: { address: "<entity address>/$management" },
    source: { address: ""<my request link unique address>" }
)

```
  
### <a name="create-link-for-receiving-responses"></a>Criar link para receber respostas  

Cria um link para receber respostas do nó de gestão.  
  
```
responseLink = session.attach(
role: RECEIVER,
    source: { address: "<entity address>/$management" }
    target: { address: "<my response link unique address>" }
)

```
  
### <a name="transfer-a-request-message"></a>Transfira uma mensagem de pedido  

Transfere uma mensagem de pedido.  
Um estado de transação pode ser adicionado opcionalmente para operações que suportem a transação.

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
  
### <a name="receive-a-response-message"></a>Receba uma mensagem de resposta  

Recebe a mensagem de resposta do link de resposta.  
  
```
responseMessage = responseLink.receiveTransfer()
```
  
A mensagem de resposta está na seguinte forma:
  
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
  
### <a name="service-bus-entity-address"></a>Endereço da entidade do ônibus de serviço  

As entidades de ônibus de serviço devem ser abordadas da seguinte forma:  
  
|Tipo de entidade|Endereço|Exemplo|  
|-----------------|-------------|-------------|  
|fila|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|tópico|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|subscrição|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Operações de mensagem  
  
### <a name="message-renew-lock"></a>Bloqueio de renovação de mensagem  

Estende o bloqueio de uma mensagem pelo tempo especificado na descrição da entidade.  
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|operação|string|Sim|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|Não|Operação tempo de funcionamento do servidor em milissegundos.|  
  
 O organismo de mensagem de pedido deve consistir numa secção de valor amqp contendo um mapa com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|matriz de uuid|Sim|Fichas de bloqueio de mensagem para renovar.|  

> [!NOTE]
> Fichas de bloqueio `DeliveryTag` são a propriedade em mensagens recebidas. Veja o seguinte exemplo no [.NET SDK](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) que os recupera. No entanto, o símbolo pode também figurar nas "Ações de Entrega" como "x-opt-lock-token", no entanto, isso não está garantido e o `DeliveryTag` deve ser preferido. 
> 
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|estadoCódigo|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – sucesso, caso contrário falhou.|  
|estadoDescrição|string|Não|Descrição do estado.|  
  
O organismo de mensagem de resposta deve consistir numa secção de valor amqp contendo um mapa com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|expirações|conjunto de carimbo supor|Sim|O bloqueio de mensagem ficha nova expiração correspondente às fichas de bloqueio de pedido.|  
  
### <a name="peek-message"></a>Mensagem peek  

Espreite mensagens sem trancar.  
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|operação|string|Sim|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Não|Operação tempo de funcionamento do servidor em milissegundos.|  
  
O organismo de mensagem de pedido deve consistir numa secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|long|Sim|Número de sequência a partir do qual começar a espreitar.|  
|`message-count`|int|Sim|Número máximo de mensagens para espreitar.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|estadoCódigo|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 204: Sem conteúdo – sem mais mensagens|  
|estadoDescrição|string|Não|Descrição do estado.|  
  
O organismo de mensagem de resposta deve consistir numa secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|mensagens|lista de mapas|Sim|Lista de mensagens em que cada mapa representa uma mensagem.|  
  
O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|message|conjunto de byte|Sim|Mensagem codificada por arame AMQP 1.0.|  
  
### <a name="schedule-message"></a>Mensagem de agenda  

Agenda mensagens. Esta operação suporta transações.
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|operação|string|Sim|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Não|Operação tempo de funcionamento do servidor em milissegundos.|  
  
O organismo de mensagem de pedido deve consistir numa secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|mensagens|lista de mapas|Sim|Lista de mensagens em que cada mapa representa uma mensagem.|  
  
O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|mensagem-id|string|Sim|`amqpMessage.Properties.MessageId`como corda|  
|sessão-id|string|Não|`amqpMessage.Properties.GroupId as string`|  
|chave de partição|string|Não|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|via-divisória-chave|string|Não|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|message|conjunto de byte|Sim|Mensagem codificada por arame AMQP 1.0.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|estadoCódigo|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – sucesso, caso contrário falhou.|  
|estadoDescrição|string|Não|Descrição do estado.|  
  
O organismo de mensagem de resposta deve consistir numa secção **de valor amqp** contendo um mapa com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|números de sequência|conjunto de longo|Sim|Número de sequência de mensagens programadas. O número da sequência é usado para cancelar.|  
  
### <a name="cancel-scheduled-message"></a>Cancelar mensagem agendada  

Cancela as mensagens programadas.  
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|operação|string|Sim|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|Não|Operação tempo de funcionamento do servidor em milissegundos.|  
  
O organismo de mensagem de pedido deve consistir numa secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|números de sequência|conjunto de longo|Sim|Números de sequência de mensagens programadas para cancelar.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|estadoCódigo|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – sucesso, caso contrário falhou.|  
|estadoDescrição|string|Não|Descrição do estado.|   
  
## <a name="session-operations"></a>Operações de Sessão  
  
### <a name="session-renew-lock"></a>Sessão Renovar Bloqueio  

Estende o bloqueio de uma mensagem pelo tempo especificado na descrição da entidade.  
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|operação|string|Sim|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Não|Operação tempo de funcionamento do servidor em milissegundos.|  
  
O organismo de mensagem de pedido deve consistir numa secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|sessão-id|string|Sim|Id da sessão.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|estadoCódigo|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 204: Sem conteúdo – sem mais mensagens|  
|estadoDescrição|string|Não|Descrição do estado.|  
  
O organismo de mensagem de resposta deve consistir numa secção **de valor amqp** contendo um mapa com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|expiração|carimbo de data/hora|Sim|Nova expiração.|  
  
### <a name="peek-session-message"></a>Mensagem de sessão de espreitar  

Espreite as mensagens da sessão sem trancar.  
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|operação|string|Sim|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Não|Operação tempo de funcionamento do servidor em milissegundos.|  
  
O organismo de mensagem de pedido deve consistir numa secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|a partir do número de sequência|long|Sim|Número de sequência a partir do qual começar a espreitar.|  
|contagem de mensagens|int|Sim|Número máximo de mensagens para espreitar.|  
|sessão-id|string|Sim|Id da sessão.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|estadoCódigo|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 204: Sem conteúdo – sem mais mensagens|  
|estadoDescrição|string|Não|Descrição do estado.|  
  
O organismo de mensagem de resposta deve consistir numa secção **de valor amqp** contendo um mapa com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|mensagens|lista de mapas|Sim|Lista de mensagens em que cada mapa representa uma mensagem.|  
  
 O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|message|conjunto de byte|Sim|Mensagem codificada por arame AMQP 1.0.|  
  
### <a name="set-session-state"></a>Estado de sessão de conjunto  

Define o estado de uma sessão.  
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|operação|string|Sim|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|Não|Operação tempo de funcionamento do servidor em milissegundos.|  
  
O organismo de mensagem de pedido deve consistir numa secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|sessão-id|string|Sim|Id da sessão.|  
|estado de sessão|conjunto de bytes|Sim|Dados binários opacos.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|estadoCódigo|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – sucesso, caso contrário falhou|  
|estadoDescrição|string|Não|Descrição do estado.|  
  
### <a name="get-session-state"></a>Obter Estado de Sessão  

Obtém o estado de uma sessão.  
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|operação|string|Sim|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Não|Operação tempo de funcionamento do servidor em milissegundos.|  
  
O organismo de mensagem de pedido deve consistir numa secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|sessão-id|string|Sim|Id da sessão.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|estadoCódigo|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – sucesso, caso contrário falhou|  
|estadoDescrição|string|Não|Descrição do estado.|  
  
O organismo de mensagem de resposta deve consistir numa secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|estado de sessão|conjunto de bytes|Sim|Dados binários opacos.|  
  
### <a name="enumerate-sessions"></a>Sessões enumeradas  

Enumera sessões numa entidade de mensagens.  
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|operação|string|Sim|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|Não|Operação tempo de funcionamento do servidor em milissegundos.|  
  
O organismo de mensagem de pedido deve consistir numa secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|última-atualização-tempo|carimbo de data/hora|Sim|Filtrar para incluir apenas sessões atualizadas após um dado tempo.|  
|saltar|int|Sim|Ignore uma série de sessões.|  
|superior|int|Sim|Número máximo de sessões.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|estadoCódigo|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 204: Sem conteúdo – sem mais mensagens|  
|estadoDescrição|string|Não|Descrição do estado.|  
  
O organismo de mensagem de resposta deve consistir numa secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|saltar|int|Sim|Número de sessões ignoradas se o código de estado for de 200.|  
|sessões-ids|matriz de cadeias (de carateres)|Sim|Variedade de iDs de sessão se o código de estado for de 200.|  
  
## <a name="rule-operations"></a>Operações de regras  
  
### <a name="add-rule"></a>Adicionar Regra  
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|operação|string|Sim|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Não|Operação tempo de funcionamento do servidor em milissegundos.|  
  
O organismo de mensagem de pedido deve consistir numa secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|nome de regras|string|Sim|Nome da regra, sem incluir nomes de subscrição e tópicos.|  
|descrição das regras|map|Sim|Descrição da regra especificada na secção seguinte.|  
  
O mapa **de descrição das regras** deve incluir as seguintes entradas, onde o filtro de **sql** e **o filtro de correlação** são mutuamente exclusivos:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|sql-filter|map|Sim|`sql-filter`, conforme especificado na secção seguinte.|  
|correlação-filtro|map|Sim|`correlation-filter`, conforme especificado na secção seguinte.|  
|sql-rule-action|map|Sim|`sql-rule-action`, conforme especificado na secção seguinte.|  
  
O mapa do filtro sql deve incluir as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|expression|string|Sim|Expressão do filtro sql.|  
  
O mapa **do filtro de correlação** deve incluir pelo menos uma das seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|correlation-id|string|Não||  
|mensagem-id|string|Não||  
|para|string|Não||  
|resposta-a|string|Não||  
|label|string|Não||  
|sessão-id|string|Não||  
|resposta-a-sessão-id|string|Não||  
|tipo de conteúdo|string|Não||  
|propriedades|map|Não|Mapas para serviço bus [brokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).|  
  
O mapa **de ação por regras sql** deve incluir as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|expression|string|Sim|Expressão de ação sql.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|estadoCódigo|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – sucesso, caso contrário falhou|  
|estadoDescrição|string|Não|Descrição do estado.|  
  
### <a name="remove-rule"></a>Regra de remoção  
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|operação|string|Sim|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Não|Operação tempo de funcionamento do servidor em milissegundos.|  
  
O organismo de mensagem de pedido deve consistir numa secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|nome de regras|string|Sim|Nome da regra, sem incluir nomes de subscrição e tópicos.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|estadoCódigo|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – sucesso, caso contrário falhou|  
|estadoDescrição|string|Não|Descrição do estado.|  
  
### <a name="get-rules"></a>Obter Regras

#### <a name="request"></a>Pedir

A mensagem de pedido deve incluir as seguintes propriedades de aplicação:

|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|operação|string|Sim|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|Não|Operação tempo de funcionamento do servidor em milissegundos.|  

O organismo de mensagem de pedido deve consistir numa secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|superior|int|Sim|O número de regras para ir buscar na página.|  
|saltar|int|Sim|O número de regras a saltar. Define o índice de partida (+1) na lista de regras. | 

#### <a name="response"></a>Resposta

A mensagem de resposta inclui as seguintes propriedades:

|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|estadoCódigo|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – sucesso, caso contrário falhou|  
|regras| conjunto de mapa|Sim|Várias regras. Cada regra é representada por um mapa.|

Cada entrada do mapa na matriz inclui as seguintes propriedades:

|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|descrição das regras|conjunto de objetos descritos|Sim|`com.microsoft:rule-description:list`com amqp descrito código 0x00000137000000000000004| 

`com.microsoft.rule-description:list`é uma variedade de objetos descritos. A matriz inclui o seguinte:

|Índice|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
| 0 | conjunto de objetos descritos | Sim | `filter`conforme especificado abaixo. |
| 1 | conjunto de objeto descrito | Sim | `ruleAction`conforme especificado abaixo. |
| 2 | string | Sim | nome da regra. |

`filter`pode ser de qualquer um dos seguintes tipos:

| Nome do Descritor | Código descritor | Valor |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000000006 | Filtro SQL |
| `com.microsoft:correlation-filter:list` | 0x0000013700000000009 | Filtro de correlação |
| `com.microsoft:true-filter:list` | 0x00000137000000000007 | Filtro verdadeiro que representa 1=1 |
| `com.microsoft:false-filter:list` | 0x000001370000000008 | Filtro falso que representa 1=0 |

`com.microsoft:sql-filter:list`é uma matriz descrita que inclui:

|Índice|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
| 0 | string | Sim | Expressão do filtro sql |

`com.microsoft:correlation-filter:list`é uma matriz descrita que inclui:

|Índice (se existir)|Tipo de Valor|Conteúdos de Valor|  
|---------|----------------|--------------|
| 0 | string | ID de Correlação |
| 1 | string | ID da mensagem |
| 2 | string | Para |
| 3 | string | Resposta a |
| 4 | string | Etiqueta |
| 5 | string | ID de sessão |
| 6 | string | Resposta ao ID da sessão|
| 7 | string | Tipo de Conteúdo |
| 8 | Mapa | Mapa das propriedades definidas da aplicação |

`ruleAction`podem ser um dos seguintes tipos:

| Nome do Descritor | Código descritor | Valor |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x00000137000000000000000005 | Ação de Regra Vazia - Nenhuma ação de regra presente |
| `com.microsoft:sql-rule-action:list` | 0x000001370000000000000006 | Ação de regra sql |

`com.microsoft:sql-rule-action:list`é uma variedade de objetos descritos cuja primeira entrada é uma cadeia que contém a expressão da ação da regra SQL.

## <a name="deferred-message-operations"></a>Operações de mensagens diferidas  
  
### <a name="receive-by-sequence-number"></a>Receber por número de sequência  

Recebe mensagens diferidas por número de sequência.  
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|operação|string|Sim|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Não|Operação tempo de funcionamento do servidor em milissegundos.|  
  
O organismo de mensagem de pedido deve consistir numa secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|números de sequência|conjunto de longo|Sim|Números de sequência.|  
|receptor-settle-mode|ubyte|Sim|**Modo de assentamento** do recetor conforme especificado no núcleo AMQP v1.0.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|estadoCódigo|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – sucesso, caso contrário falhou|  
|estadoDescrição|string|Não|Descrição do estado.|  
  
O organismo de mensagem de resposta deve consistir numa secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|mensagens|lista de mapas|Sim|Lista de mensagens onde cada mapa representa uma mensagem.|  
  
O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|lock-token|uuid|Sim|Bloqueie o `receiver-settle-mode` símbolo se for 1.|  
|message|conjunto de byte|Sim|Mensagem codificada por arame AMQP 1.0.|  
  
### <a name="update-disposition-status"></a>Atualizar estado de disposição  

Atualiza o estado de disposição das mensagens diferidas. Esta operação suporta transações.
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|operação|string|Sim|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Não|Operação tempo de funcionamento do servidor em milissegundos.|  
  
O organismo de mensagem de pedido deve consistir numa secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|estatuto de disposição|string|Sim|concluído<br /><br /> abandonado<br /><br /> suspenso|  
|fichas de bloqueio|matriz de uuid|Sim|Fichas de bloqueio de mensagem para atualizar o estado de disposição.|  
|deadletter-razão|string|Não|Pode ser definido se o estado de disposição estiver definido para **ser suspenso**.|  
|deadletter-descrição|string|Não|Pode ser definido se o estado de disposição estiver definido para **ser suspenso**.|  
|propriedades-a-modificar|map|Não|Lista de propriedades de mensagens mediadas service Bus para modificar.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades de aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdos de Valor|  
|---------|----------------|--------------|--------------------|  
|estadoCódigo|int|Sim|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – sucesso, caso contrário falhou|  
|estadoDescrição|string|Não|Descrição do estado.|

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre amqp e ônibus de serviço, visite os seguintes links:

* [Visão geral do ônibus de serviço AMQP]
* [Guia do protocolo AMQP 1.0]
* [AMQP em ônibus de serviço para servidor windows]

[Visão geral do ônibus de serviço AMQP]: service-bus-amqp-overview.md
[Guia do protocolo AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP em ônibus de serviço para servidor windows]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
