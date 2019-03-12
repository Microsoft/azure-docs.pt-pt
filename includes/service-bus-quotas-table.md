---
title: incluir ficheiro
description: incluir ficheiro
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 12/13/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bb5dc4e01a872178af1bc7d5d57fc189424e7e9d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554098"
---
A tabela seguinte lista as informações de quota específicas de mensagens do Service bus do Azure. Para obter informações sobre preços e outras quotas do Service Bus, consulte [preços do Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

| Nome da quota | Âmbito | Notas | Value |
| --- | --- | --- | --- | --- |
| Número máximo de um espaço de nomes básico ou Standard por subscrição do Azure |Espaço de nomes |Pedidos subsequentes para espaços de nomes básico ou Standard adicionais são rejeitados pelo portal do Azure. |100|
| Número máximo de espaços de nomes Premium por subscrição do Azure |Espaço de nomes |Pedidos subsequentes para espaços de nomes Premium adicionais são rejeitados pelo portal. |25 |
| Tamanho da fila ou tópico |Entidade |Definido durante a criação da fila ou tópico. <br/><br/> Mensagens de entrada subsequentes são rejeitadas e, uma exceção é recebida pelo código de chamada. |1, 2, 3, 4 GB ou 5 GB.<br /><br />O SKU Premium e o SKU Standard com [particionamento](/azure/service-bus-messaging/service-bus-partitioning) ativada, o tamanho máximo da fila ou tópico é 80 GB. |
| Número de conexões simultâneas num espaço de nomes |Espaço de nomes |Pedidos subsequentes para ligações adicionais são rejeitados e, uma exceção é recebida pelo código de chamada. Operações REST não contam para conexões TCP simultâneas. |NetMessaging: 1,000.<br /><br />AMQP: 5,000. |
| Número de simultâneas receber pedidos numa entidade de fila, tópico ou subscrição |Entidade |Subsequentes receber pedidos são rejeitados e, uma exceção é recebida pelo código de chamada. Esta quota aplica-se para o combinado diversas simultâneas receber operações em todas as subscrições num tópico. |5,000 |
| Número de tópicos ou filas por espaço de nomes |Espaço de nomes |Os pedidos subsequentes para a criação de um novo tópico ou fila no espaço de nomes são rejeitados. Como resultado, se configurada por meio da [portal do Azure][Azure portal], uma mensagem de erro é gerada. Se chamado a partir da API de gestão, uma exceção é recebida pelo código de chamada. |1000 para o escalão básico ou Standard. O número total de tópicos e filas num espaço de nomes tem de ser menor ou igual a 1.000. <br/><br/>Para o escalão Premium, 1000 por unidade de mensagens (MU). Limite máximo é de 4.000. |
| Número de [particionados tópicos ou filas](/azure/service-bus-messaging/service-bus-partitioning) por espaço de nomes |Espaço de nomes |Os pedidos subsequentes para a criação de um novo tópico particionado ou de uma fila no espaço de nomes são rejeitados. Como resultado, se configurada por meio da [portal do Azure][Azure portal], uma mensagem de erro é gerada. Se chamado a partir da API de gestão, a exceção **QuotaExceededException** é recebida pelo código de chamada. |Básica e escalões Standard: 100.<br/><br/>Entidades particionadas não são suportadas no [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) escalão.<br/><br />Cada particionada fila ou tópico conta para a quota de 1.000 entidades por espaço de nomes. |
| Tamanho máximo de qualquer caminho de entidade de mensagens: fila ou tópico |Entidade |- |260 carateres. |
| Tamanho máximo de qualquer nome de entidade de mensagens: regra de espaço de nomes, subscrição ou subscrição |Entidade |- |50 carateres. |
| Tamanho máximo de um [ID de mensagem](/dotnet/api/microsoft.azure.servicebus.message.messageid) | Entidade |- | 128 |
| Tamanho máximo de uma mensagem [ID de sessão](/dotnet/api/microsoft.azure.servicebus.message.sessionid) | Entidade |- | 128 |
| Tamanho da mensagem para uma entidade de fila, tópico ou subscrição |Entidade |Mensagens de entrada que excederem estes quotas são rejeitadas e, uma exceção é recebida pelo código de chamada. |Tamanho máximo de mensagem: 256 KB para [escalão Standard](../articles/service-bus-messaging/service-bus-premium-messaging.md), 1 MB para [escalão Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />Devido à sobrecarga de sistema, este limite é menor do que esses valores.<br /><br />Tamanho máximo do cabeçalho: 64 KB.<br /><br />Número máximo de propriedades de cabeçalho na matriz de propriedades: **bytes/int. MaxValue**.<br /><br />Tamanho máximo de propriedade na matriz de propriedades: Sem limite explícito. Limitado pelo tamanho do cabeçalho máximo. |
| Tamanho de propriedade da mensagem para uma entidade de fila, tópico ou subscrição |Entidade | A exceção **SerializationException** é gerado. |Tamanho de propriedade da mensagem máximo para cada propriedade é 32.000. Tamanho cumulativo de todas as propriedades não pode ter mais de 64.000. Este limite aplica-se ao cabeçalho de todo a [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), que tem as propriedades do utilizador e as propriedades do sistema, como [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [etiqueta](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)e [ MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid). |
| Número de subscrições por tópico |Entidade |Os pedidos subsequentes para a criação de subscrições adicionais para o tópico são rejeitados. Como resultado, se configurado através do portal, é apresentada uma mensagem de erro. Se chamado a partir da API de gestão, uma exceção é recebida pelo código de chamada. |Escalão Standard: Cada subscrição é deduzida da quota de 1.000 entidades, ou seja, filas, tópicos e subscrições, por espaço de nomes. <br/> <br/> Escalão Premium: 2,000. |
| Número de filtros SQL por tópico |Entidade |Os pedidos subsequentes para a criação de filtros adicionais sobre o tópico são rejeitados e, uma exceção é recebida pelo código de chamada. |2.000 |
| Número de filtros de correlação por tópico |Entidade |Os pedidos subsequentes para a criação de filtros adicionais sobre o tópico são rejeitados e, uma exceção é recebida pelo código de chamada. |100.000 |
| Tamanho dos filtros SQL ou ações |Espaço de nomes |Os pedidos subsequentes para a criação de filtros adicionais são rejeitados e, uma exceção é recebida pelo código de chamada. |Comprimento máximo da cadeia de condição de filtro: 1.024 (1 K).<br /><br />Comprimento máximo da cadeia de caracteres de ação de regra: 1.024 (1 K).<br /><br />Número máximo de expressões por ação de regra: 32. |
| Número de [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) regras por espaço de nomes, fila ou tópico |Entidade, o espaço de nomes |Os pedidos subsequentes para a criação de regras adicionais são rejeitados e, uma exceção é recebida pelo código de chamada. |Número máximo de regras: 12. <br /><br /> Regras que estão configuradas num espaço de nomes do Service Bus são aplicadas a todas as filas e tópicos nesse espaço de nomes. |
| Número de mensagens por transação | Transação | Mensagens de entrada adicionais são rejeitadas e, uma exceção informando "não é possível enviar mais de 100 mensagens numa única transação" é recebida pelo código de chamada. | 100 <br /><br /> Para ambos **Send()** e **SendAsync()** operações. |

[Azure portal]: https://portal.azure.com