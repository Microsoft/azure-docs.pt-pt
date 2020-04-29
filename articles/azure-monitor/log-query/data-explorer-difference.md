---
title: Diferenças linguísticas de consulta de log Do Azure Monitor [ Microsoft Docs
description: Informações de referência para a linguagem de consulta Kusto utilizada saqueada pelo Azure Monitor. Inclui elementos adicionais específicos do Monitor Azure e elementos não suportados nas consultas de registo do Monitor Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/01/2020
ms.openlocfilehash: 265179909c8ae4a6fa630b835bc9993f042d6460
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585702"
---
# <a name="azure-monitor-log-query-language-differences"></a>Diferenças linguísticas de consulta de log do Monitor Azure

Enquanto [os registos no Azure Monitor](log-query-overview.md) são construídos no [Azure Data Explorer](/azure/data-explorer) e usam a mesma linguagem de consulta [Kusto,](/azure/kusto/query)a versão do idioma tem algumas diferenças. Este artigo identifica elementos diferentes entre a versão do idioma utilizada para o Data Explorer e a versão utilizada para consultas de registo do Monitor Do Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>Elementos KQL não suportados no Monitor Azure
As seguintes secções descrevem elementos da linguagem de consulta kusto que não são suportados pelo Monitor Azure.

### <a name="statements-not-supported-in-azure-monitor"></a>Declarações não apoiadas no Monitor Azure

* [Pseudónimo](/azure/kusto/query/aliasstatement)
* [Parâmetros de consulta](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Funções não suportadas no Monitor Azure

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Operadores não apoiados no Monitor Azure

* [Cruzamento de cluster](/azure/kusto/query/joincrosscluster)

### <a name="plugins-not-supported-in-azure-monitor"></a>Plugins não suportados no Monitor Azure

* [Plugin python](/azure/kusto/query/pythonplugin)
* [plugin sql_request](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Operadores adicionais no Monitor Azure
Os seguintes operadores suportam funcionalidades específicas do Monitor Azure e não estão disponíveis fora do Monitor Azure.

* [app()](app-expression.md)
* [espaço de trabalho()](workspace-expression.md)

## <a name="next-steps"></a>Passos seguintes

- Obtenha referências a diferentes recursos para escrever consultas de [registo do Monitor Azure](query-language.md).
- Aceda à documentação completa de referência para a [linguagem de consulta kusto](/azure/kusto/query/).
