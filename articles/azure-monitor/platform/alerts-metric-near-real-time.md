---
title: Recursos suportados para alertas de métricas no Azure Monitor
description: Referência no suporte de métricas e registos para alertas de métricas no Azure Monitor
author: snehithm
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 1506911902940ebd6fa05a0d3534ce599c6d941a
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961334"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Recursos suportados para alertas de métricas no Azure Monitor

Agora suporta o Monitor do Azure um [novo tipo de alerta de métrica](../../azure-monitor/platform/alerts-overview.md) que apresenta vantagens consideráveis sobre o mais antigo [alertas de métricas clássicas](../../azure-monitor/platform/alerts-classic.overview.md). As métricas estão disponíveis para [grande lista de serviços do Azure](../../azure-monitor/platform/metrics-supported.md). Os alertas mais recente suportam um subconjunto (crescente) dos tipos de recursos. Este artigo lista esse subconjunto.

Também pode utilizar alertas de métricas mais recente em populares registos do Log Analytics extraídos como métricas. Para obter mais informações, veja [alertas de métrica para os registos](../../azure-monitor/platform/alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portal, PowerShell, CLI, REST suporte
Atualmente, pode criar alertas de métricas mais recente apenas no portal do Azure, [REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts/), ou [modelos do Resource Manager](../../azure-monitor/platform/alerts-metric-create-templates.md). Suporte para configurar alertas mais recentes com as versões do PowerShell e CLI do Azure 2.0 e versões posteriores estará disponível brevemente.

## <a name="metrics-and-dimensions-supported"></a>Métricas e dimensões suportadas
Alertas de métricas mais recente suportam alertas de métricas que usam dimensões. Pode usar as dimensões para filtrar a métrica para o nível certo. Podem ser exploradas e visualizadas a partir de todas as métricas suportadas, juntamente com dimensões aplicáveis [do Azure Monitor - Explorador de métricas](../../azure-monitor/platform/metrics-charts.md).

Eis a lista completa de origens de métrica de monitor do Azure suportadas pelos alertas mais recentes:

|Tipo de recurso  |Dimensões suportadas  | Métricas disponíveis|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Sim        | [Gestão de API](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Sim   | [Contas de automatização](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | N/A| [Contas do batch](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    N/A     |[Cache do Azure para Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft.CognitiveServices/accounts     |    N/A     | [Serviços Cognitivos](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines     |    N/A     | [Máquinas Virtuais](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   N/A      |[Conjuntos de dimensionamento de máquinas virtuais](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Sim| [Grupos de contentores](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Sim | [Clusters geridos](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataFactory/datafactories| Sim| [V1 de fábricas de dados](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   Sim     |[V2 de fábricas de dados](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   N/A      |[DB para MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    N/A     | [DB para o PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  Sim      |[Hubs de Eventos](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Não | [cofres](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     N/A    |[Aplicações Lógicas](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    N/A     | [Gateways de Aplicação](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/dnsZones | N/A| [Zonas DNS](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | N/A |  [Express Route circuitos](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Network/loadbalancers (apenas para o SKU Standard)| Sim| [Balanceadores de carga](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  N/A       |[Endereços IP públicos](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Sim | [Perfis do Gestor de tráfego](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Sim|[Áreas de trabalho do log Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.PowerBIDedicated/capacities | N/A | [Capacidades](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices     |   N/A      |[Serviços de pesquisa](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Sim       |[Service Bus](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Sim     | [Contas de armazenamento](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Sim    | [Serviços de BLOBs](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [serviços de ficheiros](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices), [serviços de fila](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) e [serviços de tabela](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  N/A       | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
| Microsoft.Web/serverfarms | Sim | [Planos de serviço de aplicações](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)  |
| Microsoft.Web/sites | Sim | [Serviços aplicacionais](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) e [funções](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
| Microsoft.Web/sites/slots | Sim | [Ranhuras de serviço de aplicações](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|


## <a name="payload-schema"></a>Esquema do payload

A operação de POSTAGEM contém o JSON payload seguinte e o esquema para todos os perto de alertas de métricas mais recentes quando um configurado de forma apropriada [grupo de ação](../../azure-monitor/platform/action-groups.md) é usado:

```json
{
  "schemaId": "AzureMonitorMetricAlert",
  "data": {
    "version": "2.0",
    "status": "Activated",
    "context": {
      "timestamp": "2018-02-28T10:44:10.1714014Z",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
      "name": "StorageCheck",
      "description": "",
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "dimensions": [
              {
                "name": "AccountResourceId",
                "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
              },
              {
                "name": "GeoType",
                "value": "Primary"
              }
            ],
            "operator": "GreaterThan",
            "threshold": "0",
            "timeAggregation": "PT5M",
            "metricValue": 1
          }
        ]
      },
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "resourceGroupName": "Contoso",
      "resourceName": "diag500",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
      "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
    },
    "properties": {
      "key1": "value1",
      "key2": "value2"
    }
  }
}
```

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre a nova [experiência de alertas](../../azure-monitor/platform/alerts-overview.md).
* Saiba mais sobre [alertas de registo no Azure](../../azure-monitor/platform/alerts-unified-log.md).
* Saiba mais sobre [alertas no Azure](../../azure-monitor/platform/alerts-overview.md).
