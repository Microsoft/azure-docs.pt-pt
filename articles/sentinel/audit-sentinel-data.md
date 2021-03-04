---
title: Auditoria Azure Sentinel consultas e atividades | Microsoft Docs
description: Este artigo descreve como auditar consultas e atividades realizadas em Azure Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2021
ms.author: bagol
ms.openlocfilehash: a8ea32d84da521c8a1af926c6cb5e26bc2738de2
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054984"
---
# <a name="audit-azure-sentinel-queries-and-activities"></a>Auditoria Azure Sentinel consultas e atividades

Este artigo descreve como pode ver os dados de auditoria para consultas executadas e atividades realizadas no seu espaço de trabalho Azure Sentinel, tais como para requisitos de conformidade interna e externa no seu espaço de trabalho de Operações de Segurança (SOC).

Azure Sentinel dá acesso a:

- A tabela **AzureActivity,** que fornece detalhes sobre todas as ações tomadas no Azure Sentinel, como regras de alerta de edição. A tabela **AzureActivity** não regista dados de consulta específicos. Para obter mais informações, consulte [auditação com registos de AtividadeS Azure](#auditing-with-azure-activity-logs).

- A tabela **LAQueryLogs,** que fornece detalhes sobre as consultas executadas no Log Analytics, incluindo consultas de Azure Sentinel. Para obter mais informações, consulte [Auditing com LAQueryLogs](#auditing-with-laquerylogs).

> [!TIP]
> Além das consultas manuais descritas neste artigo, o Azure Sentinel fornece um livro embutido para ajudá-lo a auditar as atividades no seu ambiente SOC.
>
> Na área de **Livros** Azure Sentinel, procure o livro de auditoria do **Espaço** de Trabalho.



## <a name="auditing-with-azure-activity-logs"></a>Auditoria com registos de atividades da Azure

Os registos de auditoria do Azure Sentinel são mantidos nos Registos de Atividade do [Azure,](../azure-monitor/essentials/platform-logs-overview.md)onde a tabela **AzureActivity** inclui todas as ações tomadas no seu espaço de trabalho Azure Sentinel.

Pode utilizar a tabela **AzureActivity** ao auditar atividade no seu ambiente SOC com o Azure Sentinel.

**Para consultar a tabela AzureActivity:**

1. Ligue a fonte de dados da [Azure Activity](connect-azure-activity.md) para iniciar eventos de auditoria de streaming numa nova tabela no ecrã **logs** chamada AzureActivity.

1. Em seguida, consultar os dados usando KQL, como faria em qualquer outra tabela.

    A tabela **AzureActivity** inclui dados de muitos serviços, incluindo Azure Sentinel. Para filtrar apenas os dados do Azure Sentinel, inicie a sua consulta com o seguinte código:

    ```kql
     AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS"
    ```

    Por exemplo, para saber quem foi o último utilizador a editar uma determinada regra de análise, utilize a seguinte consulta (substituindo `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` a regra ID da regra que pretende verificar):

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```

Adicione mais parâmetros à sua consulta para explorar ainda mais a tabela **AzureActivities,** dependendo do que precisa reportar. As seguintes secções fornecem outras consultas de amostra a utilizar ao auditar com dados de tabela **AzureActivity.** 

Para obter mais informações, consulte [os dados do Azure Sentinel incluídos nos registos da Atividade Azure](#azure-sentinel-data-included-in-azure-activity-logs).

### <a name="find-all-actions-taken-by-a-specific-user-in-the-last-24-hours"></a>Encontre todas as ações tomadas por um utilizador específico nas últimas 24 horas

A seguinte consulta de tabela **AzureActivity** lista todas as ações tomadas por um utilizador AZure AD específico nas últimas 24 horas.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where Caller == "[AzureAD username]"
| where TimeGenerated > ago(1d)
```

### <a name="find-all-delete-operations"></a>Localizar todas as operações de eliminação

A seguinte consulta de tabela **AzureActivity** lista todas as operações de eliminação realizadas no seu espaço de trabalho Azure Sentinel.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where OperationName contains "Delete"
| where ActivityStatusValue contains "Succeeded"
| project TimeGenerated, Caller, OperationName
``` 


### <a name="azure-sentinel-data-included-in-azure-activity-logs"></a>Dados do Azure Sentinel incluídos nos registos de atividade do Azure
 
Os registos de auditoria do Azure Sentinel são mantidos nos Registos de Atividade do [Azure,](../azure-monitor/essentials/platform-logs-overview.md)e incluem os seguintes tipos de informação:

|Operação  |Tipos de informação  |
|---------|---------|
|**Criado**     |Regras de alertas <br> Comentários de caso <br>Comentários de incidentes <br>Pesquisas guardadas<br>Listas de observação    <br>Livros     |
|**Eliminado**     |Regras de alertas <br>Marcadores <br>Conectores de dados <br>Incidentes <br>Pesquisas guardadas <br>Definições <br>Relatórios de inteligência de ameaça <br>Listas de observação      <br>Livros <br>Fluxo de trabalho  |
|**Atualizado**     |  Regras de alertas<br>Marcadores <br> Casos <br> Conectores de dados <br>Incidentes <br>Comentários de incidentes <br>Relatórios de inteligência de ameaça <br> Livros <br>Fluxo de trabalho       |
|     |         |

Também pode utilizar os registos da Azure Activity para verificar as autorizações e licenças do utilizador. 

Por exemplo, as seguintes listas de tabelas selecionadas encontradas nos registos de Atividade Azure com o recurso específico de que os dados de registo são retirados.

|Nome da operação|    Tipo de recurso|
|----|----|
|Criar ou atualizar o livro  |Microsoft.Insights/livros|
|Apagar livro    |Microsoft.Insights/livros|
|Definir fluxo de trabalho   |Microsoft.Logic/workflows|
|Eliminar fluxo de trabalho    |Microsoft.Logic/workflows|
|Criar pesquisa guardada    |Microsoft.OperationalInsights/workspaces/saveSearches|
|Eliminar pesquisa guardada    |Microsoft.OperationalInsights/workspaces/saveSearches|
|Atualizar regras de alerta |Microsoft.SecurityInsights/alertRules|
|Eliminar regras de alerta |Microsoft.SecurityInsights/alertRules|
|Atualizar ações de resposta à regra de alerta |Microsoft.SecurityInsights/alertRules/actions|
|Eliminar ações de resposta à regra de alerta |Microsoft.SecurityInsights/alertRules/actions|
|Atualizar marcadores   |Microsoft.SecurityInsights/bookmarks|
|Eliminar marcadores   |Microsoft.SecurityInsights/bookmarks|
|Atualizar casos   |Microsoft.SecurityInsights/Cases|
|Atualizar investigação de caso  |Microsoft.SecurityInsights/Cases/investigations|
|Criar comentários de casos   |Microsoft.SecurityInsights/Cases/comments|
|Atualizar conectores de dados |Microsoft.SecurityInsights/dataConnectors|
|Eliminar conectores de dados |Microsoft.SecurityInsights/dataConnectors|
|Atualizar definições    |Microsoft.SecurityInsights/configurações|
| | |

Para mais informações, consulte [o esquema do evento Azure Activity Log](/azure/azure-monitor/essentials/activity-log-schema).


## <a name="auditing-with-laquerylogs"></a>Auditoria com LAQueryLogs

A tabela **LAQueryLogs** fornece detalhes sobre consultas de registo executadas no Log Analytics. Uma vez que o Log Analytics é utilizado como a loja de dados subjacente do Azure Sentinel, pode configurar o seu sistema para recolher dados de LAQueryLogs no seu espaço de trabalho Azure Sentinel.

Os dados do LAQueryLogs incluem informações como:

- Quando as consultas foram executadas
- Quem fez consultas no Log Analytics
- Que ferramenta foi usada para executar consultas em Log Analytics, como Azure Sentinel
- Os próprios textos de consulta
- Dados de desempenho em cada consulta

> [!NOTE]
> - A tabela **LAQueryLogs** inclui apenas consultas que tenham sido executadas na lâmina de Logs de Azure Sentinel. Não inclui as consultas executadas por regras de análise programadas, utilizando o **Gráfico de Investigação** ou na página de **Caça sentinela** Azure.
> - Pode haver um pequeno atraso entre o tempo de execução de uma consulta e os dados são preenchidos na tabela **LAQueryLogs.** Recomendamos esperar cerca de 5 minutos para consultar a tabela **LAQueryLogs** para os dados de auditoria.
>


**Para consultar a tabela LAQueryLogs:**

1. A tabela **LAQueryLogs** não é ativada por padrão no seu espaço de trabalho Log Analytics. Para utilizar os **dados do LAQueryLogs** durante a auditoria no Azure Sentinel, primeiro ative os **LAQueryLogs** na área de **definições** de Diagnóstico do seu log Analytics.

    Para obter mais informações, consulte [as consultas de auditoria nos registos do Azure Monitor](/azure/azure-monitor/logs/query-audit).


1. Em seguida, consultar os dados usando KQL, como faria em qualquer outra tabela.

    Por exemplo, a seguinte consulta mostra quantas consultas foram executadas na última semana, numa base diária:

    ```kql
    LAQueryLogs
    | where TimeGenerated > ago(7d)
    | summarize events_count=count() by bin(TimeGenerated, 1d)
    ```

As seguintes secções mostram mais consultas de amostra para executar na tabela **LAQueryLogs** ao auditar atividades no seu ambiente SOC usando Azure Sentinel.

### <a name="the-number-of-queries-run-where-the-response-wasnt-ok"></a>O número de consultas é onde a resposta não foi "OK"

A seguinte consulta de tabela **LAQueryLogs** mostra o número de consultas executadas, onde qualquer outra coisa que não uma resposta HTTP de **200 OK** foi recebida. Por exemplo, este número incluirá consultas que não tinham sido executadas.

```kql
LAQueryLogs
| where ResponseCode != 200 
| count 
```

### <a name="show-users-for-cpu-intensive-queries"></a>Mostrar aos utilizadores consultas intensivas de CPU

A seguinte consulta de tabela **LAQueryLogs** lista os utilizadores que executaram as consultas mais intensivas de CPU, com base no CPU utilizado e duração do tempo de consulta.

```kql
LAQueryLogs
|summarize arg_max(StatsCPUTimeMs, *) by AADClientId
| extend User = AADEmail, QueryRunTime = StatsCPUTimeMs
| project User, QueryRunTime, QueryText
| order by QueryRunTime desc
```

### <a name="show-users-who-ran-the-most-queries-in-the-past-week"></a>Mostrar aos utilizadores que fizeram mais consultas na semana passada

A seguinte consulta de tabela **LAQueryLogs** lista os utilizadores que fizeram mais consultas na última semana.

```kql
LAQueryLogs
| where TimeGenerated > ago(7d)
| summarize events_count=count() by AADEmail
| extend UserPrincipalName = AADEmail, Queries = events_count
| join kind= leftouter (
    SigninLogs)
    on UserPrincipalName
| project UserDisplayName, UserPrincipalName, Queries
| summarize arg_max(Queries, *) by UserPrincipalName
| sort by Queries desc
```

## <a name="configuring-alerts-for-azure-sentinel-activities"></a>Configurar alertas para as atividades do Azure Sentinel

Pode querer utilizar recursos de auditoria do Azure Sentinel para criar alertas proactivos.

Por exemplo, se tiver mesas sensíveis no seu espaço de trabalho Azure Sentinel, utilize a seguinte consulta para o notificar sempre que essas tabelas forem consultadas:

```kql
LAQueryLogs
| where QueryText contains "[Name of sensitive table]"
| where TimeGenerated > ago(1d)
| extend User = AADEmail, Query = QueryText
| project User, Query
```


## <a name="next-steps"></a>Passos seguintes

Em Azure Sentinel, utilize o livro de **auditoria do Espaço de Trabalho** para auditar as atividades no seu ambiente SOC.

Para mais informações, consulte [Tutorial: Visualize e monitorize os seus dados.](tutorial-monitor-your-data.md)
