---
title: AmQP 1.0 operações de pedido/resposta no Azure Service Bus
description: Este artigo define a lista de operações baseadas em pedidos de AMQP/resposta no Microsoft Azure Service Bus.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: b845f4086ee1ac4fe868571c1754caf6d29b9021
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88064421"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>AMQP 1.0 no Microsoft Azure Service Bus: operações baseadas em pedidos

Este artigo define a lista de pedidos de autocarros de serviço da Microsoft Azure. Esta informação baseia-se no projeto de trabalho da Versão 1.0 da AMQP Management.  
  
Para obter um guia de protocolo de nível de fio detalhado AMQP 1.0, que explica como o Service Bus implementa e baseia-se na especificação técnica OASIS AMQP, consulte o guia de [protocolo AMQP 1.0 da Azure Service Bus and Event Hubs][AMQP 1.0.]  
  
## <a name="concepts"></a>Conceitos  
  
### <a name="entity-description"></a>Descrição da entidade  

Uma descrição da entidade refere-se a uma classe de fila de [autocarros](/dotnet/api/microsoft.servicebus.messaging.queuedescription)de serviço, [classe Descrição de Tópicos](/dotnet/api/microsoft.servicebus.messaging.topicdescription)ou objeto [da classe Subscrição.](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription)  
  
### <a name="brokered-message"></a>Mensagem intermediada  

Representa uma mensagem no Service Bus, que está mapeada para uma mensagem AMQP. O mapeamento é definido no guia de [protocolo AMQP do Service Bus.](service-bus-amqp-protocol-guide.md)  
  
## <a name="attach-to-entity-management-node"></a>Anexar ao nó de gestão de entidades  

Todas as operações descritas neste documento seguem um padrão de pedido/resposta, são a nível de uma entidade, e exigem a anexação a um nó de gestão de entidades.  
  
### <a name="create-link-for-sending-requests"></a>Criar link para envio de pedidos  

Cria um link para o nó de gestão para envio de pedidos.  
  
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
  
### <a name="transfer-a-request-message"></a>Transferir uma mensagem de pedido  

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
  
### <a name="receive-a-response-message"></a>Receber uma mensagem de resposta  

Recebe a mensagem de resposta a partir do link de resposta.  
  
```
responseMessage = responseLink.receiveTransfer()
```
  
A mensagem de resposta é da seguinte forma:
  
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
  
### <a name="service-bus-entity-address"></a>Endereço da entidade de ônibus de serviço  

As entidades de serviço bus devem ser endereçadas da seguinte forma:  
  
|Tipo de entidade|Endereço|Exemplo|  
|-----------------|-------------|-------------|  
|fila|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|tópico|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|subscrição|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Operações de mensagens  
  
### <a name="message-renew-lock"></a>Bloqueio de renovação de mensagens  

Estende o bloqueio de uma mensagem pelo tempo especificado na descrição da entidade.  
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Yes|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|No|Operação tempo limite do servidor em milissegundos.|  
  
 O organismo de mensagem de pedido deve ser constituído por uma secção de valor amqp contendo um mapa com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|matriz de uuid|Yes|Fichas de bloqueio de mensagem para renovar.|  

> [!NOTE]
> As fichas de bloqueio são a `DeliveryTag` propriedade em mensagens recebidas. Veja o seguinte exemplo no [.NET SDK](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) que os recupera. O token também pode aparecer nas "DeliveryAnnotations" como 'x-opt-lock-token' no entanto, este não está garantido e deve `DeliveryTag` ser preferido. 
> 
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – sucesso, caso contrário falhou.|  
|estadoDescrição|cadeia (de carateres)|No|Descrição do estado.|  
  
O organismo de mensagem de resposta deve ser constituído por uma secção de valor amqp contendo um mapa com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|expirações|matriz de timetamp|Yes|Bloqueio de mensagem novo expiração correspondente às fichas de bloqueio de pedido.|  
  
### <a name="peek-message"></a>Mensagem de espreitar  

Espreita mensagens sem trancar.  
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Yes|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|No|Operação tempo limite do servidor em milissegundos.|  
  
O organismo de mensagem de pedido deve ser constituído por uma secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|long|Yes|Número de sequência a partir do qual começar a espreitar.|  
|`message-count`|int|Yes|Número máximo de mensagens para espreitar.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 204: Sem conteúdo – sem mais mensagens|  
|estadoDescrição|cadeia (de carateres)|No|Descrição do estado.|  
  
O organismo de mensagem de resposta deve ser constituído por uma secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|mensagens|lista de mapas|Yes|Lista de mensagens em que cada mapa representa uma mensagem.|  
  
