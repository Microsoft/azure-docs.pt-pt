---
title: Azure Monitor consultas de log | Microsoft Docs
description: Referências a recursos para aprender a gravar consultas de log no Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2019
ms.openlocfilehash: 7c6530416f0eb6b822b6f5f3eb53f59aeae05d53
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894341"
---
# <a name="azure-monitor-log-queries"></a>Azure Monitor consultas de log
Azure Monitor logs são criados no Azure Data Explorer e Azure Monitor consultas de log usam uma versão da mesma linguagem de consulta Kusto. A [documentação da linguagem de consulta do Kusto](/azure/kusto/query) tem todos os detalhes para o idioma e deve ser o recurso principal para gravar Azure monitor consultas de log. Esta página fornece links para outros recursos para aprender a escrever consultas e sobre diferenças com a Azure Monitor implementação do idioma.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="getting-started"></a>Introdução

- [Introdução ao Azure Monitor log Analytics](get-started-portal.md) é uma lição para escrever consultas e trabalhar com resultados no portal do Azure.
- A [introdução às consultas de log do Azure monitor](get-started-queries.md) é uma lição para escrever consultas usando dados de log Azure monitor.

## <a name="concepts"></a>Conceitos
- [Analisar dados de log no Azure monitor](../../azure-monitor/log-query/log-query-overview.md) fornece uma breve visão geral das consultas de log e descreve como Azure monitor dados de log são estruturados.
- [Exibir e analisar dados de log no Azure monitor](../../azure-monitor/log-query/portals.md) explica os portais em que você cria e executa consultas de log.

## <a name="reference"></a>Referência

- [Referência de linguagem de consulta](/azure/kusto/query) é a referência de linguagem completa para a linguagem de consulta Kusto.
- As [diferenças de linguagem de consulta de log Azure monitor](data-explorer-difference.md) descreve as diferenças entre as versões da linguagem de consulta Kusto.
- [As propriedades padrão em registros de log Azure monitor](../../azure-monitor/platform/log-standard-properties.md) descrevem propriedades que são padrão para todos os Azure monitor dados de log.
- [Executar consultas de log entre recursos no Azure monitor](../../azure-monitor/log-query/cross-workspace-query.md) descreve como gravar consultas de log que usam dados de vários espaços de trabalho Log Analytics e Application insights aplicativos.


## <a name="examples"></a>Exemplos

- [Azure monitor exemplos de consulta de log](examples.md) fornece consultas de exemplo usando Azure monitor dados de log.



## <a name="lessons"></a>Lições

- [Trabalhar com cadeias de caracteres em consultas de log Azure monitor](string-operations.md) descreve como trabalhar com dados de cadeia de caracteres.
- [Trabalhar com valores de data e hora em Azure monitor consultas de log](datetime-operations.md) descreve como trabalhar com dados de data e hora. 
- As [agregações em consultas de log Azure monitor](aggregations.md) e [agregações avançadas em consultas de log de Azure monitor](advanced-aggregations.md) descrevem como agregar e resumir dados.
- [Junções em Azure monitor consultas de log](joins.md) descreve como unir dados de várias tabelas.
- [Trabalhar com estruturas de dados e JSON em Azure monitor consultas de log](json-data-structures.md) descreve como analisar dados JSON.
- [Escrever consultas de log avançadas no Azure monitor](advanced-query-writing.md) descreve estratégias para criar consultas complexas e reutilizar código.
- A [criação de gráficos e diagramas de consultas Azure monitor log](charts.md) descreve como Visualizar dados de uma consulta de log.

## <a name="cheatsheets"></a>Truques e dicas

-  O [SQL para Azure monitor consulta de log](sql-cheatsheet.md) ajuda os usuários que já estão familiarizados com o SQL.
-  [Splunk para Azure monitor consulta de log](splunk-cheatsheet.md) auxilia os usuários que já estão familiarizados com o Splunk.
 
## <a name="next-steps"></a>Passos seguintes

- Acesse a [documentação de referência completa para a linguagem de consulta Kusto](/azure/kusto/query/).
