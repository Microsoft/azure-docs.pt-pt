---
title: Recursos suportados para alertas métricos no Monitor Do Azure
description: Referência sobre métricas de suporte e registos para alertas métricos no Monitor Azure
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 3/5/2020
ms.subservice: alerts
ms.openlocfilehash: 62c1e29b668c3ef58b0e26e2749c55b913279425
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545684"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Recursos suportados para alertas métricos no Monitor Do Azure

O Azure Monitor suporta agora um [novo tipo de alerta métrico](../../azure-monitor/platform/alerts-overview.md) que tem benefícios significativos em relação aos [alertas métricos clássicos mais antigos.](../../azure-monitor/platform/alerts-classic.overview.md) As métricas estão disponíveis para [uma grande lista de serviços Azure.](../../azure-monitor/platform/metrics-supported.md) Os alertas mais recentes suportam um subconjunto (crescente) dos tipos de recursos. Este artigo lista o subconjunto.

Também pode utilizar novos alertas métricos em dados de registo populares armazenados num espaço de trabalho de Log Analytics extraído como métricas. Para mais informações, consulte [alertas métricos para registos](../../azure-monitor/platform/alerts-metric-logs.md).

## <a name="portal-powershell-cli-rest-support"></a>Portal, PowerShell, CLI, suporte REST
Atualmente, pode criar novos alertas métricos apenas no portal Azure, [REST API,](https://docs.microsoft.com/rest/api/monitor/metricalerts/)ou [Modelos](../../azure-monitor/platform/alerts-metric-create-templates.md)de Gestor de Recursos . O suporte para configurar novos alertas utilizando as versões PowerShell e Azure CLI 2.0 e superior está para breve.

## <a name="metrics-and-dimensions-supported"></a>Métricas e Dimensões Suportadas
Alertas métricos mais recentes suportam alertas para métricas que usam dimensões. Pode utilizar dimensões para filtrar a sua métrica para o nível certo. Todas as métricas suportadas, juntamente com as dimensões aplicáveis, podem ser exploradas e visualizadas a partir do [Azure Monitor - Metrics Explorer](../../azure-monitor/platform/metrics-charts.md).

Aqui está a lista completa de fontes métricas de monitor estoque azure suportadas pelos novos alertas:

|Tipo de recurso  |Dimensões Suportadas |Alertas de vários recursos| Métricas Disponíveis|
|---------|---------|-----|----------|
|Microsoft.ApiManagement/service | Sim| Não | [Gestão de API](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft.AppPlatform/Spring |Não| Sim|
|Microsoft.Automation/automationAccounts | Sim| Não | [Contas de Automatização](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | N/D| Não | [Contas do Batch](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis|Sim| Não |[Cache do Azure para Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic|Não|Sim|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/blobServices|Não|Sim|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/fileServices|Não|Sim|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/queueServices|Não|Sim|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/tableServices|Não|Sim| |
|Microsoft.CognitiveServices/accounts| N/D | Não | [Serviços Cognitivos](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines |Sim | Sim | [Máquinas Virtuais](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets |N/D | Sim |[Conjuntos de dimensionamento de máquinas virtuais](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Sim| Não | [Grupos de contentores](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Sim | Não | [Clusters Geridos](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | Sim | Sim | |
|Microsoft.DataFactory/datafactores| Sim| Não | [Fábricas de Dados V1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/fábricas |Sim | Não |[Fábricas de Dados V2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft.DataShare/contas |Não| Sim|
|Microsoft.DBforMySQL/servidores |N/D| Não |[DB para MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servidores |N/D | Não | [DB para PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs | N/D | Não |[Métricas do Hub IoT](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)|
|Microsoft.Dispositivos/serviços de provisionamento| Sim | Não |[Métricas DPS](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)|
|Microsoft.EventGrid/domínios|Não|Sim| |
|Microsoft.EventGrid/tópicos |Sim | Não |[Tópicos da Grelha de Eventos](../../azure-monitor/platform/metrics-supported.md#microsofteventgridtopics)|
|Microsoft.EventHub/clusters |Sim| Não |[Aglomerados de Hubs de Eventos](../../azure-monitor/platform/metrics-supported.md#microsofteventhubclusters)|
|Microsoft.EventHub/espaços de nome |Sim| Não |[Hubs de Eventos](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/cofres| Não |Não |[Cofres](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows |N/D | Não |[Aplicações Lógicas](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/espaços de trabalho|Sim| Não | [Aprendizagem automática](../../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.NetApp/netAppAccounts/capacityPools |Sim| Não | [Piscinas de Capacidade Azure NetApp](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft.NetApp/netAppAccounts/capacityPools/volumes |Sim| Não | [Azure NetApp Volumes](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways|N/D| Não |  |
|Microsoft.Network/dnsZones | N/D| Não | [Zonas DNS](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | N/D | Não |[Circuitos do ExpressRoute](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/loadBalancers (apenas para SKUs standard)| Sim| Não | [Balanceadores de carga](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/natGateways|Não|Sim|
|Microsoft.Network/privateEndpoints|Não|Sim|
|Microsoft.Network/privateLinkServices|Não|Sim|
|Microsoft.Network/publicipaddresss |N/D | Não |[Endereços IP públicos](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Sim | Não | [Perfis do Gestor de Tráfego](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/espaços de trabalho| Sim | Não | [Áreas de trabalho do Log Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.Relay/namespaces | Sim | Não | [Reencaminhamentos](../../azure-monitor/platform/metrics-supported.md#microsoftrelaynamespaces)|
|Microsoft.Peering/peeringServices|Não|Sim|
|Microsoft.PowerBIDedicated/capacities | N/D | Não | [Capacidades](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices |N/D|Não | [Serviços de pesquisa](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/espaços de nome |Sim| Não |[Service Bus](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Sql/servidores/elasticPools |    Não | Sim |
|Microsoft.Sql/servidores/bases de dados    | Não | Sim |
|Microsoft.Storage/storageAccounts |Sim | Não | [Contas de Armazenamento](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Armazenamento/armazenamentoContas/serviços | Sim| Não | [Serviços Blob,](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices) [Serviços de Arquivo,](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices) [Serviços de Fila](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) e [Serviços de Mesa](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs |N/D| Não | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.Microsoft.VMWareCloudSimple/virtualMachines |Sim|Não |[Máquinas Virtuais do CloudSimple](../../azure-monitor/platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines)|
|Microsoft.Web/hostingEnvironments/multiRolePools | Sim | Não | [App Service Ambiente Piscinas multi-funções](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | Sim | Não | [Piscinas de trabalhadores do ambiente do serviço de aplicativos](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | Sim | Não | [Planos de Serviço de Aplicações](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | Sim | Não | [Serviços Aplicacionais](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites)|
|Microsoft.Web/sites/slots | Sim | Não | [Slots de serviço de aplicativos](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|

## <a name="payload-schema"></a>Esquema de carga útil

> [!NOTE]
> Também pode utilizar o esquema de [alerta comum,](https://aka.ms/commonAlertSchemaDocs)que proporciona a vantagem de ter uma única carga de alerta extensível e unificada em todos os serviços de alerta no Monitor Azure, para as suas integrações no webhook. [Conheça as definições comuns de esquemade alerta.](https://aka.ms/commonAlertSchemaDefinitions)


A operação POST contém a seguinte carga útil e esquema JSON para todos os alertas métricos mais recentes quando um grupo de [ação](../../azure-monitor/platform/action-groups.md) devidamente configurado é utilizado:

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

* Saiba mais sobre a nova [experiência alerts.](../../azure-monitor/platform/alerts-overview.md)
* Saiba mais sobre alertas de [log em Azure.](../../azure-monitor/platform/alerts-unified-log.md)
* Saiba mais sobre [alertas em Azure.](../../azure-monitor/platform/alerts-overview.md)
