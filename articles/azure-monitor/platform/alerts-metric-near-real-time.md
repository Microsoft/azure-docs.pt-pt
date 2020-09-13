---
title: Recursos apoiados para alertas métricos no Azure Monitor
description: Referência sobre métricas de suporte e registos para alertas métricos no Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 8/20/2020
ms.subservice: alerts
ms.openlocfilehash: 8a608e43ef41abfb7002811df5629eb789c3ae22
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89595585"
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
|Microsoft.Aadiam/azureADMetrics | Yes | No | |
|Microsoft.ApiManagement/service | Yes | No | [Gestão de API](./metrics-supported.md#microsoftapimanagementservice) |
|Microsoft.AppConfiguration/configurationStores |Yes | No | [App Configuration](./metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Microsoft.AppPlatform/primavera | Yes | No | [Azure Spring Cloud](./metrics-supported.md#microsoftappplatformspring) |
|Microsoft.Automation/automation | Yes| No | [Contas de Automatização](./metrics-supported.md#microsoftautomationautomationaccounts) |
|Microsoft.AVS/privateClouds | No | No | |
|Microsoft.Batcontas ch/batch | Yes | No | [Contas do Batch](./metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft.Cache/Redis | Yes | No | [Cache do Azure para Redis](./metrics-supported.md#microsoftcacheredis) |
|Microsoft.ClassicStorage/storageAcontas | Yes | No | [Contas de Armazenamento (clássicas)](./metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Microsoft.ClassicStorage/storageAccounts/blobServices | Yes | No | |
|Microsoft.ClassicStorage/storageAcons/fileServices | Yes | No | |
|Microsoft.ClassicStorage/storageAccounts/queueServices | Yes | No | |
|Microsoft.ClassicStorage/storageAccounts/tableServices | Yes | No | |
|Microsoft.CognitiveServices/contas | Yes | No | [Serviços Cognitivos](./metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft.Compute/virtualMachines | Yes | Sim<sup>1</sup> | [Máquinas Virtuais](./metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | Yes | No |[Conjuntos de escala de máquina virtual](./metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Microsoft.ContainerInstance/containerGroups | Yes| No | [Grupos de contentores](./metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Microsoft.ContainerRegistry/registries | No | No | [Registos de Contentores](./metrics-supported.md#microsoftcontainerregistryregistries) |
|Microsoft.ContainerService/managedClusters | Yes | No | [Clusters Geridos](./metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | Yes | Yes | [Data Box](./metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Microsoft.DataFactory/datafactories| Yes| No | [Fábricas de Dados V1](./metrics-supported.md#microsoftdatafactorydatafactories) |
|Microsoft.DataFactory/fábricas |Yes | No | [Fábricas de Dados V2](./metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft.DataShare/contas | Yes | No | |
|Microsoft.DBforMariaDB/servidores | No | No | [DB para MariaDB](./metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft.DBforMySQL/servidores | No | No |[DB para MySQL](./metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/flexibleServers | Yes | No | |
|Microsoft.DBforPostgreSQL/servidores | No | No | [DB para PostgreSQL](./metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.DBforPostgreSQL/serversv2 | No | No | [DB para PostgreSQL V2](./metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft.DBforPostgreSQL/singleservers | No | No | [DB para PostgreSQL (servidores individuais)](./metrics-supported.md#microsoftdbforpostgresqlsingleservers)|
|Microsoft.Devices/IotHubs | Yes | No |[Hub IoT](./metrics-supported.md#microsoftdevicesiothubs) |
|Microsoft.Devices/provisioningServices| Yes | No | [Serviços de prestação de dispositivos](./metrics-supported.md#microsoftdevicesprovisioningservices) |
|Microsoft.DigitalTwins/digitalTwinsInstances | Yes | No | |
|Microsoft.DocumentDB/base de dadosAcontas | Yes | No | [BD do Cosmos](./metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Microsoft.EventGrid/domínios | Yes | No | [Domínios do Event Grid](./metrics-supported.md#microsofteventgriddomains) |
|Microsoft.EventGrid/systemTopics | Yes | No | [Tópicos do sistema de grelha de eventos](./metrics-supported.md#microsofteventgridsystemtopics) |
|Microsoft.EventGrid/tópicos |Yes | No | [Tópicos do Event Grid](./metrics-supported.md#microsofteventgridtopics) |
|Microsoft.EventHub/clusters |Yes| No | [Clusters hubs de eventos](./metrics-supported.md#microsofteventhubclusters) |
|Microsoft.EventHub/espaços de nome |Yes| No | [Hubs de Eventos](./metrics-supported.md#microsofteventhubnamespaces) |
|Microsoft.HDInsight/clusters | Yes | No | [Clusters do HDInsight](./metrics-supported.md#microsofthdinsightclusters) |
|Microsoft.Insights/Componentes | Yes | No | [Application Insights](./metrics-supported.md#microsoftinsightscomponents) |
|Microsoft.KeyVault/cofres | Yes |Yes |[Cofres](./metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Kusto/Clusters | Yes |No |[Clusters de Exploradores de Dados](./metrics-supported.md#microsoftkustoclusters)|
|Microsoft.Logic/integrationServiceEnvironments | Yes | No |[Ambientes de Serviço de Integração](./metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft.Logic/workflows | No | No |[Logic Apps](./metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces | Yes | No | [Machine Learning](./metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.Maps/contas | Yes | No | [Contas de Mapas](./metrics-supported.md#microsoftmapsaccounts) |
|Microsoft.Media/mediaservices | No | No | [Serviços de Multimédia](./metrics-supported.md#microsoftmediamediaservices) |
|Microsoft.Media/mediaservices/streamingEndpoints | Yes | No | [Pontos finais de streaming de serviços de mídia](./metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Microsoft.NetApp/netAppAccounts/capacityPools | Yes | Yes | [Piscinas de capacidade Azure NetApp](./metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft.NetApp/netAppAccounts/capacityPools/volumes | Yes | Yes | [Azure NetApp Volumes](./metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways | Yes | No | [Gateways de aplicação](./metrics-supported.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/azurefirewalls | Yes | No | [Firewalls](./metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft.Network/dnsZones | No | No | [Zonas DNS](./metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | N/D | No |[Circuitos do ExpressRoute](./metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/loadBalancers (apenas para SKUs padrão)| Yes| No | [Balançadores de Carga](./metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/natGateways| No | No | |
|Microsoft.Network/privateEndpoints| No | No | |
|Microsoft.Network/privateLinkServices| No | No |
|Microsoft.Network/publicipaddresss | No | No |[Endereços IP públicos](./metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Yes | No | [Perfis do Gestor de Tráfego](./metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Yes | No | [Áreas de trabalho do Log Analytics](./metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.Peering/peerings | Yes | No | [Peerings](./metrics-supported.md#microsoftpeeringpeerings) |
|Microsoft.Peering/peeringServices | Yes | No | [Peering Services](./metrics-supported.md#microsoftpeeringpeeringservices) |
|Microsoft.PowerBIDedicated/capacities | No | No | [Capacidades](./metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Microsoft.Relay/namespaces | Yes | No | [Reencaminhamentos](./metrics-supported.md#microsoftrelaynamespaces) |
|Microsoft.Search/searchServices | No | No | [Serviços de pesquisa](./metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft.ServiceBus/namespaces | Yes | No | [Service Bus](./metrics-supported.md#microsoftservicebusnamespaces) |
|Microsoft.Sql/managedInstances | No | Yes | [Instâncias Geridas SQL](./metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft.Sql/servidores/bases de dados | No | Yes | [Bases de Dados SQL](./metrics-supported.md#microsoftsqlserversdatabases) |
|Microsoft.Sql/servidores/elásticos | No | Yes | [Piscinas Elásticas SQL](./metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft.Storage/storageAcontas |Yes | No | [Contas de Armazenamento](./metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAcontas/serviços | Yes| No | [Serviços Blob](./metrics-supported.md#microsoftstoragestorageaccountsblobservices), [Serviços de Arquivo,](./metrics-supported.md#microsoftstoragestorageaccountsfileservices) [Serviços de Fila](./metrics-supported.md#microsoftstoragestorageaccountsqueueservices) e [Serviços de Mesa](./metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StorageCache/caches | Yes | No | |
|Microsoft.StorageSync/storageSyncServices | Yes | No | [Serviços de Sincronização de Armazenamento](./metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Microsoft.StreamAnalytics/streamingjobs | Yes | No | [Stream Analytics](./metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Microsoft.VMWareCloudSimple/virtualMachines | Yes | No | [Máquinas Virtuais do CloudSimple](./metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Microsoft.Web/hostingEnvironments/multiRolePools | Yes | No | [Piscinas multi-role de ambiente de serviço de aplicativos](./metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | Yes | No | [Piscinas de trabalhadores do ambiente do serviço de aplicações](./metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | Yes | No | [Planos de Serviço de Aplicações](./metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | Yes | No | [Serviços](./metrics-supported.md#microsoftwebsites-excluding-functions) e [Funções de Aplicativos](./metrics-supported.md#microsoftwebsites-functions)|
|Microsoft.Web/sites/slots | Yes | No | [Slots do Serviço de Aplicações](./metrics-supported.md#microsoftwebsitesslots)|

<sup>1</sup> Não suportado para métricas de rede de máquinas virtuais (Rede Total, Rede Fora Total, Fluxos de Entrada, Fluxos de Saída, Fluxos de Entrada Taxa máxima de criação, Taxa máxima de criação de fluxos de saída) e métricas personalizadas.

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

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre a nova [experiência Alertas.](./alerts-overview.md)
* Saiba mais sobre [os alertas de registo em Azure](./alerts-unified-log.md).
* Conheça os [alertas em Azure.](./alerts-overview.md)

