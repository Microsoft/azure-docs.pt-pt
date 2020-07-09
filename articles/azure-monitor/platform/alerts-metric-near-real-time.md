---
title: Recursos apoiados para alertas métricos no Azure Monitor
description: Referência sobre métricas de suporte e registos para alertas métricos no Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 3/5/2020
ms.subservice: alerts
ms.openlocfilehash: c036fa3708d718d6199d27989e60b11015a1227e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80585859"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Recursos apoiados para alertas métricos no Azure Monitor

O Azure Monitor suporta agora um [novo tipo de alerta métrico](../../azure-monitor/platform/alerts-overview.md) que tem benefícios significativos em relação aos mais [antigos alertas métricos clássicos.](../../azure-monitor/platform/alerts-classic.overview.md) As métricas estão disponíveis para [uma grande lista de serviços Azure.](../../azure-monitor/platform/metrics-supported.md) Os alertas mais recentes suportam um subconjunto (crescente) dos tipos de recursos. Este artigo lista o subconjunto.

Também pode utilizar alertas métricos mais recentes em dados de registo populares armazenados num espaço de trabalho log analytics extraído como métricas. Para mais informações, consulte [Alertas métricos para Registos](../../azure-monitor/platform/alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Suporte portal, PowerShell, CLI, REST
Atualmente, pode criar alertas métricos mais recentes apenas no portal Azure, [REST API,](https://docs.microsoft.com/rest/api/monitor/metricalerts/)ou [Modelos de Gestor de Recursos.](../../azure-monitor/platform/alerts-metric-create-templates.md) O suporte para configurar alertas mais recentes utilizando as versões PowerShell e Azure CLI 2.0 e superiores está para breve.

## <a name="metrics-and-dimensions-supported"></a>Métricas e Dimensões Suportadas
Mais recentes alertas métricos alertam para métricas que usam dimensões. Pode utilizar as dimensões para filtrar a sua métrica para o nível certo. Todas as métricas suportadas juntamente com as dimensões aplicáveis podem ser exploradas e visualizadas a partir do [Azure Monitor - Metrics Explorer](../../azure-monitor/platform/metrics-charts.md).

Aqui está a lista completa de fontes métricas do monitor Azure apoiadas pelos alertas mais recentes:

|Tipo de recurso  |Dimensões Suportadas |Alertas multi-recursos| Métricas disponíveis|
|---------|---------|-----|----------|
|Microsoft.ApiManagement/service | Yes| Não | [Gestão de API](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft.AppPlatform/primavera |Não| Yes|
|Microsoft.Automation/automation | Yes| Não | [Contas de Automatização](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batcontas ch/batch | N/D| No | [Contas do Batch](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis|Yes| Não |[Cache do Azure para Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic|Não|Yes|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/blobServices|Não|Yes|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/fileServices|Não|Yes|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/queueServices|Não|Yes|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/tableServices|Não|Yes| |
|Microsoft.CognitiveServices/contas| N/D | No | [Serviços Cognitivos](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines |Sim | Sim | [Máquinas Virtuais](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets |N/D | Yes |[Conjuntos de escala de máquina virtual](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Yes| Não | [Grupos de contentores](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Yes | Não | [Clusters Geridos](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | Sim | Sim | |
|Microsoft.DataFactory/datafactories| Yes| Não | [Fábricas de Dados V1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/fábricas |Yes | Não |[Fábricas de Dados V2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft.DataShare/contas |Não| Yes|
|Microsoft.DBforMySQL/servidores |N/D| No |[DB para MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servidores |N/D | No | [DB para PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs | N/D | No |[Métricas do Hub IoT](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)|
|Microsoft.Devices/provisioningServices| Sim | No |[Métricas de DPS](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)|
|Microsoft.EventGrid/domínios|No|Sim| |
|Microsoft.EventGrid/tópicos |Sim | No |[Tópicos do Event Grid](../../azure-monitor/platform/metrics-supported.md#microsofteventgridtopics)|
|Microsoft.EventHub/clusters |Sim| No |[Clusters hubs de eventos](../../azure-monitor/platform/metrics-supported.md#microsofteventhubclusters)|
|Microsoft.EventHub/espaços de nome |Sim| No |[Hubs de Eventos](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/cofres| Não |Não |[Cofres](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows |N/D | No |[Logic Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces|Sim| No | [Machine Learning](../../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.NetApp/netAppAccounts/capacityPools |Sim| No | [Piscinas de capacidade Azure NetApp](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft.NetApp/netAppAccounts/capacityPools/volumes |Sim| No | [Azure NetApp Volumes](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways|N/D| No |  |
|Microsoft.Network/dnsZones | N/D| No | [Zonas DNS](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | N/D | No |[Circuitos do ExpressRoute](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/loadBalancers (apenas para SKUs padrão)| Sim| No | [Balançadores de Carga](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/natGateways|No|Sim|
|Microsoft.Network/privateEndpoints|No|Sim|
|Microsoft.Network/privateLinkServices|No|Sim|
|Microsoft.Network/publicipaddresss |N/D | No |[Endereços IP públicos](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Sim | No | [Perfis do Gestor de Tráfego](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Sim | No | [Áreas de trabalho do Log Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.Relay/namespaces | Sim | No | [Reencaminhamentos](../../azure-monitor/platform/metrics-supported.md#microsoftrelaynamespaces)|
|Microsoft.Peering/peeringServices|No|Sim|
|Microsoft.PowerBIDedicated/capacities | N/D | No | [Capacidades](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices |N/D|No | [Serviços de pesquisa](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces |Sim| No |[Service Bus](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Sql/servidores/elásticos |    No | Sim |
|Microsoft.Sql/servidores/bases de dados    | No | Sim |
|Microsoft.Storage/storageAcontas |Sim | Não | [Contas de Armazenamento](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAcontas/serviços | Sim| Não | [Serviços Blob](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices), [Serviços de Arquivo,](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices) [Serviços de Fila](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) e [Serviços de Mesa](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs |N/D| No | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.Microsoft.VMWareCloudSimple/virtualMachines |Sim|Não |[Máquinas Virtuais do CloudSimple](../../azure-monitor/platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines)|
|Microsoft.Web/hostingEnvironments/multiRolePools | Sim | Não | [Piscinas multi-role de ambiente de serviço de aplicativos](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | Sim | Não | [Piscinas de trabalhadores do ambiente do serviço de aplicações](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | Sim | Não | [Planos de Serviço de Aplicações](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | Sim | Não | [Serviços](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) e [Funções de Aplicativos](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
|Microsoft.Web/sites/slots | Sim | Não | [Slots do Serviço de Aplicações](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|

## <a name="payload-schema"></a>Esquema de carga útil

> [!NOTE]
> Também pode utilizar o [esquema de alerta comum,](https://aka.ms/commonAlertSchemaDocs)que proporciona a vantagem de ter uma única carga de alerta extensível e unificada em todos os serviços de alerta em Azure Monitor, para as suas integrações webhook. [Conheça as definições comuns de esquema de alerta.](https://aka.ms/commonAlertSchemaDefinitions)


A operação POST contém a seguinte carga útil e esquema JSON para todos os alertas métricos mais recentes quando é utilizado um [grupo de ação](../../azure-monitor/platform/action-groups.md) devidamente configurado:

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

* Saiba mais sobre a nova [experiência Alertas.](../../azure-monitor/platform/alerts-overview.md)
* Saiba mais sobre [os alertas de registo em Azure](../../azure-monitor/platform/alerts-unified-log.md).
* Conheça os [alertas em Azure.](../../azure-monitor/platform/alerts-overview.md)
