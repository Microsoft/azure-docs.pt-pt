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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74795703"
---
| Limite | Descrição |
|:---|:---|
| Linguagem da consulta | O Azure Monitor usa a mesma [linguagem de consulta Kusto](/azure/kusto/query/) que o Azure Data Explorer. Consulte [as diferenças linguísticas](../articles/azure-monitor/log-query/data-explorer-difference.md) de consulta de registo do Azure Monitor para elementos linguísticos KQL não suportados no Monitor Azure. |
| Regiões do Azure | As consultas de registo podem experimentar sobrecargas excessivas quando os dados abrangem espaços de trabalho do Log Analytics em várias regiões do Azure. Consulte [os limites](../articles/azure-monitor/log-query/scope.md#query-limits) de consulta para mais detalhes. |
| Consultas de recursos cruzados | Número máximo de recursos de Application Insights e espaços de trabalho log Analytics numa única consulta limitada a 100.<br>A consulta de recursos cruzados não é suportada no View Designer.<br>A consulta de recursos cruzados em alertas de registo é suportada na nova API agendada.<br>Consulte [os limites de consulta de recursos cruzados](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) para obter detalhes. |
| Estrangulamento de consulta | Um utilizador está limitado a 200 consultas por 30 segundos em qualquer número de espaços de trabalho. Este limite aplica-se a consultas programáticas ou a consultas iniciadas por peças de visualização como os dashboards Azure e a página de resumo do espaço de trabalho Log Analytics. |