O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|message|matriz de byte|Yes|Mensagem codificada por fios AMQP 1.0.|  
  
### <a name="schedule-message"></a>Agendar mensagem  

Agenda mensagens. Esta operação suporta transações.
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Yes|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|No|Operação tempo limite do servidor em milissegundos.|  
  
O organismo de mensagem de pedido deve ser constituído por uma secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|mensagens|lista de mapas|Yes|Lista de mensagens em que cada mapa representa uma mensagem.|  
  
O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|mensagem id|string|Yes|`amqpMessage.Properties.MessageId` como corda|  
|sessão id|cadeia (de carateres)|No|`amqpMessage.Properties.GroupId as string`|  
|chave de partição|cadeia (de carateres)|No|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|via-partição-chave|cadeia (de carateres)|No|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|message|matriz de byte|Yes|Mensagem codificada por fios AMQP 1.0.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – sucesso, caso contrário falhou.|  
|estadoDescrição|cadeia (de carateres)|No|Descrição do estado.|  
  
O organismo de mensagem de resposta deve ser constituído por uma secção **de valor amqp** contendo um mapa com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|sequência-números|matriz de longo|Yes|Número de sequência de mensagens programadas. O número da sequência é usado para cancelar.|  
  
### <a name="cancel-scheduled-message"></a>Cancelar mensagem agendada  

Cancela as mensagens programadas.  
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Yes|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|No|Operação tempo limite do servidor em milissegundos.|  
  
O organismo de mensagem de pedido deve ser constituído por uma secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|sequência-números|matriz de longo|Yes|Sequência de números de mensagens programadas para cancelar.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – sucesso, caso contrário falhou.|  
|estadoDescrição|cadeia (de carateres)|No|Descrição do estado.|   
  
## <a name="session-operations"></a>Operações de Sessão  
  
### <a name="session-renew-lock"></a>Bloqueio de renovação de sessão  

Estende o bloqueio de uma mensagem pelo tempo especificado na descrição da entidade.  
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Yes|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|No|Operação tempo limite do servidor em milissegundos.|  
  
O organismo de mensagem de pedido deve ser constituído por uma secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|sessão id|string|Yes|Identificação da sessão.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 204: Sem conteúdo – sem mais mensagens|  
|estadoDescrição|cadeia (de carateres)|No|Descrição do estado.|  
  
O organismo de mensagem de resposta deve ser constituído por uma secção **de valor amqp** contendo um mapa com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|expiração|carimbo de data/hora|Yes|Nova expiração.|  
  
### <a name="peek-session-message"></a>Mensagem de sessão de espreitar  

Espreita mensagens de sessão sem bloquear.  
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Yes|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|No|Operação tempo limite do servidor em milissegundos.|  
  
O organismo de mensagem de pedido deve ser constituído por uma secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|a partir de sequência-número|long|Yes|Número de sequência a partir do qual começar a espreitar.|  
|contagem de mensagens|int|Yes|Número máximo de mensagens para espreitar.|  
|sessão id|string|Yes|Identificação da sessão.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 204: Sem conteúdo – sem mais mensagens|  
|estadoDescrição|cadeia (de carateres)|No|Descrição do estado.|  
  
O organismo de mensagem de resposta deve ser constituído por uma secção **de valor amqp** contendo um mapa com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|mensagens|lista de mapas|Yes|Lista de mensagens em que cada mapa representa uma mensagem.|  
  
 O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|message|matriz de byte|Yes|Mensagem codificada por fios AMQP 1.0.|  
  
### <a name="set-session-state"></a>Estado da sessão definida  

Define o estado de uma sessão.  
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Yes|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|No|Operação tempo limite do servidor em milissegundos.|  
  
O organismo de mensagem de pedido deve ser constituído por uma secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|sessão id|string|Yes|Identificação da sessão.|  
|sessão-estado|matriz de bytes|Yes|Dados binários opacos.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – sucesso, caso contrário falhou|  
|estadoDescrição|cadeia (de carateres)|No|Descrição do estado.|  
  
### <a name="get-session-state"></a>Obter Estado de Sessão  

Fica com o estado de uma sessão.  
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Yes|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|No|Operação tempo limite do servidor em milissegundos.|  
  
O organismo de mensagem de pedido deve ser constituído por uma secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|sessão id|string|Yes|Identificação da sessão.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – sucesso, caso contrário falhou|  
|estadoDescrição|cadeia (de carateres)|No|Descrição do estado.|  
  
O organismo de mensagem de resposta deve ser constituído por uma secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|sessão-estado|matriz de bytes|Yes|Dados binários opacos.|  
  
### <a name="enumerate-sessions"></a>Sessões enumeras  

