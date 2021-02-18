---
title: Recursos úteis ao trabalhar com Azure Sentinel| Microsoft Docs
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
ms.date: 01/27/2021
ms.author: yelevin
ms.openlocfilehash: c404aa93669cd95dccb0ad185d71d2ec16256d0d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570441"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Recursos úteis para trabalhar com Azure Sentinel



Este artigo lista recursos que podem ajudá-lo a obter mais informações sobre o trabalho com a Azure Sentinel.

- **Conectores Azure Logic Apps**: <https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Auditoria e reporte
Os registos de auditoria do Azure Sentinel são mantidos em [Registos de Atividades Azure](../azure-monitor/essentials/platform-logs-overview.md).

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

1. Em seguida, consultar os dados usando KQL, como faria em qualquer outra tabela.

    Por exemplo, para saber quem foi o último utilizador a editar uma determinada regra de análise, utilize a seguinte consulta (substituindo `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` a regra ID da regra que pretende verificar):

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```


## <a name="blogs-and-forums"></a>Blogs e fóruns

Adoramos ouvir dos nossos utilizadores!

- **Publique as suas perguntas** no [espaço TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) para O Azure Sentinel. 

- **Envie sugestões para melhorias** através do nosso programa [Voz do Utilizador.](https://feedback.azure.com/forums/920458-azure-sentinel)

- **Ver e comentar** as nossas publicações de blogue do Azure Sentinel:

    - [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) 
    - [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/)


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Certifica-se!](/learn/paths/security-ops-sentinel/)

> [!div class="nextstepaction"]
> [Leia as histórias de casos de uso do cliente](https://customers.microsoft.com/en-us/search?sq=%22Azure%20Sentinel%20%22&ff=&p=0&so=story_publish_date%20desc)

