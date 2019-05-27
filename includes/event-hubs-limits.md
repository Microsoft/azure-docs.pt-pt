---
title: incluir ficheiro
description: incluir ficheiro
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 02/26/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 38f7dd6eb1c4965eca003e5ba337ec5912a53420
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66148224"
---
A tabela seguinte apresenta uma lista de quotas e limites específica [os Hubs de eventos do Azure](https://azure.microsoft.com/services/event-hubs/). Para obter informações sobre os preços de Hubs de eventos, consulte [preços de Hubs de eventos](https://azure.microsoft.com/pricing/details/event-hubs/).

| Limite | Scope | Notas | Value |
| --- | --- | --- | --- |
| Número de espaços de nomes de Hubs de eventos por subscrição |Subscrição |- |100 |
| Número de hubs de eventos por espaço de nomes |Espaço de nomes |Os pedidos subsequentes para a criação de um novo hub de eventos são rejeitados. |10 |
| Número de partições por hub de eventos |Entidade |- |32 |
| Número de grupos de consumidores por hub de eventos |Entidade |- |20 |
| Número de ligações AMQP por espaço de nomes |Espaço de nomes |Pedidos subsequentes para ligações adicionais são rejeitados e, uma exceção é recebida pelo código de chamada. |5,000 |
| Tamanho máximo de eventos de Hubs de eventos|Entidade |- |1 MB |
| Tamanho máximo de um nome de hub de eventos |Entidade |- |50 carateres |
| Número de recetores de não "Epoch" por grupo de consumidores |Entidade |- |5 |
| Período máximo de retenção de dados de eventos |Entidade |- |1 a 7 dias |
| Unidades de débito máximas |Espaço de nomes |Excede o limite de unidade de débito faz com que seus dados para ser otimizados e gera um [exceção de servidor ocupado](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Para pedir um número maior de unidades de débito para o escalão Standard, de ficheiros uma [pedido de suporte](/azure/azure-supportability/how-to-create-azure-support-request). [Unidades de débito adicionais](../articles/event-hubs/event-hubs-auto-inflate.md) estão disponíveis em blocos de 20 de forma de compra. |20 |
| Número de regras de autorização por espaço de nomes |Espaço de nomes|Pedidos subsequentes para criação de regras de autorização são rejeitados.|12 |
| Número de chamadas para o método GetRuntimeInformation | Entidade | - | 50 por segundo | 