Enumera sessões sobre uma entidade de mensagens.  
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Yes|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|No|Operação tempo limite do servidor em milissegundos.|  
  
O organismo de mensagem de pedido deve ser constituído por uma secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|última atualização da hora|carimbo de data/hora|Yes|Filtrar para incluir apenas sessões atualizadas após um determinado tempo.|  
|saltar|int|Yes|Ignore várias sessões.|  
|top|int|Yes|Número máximo de sessões.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – tem mais mensagens<br /><br /> 204: Sem conteúdo – sem mais mensagens|  
|estadoDescrição|cadeia (de carateres)|No|Descrição do estado.|  
  
O organismo de mensagem de resposta deve ser constituído por uma secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|saltar|int|Yes|Número de sessões ignoradas se o código de estado for 200.|  
|sessões ids|matriz de cadeias (de carateres)|Yes|Conjunto de identificações de sessão se o código de estado for 200.|  
  
## <a name="rule-operations"></a>Operações de regra  
  
### <a name="add-rule"></a>Adicionar Regra  
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Yes|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|No|Operação tempo limite do servidor em milissegundos.|  
  
O organismo de mensagem de pedido deve ser constituído por uma secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|regra-nome|string|Yes|Nome da regra, sem incluir nomes de subscrição e tópicos.|  
|regra-descrição|mapa|Yes|Descrição da regra, conforme especificado na secção seguinte.|  
  
O mapa **de descrição das regras** deve incluir as seguintes entradas, em que **o filtro sql** e **o filtro de correlação** são mutuamente exclusivos:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|sql-filter|mapa|Yes|`sql-filter`, conforme especificado na secção seguinte.|  
|filtro de correlação|mapa|Yes|`correlation-filter`, conforme especificado na secção seguinte.|  
|sql-regra-ação|mapa|Yes|`sql-rule-action`, conforme especificado na secção seguinte.|  
  
O mapa do filtro sql deve incluir as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|expression|string|Yes|Expressão de filtro sql.|  
  
O mapa **do filtro de correlação** deve incluir pelo menos uma das seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|correlation-id|cadeia (de carateres)|No||  
|mensagem id|cadeia (de carateres)|No||  
|para|cadeia (de carateres)|No||  
|resposta a|cadeia (de carateres)|No||  
|etiqueta|cadeia (de carateres)|No||  
|sessão id|cadeia (de carateres)|No||  
|resposta-a-sessão id|cadeia (de carateres)|No||  
|tipo de conteúdo|cadeia (de carateres)|No||  
|propriedades|mapa|No|Mapas para Service Bus [BrokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).|  
  
O mapa **de ação com regras sql** deve incluir as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|expression|string|Yes|Expressão de ação sql.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – sucesso, caso contrário falhou|  
|estadoDescrição|cadeia (de carateres)|No|Descrição do estado.|  
  
### <a name="remove-rule"></a>Remover Regra  
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Yes|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|No|Operação tempo limite do servidor em milissegundos.|  
  
O organismo de mensagem de pedido deve ser constituído por uma secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|regra-nome|string|Yes|Nome da regra, sem incluir nomes de subscrição e tópicos.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – sucesso, caso contrário falhou|  
|estadoDescrição|cadeia (de carateres)|No|Descrição do estado.|  
  
### <a name="get-rules"></a>Obter Regras

#### <a name="request"></a>Pedir

A mensagem de pedido deve incluir as seguintes propriedades da aplicação:

|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Yes|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|No|Operação tempo limite do servidor em milissegundos.|  

O organismo de mensagem de pedido deve ser constituído por uma secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|top|int|Yes|O número de regras para ir buscar na página.|  
|saltar|int|Yes|O número de regras a ignorar. Define o índice inicial (+1) na lista de regras. | 

#### <a name="response"></a>Resposta

A mensagem de resposta inclui as seguintes propriedades:

|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – sucesso, caso contrário falhou|  
|regras| matriz de mapa|Yes|Conjunto de regras. Cada regra é representada por um mapa.|

Cada entrada de mapa na matriz inclui as seguintes propriedades:

|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|regra-descrição|matriz de objetos descritos|Yes|`com.microsoft:rule-description:list` com AMQP descreveu código 0x0000013700000004| 

`com.microsoft.rule-description:list` é uma matriz de objetos descritos. A matriz inclui o seguinte:

|Índice|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
| 0 | matriz de objetos descritos | Yes | `filter` conforme especificado abaixo. |
| 1 | matriz de objeto descrito | Yes | `ruleAction` conforme especificado abaixo. |
| 2 | string | Yes | nome da regra. |

