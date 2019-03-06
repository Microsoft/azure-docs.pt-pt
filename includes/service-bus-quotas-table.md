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
ms.openlocfilehash: 1123f16e11dd5e35f49e1435eef097f0d53f613b
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57350439"
---
A tabela seguinte lista as informações de quota específicas de mensagens do Service Bus. Para obter informações sobre preços e outras quotas do Service Bus, consulte a [preços do Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) descrição geral.

| Nome da quota | Âmbito | Notas | Value |
| --- | --- | --- | --- | --- |
| Número máximo de um espaço de nomes básico / standard por subscrição do Azure |Espaço de nomes |Pedidos subsequentes para espaços de nomes básicos / standard adicionais são rejeitados pelo portal. |100|
| Número máximo de espaços de nomes premium por subscrição do Azure |Espaço de nomes |Pedidos subsequentes para espaços de nomes premium adicionais são rejeitados pelo portal. |25 |
| Tamanho da fila/tópico |Entidade |Definido após a criação de fila/tópico. <br/><br/> Mensagens de entrada subsequentes são rejeitadas e uma exceção é recebida pelo código de chamada. |1, 2, 3, 4 GB ou 5 GB.<br /><br />O Premium SKU, bem como Standard com [particionamento](/azure/service-bus-messaging/service-bus-partitioning) ativada, o tamanho da fila/tópico máximo é de 80 GB. |
| Número de conexões simultâneas num espaço de nomes |Espaço de nomes |Pedidos subsequentes para ligações adicionais são rejeitados e uma exceção é recebida pelo código de chamada. Operações REST não são considerados conexões TCP simultâneas. |NetMessaging: 1,000<br /><br />AMQP: 5.000 |
| Número de simultâneas receber pedidos numa entidade de fila/tópico/subscrição |Entidade |Subsequentes receber pedidos são rejeitados e uma exceção é recebida pelo código de chamada. Esta quota aplica-se para o combinado diversas simultâneas receber operações em todas as subscrições num tópico. |5.000 |
| Número de filas/tópicos por espaço de nomes |Espaço de nomes |Os pedidos subsequentes para a criação de um novo tópico ou fila no espaço de nomes são rejeitados. Como resultado, se configurada por meio da [portal do Azure][Azure portal], uma mensagem de erro é gerada. Se chamado a partir da API de gestão, uma exceção é recebida pelo código de chamada. |1000 (escalão básico/Standard). O número total de tópicos e filas num espaço de nomes tem de ser menor ou igual a 1000. <br/><br/>Para o escalão premium, 1000 por unit(MU) mensagens. Limite máximo é de 4000. |
| Número de [particionados filas/tópicos](/azure/service-bus-messaging/service-bus-partitioning) por espaço de nomes |Espaço de nomes |Os pedidos subsequentes para a criação de um novo tópico particionado ou de uma fila no espaço de nomes são rejeitados. Como resultado, se configurada por meio da [portal do Azure][Azure portal], uma mensagem de erro é gerada. Se forem chamados da gestão de API, um **QuotaExceededException** exceção é recebida pelo código de chamada. |Escalões básico e Standard - 100<br/><br/>Entidades particionadas não são suportadas os [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) escalão.<br/><br />Cada particionada fila ou tópico conta para a quota de entidades de 1000 por espaço de nomes. |
| Tamanho máximo de qualquer caminho de entidade de mensagens: fila ou tópico |Entidade |- |260 carateres |
| Tamanho máximo de qualquer nome de entidade de mensagens: regra de espaço de nomes, subscrição ou subscrição |Entidade |- |50 carateres |
| Tamanho máximo de um [ID de mensagem](/dotnet/api/microsoft.azure.servicebus.message.messageid) | Entidade |- | 128 |
| Tamanho máximo de uma mensagem [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) | Entidade |- | 128 |
| Tamanho da mensagem para uma entidade de fila/tópico/subscrição |Entidade |Mensagens de entrada que excederem estes quotas são rejeitadas e uma exceção é recebida pelo código de chamada. |Tamanho máximo de mensagem: 256 KB ([escalão Standard](../articles/service-bus-messaging/service-bus-premium-messaging.md)) / 1 MB ([escalão Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md)). <br /><br />Devido à sobrecarga de sistema, este limite é menor do que esses valores.<br /><br />Tamanho máximo do cabeçalho: 64 KB<br /><br />Número máximo de propriedades de cabeçalho na matriz de propriedades: **bytes/int. MaxValue**<br /><br />Tamanho máximo de propriedade na matriz de propriedades: Sem limite explícito. Limitado pelo tamanho do cabeçalho máximo. |
| Tamanho de propriedade da mensagem para uma entidade de fila/tópico/subscrição |Entidade |R **SerializationException** exceção é gerada. |Tamanho de propriedade para cada propriedade da mensagem máximo é 32 k. o tamanho cumulativo de todas as propriedades não pode exceder os 64 k. Este limite aplica-se ao cabeçalho de todo a [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), que tem ambos as propriedades do utilizador, bem como as propriedades do sistema (tais como [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [etiqueta](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label), [ MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid)e assim por diante). |
| Número de subscrições por tópico |Entidade |Os pedidos subsequentes para a criação de subscrições adicionais para o tópico são rejeitados. Como resultado, se configurado através do portal, é apresentada uma mensagem de erro. Se forem chamados a partir da API de gestão uma exceção é recebida pelo código de chamada. |Escalão Standard - conta de cada subscrição para a quota de entidades de 1000 (filas, tópicos e subscrições) por espaço de nomes. <br/> <br/> Escalão Premium - 2.000 |
| Número de filtros SQL por tópico |Entidade |Os pedidos subsequentes para a criação de filtros adicionais sobre o tópico são rejeitados e uma exceção é recebida pelo código de chamada. |2.000 |
| Número de filtros de correlação por tópico |Entidade |Os pedidos subsequentes para a criação de filtros adicionais sobre o tópico são rejeitados e uma exceção é recebida pelo código de chamada. |100 000 |
| Tamanho de filtros/ações de SQL |Espaço de nomes |Os pedidos subsequentes para a criação de filtros adicionais são rejeitados e uma exceção é recebida pelo código de chamada. |Comprimento máximo da cadeia de condição de filtro: 1024 (1 K).<br /><br />Comprimento máximo da cadeia de caracteres de ação de regra: 1024 (1 K).<br /><br />Número máximo de expressões por ação de regra: 32. |
| Número de [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) regras por espaço de nomes, fila ou tópico |Entidade, o espaço de nomes |Os pedidos subsequentes para a criação de regras adicionais são rejeitados e uma exceção é recebida pelo código de chamada. |Número máximo de regras: 12. <br /><br /> Regras que estão configuradas num espaço de nomes do Service Bus são aplicadas a todas as filas e tópicos nesse espaço de nomes. |
| Número de mensagens por transação | Transação | Mensagens de entrada adicionais são rejeitadas e uma exceção informando "não é possível enviar mais de 100 mensagens numa única transação" é recebida pelo código de chamada. | 100 <br /><br /> Para ambos **Send()** e **SendAsync()** operações. |

[Azure portal]: https://portal.azure.com