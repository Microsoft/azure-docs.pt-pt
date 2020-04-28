---
title: Consultas de registo do Monitor Azure Microsoft Docs
description: Referências aos recursos para aprender a escrever consultas de registo no Monitor Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2019
ms.openlocfilehash: 6b81aba553fc775821c80631aa83bbb3e8ac63b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80631786"
---
# <a name="azure-monitor-log-queries"></a>Consultas de registo do Monitor Azure

Os registos do Azure Monitor são construídos no Azure Data Explorer, e as consultas de registo do Azure Monitor utilizam uma versão da mesma linguagem de consulta Kusto. A documentação da linguagem de [consulta kusto](/azure/kusto/query) tem todos os detalhes para a língua e deve ser o seu principal recurso para escrever consultas de registo do Monitor Azure. Esta página fornece links para outros recursos para aprender a escrever consultas e sobre diferenças com a implementação do Azure Monitor da língua.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>Introdução

- [Começar com o Azure Monitor Log Analytics](get-started-portal.md) é uma lição para escrever consultas e trabalhar com resultados no portal Azure.
- Começar com consultas de [registo do Monitor Azure](get-started-queries.md) é uma lição para escrever consultas usando dados de registo do Monitor Azure.

## <a name="concepts"></a>Conceitos

- [Analise os dados](../../azure-monitor/log-query/log-query-overview.md) de registo no Monitor Azure dá uma breve visão geral das consultas de registo e descreve como os dados de registo do Azure Monitor são estruturados.
- [Visualizar e analisar dados](../../azure-monitor/log-query/portals.md) de registo no Monitor Do Azure explica os portais onde cria e executa consultas de registo.

## <a name="reference"></a>Referência

- [A referência linguística](/azure/kusto/query) é a referência linguística completa para a linguagem de consulta kusto.
- As [diferenças linguísticas](data-explorer-difference.md) de consulta de registo do Azure Monitor descrevem diferenças entre versões da linguagem de consulta kusto.
- [As propriedades standard nos registos](../../azure-monitor/platform/log-standard-properties.md) de registo do Azure Monitor descrevem propriedades que são padrão para todos os dados de registo do Monitor Azure.
- [Realizar consultas de registo de recursos cruzados no Monitor Azure](../../azure-monitor/log-query/cross-workspace-query.md) descreve como escrever consultas de registo que utilizam dados de vários espaços de trabalho do Log Analytics e aplicações de Application Insights.

## <a name="examples"></a>Exemplos

- [Os exemplos](examples.md) de consulta de registo do Monitor Azure fornecem consultas de exemplo utilizando dados de registo do Monitor Azure.

## <a name="lessons"></a>Lições

- Trabalhar com cordas em consultas de [registo do Monitor Azure](string-operations.md) descreve como trabalhar com dados de cordas.
- [Trabalhar com os valores](datetime-operations.md) da data em consultas de registo do Monitor Azure descreve como trabalhar com dados de data e hora.
- [As agregações em consultas](aggregations.md) de registo do Monitor Azure e [agregações avançadas em consultas](advanced-aggregations.md) de registo do Monitor Azure descrevem como agregar e resumir dados.
- [Junta-se a consultas](joins.md) de registo do Monitor Azure descreve como juntar dados de várias tabelas.
- [Trabalhar com a JSON e as estruturas de dados em consultas](json-data-structures.md) de registo do Monitor Azure descreve como analisar dados de json.
- [Escrever consultas de registo avançadas no Azure Monitor](advanced-query-writing.md) descreve estratégias para criar consultas complexas e reutilizar código.
- [A criação de gráficos e diagramas a partir de consultas](charts.md) de registo do Monitor Azure descreve como visualizar dados a partir de uma consulta de registo.

## <a name="cheatsheets"></a>Truques e dicas

- A consulta de [log SQL para O Monitor Azure](sql-cheatsheet.md) ajuda os utilizadores que já estão familiarizados com o SQL.
- [Splunk para a consulta](splunk-cheatsheet.md) de log Do Monitor Azure ajuda os utilizadores que já estão familiarizados com o Splunk.

## <a name="next-steps"></a>Passos seguintes

- Aceda à documentação completa [de referência para a linguagem de consulta kusto](/azure/kusto/query/).