`filter` pode ser de qualquer um dos seguintes tipos:

| Nome do descritor | Código de descritor | Valor |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | Filtro SQL |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | Filtro de correlação |
| `com.microsoft:true-filter:list` | 0x000001370000007 | Filtro verdadeiro representando 1=1 |
| `com.microsoft:false-filter:list` | 0x000001370000008 | Filtro falso representando 1=0 |

`com.microsoft:sql-filter:list` é uma matriz descrita que inclui:

|Índice|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
| 0 | string | Yes | Expressão do filtro sql |

`com.microsoft:correlation-filter:list` é uma matriz descrita que inclui:

|Índice (se existir)|Tipo de Valor|Conteúdo de valor|  
|---------|----------------|--------------|
| 0 | string | ID de Correlação |
| 1 | string | ID de mensagem |
| 2 | string | Para |
| 3 | string | Resposta a |
| 4 | string | Etiqueta |
| 5 | string | ID de sessão |
| 6 | string | Resposta ao ID da sessão|
| 7 | string | Tipo de Conteúdo |
| 8 | Mapa | Mapa das propriedades definidas pela aplicação |

`ruleAction` pode ser um dos seguintes tipos:

| Nome do descritor | Código de descritor | Valor |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | Empty Rule Action - Nenhuma ação de regras presente |
| `com.microsoft:sql-rule-action:list` | 0x0000013700000006 | Ação de Regras SQL |

`com.microsoft:sql-rule-action:list` é uma matriz de objetos descritos cuja primeira entrada é uma cadeia que contém a expressão da regra SQL.

## <a name="deferred-message-operations"></a>Operações de mensagens diferidas  
  
### <a name="receive-by-sequence-number"></a>Receber por número de sequência  

Recebe mensagens diferidas por número de sequência.  
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Yes|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|No|Operação tempo limite do servidor em milissegundos.|  
  
O organismo de mensagem de pedido deve ser constituído por uma secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|sequência-números|matriz de longo|Yes|Números de sequência.|  
|modo de liquidação receptor-settle|ubyte|Yes|**Modo de liquidação do recetor,** conforme especificado no núcleo amqp v1.0.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – sucesso, caso contrário falhou|  
|estadoDescrição|cadeia (de carateres)|No|Descrição do estado.|  
  
O organismo de mensagem de resposta deve ser constituído por uma secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|mensagens|lista de mapas|Yes|Lista de mensagens onde cada mapa representa uma mensagem.|  
  
O mapa que representa uma mensagem deve conter as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|símbolo de bloqueio|uuid|Yes|Sinal de fechadura se `receiver-settle-mode` for 1.|  
|message|matriz de byte|Yes|Mensagem codificada por fios AMQP 1.0.|  
  
### <a name="update-disposition-status"></a>Atualizar o estado de disposição  

Atualiza o estado de disposição das mensagens diferidas. Esta operação suporta transações.
  
#### <a name="request"></a>Pedir  

A mensagem de pedido deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|operation|string|Yes|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|No|Operação tempo limite do servidor em milissegundos.|  
  
O organismo de mensagem de pedido deve ser constituído por uma secção **de valor amqp** contendo um **mapa** com as seguintes entradas:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|estado de disposição|string|Yes|concluído<br /><br /> abandonado<br /><br /> suspenso|  
|fichas de bloqueio|matriz de uuid|Yes|Fichas de bloqueio de mensagem para atualizar o estado de disposição.|  
|deadletter-reason|cadeia (de carateres)|No|Pode ser definido se o estado de disposição for definido para **ser suspenso**.|  
|deadletter descrição|cadeia (de carateres)|No|Pode ser definido se o estado de disposição for definido para **ser suspenso**.|  
|propriedades para modificar|mapa|No|Lista de propriedades de mensagens intermediadas do Service Bus para modificar.|  
  
#### <a name="response"></a>Resposta  

A mensagem de resposta deve incluir as seguintes propriedades da aplicação:  
  
|Chave|Tipo de Valor|Necessário|Conteúdo de valor|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Código de resposta HTTP [RFC2616]<br /><br /> 200: OK – sucesso, caso contrário falhou|  
|estadoDescrição|cadeia (de carateres)|No|Descrição do estado.|

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre AMQP e Service Bus, visite os seguintes links:

* [Visão geral do AMQP do autocarro de serviço]
* [Guia do protocolo AMQP 1.0]
* [AMQP no ônibus de serviço para windows server]

[Visão geral do AMQP do autocarro de serviço]: service-bus-amqp-overview.md
[Guia do protocolo AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP no ônibus de serviço para windows server]: /previous-versions/service-bus-archive/dn282144(v=azure.100)