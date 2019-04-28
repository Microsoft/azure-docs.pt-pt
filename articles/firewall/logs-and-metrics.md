---
title: Descrição geral dos registos de Firewall do Azure
description: Este artigo é uma visão geral dos registos de diagnóstico do Firewall do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: c129c394f3d694b832722287027c1f9e58028a33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61065857"
---
# <a name="azure-firewall-logs"></a>Registos de Firewall do Azure

Pode monitorizar os registos do Azure Firewall com registos de firewall. Também pode utilizar os registos de atividades para auditar operações nos recursos do Azure Firewall.

Pode aceder a alguns destes registos através do portal. Os registos podem ser enviados para [registos do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md), armazenamento e os Hubs de eventos e analisados no Azure Monitor registos ou através de ferramentas diferentes, como o Excel e o Power BI.

## <a name="diagnostic-logs"></a>Registos de diagnósticos

 Os seguintes registos de diagnóstico estão disponíveis para o Azure Firewall:

* **Registo de regra de Aplicação**

   O registo de regra de aplicação é guardado para uma conta de armazenamento, transmitidos em fluxo aos hubs de eventos e/ou enviados para os registos do Azure Monitor apenas se está ativado para cada Firewall do Azure. Cada nova ligação que corresponde a uma das suas regras de aplicação configuradas resulta num registo da ligação aceite/negada. Os dados são registados no formato JSON, conforme mostrado no exemplo seguinte:

   ```
   Category: application rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **Registo de regra de Rede**

   O registo de regra de rede é guardado para uma conta de armazenamento, transmitidos em fluxo aos hubs de eventos e/ou enviados para os registos do Azure Monitor apenas se está ativado para cada Firewall do Azure. Cada nova ligação que corresponde a uma das suas regras de rede configuradas resulta num registo da ligação aceite/negada. Os dados são registados no formato JSON, conforme mostrado no exemplo seguinte:

   ```
   Category: network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

Tem três opções para armazenar os registos:

* **Conta de armazenamento**: Contas de armazenamento são melhor utilizadas para os registos quando os registos são armazenados durante um período mais longo e revisados quando necessário.
* **Os hubs de eventos**: Os hubs de eventos são uma ótima opção para integrar com outras ferramentas de gestão (SEIM) de informações e eventos da segurança para obter alertas sobre seus recursos.
* **Registos de Monitor do Azure**: Registos de Monitor do Azure melhor é utilizada para monitorização em tempo real gerais da sua aplicação ou ao procurar tendências.

## <a name="activity-logs"></a>Registos de atividade

   As entradas de registos de atividades são recolhidas por predefinição e pode visualizá-las no portal do Azure.

   Pode utilizar os [registos de atividades do Azure](../azure-resource-manager/resource-group-audit.md) (anteriormente conhecidos como registos operacionais e registos de auditoria) para ver todas as operações que são submetidas para a sua subscrição do Azure.


## <a name="next-steps"></a>Passos Seguintes

Para saber como monitorizar as métricas e registos de Firewall do Azure, veja [Tutorial: Monitorize registos de Firewall do Azure](tutorial-diagnostics.md).