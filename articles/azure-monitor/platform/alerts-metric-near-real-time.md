---
title: Recursos apoiados para alertas métricos no Azure Monitor
description: Referência sobre métricas de suporte e registos para alertas métricos no Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 7/26/2020
ms.subservice: alerts
ms.openlocfilehash: bbfbc5e92fc49e45d1d16621992dbf4483f01bb5
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87327162"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Recursos apoiados para alertas métricos no Azure Monitor

O Azure Monitor suporta agora um [novo tipo de alerta métrico](./alerts-overview.md) que tem benefícios significativos em relação aos mais [antigos alertas métricos clássicos.](./alerts-classic.overview.md) As métricas estão disponíveis para [uma grande lista de serviços Azure.](./metrics-supported.md) Os alertas mais recentes suportam um subconjunto (crescente) dos tipos de recursos. Este artigo lista o subconjunto.

Também pode utilizar alertas métricos mais recentes em dados de registo populares armazenados num espaço de trabalho log analytics extraído como métricas. Para mais informações, consulte [Alertas métricos para Registos](./alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Suporte portal, PowerShell, CLI, REST
Atualmente, pode criar alertas métricos mais recentes apenas no portal Azure, [REST API,](/rest/api/monitor/metricalerts/)ou [Modelos de Gestor de Recursos.](./alerts-metric-create-templates.md) O suporte para configurar alertas mais recentes utilizando as versões PowerShell e Azure CLI 2.0 e superiores está para breve.

## <a name="metrics-and-dimensions-supported"></a>Métricas e Dimensões Suportadas
Mais recentes alertas métricos alertam para métricas que usam dimensões. Pode utilizar as dimensões para filtrar a sua métrica para o nível certo. Todas as métricas suportadas juntamente com as dimensões aplicáveis podem ser exploradas e visualizadas a partir do [Azure Monitor - Metrics Explorer](./metrics-charts.md).

Aqui está a lista completa de fontes métricas do monitor Azure apoiadas pelos alertas mais recentes:

|Tipo de recurso  |Dimensões Suportadas |Alertas multi-recursos| Métricas disponíveis|
|---------|---------|-----|----------|
|Microsoft.ApiManagement/service | Sim | Não | [Gestão de API](./metrics-supported.md#microsoftapimanagementservice) |
|Microsoft.AppConfiguration/configurationStores |Sim | Não | [App Configuration](./metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft.AppPlatform/primavera | Sim | Não | [Azure Spring Cloud](./metrics-supported.md#microsoftappplatformspring) |
|Microsoft.Automation/automation | Sim| Não | [Contas de Automatização](./metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft.Batcontas ch/batch | Sim | Não | [Contas do Batch](./metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft.Cache/Redis | Sim | Não | [Cache do Azure para Redis](./metrics-supported.md#microsoftcacheredis) |
|Microsoft.ClassicStorage/storageAcontas | Sim | Não | [Contas de Armazenamento (clássicas)](./metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft.ClassicStorage/storageAccounts/blobServices | Sim | Não | |
|Microsoft.ClassicStorage/storageAcons/fileServices | Sim | Não | |
|Microsoft.ClassicStorage/storageAccounts/queueServices | Sim | Não | |
|Microsoft.ClassicStorage/storageAccounts/tableServices | Sim | Não | |
|Microsoft.CognitiveServices/contas | Sim | Não | [Serviços Cognitivos](./metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft.Compute/virtualMachines | Sim | Sim | [Máquinas Virtuais](./metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | Sim | Não |[Conjuntos de escala de máquina virtual](./metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft.ContainerInstance/containerGroups | Sim| Não | [Grupos de contentores](./metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft.ContainerRegistry/registries | Não | Não | [Registos de Contentores](./metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft.ContainerService/managedClusters | Sim | Não | [Clusters Geridos](./metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | Sim | Sim | [Data Box](./metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft.DataFactory/datafactories| Sim| Não | [Fábricas de Dados V1](./metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft.DataFactory/fábricas |Sim | Não | [Fábricas de Dados V2](./metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft.DataShare/contas | Sim | Não | |
|Microsoft.DBforMariaDB/servidores | Não | Não | [DB para MariaDB](./metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft.DBforMySQL/servidores | Não | Não |[DB para MySQL](./metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servidores | Não | Não | [DB para PostgreSQL](./metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.DBforPostgreSQL/serversv2 | Não | Não | [DB para PostgreSQL V2](./metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft.DBforPostgreSQL/singleservers | Não | Não | [DB para PostgreSQL (servidores individuais)](./metrics-supported.md#microsoftdbforpostgresqlsingleservers)|
|Microsoft.Devices/IotHubs | Sim | Não |[Hub IoT](./metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft.Devices/provisioningServices| Sim | Não | [Serviços de prestação de dispositivos](./metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft.DocumentDB/base de dadosAcontas | Sim | Não | [BD do Cosmos](./metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft.EventGrid/domínios | Sim | Não | [Domínios do Event Grid](./metrics-supported.md#microsofteventgriddomains) |
|Microsoft.EventGrid/systemTopics | Sim | Não | [Tópicos do sistema de grelha de eventos](./metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft.EventGrid/tópicos |Sim | Não | [Tópicos do Event Grid](./metrics-supported.md#microsofteventgridtopics) |
|Microsoft.EventHub/clusters |Sim| Não | [Clusters hubs de eventos](./metrics-supported.md#microsofteventhubclusters) |
|Microsoft.EventHub/espaços de nome |Sim| Não | [Hubs de Eventos](./metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft.HDInsight/clusters | Sim | Não | [Clusters do HDInsight](./metrics-supported.md#microsofthdinsightclusters) |
|Microsoft.Insights/Componentes | Sim | Não | [Application Insights](./metrics-supported.md#microsoftinsightscomponents) |
|Microsoft.KeyVault/cofres | Não |Não |[Cofres](./metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Kusto/Clusters | Sim |Não |[Clusters de Exploradores de Dados](./metrics-supported.md#microsoftkustoclusters)|
|Microsoft.Logic/workflows | Não | Não |[Logic Apps](./metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces | Sim | Não | [Machine Learning](./metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.Maps/contas | Sim | Não | [Contas de Mapas](./metrics-supported.md#microsoftmapsaccounts) |
|Microsoft.Media/mediaservices | Não | Não | [Serviços de Multimédia](./metrics-supported.md#microsoftmediamediaservices) |
|Microsoft.Media/mediaservices/streamingEndpoints | Sim | Não | [Pontos finais de streaming de serviços de mídia](./metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft.NetApp/netAppAccounts/capacityPools | Sim | Não | [Piscinas de capacidade Azure NetApp](./metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft.NetApp/netAppAccounts/capacityPools/volumes | Sim | Não | [Azure NetApp Volumes](./metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways | Sim | Não | [Gateways de aplicação](./metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/azurefirewalls | Sim | Não | [Firewalls](./metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft.Network/dnsZones | Não | Não | [Zonas DNS](./metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | N/D | Não |[Circuitos do ExpressRoute](./metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/loadBalancers (apenas para SKUs padrão)| Sim| Não | [Balançadores de Carga](./metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/natGateways| Não | Não | |
|Microsoft.Network/privateEndpoints| Não | Não | |
|Microsoft.Network/privateLinkServices| Não | Não |
|Microsoft.Network/publicipaddresss | Não | Não |[Endereços IP públicos](./metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Sim | Não | [Perfis do Gestor de Tráfego](./metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Sim | Não | [Áreas de trabalho do Log Analytics](./metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.Peering/peerings | Sim | Não | [Peerings](./metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft.Peering/peeringServices | Sim | Não | [Peering Services](./metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft.PowerBIDedicated/capacities | Não | Não | [Capacidades](./metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft.Relay/namespaces | Sim | Não | [Reencaminhamentos](./metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft.Search/searchServices | Não | Não | [Serviços de pesquisa](./metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft.ServiceBus/namespaces | Sim | Não | [Service Bus](./metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft.Sql/managedInstances | Não | Sim | [Instâncias Geridas SQL](./metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft.Sql/servidores/bases de dados | Não | Sim | [Bases de Dados SQL](./metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft.Sql/servidores/elásticos | Não | Sim | [Piscinas Elásticas SQL](./metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft.Storage/storageAcontas |Sim | Não | [Contas de armazenamento](./metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAcontas/serviços | Sim| Não | [Serviços Blob](./metrics-supported.md#microsoftstoragestorageaccountsblobservices), [Serviços de Arquivo,](./metrics-supported.md#microsoftstoragestorageaccountsfileservices) [Serviços de Fila](./metrics-supported.md#microsoftstoragestorageaccountsqueueservices) e [Serviços de Mesa](./metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StorageCache/caches | Sim | Não | |
|Microsoft.StorageSync/storageSyncServices | Sim | Não | [Serviços de Sincronização de Armazenamento](./metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft.StreamAnalytics/streamingjobs | Sim | Não | [Stream Analytics](./metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft.VMWareCloudSimple/virtualMachines | Sim | Não | [Máquinas Virtuais do CloudSimple](./metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft.Web/hostingEnvironments/multiRolePools | Sim | Não | [Piscinas multi-role de ambiente de serviço de aplicativos](./metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | Sim | Não | [Piscinas de trabalhadores do ambiente do serviço de aplicações](./metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | Sim | Não | [Planos de Serviço de Aplicações](./metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | Sim | Não | [Serviços](./metrics-supported.md#microsoftwebsites-excluding-functions) e [Funções de Aplicativos](./metrics-supported.md#microsoftwebsites-functions)|
|Microsoft.Web/sites/slots | Sim | Não | [Slots do Serviço de Aplicações](./metrics-supported.md#microsoftwebsitesslots)|

## <a name="payload-schema"></a>Esquema de carga útil

> [!NOTE]
> Também pode utilizar o [esquema de alerta comum,](https://aka.ms/commonAlertSchemaDocs)que proporciona a vantagem de ter uma única carga de alerta extensível e unificada em todos os serviços de alerta em Azure Monitor, para as suas integrações webhook. [Conheça as definições comuns de esquema de alerta.](https://aka.ms/commonAlertSchemaDefinitions)


A operação POST contém a seguinte carga útil e esquema JSON para todos os alertas métricos mais recentes quando é utilizado um [grupo de ação](./action-groups.md) devidamente configurado:

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

* Saiba mais sobre a nova [experiência Alertas.](./alerts-overview.md)
* Saiba mais sobre [os alertas de registo em Azure](./alerts-unified-log.md).
* Conheça os [alertas em Azure.](./alerts-overview.md)

