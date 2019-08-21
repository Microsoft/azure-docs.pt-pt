---
title: Visão geral dos logs e das métricas do firewall do Azure
description: Este artigo é uma visão geral dos logs e das métricas de diagnóstico do firewall do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 8524c8f05a5d48755ab1ccca62f0fd53870190bb
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640250"
---
# <a name="azure-firewall-logs-and-metrics"></a>Métricas e logs de firewall do Azure

Pode monitorizar os registos do Azure Firewall com registos de firewall. Também pode utilizar os registos de atividades para auditar operações nos recursos do Azure Firewall.

Pode aceder a alguns destes registos através do portal. Os logs podem ser enviados para [Azure monitor logs](../azure-monitor/insights/azure-networking-analytics.md), armazenamento e hubs de eventos e analisados em logs de Azure monitor ou ferramentas diferentes, como Excel e Power bi.

As métricas são leves e podem dar suporte a cenários quase em tempo real, tornando-as úteis para alertas e detecção rápida de problemas. 

## <a name="diagnostic-logs"></a>Registos de diagnósticos

 Os seguintes registos de diagnóstico estão disponíveis para o Azure Firewall:

* **Registo de regra de Aplicação**

   O log de regras de aplicativo é salvo em uma conta de armazenamento, transmitida para os hubs de eventos e/ou enviados para Azure Monitor logs somente se você o tiver habilitado para cada Firewall do Azure. Cada nova ligação que corresponde a uma das suas regras de aplicação configuradas resulta num registo da ligação aceite/negada. Os dados são registados no formato JSON, conforme mostrado no exemplo seguinte:

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

   O log de regras de rede é salvo em uma conta de armazenamento, transmitida para os hubs de eventos e/ou enviados para Azure Monitor logs somente se você o tiver habilitado para cada Firewall do Azure. Cada nova ligação que corresponde a uma das suas regras de rede configuradas resulta num registo da ligação aceite/negada. Os dados são registados no formato JSON, conforme mostrado no exemplo seguinte:

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

* **Conta de armazenamento**: As contas de armazenamento são mais bem usadas para logs quando os logs são armazenados por uma duração maior e revisados quando necessário.
* **Hubs de eventos**: Os hubs de eventos são uma ótima opção para integração com outras ferramentas de SEIM (gerenciamento de informações e eventos de segurança) para obter alertas sobre seus recursos.
* **Logs de Azure monitor**: Os logs de Azure Monitor são mais bem usados para o monitoramento geral em tempo real de seu aplicativo ou a análise de tendências.

## <a name="activity-logs"></a>Registos de atividade

   As entradas de registos de atividades são recolhidas por predefinição e pode visualizá-las no portal do Azure.

   Você pode usar [os logs de atividades do Azure](../azure-resource-manager/resource-group-audit.md) (anteriormente conhecidos como logs operacionais e logs de auditoria) para exibir todas as operações enviadas à sua assinatura do Azure.

## <a name="metrics"></a>Métricas

As métricas em Azure Monitor são valores numéricos que descrevem algum aspecto de um sistema em um determinado momento. As métricas são coletadas a cada minuto e são úteis para alertas porque podem ser amostradas com frequência. Um alerta pode ser acionado rapidamente com lógica relativamente simples.

As seguintes métricas estão disponíveis para o Firewall do Azure:

- **Contagem** de ocorrências de regras de aplicativo-o número de vezes que uma regra de aplicativo foi atingida.

    Unidade: contagem

- **Dados processados** -quantidade de dados atravessando o firewall.

    Unidade: bytes

- **Estado de integridade do firewall** – indica a integridade do firewall.

    Unidade: porcentagem

   Essa métrica tem duas dimensões:
  - **Status**: Os valores possíveis são *íntegro*, *degradado*, não *íntegro*.
  - **Motivo**: Indica o motivo do status correspondente do firewall. Por exemplo, ele pode indicar *portas SNAT* se o status do firewall for degradado ou não íntegro.



- **Contagem** de ocorrências de regras de rede-o número de vezes que uma regra de rede foi atingida.

    Unidade: contagem

- **Utilização da porta SNAT** -a porcentagem de portas SNAT que foram utilizadas pelo firewall.

    Unidade: porcentagem

   Quando você adiciona mais endereços IP públicos ao firewall, mais portas SNAT estão disponíveis, reduzindo a utilização de portas SNAT. Além disso, quando o firewall é dimensionado por diferentes motivos (por exemplo, CPU ou taxa de transferência) portas SNAT adicionais também ficam disponíveis. De modo eficaz, um determinado percentual de utilização de portas SNAT pode ficar inativo sem a adição de endereços IP públicos, apenas porque o serviço foi expandido. Você pode controlar diretamente o número de endereços IP públicos disponíveis para aumentar as portas disponíveis no firewall. Mas, você não pode controlar diretamente o dimensionamento de firewall. Atualmente, as portas SNAT são adicionadas somente aos cinco primeiros endereços IP públicos.   


## <a name="next-steps"></a>Passos Seguintes

- Para saber como monitorar os logs e as métricas do firewall do [Azure, consulte o tutorial: Monitorar logs](tutorial-diagnostics.md)de firewall do Azure.

- Para saber mais sobre as métricas em Azure Monitor, consulte [métricas em Azure monitor](../azure-monitor/platform/data-platform-metrics.md).