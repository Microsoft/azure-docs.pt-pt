---
title: Consulta de recursos cruzados Azure Data Explorer usando O Monitor de Azure
description: Utilize o Azure Monitor para realizar consultas entre o Azure Data Explorer, os espaços de trabalho do Log Analytics e as aplicações clássicas de Insights de Aplicações no Azure Monitor.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: a800f78df26ce76144994bb9da2cac6271323eb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419427"
---
# <a name="cross-resource-query-azure-data-explorer-by-using-azure-monitor"></a>Consulta de recursos cruzados Azure Data Explorer usando O Monitor de Azure
O Azure Monitor suporta consultas de serviços cruzados entre O Explorador de Dados Azure, [Insights de Aplicação](../app/app-insights-overview.md)e [Log Analytics](../logs/data-platform-logs.md). Em seguida, pode consultar o seu cluster Azure Data Explorer utilizando ferramentas De Registo Analytics/Application Insights e consultar-se numa consulta de serviço transversal. O artigo mostra como fazer uma consulta de cross-service.

O seguinte diagrama mostra o fluxo de serviço transversal Azure Monitor:

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="Diagrama que mostra o fluxo de consultas entre um utilizador, Azure Monitor, um proxy, e Azure Data Explorer.":::

>[!NOTE]
> A consulta de serviço cross-service Azure Monitor está em pré-visualização pública. Contacte a [Equipa de Serviço](mailto:ADXProxy@microsoft.com) com quaisquer perguntas.

## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Questione os recursos de Log Analytics ou Application Insights e Azure Data Explorer

Pode executar consultas de recursos cruzados utilizando ferramentas de cliente que suportam consultas kusto. Exemplos destas ferramentas incluem a UI web Log Analytics, Workbooks, PowerShell e a API REST.

Introduza o identificador para um cluster Azure Data Explorer numa consulta dentro do `adx` padrão, seguida do nome e tabela da base de dados.

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="Screenshot que mostra um exemplo de uma consulta de cross-service.":::

> [!NOTE]
>* Os nomes da base de dados são sensíveis a maiôs.
>* A consulta de recursos cruzados como alerta não é suportada.
>* A identificação da coluna Timestamp no cluster não é suportada, a consulta de Log Analytics API não passará ao longo do filtro de tempo

## <a name="combine-azure-data-explorer-cluster-tables-with-a-log-analytics-workspace"></a>Combine as tabelas de cluster do Azure Data Explorer com um espaço de trabalho Log Analytics

Utilize o `union` comando para combinar mesas de cluster com um espaço de trabalho Log Analytics.

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="Screenshot que mostra um exemplo de consulta cruzada com o comando do sindicato.":::

> [!Tip]
> O formato de abreviatura é permitido: *ClusterName* / *InitialCatalog*. Por exemplo, `adx('help/Samples')` é traduzido para `adx('help.kusto.windows.net/Samples')` .

## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Junte os dados de um cluster Azure Data Explorer em um inquilino com um recurso Azure Monitor em outro

As consultas entre os inquilinos entre os serviços não são apoiadas. Está inscrito num único inquilino para dirigir a consulta que abrange ambos os recursos.

Se o recurso Azure Data Explorer estiver no Inquilino A e o espaço de trabalho Log Analytics estiver no Inquilino B, utilize um dos seguintes métodos:

*  O Azure Data Explorer permite-lhe adicionar funções para os principais em diferentes inquilinos. Adicione o seu ID de utilizador no Inquilino B como um utilizador autorizado no cluster Azure Data Explorer. Valide que a propriedade [TrustedExternalTenant](/powershell/module/az.kusto/update-azkustocluster) no cluster Azure Data Explorer contém o Inquilino B. Execute a consulta cruzada totalmente no Inquilino B.
*  Utilize o [Farol](../../lighthouse/index.yml) para projetar o recurso Azure Monitor no Inquilino A.

## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Ligue-se aos clusters Azure Data Explorer de diferentes inquilinos

O Kusto Explorer assina automaticamente o seu contrato com o arrendatário a que a conta de utilizador pertence originalmente. Para aceder a recursos em outros inquilinos com a mesma conta de utilizador, deve especificar explicitamente `TenantId` na cadeia de ligação:

`Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=TenantId`

## <a name="next-steps"></a>Passos seguintes
* [Escrever consultas](/azure/data-explorer/write-queries)
* [Dados de consulta no Azure Monitor utilizando o Azure Data Explorer](/azure/data-explorer/query-monitor-data)
* [Realize consultas de registo de recursos cruzados no Azure Monitor](../logs/cross-workspace-query.md)