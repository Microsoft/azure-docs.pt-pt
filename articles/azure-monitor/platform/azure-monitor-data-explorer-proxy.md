---
title: Consulta de recursos cruzados Azure Data Explorer usando O Monitor Azure
description: Utilize o Azure Monitor para realizar consultas de produtos cruzados entre o Azure Data Explorer, os espaços de trabalho do Log Analytics e as aplicações clássicas de Insights de Aplicações no Azure Monitor.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: 5cb2f7b3b07c20e09d61e97412bc35f03b15cb3b
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572155"
---
# <a name="cross-resource-query-azure-data-explorer-using-azure-monitor"></a>Consulta de recursos cruzados Azure Data Explorer usando O Monitor Azure
O Azure Monitor suporta consultas de serviços cruzados entre Azure Data Explorer, [Application Insights (IA)](/azure/azure-monitor/app/app-insights-overview)e [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs). Em seguida, pode consultar o seu cluster Azure Data Explorer utilizando ferramentas De registo Analytics/Application Insights e encaminhá-lo numa consulta de serviço cruzado. O artigo mostra como fazer uma consulta de serviço cruzado.

O fluxo de serviço cruzado Azure Monitor: :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="Monitor Azure e fluxo de serviço cross Explorer Azure Data Explorer.":::

>[!NOTE]
>* A consulta de serviço cross monitor Azure Monitor está em pré-visualização privada - AllowListing é necessário.
>* Contacte a [Equipa de Serviço](mailto:ADXProxy@microsoft.com) com quaisquer perguntas.
## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Consulta cruzada os recursos de Log Analytics ou Application Insights e Azure Data Explorer

Pode executar as consultas de recursos cruzados utilizando ferramentas de cliente que suportam consultas kusto, tais como: Log Analytics web UI, Workbooks, PowerShell, REST API e muito mais.

* Introduza o identificador de um cluster Azure Data Explorer numa consulta dentro do padrão 'adx' seguido pelo nome e tabela da base de dados.

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="Exemplo de consulta de serviço transversal.":::

> [!NOTE]
>* Os nomes da base de dados são sensíveis a maiôs.
>* A consulta de recursos cruzados como um alerta não é suportada.
## <a name="combining-azure-data-explorer-cluster-tables-using-union-and-join-with-la-workspace"></a>Combinando tabelas de cluster Azure Data Explorer (usando união e união) com espaço de trabalho de LA.

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="Cross service consulta exemplo com união.":::

>[!Tip]
>* O formato de mão curta é permitido - ClusterName/InitialCatalog. Por exemplo, o adx ('help/samples') é traduzido para adx ('help.kusto.windows.net/Samples')
## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Junte os dados de um cluster Azure Data Explorer em um inquilino com um recurso Azure Monitor em outro

As consultas entre os inquilinos entre os serviços não são apoiadas. Você está inscrito em um único inquilino para executar a consulta abrangendo ambos os recursos.

Se o recurso Azure Data Explorer estiver no espaço de trabalho do Inquilino 'A' e do Log Analytics está no Arrendatário 'B' utilize um dos dois métodos seguintes:

*  O Azure Data Explorer permite-lhe adicionar funções para os principais em diferentes inquilinos. Adicione o seu ID de utilizador no Inquilino 'B' como um utilizador autorizado no cluster Azure Data Explorer. Validar a propriedade *['TrustedExternalTenant'](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* no cluster Azure Data Explorer contém O Inquilino 'B'. Executar a consulta cruzada completamente no Inquilino 'B'.
*  Utilize o [Farol](https://docs.microsoft.com/azure/lighthouse/) para projetar o recurso Azure Monitor no Inquilino 'A'.
## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Ligue-se aos clusters Azure Data Explorer de diferentes inquilinos

O Kusto Explorer inscreve-o automaticamente no inquilino a que a conta de utilizador pertence originalmente. Para aceder a recursos em outros inquilinos com a mesma conta de utilizador, o `tenantId` tem de ser explicitamente especificado na cadeia de ligação: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="next-steps"></a>Passos seguintes
* [Escrever Consultas](https://docs.microsoft.com/azure/data-explorer/write-queries)
* [Dados de consulta no Azure Monitor usando O Explorador de Dados Azure](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)
* [Realize consultas de registo de recursos cruzados no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query)
