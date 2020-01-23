---
title: Recursos úteis ao trabalhar com o Azure Sentinel | Microsoft Docs
description: Este documento fornece uma lista de recursos úteis ao trabalhar com o Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
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
ms.author: rkarlin
ms.openlocfilehash: 2ce8b9e82907402538ca2d14b864c02bb8bc2d82
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548004"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Recursos úteis para trabalhar com o Azure Sentinel



Este artigo lista os recursos que podem ajudá-lo a obter mais informações sobre como trabalhar com o Azure Sentinel.

Conectores de aplicativos lógicos do Azure: <https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Auditoria e relatórios
Os logs de auditoria do Azure Sentinel são mantidos nos [logs de atividades do Azure](../azure-monitor/platform/platform-logs-overview.md).

As operações com suporte a seguir podem ser auditadas.

|Nome da operação|    Tipo de recurso|
|----|----|
|Criar ou atualizar pasta de trabalho  |Microsoft. insights/pastas de trabalho|
|Excluir pasta de trabalho    |Microsoft. insights/pastas de trabalho|
|Definir fluxo de trabalho   |Microsoft.Logic/workflows|
|Excluir fluxo de trabalho    |Microsoft.Logic/workflows|
|Criar pesquisa salva    |Microsoft. OperationalInsights/Workspaces/savedSearches|
|Excluir pesquisa salva    |Microsoft. OperationalInsights/Workspaces/savedSearches|
|Atualizar regras de alerta |Microsoft. SecurityInsights/alertRules|
|Excluir regras de alerta |Microsoft. SecurityInsights/alertRules|
|Atualizar ações de resposta da regra de alerta |Microsoft. SecurityInsights/alertRules/Actions|
|Excluir ações de resposta da regra de alerta |Microsoft. SecurityInsights/alertRules/Actions|
|Atualizar indicadores   |Microsoft. SecurityInsights/bookmarks|
|Excluir indicadores   |Microsoft. SecurityInsights/bookmarks|
|Atualizar casos   |Microsoft. SecurityInsights/casos|
|Atualizar investigação de caso  |Microsoft. SecurityInsights/casos/investigações|
|Criar comentários de caso   |Microsoft. SecurityInsights/casos/comentários|
|Atualizar conectores de dados |Microsoft. SecurityInsights/dataconnecters|
|Excluir conectores de dados |Microsoft. SecurityInsights/dataconnecters|
|Atualizar Definições    |Microsoft. SecurityInsights/Settings|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Exibir dados de auditoria e relatórios no Azure Sentinel

Você pode exibir esses dados transmitindo-os do log de atividades do Azure para o Azure Sentinel, em que você pode executar pesquisa e análise nele.

1. Conecte a fonte de dados da [atividade do Azure](connect-azure-activity.md) . Depois de fazer isso, os eventos de auditoria são transmitidos em uma nova tabela na tela de **logs** chamada AzureActivity.
2. Em seguida, consulte os dados usando KQL, como faria com qualquer outra tabela.



## <a name="vendor-documentation"></a>Documentação do fornecedor

| **Fornecedor**  | **Usar o incidente no Azure Sentinel** | **Ligação**|
|----|----|----|
| GitHub| Usado para acessar a página da Comunidade| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto| Configurar CEF| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight | Curso de linguagem de consulta Kusto| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>Blogs e fóruns

Poste suas perguntas no [espaço do TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) para o Azure Sentinel.

Veja as postagens no blog do Azure Sentinel do [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) e [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/).


## <a name="next-steps"></a>Passos seguintes
Neste documento, você tem uma lista de recursos que são úteis quando você está trabalhando com o Azure Sentinel. Você encontrará informações adicionais sobre a segurança e a conformidade do Azure no [blog Microsoft Azure segurança e conformidade](https://blogs.msdn.com/b/azuresecurity/).
