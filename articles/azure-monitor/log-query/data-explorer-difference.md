---
title: Azure diferenças de linguagem de consulta do Monitor log | Documentos da Microsoft
description: Informações de referência de linguagem de consulta de Kusto utilizada pelo Azure Monitor. Inclui elementos adicionais específicos para o Azure Monitor e elementos que não é suportados em consultas de registo do Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: bwren
ms.openlocfilehash: 1185f3f96fd39f168d138d7dbf66e7780884b1fa
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60589265"
---
# <a name="azure-monitor-log-query-language-differences"></a>Azure diferenças de linguagem de consulta do Monitor log

Embora [inicia sessão no Azure Monitor](log-query-overview.md) baseia [Explorador de dados do Azure](/azure/data-explorer) e utiliza as mesmas [linguagem de consulta de Kusto](/azure/kusto/query), a versão da linguagem tem algumas diferenças. Este artigo identifica os elementos que são diferentes entre a versão do idioma usado para o Data Explorer e a versão utilizada para consultas de registo do Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>Elementos KQL não suportados no Azure Monitor
As secções seguintes descrevem os elementos de linguagem de consulta de Kusto que não são suportados pelo Azure Monitor.

### <a name="statements-not-supported-in-azure-monitor"></a>Instruções não são suportadas no Azure Monitor

* [Alias](/azure/kusto/query/aliasstatement)
* [Parâmetros de consulta](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Funções não suportadas no Azure Monitor

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Operadores não suportados no Azure Monitor

* [Associação cruzada do Cluster](/azure/kusto/query/joincrosscluster)
* [operador de externaldata](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-azure-monitor"></a>Plug-ins não suportado no Azure Monitor

* [sql_request plugin](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Operadores adicionais no Azure Monitor
Os seguintes operadores suportam funcionalidades específicas do Azure Monitor e não estão disponíveis fora do Azure Monitor.

* [app()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>Passos Seguintes

- Obter referências a diferentes [consultas de registo de recursos para a escrita do Azure Monitor](query-language.md).
- Aceder a completa [fazer referência a documentação para a linguagem de consulta de Kusto](/azure/kusto/query/).
