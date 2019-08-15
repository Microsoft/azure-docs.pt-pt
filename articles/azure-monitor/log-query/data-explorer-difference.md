---
title: Diferenças de linguagem de consulta de log Azure Monitor | Microsoft Docs
description: Informações de referência para a linguagem de consulta Kusto usada pelo Azure Monitor. Inclui elementos adicionais específicos para Azure Monitor e elementos sem suporte em consultas de log de Azure Monitor.
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
ms.date: 08/13/2019
ms.author: bwren
ms.openlocfilehash: 9892b8884d901033a80fae4168f25da97edb5a68
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990169"
---
# <a name="azure-monitor-log-query-language-differences"></a>Diferenças de linguagem de consulta de log Azure Monitor

Embora os [logs no Azure monitor](log-query-overview.md) sejam criados no [Data Explorer do Azure](/azure/data-explorer) e usem a mesma [linguagem de consulta Kusto](/azure/kusto/query), a versão da linguagem tem algumas diferenças. Este artigo identifica os elementos que são diferentes entre a versão do idioma usado para Data Explorer e a versão usada para Azure Monitor consultas de log.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>Elementos KQL sem suporte no Azure Monitor
As seções a seguir descrevem os elementos da linguagem de consulta Kusto que não têm suporte do Azure Monitor.

### <a name="statements-not-supported-in-azure-monitor"></a>Instruções sem suporte no Azure Monitor

* [Alias](/azure/kusto/query/aliasstatement)
* [Parâmetros de consulta](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Funções sem suporte no Azure Monitor

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Operadores sem suporte no Azure Monitor

* [Junção entre clusters](/azure/kusto/query/joincrosscluster)
* [operador externaldata](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-azure-monitor"></a>Não há suporte para plug-ins no Azure Monitor

* [Plug-in do Python](/azure/kusto/query/pythonplugin)
* [sql_request plugin](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Operadores adicionais no Azure Monitor
Os operadores a seguir dão suporte a recursos específicos de Azure Monitor e não estão disponíveis fora do Azure Monitor.

* [app()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>Passos seguintes

- Obtenha referências a diferentes [recursos para gravar Azure monitor consultas de log](query-language.md).
- Acesse a [documentação de referência completa para a linguagem de consulta do Kusto](/azure/kusto/query/).
