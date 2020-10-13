---
title: Azure Monitor consulta de línguas de consulta de log / Microsoft Docs
description: Informações de referência para a língua de consulta kusto utilizada pelo Azure Monitor. Inclui elementos adicionais específicos do Azure Monitor e elementos não suportados em consultas de registo do Monitor Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/01/2020
ms.openlocfilehash: bfa27b0df7febbfb8c97f11f69f87c352810699b
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91993180"
---
# <a name="azure-monitor-log-query-language-differences"></a>Diferenças linguísticas de consulta de log do Azure Monitor

Enquanto [os registos no Azure Monitor](log-query-overview.md) são construídos no [Azure Data Explorer](/azure/data-explorer) e utilizam a mesma linguagem de consulta [Kusto,](/azure/kusto/query)a versão do idioma tem algumas diferenças. Este artigo identifica elementos que são diferentes entre a versão do idioma utilizado para o Data Explorer e a versão utilizada para consultas de registo do Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>Elementos KQL não suportados no Azure Monitor
As secções seguintes descrevem elementos da linguagem de consulta kusto que não são suportados pelo Azure Monitor.

### <a name="statements-not-supported-in-azure-monitor"></a>Declarações não apoiadas no Azure Monitor

* [Pseudónimo](/azure/kusto/query/aliasstatement)
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

### <a name="operators-not-supported-in-azure-monitor"></a>Operadores não apoiados no Azure Monitor

* [União cross-cluster](/azure/kusto/query/joincrosscluster)

### <a name="plugins-not-supported-in-azure-monitor"></a>Plugins não suportados no Azure Monitor

* [Plugin python](/azure/kusto/query/pythonplugin)
* [plug-in sql_request](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Operadores adicionais no Azure Monitor
Os seguintes operadores suportam funcionalidades específicas do Azure Monitor e não estão disponíveis fora do Azure Monitor.

* [app()](app-expression.md)
* [espaço de trabalho()](workspace-expression.md)

## <a name="next-steps"></a>Passos seguintes

- Obtenha referências a diferentes [recursos para escrever consultas de registo do Azure Monitor](/azure/data-explorer/kusto/query/).
- Aceda à documentação completa [de referência para a língua de consulta de Kusto.](/azure/kusto/query/)