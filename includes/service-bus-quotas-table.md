---
title: incluir ficheiro
description: incluir ficheiro
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/17/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ee066ff46f319749469a41e6decf12b35c0ee27e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100651961"
---
A tabela que se segue lista informações de quota específicas para as mensagens do Azure Service Bus. Para obter informações sobre preços e outras quotas para Service Bus, consulte [os preços do Service Bus.](https://azure.microsoft.com/pricing/details/service-bus/)

| Nome da quota | Âmbito | Notas | Valor |
| --- | --- | --- | --- |
| Número máximo de espaços de nome básico ou padrão por subscrição de Azure |Espaço de Nomes |Os pedidos subsequentes de espaços de nome básicos ou padrão adicionais são rejeitados pelo portal Azure. |100|
| Número máximo de espaços de nome Premium por subscrição de Azure |Espaço de Nomes |Os pedidos subsequentes de espaços de nome Premium adicionais são rejeitados pelo portal. |100 |
| Tamanho da fila ou tópico |Entidade |Definido na criação da fila ou tópico. <br/><br/> As mensagens recebidas subsequentes são rejeitadas e uma exceção é recebida pelo código de chamada. |1, 2, 3, 4 GB ou 5 GB.<br /><br />No SKU Premium e no SKU Standard com [partição](../articles/service-bus-messaging/service-bus-partitioning.md) ativada, a fila máxima ou o tamanho do tópico é de 80 GB. |
| Número de ligações simultâneas num espaço com nomes |Espaço de Nomes |Os pedidos subsequentes de ligações adicionais são rejeitados e uma exceção é recebida pelo código de chamada. As operações de REPOUSO não contam para ligações TCP simultâneas. |Mensagem Líquida: 1.000.<br /><br />AMQP: 5.000. |
| Número de pedidos simultâneos de receção em uma fila, tópico ou entidade de subscrição |Entidade |Os pedidos de receção subsequentes são rejeitados e uma exceção é recebida pelo código de chamada. Este contingente aplica-se ao número combinado de operações de receção simultâneas em todas as subscrições sobre um tópico. |5000 |
| Número de tópicos ou filas por espaço de nome |Espaço de Nomes |Os pedidos subsequentes para a criação de um novo tópico ou fila no espaço de nomes são rejeitados. Como resultado, se configurado através do [portal Azure,][Azure portal]é gerada uma mensagem de erro. Se for chamada da API de gestão, uma exceção é recebida pelo código de chamada. |10.000 para o nível Básico ou Padrão. O número total de tópicos e filas num espaço de nome deve ser inferior ou igual a 10.000. <br/><br/>Para o nível Premium, 1.000 por unidade de mensagens (MU). |
| Número de [tópicos divididos ou filas](../articles/service-bus-messaging/service-bus-partitioning.md) por espaço de nome |Espaço de Nomes |Os pedidos subsequentes para a criação de um novo tópico dividido ou fila no espaço de nome são rejeitados. Como resultado, se configurado através do [portal Azure,][Azure portal]é gerada uma mensagem de erro. Se for chamada da API de gestão, a exceção **QuotaExceedededException** é recebida pelo código de chamada. |Níveis básicos e standard: 100.<br/><br/>As entidades divididas não são apoiadas no nível [Premium.](../articles/service-bus-messaging/service-bus-premium-messaging.md)<br/><br />Cada fila ou tópico dividido conta para a quota de 1.000 entidades por espaço de nome. |
| Tamanho máximo de qualquer caminho da entidade de mensagens: fila ou tópico |Entidade |- |260 caracteres. |
| Tamanho máximo de qualquer nome de entidade de mensagens: namespace, subscrição ou regra de subscrição |Entidade |- |50 caracteres. |
| Tamanho máximo de uma iD de mensagem | Entidade |- | 128 |
| Tamanho máximo de uma iD de sessão de mensagem | Entidade |- | 128 |
| Tamanho da mensagem para uma fila, tópico ou entidade de subscrição |Entidade |As mensagens recebidas que excedam estas quotas são rejeitadas e uma exceção é recebida pelo código de chamada. |Tamanho máximo da mensagem: 256 KB para [nível Standard,](../articles/service-bus-messaging/service-bus-premium-messaging.md)1 MB para [nível Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />Devido à sobrecarga do sistema, este limite é inferior a estes valores.<br /><br />Tamanho máximo do cabeçalho: 64 KB.<br /><br />Número máximo de propriedades de cabeçalho no saco de propriedade: **byte/int. MaxValue.**<br /><br />Tamanho máximo da propriedade no saco de propriedade: Sem limite explícito. Limitado pelo tamanho máximo do cabeçalho. |
| Tamanho da propriedade da mensagem para uma fila, tópico ou entidade de subscrição |Entidade | A exceção `SerializationException` é gerada. |O tamanho máximo da propriedade de mensagem para cada propriedade é de 32.000. O tamanho acumulado de todas as propriedades não pode exceder 64.000. Este limite aplica-se a todo o cabeçalho da mensagem intermediada, que tem propriedades do utilizador e propriedades do sistema, tais como número de sequência, etiqueta e ID de mensagem. |
| Número de subscrições por tópico |Entidade |Os pedidos subsequentes de criação de subscrições adicionais para o tema são rejeitados. Como resultado, se configurado através do portal, é mostrada uma mensagem de erro. Se for chamada da API de gestão, uma exceção é recebida pelo código de chamada. |2.000 por tópico para o nível Standard e nível Premium. |
| Número de filtros SQL por tópico |Entidade |Os pedidos subsequentes de criação de filtros adicionais sobre o tema são rejeitados e uma exceção é recebida pelo código de chamada. |2.000 |
| Número de filtros de correlação por tópico |Entidade |Os pedidos subsequentes de criação de filtros adicionais sobre o tema são rejeitados e uma exceção é recebida pelo código de chamada. |100.000 |
| Tamanho dos filtros ou ações SQL |Espaço de Nomes |Os pedidos subsequentes de criação de filtros adicionais são rejeitados e uma exceção é recebida pelo código de chamada. |Comprimento máximo da cadeia de condição do filtro: 1.024 (1 K).<br /><br />Comprimento máximo do fio de ação da regra: 1.024 (1 K).<br /><br />Número máximo de expressões por ação de regra: 32. |
| Número de regras de autorização de acesso partilhado por espaço, fila ou tópico |Entidade, espaço de nome |Os pedidos subsequentes de criação de regras adicionais são rejeitados e uma exceção é recebida pelo código de chamada. |Número máximo de regras por tipo de entidade: 12. <br /><br /> As regras configuradas num espaço de nomes de Service Bus aplicam-se a todos os tipos: filas, tópicos. |
| Número de mensagens por transação | Transação | As mensagens recebidas adicionais são rejeitadas e uma exceção indicando "Não é possível enviar mais de 100 mensagens numa única transação" é recebida pelo código de chamada. | 100 <br /><br /> Para as operações **de Envio e** **SendAsync().** |
| Número de regras de rede virtual e filtro IP | Espaço de Nomes | &nbsp; | 128 | 

[Azure portal]: https://portal.azure.com
