---
title: incluir ficheiro
description: incluir ficheiro
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: f2092753ab054a639e208aab4a6b7317c1bd5edb
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795703"
---
| Limite | Descrição |
|:---|:---|
| Linguagem de consulta | Azure Monitor usa a mesma [linguagem de consulta Kusto](/azure/kusto/query/) que o data Explorer do Azure. Veja [Azure monitor diferenças de linguagem de consulta de log](../articles/azure-monitor/log-query/data-explorer-difference.md) para elementos de linguagem KQL sem suporte no Azure monitor. |
| Regiões do Azure | As consultas de log podem enfrentar sobrecarga excessiva quando os dados abrangem espaços de trabalho Log Analytics em várias regiões do Azure. Consulte [os limites de consulta](../articles/azure-monitor/log-query/scope.md#query-limits) para obter detalhes. |
| Consultas entre recursos | Número máximo de recursos de Application Insights e espaços de trabalho de Log Analytics em uma única consulta limitada a 100.<br>Não há suporte para a consulta entre recursos no designer de exibição.<br>Há suporte para a consulta entre recursos em alertas de log na nova API do scheduledQueryRules.<br>Consulte [limites de consulta entre recursos](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) para obter detalhes. |
| Limitação de consulta | Um usuário é limitado a 200 consultas por 30 segundos em qualquer número de espaços de trabalho. Esse limite se aplica a consultas programáticas ou a consultas iniciadas por partes de visualização, como painéis do Azure e a página de resumo do Log Analytics espaço de trabalho. |
