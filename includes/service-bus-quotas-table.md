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
ms.openlocfilehash: b19dc7a85fafa1a4d875c84db9bbefabb3cd5a7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651569"
---
A tabela seguinte lista informações de quota específicas para mensagens Azure Service Bus. Para obter informações sobre preços e outras quotas para o Service Bus, consulte [o preço do Ônibus de serviço.](https://azure.microsoft.com/pricing/details/service-bus/)

| Nome de quota | Âmbito | Notas | Valor |
| --- | --- | --- | --- |
| Número máximo de espaços de nome básico ou standard por subscrição do Azure |Espaço de nomes |Os pedidos subsequentes de espaços de nome sino ou standard adicionais são rejeitados pelo portal Azure. |100|
| Número máximo de espaços de nome Premium por subscrição do Azure |Espaço de nomes |Os pedidos subsequentes de espaços de nome premium adicionais são rejeitados pelo portal. |100 |
| Tamanho da fila ou tópico |Entidade |Definido sobre a criação da fila ou tópico. <br/><br/> As mensagens recebidas subsequentes são rejeitadas e uma exceção é recebida pelo código de chamada. |1, 2, 3, 4 GB ou 5 GB.<br /><br />No Premium SKU, e no SKU Standard com [partição](/azure/service-bus-messaging/service-bus-partitioning) ativada, a fila máxima ou o tamanho do tópico é de 80 GB. |
| Número de conexões simultâneas num espaço de nome |Espaço de nomes |Os pedidos subsequentes de ligações adicionais são rejeitados e uma exceção é recebida pelo código de chamada. As operações de REPOUSO não contam para ligações tcp simultâneas. |NetMessaging: 1.000.<br /><br />AMQP: 5.000. |
| Número de pedidos simultâneos recebem pedidos numa fila, tópico ou entidade de subscrição |Entidade |Os pedidos de receção subsequentes são rejeitados e uma exceção é recebida pelo código de chamada. Esta quota aplica-se ao número combinado de operações de receção simultâneas em todas as subscrições sobre um tópico. |5000 |
| Número de tópicos ou filas por espaço de nome |Espaço de nomes |Os pedidos subsequentes de criação de um novo tópico ou fila no espaço de nome são rejeitados. Como resultado, se configurado através do [portal Azure,][Azure portal]gera-se uma mensagem de erro. Se for chamado pela API de gestão, uma exceção é recebida pelo código de chamada. |10.000 para o nível Básico ou Standard. O número total de tópicos e filas num espaço de nome deve ser inferior ou igual a 10.000. <br/><br/>Para o nível Premium, 1.000 por unidade de mensagens (MU). Limite máximo é de 4.000. |
| Número de [tópicos ou filas divididos](/azure/service-bus-messaging/service-bus-partitioning) por espaço de nome |Espaço de nomes |Os pedidos subsequentes de criação de um novo tópico ou fila dividido no espaço de nome são rejeitados. Como resultado, se configurado através do [portal Azure,][Azure portal]gera-se uma mensagem de erro. Se for chamado da API de gestão, a exceção **QuotaExceededException** é recebida pelo código de chamada. |Níveis básicos e standard: 100.<br/><br/>As entidades divididas não são apoiadas no escalão [Premium.](../articles/service-bus-messaging/service-bus-premium-messaging.md)<br/><br />Cada fila ou tópico dividido conta para a quota de 1.000 entidades por espaço de nome. |
| Tamanho máximo de qualquer caminho de entidade de mensagens: fila ou tópico |Entidade |- |260 caracteres. |
| Tamanho máximo de qualquer nome de entidade de mensagens: espaço de nome, subscrição ou regra de subscrição |Entidade |- |50 caracteres. |
| Tamanho máximo de um ID de [mensagem](/dotnet/api/microsoft.azure.servicebus.message.messageid) | Entidade |- | 128 |
| Tamanho máximo de uma identificação de [sessão](/dotnet/api/microsoft.azure.servicebus.message.sessionid) de mensagem | Entidade |- | 128 |
| Tamanho da mensagem para uma fila, tópico ou entidade de subscrição |Entidade |As mensagens recebidas que excedam estas quotas são rejeitadas e uma exceção é recebida pelo código de chamada. |Tamanho máximo da mensagem: 256 KB para [o nível Standard](../articles/service-bus-messaging/service-bus-premium-messaging.md), 1 MB para [o nível Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />Devido à sobrecarga do sistema, este limite é inferior a estes valores.<br /><br />Tamanho máximo do cabeçalho: 64 KB.<br /><br />Número máximo de propriedades cabeçalho no saco de propriedade: **byte/int. MaxValue.**<br /><br />Tamanho máximo do imóvel em bolsa de propriedade: Sem limite explícito. Limitado pelo tamanho máximo do cabeçalho. |
| Tamanho da propriedade da mensagem para uma fila, tópico ou entidade de subscrição |Entidade | A exceção **SerializationException** é gerada. |O tamanho máximo da propriedade da mensagem para cada propriedade é de 32.000. O tamanho acumulado de todas as propriedades não pode exceder 64.000. Este limite aplica-se a todo o cabeçalho do [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), que tem propriedades do utilizador e propriedades do sistema, tais como [SequenceNumber,](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label), e [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid). |
| Número de assinaturas por tópico |Entidade |Os pedidos subsequentes de criação de subscrições adicionais para o tópico são rejeitados. Como resultado, se configurado através do portal, é mostrada uma mensagem de erro. Se for chamado pela API de gestão, uma exceção é recebida pelo código de chamada. |2.000 por tópico para o nível Standard. |
| Número de filtros SQL por tópico |Entidade |Os pedidos subsequentes de criação de filtros adicionais sobre o tema são rejeitados, e uma exceção é recebida pelo código de chamada. |2.000 |
| Número de filtros de correlação por tópico |Entidade |Os pedidos subsequentes de criação de filtros adicionais sobre o tema são rejeitados, e uma exceção é recebida pelo código de chamada. |100 000 |
| Tamanho dos filtros ou ações SQL |Espaço de nomes |Os pedidos subsequentes de criação de filtros adicionais são rejeitados e uma exceção é recebida pelo código de chamada. |Comprimento máximo da cadeia de condição do filtro: 1.024 (1 K).<br /><br />Comprimento máximo da corda de ação de regra: 1.024 (1 K).<br /><br />Número máximo de expressões por ação de regra: 32. |
| Número de regras [de Regras de Regras de Acesso partilhadopor](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) espaço de nome, fila ou tópico |Entidade, espaço de nome |Os pedidos subsequentes de criação de regras adicionais são rejeitados e uma exceção é recebida pelo código de chamada. |Número máximo de regras por entidade tipo: 12. <br /><br /> As regras configuradas num espaço de nome do Ônibus de serviço aplicam-se a todos os tipos: filas, tópicos. |
| Número de mensagens por transação | Transação | As mensagens recebidas adicionais são rejeitadas e uma exceção que diz "Não pode enviar mais de 100 mensagens numa única transação" é recebida pelo código de chamada. | 100 <br /><br /> Tanto para as operações **de envio como** de envio de **Envios.** |
| Número de regras de rede virtual e filtro IP | Espaço de nomes | &nbsp; | 128 | 

[Azure portal]: https://portal.azure.com
