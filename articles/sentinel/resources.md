---
title: Recursos úteis ao trabalhar com a Azure Sentinel. Microsoft Docs
description: Este documento fornece-lhe uma lista de recursos úteis ao trabalhar com a Azure Sentinel.
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
ms.openlocfilehash: 54c10d7941e053bec928f8f43310218d89124f9a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89461736"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Recursos úteis para trabalhar com Azure Sentinel



Este artigo lista recursos que podem ajudá-lo a obter mais informações sobre o trabalho com a Azure Sentinel.

Conectores Azure Logic Apps: <https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Auditoria e reporte
Os registos de auditoria do Azure Sentinel são mantidos em [Registos de Atividades Azure](../azure-monitor/platform/platform-logs-overview.md).

As seguintes operações apoiadas podem ser auditadas.

|Nome da operação|    Tipo de recurso|
|----|----|
|Criar ou atualizar o livro  |Microsoft.Insights/livros|
|Apagar Livro    |Microsoft.Insights/livros|
|Definir fluxo de trabalho   |Microsoft.Logic/workflows|
|Eliminar fluxo de trabalho    |Microsoft.Logic/workflows|
|Criar pesquisa guardada    |Microsoft.OperationalInsights/workspaces/saveSearches|
|Eliminar pesquisa guardada    |Microsoft.OperationalInsights/workspaces/saveSearches|
|Atualizar regras de alerta |Microsoft.SecurityInsights/alertRules|
|Eliminar regras de alerta |Microsoft.SecurityInsights/alertRules|
|Atualização atualização ações de resposta à regra de alerta |Microsoft.SecurityInsights/alertRules/actions|
|Eliminar ações de resposta à regra de alerta |Microsoft.SecurityInsights/alertRules/actions|
|Atualizar marcadores   |Microsoft.SecurityInsights/bookmarks|
|Eliminar marcadores   |Microsoft.SecurityInsights/bookmarks|
|Atualizar casos   |Microsoft.SecurityInsights/Cases|
|Atualizar investigação de caso  |Microsoft.SecurityInsights/Cases/investigations|
|Criar comentários de casos   |Microsoft.SecurityInsights/Cases/comments|
|Atualizar conectores de dados |Microsoft.SecurityInsights/dataConnectors|
|Eliminar conectores de dados |Microsoft.SecurityInsights/dataConnectors|
|Atualizar Definições    |Microsoft.SecurityInsights/configurações|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Ver dados de auditoria e reporte no Azure Sentinel

Pode visualizar estes dados transmitindo-os a partir do login da Azure Activity para o Azure Sentinel, onde poderá então realizar pesquisas e análises sobre os mesmos.

1. Ligue a fonte de dados [da Atividade Azure.](connect-azure-activity.md) Depois disso, os eventos de auditoria são transmitidos para uma nova tabela no ecrã **logs** chamado AzureActivity.
2. Em seguida, consultar os dados usando KQL, como faria em qualquer outra tabela.



## <a name="vendor-documentation"></a>Documentação do fornecedor

| **Fornecedor**  | **Incidente de uso em Azure Sentinel** | **Ligação**|
|----|----|----|
| GitHub| Usado para aceder à página comunitária| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto| CEF de configuração| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight | Kusto Query Language course (Curso sobre Kusto Query Language)| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>Blogs e fóruns

Publique as suas perguntas no [espaço TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) para O Azure Sentinel.

Ver posts de blog do Azure Sentinel da [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) e [microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/).


## <a name="next-steps"></a>Passos seguintes
Neste documento, você tem uma lista de recursos que são úteis quando você está trabalhando com Azure Sentinel. Encontrará informações adicionais sobre segurança e conformidade da Azure no [blog Microsoft Azure Security and Compliance](https://docs.microsoft.com/archive/blogs/azuresecurity/).
