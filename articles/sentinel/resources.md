---
title: Recursos úteis ao trabalhar com o Azure Sentinel. Microsoft Docs
description: Este documento fornece-lhe uma lista de recursos úteis ao trabalhar com o Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2019
ms.author: yelevin
ms.openlocfilehash: 2110d3319cebf693ef06deec26a29fa655e35035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585276"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Recursos úteis para trabalhar com o Azure Sentinel



Este artigo lista recursos que podem ajudá-lo a obter mais informações sobre o trabalho com o Azure Sentinel.

Conectores de aplicativos lógicas azure:<https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Auditoria e relatórios
Os registos de auditoria do Azure Sentinel são mantidos em Registos de Atividade do [Azure](../azure-monitor/platform/platform-logs-overview.md).

As seguintes operações apoiadas podem ser auditadas.

|Nome da operação|    Tipo de recurso|
|----|----|
|Criar ou atualizar livro  |Microsoft.Insights/livros|
|Excluir livro    |Microsoft.Insights/livros|
|Definir fluxo de trabalho   |Microsoft.Logic/workflows|
|Eliminar o Fluxo de Trabalho    |Microsoft.Logic/workflows|
|Criar pesquisa guardada    |Microsoft.OperationalInsights/workspaces/savedSearches|
|Eliminar pesquisa guardada    |Microsoft.OperationalInsights/workspaces/savedSearches|
|Atualizar regras de alerta |Microsoft.SecurityInsights/alertRules|
|Eliminar regras de alerta |Microsoft.SecurityInsights/alertRules|
|Atualizar ações de resposta da regra de alerta |Microsoft.SecurityInsights/alertRules/actions|
|Eliminar ações de resposta a regras de alerta |Microsoft.SecurityInsights/alertRules/actions|
|Atualização dos marcadores   |Microsoft.SecurityInsights/bookmarks|
|Eliminar marcadores   |Microsoft.SecurityInsights/bookmarks|
|Atualizar Casos   |Microsoft.SecurityInsights/Cases|
|Atualizar investigação de caso  |Microsoft.SecurityInsights/Cases/Investigations|
|Criar comentários de caso   |Microsoft.SecurityInsights/Cases/comments|
|Atualizar Conectores de Dados |Microsoft.SecurityInsights/dataConnectors|
|Eliminar Conectores de Dados |Microsoft.SecurityInsights/dataConnectors|
|Atualizar Definições    |Microsoft.SecurityInsights/definições|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Ver dados de auditoria e reporte em Azure Sentinel

Pode ver estes dados transmitindo-os a partir do log institulado da Atividade Azure no Azure Sentinel, onde poderá então realizar pesquisas e análises sobre os mesmos.

1. Ligue a fonte de dados da [Atividade Azure.](connect-azure-activity.md) Depois disso, os eventos de auditoria são transmitidos para uma nova tabela no ecrã de **Registos** chamado AzureActivity.
2. Em seguida, consultar os dados usando kQL, como faria qualquer outra tabela.



## <a name="vendor-documentation"></a>Documentação do fornecedor

| **Fornecedor**  | **Use incidente em Azure Sentinel** | **Ligação**|
|----|----|----|
| GitHub| Usado para aceder à página comunitária| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto| Configurar CEF| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight | Kusto Query Language course (Curso sobre Kusto Query Language)| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>Blogs e fóruns

Publique as suas perguntas sobre o [espaço TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) para o Azure Sentinel.

Ver posts de blog do Azure Sentinel da [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) e [microsoft Azure.](https://azure.microsoft.com/blog/tag/azure-sentinel/)


## <a name="next-steps"></a>Passos seguintes
Neste documento, tem uma lista de recursos que são úteis quando está a trabalhar com o Azure Sentinel. Encontrará informações adicionais sobre segurança e conformidade do Azure no blog De Segurança e Conformidade do [Microsoft Azure.](https://blogs.msdn.com/b/azuresecurity/)
