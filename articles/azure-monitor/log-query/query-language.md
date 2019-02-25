---
title: Consultas de registo do Azure Monitor | Documentos da Microsoft
description: Referências a recursos para aprender a escrever consultas de registo no Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: bwren
ms.openlocfilehash: ecbea91cef69c80975dd105c35f526b357033a6b
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56749823"
---
# <a name="azure-monitor-log-queries"></a>Consultas de registo do Azure Monitor
Registos de Monitor do Azure são criados no Explorador de dados do Azure e consultas de registo do Azure Monitor de utilizar uma versão da linguagem de consulta de Kusto mesmo. O [documentação de linguagem de consulta de Kusto](/azure/kusto/query) tem todos os detalhes para o idioma e deve ser o principal recurso para escrever consultas de registo do Azure Monitor. Esta página fornece links para outros recursos para aprender a escrever consultas e nas diferenças com a implementação do Azure Monitor da linguagem.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>Introdução

- [Introdução ao Log Analytics do Azure Monitor](get-started-portal.md) é uma lição para escrever consultas e trabalhar com os resultados no portal do Azure.
- [Introdução às consultas de registo do Azure Monitor](get-started-queries.md) é algo para escrever consultas usando os dados de registo do Azure Monitor.

## <a name="concepts"></a>Conceitos
- [Analisar dados de registo no Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) fornece uma breve descrição geral do registo de consulta e descreve como os dados de registo do Azure Monitor são estruturados.
- [Visualizando e analisando dados de registo no Azure Monitor](../../azure-monitor/log-query/portals.md) explica os portais onde criar e executar consultas de registo.

## <a name="reference"></a>Referência

- [Referência de linguagem de consulta](/azure/kusto/query) é a referência de linguagem completa para a linguagem de consulta de Kusto.
- [Azure diferenças de linguagem de consulta do Monitor log](data-explorer-difference.md) descreve as diferenças entre as versões da linguagem de consulta de Kusto.
- [Registos de propriedades padrão no Azure Monitor](../../azure-monitor/platform/log-standard-properties.md) Descreve propriedades padrão para todos os dados de registo do Azure Monitor.
- [Executar consultas de registo entre recursos no Azure Monitor](../../azure-monitor/log-query/cross-workspace-query.md) descreve como escrever consultas de registo que utilizam dados de várias áreas de trabalho do Log Analytics e aplicações do Application Insights.


## <a name="examples"></a>Exemplos

- [Exemplos de consulta de registo do Azure Monitor](examples.md) fornece consultas de exemplo com dados de registo do Azure Monitor.



## <a name="lessons"></a>Lições

- [Trabalhar com cadeias de caracteres em consultas de registo do Azure Monitor](string-operations.md) descreve como trabalhar com dados de cadeia de caracteres.
- [Trabalhar com valores de hora da data em consultas de registo do Azure Monitor](datetime-operations.md) descreve como trabalhar com dados de data e hora. 
- [Consultas de registo de agregações no Azure Monitor](aggregations.md) e [Advanced agregações em consultas de registo do Azure Monitor](advanced-aggregations.md) descrevem como agregam e resumem os dados.
- [É associado a nas consultas de registo do Azure Monitor](joins.md) descreve como associar dados de várias tabelas.
- [Trabalhar com dados e de JSON estruturas em consultas de registo do Azure Monitor](json-data-structures.md) descreve como analisar dados json.
- [Escrever avançadas registar as consultas no Azure Monitor](advanced-query-writing.md) descreve estratégias para criar consultas complexas e reutilização de código.
- [Criação de gráficos e diagramas de consultas de registo do Azure Monitor](charts.md) descreve como visualizar dados de uma consulta de registo.

## <a name="cheatsheets"></a>Truques e dicas

-  [SQL para consultar de registo do Azure Monitor](sql-cheatsheet.md) auxilia os usuários que já estejam familiarizados com o SQL.
-  [Splunk à consulta de registo do Azure Monitor](sql-cheatsheet.md) auxilia os usuários que já estão familiarizados com Splunk.
 
## <a name="next-steps"></a>Passos Seguintes

- Aceder a completa [documentação para o idioma de consulta de Kusto de referência](/azure/kusto/query/).