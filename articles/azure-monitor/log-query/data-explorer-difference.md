---
title: Diferenças de linguagem de consulta de log Azure Monitor | Microsoft Docs
description: Informações de referência para a linguagem de consulta Kusto usada pelo Azure Monitor. Inclui elementos adicionais específicos para Azure Monitor e elementos sem suporte em consultas de log de Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: article
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 6d3c2daafc9116d23d837e86e60ca628bbe34647
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900393"
---
# <a name="azure-monitor-log-query-language-differences"></a>Diferenças de linguagem de consulta de log Azure Monitor

Embora os [logs no Azure monitor](log-query-overview.md) sejam criados no [Data Explorer do Azure](/azure/data-explorer) e usem a mesma [linguagem de consulta Kusto](/azure/kusto/query), a versão da linguagem tem algumas diferenças. Este artigo identifica os elementos que são diferentes entre a versão do idioma usado para Data Explorer e a versão usada para Azure Monitor consultas de log.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>Elementos KQL sem suporte no Azure Monitor
As seções a seguir descrevem os elementos da linguagem de consulta Kusto que não têm suporte do Azure Monitor.

### <a name="statements-not-supported-in-azure-monitor"></a>Instruções sem suporte no Azure Monitor

* [Receber](/azure/kusto/query/aliasstatement)
* [Parâmetros de consulta](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Funções sem suporte no Azure Monitor

* [cluster ()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [banco de dados ()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Operadores sem suporte no Azure Monitor

* [Junção entre clusters](/azure/kusto/query/joincrosscluster)
* [operador externaldata](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-azure-monitor"></a>Não há suporte para plug-ins no Azure Monitor

* [Plug-in do Python](/azure/kusto/query/pythonplugin)
* [plug-in sql_request](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Operadores adicionais no Azure Monitor
Os operadores a seguir dão suporte a recursos específicos de Azure Monitor e não estão disponíveis fora do Azure Monitor.

* [aplicativo ()](app-expression.md)
* [espaço de trabalho ()](workspace-expression.md)

## <a name="next-steps"></a>Passos seguintes

- Obtenha referências a diferentes [recursos para gravar Azure monitor consultas de log](query-language.md).
- Acesse a [documentação de referência completa para a linguagem de consulta do Kusto](/azure/kusto/query/).
