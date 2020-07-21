---
title: Consultas de registo do Azure Monitor Microsoft Docs
description: Referências a recursos para aprender a escrever consultas de registo no Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2019
ms.openlocfilehash: 1dda2df64dc116a950498aaf581ec39a86db72bb
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86505742"
---
# <a name="azure-monitor-log-queries"></a>Consultas de registo do Azure Monitor

Os registos do Azure Monitor são construídos no Azure Data Explorer, e as consultas de registo do Azure Monitor utilizam uma versão da mesma língua de consulta Kusto. A documentação linguística de [consulta de Kusto](/azure/kusto/query) tem todos os detalhes para o idioma e deve ser o seu principal recurso para escrever consultas de log do Azure Monitor. Esta página fornece links para outros recursos para aprender a escrever consultas e sobre diferenças com a implementação do Azure Monitor da língua.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>Introdução

- [Começar com o Azure Monitor Log Analytics](get-started-portal.md) é uma lição para escrever consultas e trabalhar com resultados no portal Azure.
- [Começar com as consultas de registo do Azure Monitor](get-started-queries.md) é uma lição para escrever consultas usando dados de registo do Azure Monitor.

## <a name="concepts"></a>Conceitos

- [Analisar os dados de registo no Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) dá uma breve visão geral das consultas de registo e descreve como os dados de registo do Azure Monitor são estruturados.
- [Visualizar e analisar dados de registo no Azure Monitor](./log-query-overview.md) explica os portais onde cria e executa consultas de registo.

## <a name="reference"></a>Referência

- [A referência linguística](/azure/kusto/query) de consulta é a referência linguística completa para a língua de consulta kusto.
- [As diferenças linguísticas de consulta de registo do Azure Monitor](data-explorer-difference.md) descrevem diferenças entre versões da língua de consulta de Kusto.
- [Propriedades padrão nos registos de registo do Monitor Azure](../../azure-monitor/platform/log-standard-properties.md) descrevem propriedades que são padrão para todos os dados de registo do Azure Monitor.
- [Realizar consultas de registo de recursos cruzados no Azure Monitor](../../azure-monitor/log-query/cross-workspace-query.md) descreve como escrever consultas de registo que utilizam dados de vários espaços de trabalho do Log Analytics e aplicações de Insights de Aplicações.

## <a name="examples"></a>Exemplos

- [Exemplos de consulta de registo do Azure Monitor](examples.md) fornecem consultas de exemplo usando dados de registo do Azure Monitor.

## <a name="lessons"></a>Lições

- [Trabalhar com cordas em consultas de log do Azure Monitor](string-operations.md) descreve como trabalhar com dados de cordas.
- [Trabalhar com valores de hora de data nas consultas de registo do Azure Monitor](datetime-operations.md) descreve como trabalhar com dados de data e hora.
- [Agregações em consultas de log do Azure Monitor](aggregations.md) e [agregações avançadas em consultas de registo do Monitor Azure](advanced-aggregations.md) descrevem como agregar e resumir dados.
- [As consultas de registo do Azure Monitor](joins.md) descrevem como juntar dados de várias tabelas.
- [Trabalhar com json e estruturas de dados em consultas de registo do Azure Monitor](json-data-structures.md) descreve como analisar dados de json.
- [Escrever consultas avançadas de log no Azure Monitor](advanced-query-writing.md) descreve estratégias para criar consultas complexas e reutilizar código.
- [A criação de gráficos e diagramas a partir de consultas de registo do Azure Monitor](charts.md) descreve como visualizar dados a partir de uma consulta de registo.

## <a name="cheatsheets"></a>Truques e dicas

- [A consulta de registo sql-Azure Monitor](sql-cheatsheet.md) ajuda os utilizadores que já estão familiarizados com o SQL.
- [A consulta de registo splunk para Azure Monitor](splunk-cheatsheet.md) ajuda os utilizadores que já estão familiarizados com o Splunk.

## <a name="next-steps"></a>Próximos passos

- Aceda à documentação completa [de referência para o idioma de consulta kusto.](/azure/kusto/query/)
