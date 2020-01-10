---
title: Recursos com suporte para alertas de métrica no Azure Monitor
description: Referência sobre métricas de suporte e logs para alertas de métrica no Azure Monitor
author: harelbr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: harelbr
ms.subservice: alerts
ms.openlocfilehash: 14dc7b33a82b490f005d9684e4c9cb76bd947a7c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75364497"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Recursos com suporte para alertas de métrica no Azure Monitor

O Azure Monitor agora dá suporte a um [novo tipo de alerta de métrica](../../azure-monitor/platform/alerts-overview.md) que tem benefícios significativos em relação aos [alertas de métrica clássica](../../azure-monitor/platform/alerts-classic.overview.md)mais antigos. As métricas estão disponíveis para [uma grande lista de serviços do Azure](../../azure-monitor/platform/metrics-supported.md). Os alertas mais recentes dão suporte a um subconjunto (crescente) dos tipos de recursos. Este artigo lista esse subconjunto.

Você também pode usar alertas de métrica mais recentes em dados de log populares armazenados em um espaço de trabalho Log Analytics extraído como métricas. Para obter mais informações, veja [alertas de métricas para logs](../../azure-monitor/platform/alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portal, PowerShell, CLI, suporte REST
No momento, você pode criar alertas de métrica mais recentes somente nos modelos portal do Azure, [API REST](https://docs.microsoft.com/rest/api/monitor/metricalerts/)ou [Gerenciador de recursos](../../azure-monitor/platform/alerts-metric-create-templates.md). O suporte para configurar alertas mais recentes usando o PowerShell e o CLI do Azure versões 2,0 e superiores estará disponível em breve.

## <a name="metrics-and-dimensions-supported"></a>Métricas e dimensões com suporte
Alertas de métrica mais recentes dão suporte a alertas para métricas que usam dimensões. Você pode usar dimensões para filtrar sua métrica para o nível certo. Todas as métricas com suporte junto com as dimensões aplicáveis podem ser exploradas e visualizadas em [Azure Monitor Metrics Explorer](../../azure-monitor/platform/metrics-charts.md).

Aqui está a lista completa de fontes métricas do Azure monitor com suporte dos alertas mais recentes:

|Tipo de recurso  |Dimensões com suporte |Alertas de vários recursos| Métricas disponíveis|
|---------|---------|-----|----------|
|Microsoft.ApiManagement/service | Sim| Não | [Gestão de API](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft. AppPlatform/Spring |Não| Sim|
|Microsoft.Automation/automationAccounts | Sim| Não | [Contas de automação](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | N/A| Não | [Contas do lote](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis|Sim| Não |[Cache do Azure para Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic|Não|Sim|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic/blobservices|Não|Sim|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic/fileservices|Não|Sim|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic/queueservices|Não|Sim|
|Microsoft. ClassicStorage/storageAccounts/mmxclassic/tableservices|Não|Sim| |
|Microsoft.CognitiveServices/accounts| N/A | Não | [Serviços Cognitivos](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines |Sim | Sim | [Máquinas Virtuais](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets |N/A | Sim |[Conjuntos de dimensionamento de máquinas virtuais](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Sim| Não | [Grupos de contêineres](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Sim | Não | [Clusters gerenciados](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | Sim | Sim | |
|Microsoft.DataFactory/datafactories| Sim| Não | [Data factories v1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories |Sim | Não |[Fábricas de dados v2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft. DataShare/accounts |Não| Sim|
|Microsoft.DBforMySQL/servers |N/A| Não |[BD para MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers |N/A | Não | [BD para PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs | N/A | Não |[Métricas do Hub IoT](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)|
|Microsoft.Devices/provisioningServices| Sim | Não |[Métricas de DPS](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)|
|Microsoft.EventGrid/domains|Não|Sim| |
|Microsoft.EventGrid/topics |Sim | Não |[Tópicos da grade de eventos](../../azure-monitor/platform/metrics-supported.md#microsofteventgridtopics)|
|Microsoft.EventHub/clusters |Sim| Não |[Clusters de hubs de eventos](../../azure-monitor/platform/metrics-supported.md#microsofteventhubclusters)|
|Microsoft.EventHub/namespaces |Sim| Não |[Hubs de Eventos](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Não |Não |[Cofres](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows |N/A | Não |[Aplicações Lógicas](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces|Sim| Não | [Machine Learning](../../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.Network/applicationGateways|N/A| Não |  |
|Microsoft.Network/dnsZones | N/A| Não | [Zonas DNS](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | N/A | Não |[Circuitos de rota expressa](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft. Network/balancers (somente para SKUs padrão)| Sim| Não | [Balanceadores de carga](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft. Network/natGateways|Não|Sim|
|Microsoft. Network/privateEndpoints|Não|Sim|
|Microsoft. Network/privateLinkServices|Não|Sim|
|Microsoft.Network/publicipaddresses |N/A | Não |[Endereços IP Públicos](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Sim | Não | [Perfis do Gerenciador de tráfego](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Sim | Não | [Espaços de trabalho do Log Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft. Relay/namespaces | Sim | Não | [Direciona](../../azure-monitor/platform/metrics-supported.md#microsoftrelaynamespaces)|
|Microsoft. emparelhamento/peeringServices|Não|Sim|
|Microsoft.PowerBIDedicated/capacities | N/A | Não | [Unidades](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices |N/A|Não | [Serviços de pesquisa](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces |Sim| Não |[Service Bus](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Sql/servers/elasticPools |   Não | Sim |
|Microsoft.Sql/servers/databases    | Não | Sim |
|Microsoft.Storage/storageAccounts |Sim | Não | [Contas de armazenamento](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services | Sim| Não | [Serviços de blob](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [serviços de arquivos](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices), serviços de [fila](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) e serviços de [tabela](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs |N/A| Não | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft. Microsoft. VMWareCloudSimple/virtualMachines |Sim|Não |[CloudSimple máquinas virtuais](../../azure-monitor/platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines)|
|Microsoft.Web/serverfarms | Sim | Não | [Planos do serviço de aplicativo](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | Sim | Não | [Serviços de aplicativos](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) e [funções](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
|Microsoft.Web/sites/slots | Sim | Não | [Slots do serviço de aplicativo](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|

## <a name="payload-schema"></a>Esquema de carga

> [!NOTE]
> Você também pode usar o [esquema de alerta comum](https://aka.ms/commonAlertSchemaDocs), que fornece a vantagem de ter uma única carga de alerta extensível e unificada em todos os serviços de alerta no Azure monitor, para suas integrações de webhook. [Saiba mais sobre as definições de esquema de alerta comuns.](https://aka.ms/commonAlertSchemaDefinitions)


A operação POST contém a carga e o esquema JSON a seguir para todos os alertas de métrica próximos mais novos quando um [grupo de ações](../../azure-monitor/platform/action-groups.md) configurado adequadamente é usado:

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
      "severity":"3",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "metricNamespace":"microsoft.storage/storageAccounts",
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

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a nova [experiência de alertas](../../azure-monitor/platform/alerts-overview.md).
* Saiba mais sobre os [alertas de log no Azure](../../azure-monitor/platform/alerts-unified-log.md).
* Saiba mais sobre [alertas no Azure](../../azure-monitor/platform/alerts-overview.md).
