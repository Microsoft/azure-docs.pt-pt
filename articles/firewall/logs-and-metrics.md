---
title: Visão geral dos logs e das métricas do firewall do Azure
description: Pode monitorizar os registos do Azure Firewall com registos de firewall. Também pode utilizar os registos de atividades para auditar operações nos recursos do Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: f233b1a60202b440abf34edd1c56eebaecba18e2
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166995"
---
# <a name="azure-firewall-logs-and-metrics"></a>Registos e métricas do Azure Firewall

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

* **Conta de armazenamento**: as contas de armazenamento são ideais para os registos quando estes são armazenados durante um período mais longo e revistos quando necessário.
* **Hubs de eventos**: os Hubs de eventos são uma excelente opção para integrar com outras informações de segurança e ferramentas de gestão de eventos (SEIM) para obter alertas sobre os seus recursos.
* **Logs de Azure monitor**: os logs de Azure monitor são mais bem usados para o monitoramento geral em tempo real de seu aplicativo ou a análise de tendências.

## <a name="activity-logs"></a>Registos de atividade

   As entradas de registos de atividades são recolhidas por predefinição e pode visualizá-las no portal do Azure.

   Você pode usar [os logs de atividades do Azure](../azure-resource-manager/resource-group-audit.md) (anteriormente conhecidos como logs operacionais e logs de auditoria) para exibir todas as operações enviadas à sua assinatura do Azure.

## <a name="metrics"></a>Métricas

As métricas em Azure Monitor são valores numéricos que descrevem algum aspecto de um sistema em um determinado momento. As métricas são coletadas a cada minuto e são úteis para alertas porque podem ser amostradas com frequência. Um alerta pode ser acionado rapidamente com lógica relativamente simples.

As seguintes métricas estão disponíveis para o Firewall do Azure:

- **Contagem de ocorrências de regras de aplicativo** -o número de vezes que uma regra de aplicativo foi atingida.

    Unidade: contagem

- **Contagem de ocorrências de regras de rede** -o número de vezes que uma regra de rede foi atingida.

    Unidade: contagem

- **Dados processados** -quantidade de dados atravessando o firewall.

    Unidade: bytes

- **Estado de integridade do firewall** – indica a integridade do firewall.

    Unidade: porcentagem

   Essa métrica tem duas dimensões:
  - **Status**: os valores possíveis estão *íntegros*, *degradados*, não *íntegros*.
  - **Motivo**: indica o motivo do status correspondente do firewall. Por exemplo, ele pode indicar *portas SNAT* se o status do firewall for degradado ou não íntegro.





- **Utilização da porta SNAT** -a porcentagem de portas SNAT que foram utilizadas pelo firewall.

    Unidade: porcentagem

   Quando você adiciona mais endereços IP públicos ao firewall, mais portas SNAT estão disponíveis, reduzindo a utilização de portas SNAT. Além disso, quando o firewall é dimensionado por diferentes motivos (por exemplo, CPU ou taxa de transferência) portas SNAT adicionais também ficam disponíveis. De modo eficaz, um determinado percentual de utilização de portas SNAT pode ficar inativo sem a adição de endereços IP públicos, apenas porque o serviço foi expandido. Você pode controlar diretamente o número de endereços IP públicos disponíveis para aumentar as portas disponíveis no firewall. Mas, você não pode controlar diretamente o dimensionamento de firewall. Atualmente, as portas SNAT são adicionadas somente aos cinco primeiros endereços IP públicos.   


## <a name="next-steps"></a>Passos seguintes

- Para saber como monitorar os logs e as métricas do firewall do Azure, consulte [tutorial: monitorar logs de firewall do Azure](tutorial-diagnostics.md).

- Para saber mais sobre as métricas em Azure Monitor, consulte [métricas em Azure monitor](../azure-monitor/platform/data-platform-metrics.md).