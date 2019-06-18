---
title: incluir ficheiro
description: incluir ficheiro
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: fa6b4d6d0db09f8c4955430d6dc227356416d915
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66736023"
---
A tabela seguinte apresenta uma lista de quotas e limites específica [os Hubs de eventos do Azure](https://azure.microsoft.com/services/event-hubs/). Para obter informações sobre os preços de Hubs de eventos, consulte [preços de Hubs de eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

| Limite | Scope | Notas | Value |
| --- | --- | --- | --- |
| Número de espaços de nomes de Hubs de eventos por subscrição |Subscrição |- |100 |
| Número de hubs de eventos por espaço de nomes |Espaço de Nomes |Os pedidos subsequentes para a criação de um novo hub de eventos são rejeitados. |10 |
| Número de partições por hub de eventos |Entidade |- |32 |
| Número de grupos de consumidores por hub de eventos |Entidade |- |20 |
| Número de ligações AMQP por espaço de nomes |Espaço de Nomes |Pedidos subsequentes para ligações adicionais são rejeitados e, uma exceção é recebida pelo código de chamada. |5,000 |
| Tamanho máximo de eventos de Hubs de eventos|Entidade |- |1 MB |
| Tamanho máximo de um nome de hub de eventos |Entidade |- |50 carateres |
| Número de recetores de não "Epoch" por grupo de consumidores |Entidade |- |5 |
| Período máximo de retenção de dados de eventos |Entidade |- |1 a 7 dias |
| Unidades de débito máximas |Espaço de Nomes |Excede o limite de unidade de débito faz com que seus dados para ser otimizados e gera um [exceção de servidor ocupado](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Para pedir um número maior de unidades de débito para o escalão Standard, de ficheiros uma [pedido de suporte](/azure/azure-supportability/how-to-create-azure-support-request). [Unidades de débito adicionais](../articles/event-hubs/event-hubs-auto-inflate.md) estão disponíveis em blocos de 20 de forma de compra. |20 |
| Número de regras de autorização por espaço de nomes |Espaço de Nomes|Pedidos subsequentes para criação de regras de autorização são rejeitados.|12 |
| Número de chamadas para o método GetRuntimeInformation | Entidade | - | 50 por segundo | 

### <a name="event-hubs-dedicated---quotas-and-limits"></a>Dedicado de Hubs de eventos - quotas e limites
A oferta de Hubs de eventos dedicados é faturada por um preço mensal fixo, com um mínimo de 4 horas de utilização. O escalão dedicado oferece todas as funcionalidades do plano padrão, mas com capacidade de dimensionamento empresarial e limites para os clientes com cargas de trabalho exigentes. 

| Funcionalidade | Limits |
| --- | ---|
| Largura de banda |  20 CUs |
| Espaços de nomes | 50 por CU |
| Hubs de Eventos |  1000 por espaço de nomes |
| Eventos de entrada | Incluída |
| Tamanho da Mensagem | 1 milhão de Bytes |
| Partições | 2000 por CU |
| Grupos de consumidores | Sem limite por CU, 1000 por hub de eventos |
| Ligações mediadas | 100 mil incluídos |
| Retenção de Mensagens | Se a 7 dias (90 dias de retenção em breve), 10 TB incluídos por CU |
| Captura | Incluída |
