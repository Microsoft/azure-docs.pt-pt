---
title: Visão geral dos registos e métricas da Firewall Azure
description: Pode monitorizar os registos do Azure Firewall com registos de firewall. Também pode utilizar os registos de atividades para auditar operações nos recursos do Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 8922e6e0d5137a3a900e0f57f685d449c08b3f47
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596954"
---
# <a name="azure-firewall-logs-and-metrics"></a>Registos e métricas do Azure Firewall

Pode monitorizar os registos do Azure Firewall com registos de firewall. Também pode utilizar os registos de atividades para auditar operações nos recursos do Azure Firewall.

Pode aceder a alguns destes registos através do portal. Os registos podem ser enviados para [registos do Monitor Azure,](../azure-monitor/insights/azure-networking-analytics.md)Centros de Armazenamento e Evento e analisados em registos do Azure Monitor ou por diferentes ferramentas como o Excel e o Power BI.

As métricas são leves e podem suportar cenários perto de tempo real tornando-os úteis para alertar e detetar rapidamente problemas.

## <a name="diagnostic-logs"></a>Registos de diagnósticos

 Os seguintes registos de diagnóstico estão disponíveis para o Azure Firewall:

* **Registo de regra de Aplicação**

   O registo de regras de aplicação é guardado numa conta de armazenamento, transmitido para centros de eventos e/ou enviado para registos do Azure Monitor apenas se o tiver ativado para cada Firewall Azure. Cada nova ligação que corresponde a uma das suas regras de aplicação configuradas resulta num registo da ligação aceite/negada. Os dados são registados no formato JSON, como mostram os seguintes exemplos:

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

   ```json
   {
     "category": "AzureFirewallApplicationRule",
     "time": "2018-04-16T23:45:04.8295030Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallApplicationRuleLog",
     "properties": {
         "msg": "HTTPS request from 10.11.2.4:53344 to www.bing.com:443. Action: Allow. Rule Collection: ExampleRuleCollection. Rule: ExampleRule. Web Category: SearchEnginesAndPortals"
     }
   }
   ```

* **Registo de regra de Rede**

   O registo de regras da Rede é guardado numa conta de armazenamento, transmitido para centros de eventos e/ou enviado para registos do Azure Monitor apenas se o tiver ativado para cada Firewall Azure. Cada nova ligação que corresponde a uma das suas regras de rede configuradas resulta num registo da ligação aceite/negada. Os dados são registados no formato JSON, conforme mostrado no exemplo seguinte:

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

* **Registo de procuração de DNS**

   O registo DNS Proxy é guardado numa conta de armazenamento, transmitido para centros de eventos e/ou enviado para registos do Azure Monitor apenas se o tiver ativado para cada Firewall Azure. Este registo rastreia as mensagens DNS para um servidor DNS configurado usando o proxy DNS. Os dados são registados no formato JSON, como mostram os seguintes exemplos:


   ```
   Category: DNS proxy logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   Sucesso:
   ```json
   {
     "category": "AzureFirewallDnsProxy",
     "time": "2020-09-02T19:12:33.751Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallDnsProxyLog",
     "properties": {
         "msg": "DNS Request: 11.5.0.7:48197 – 15676 AAA IN md-l1l1pg5lcmkq.blob.core.windows.net. udp 55 false 512 NOERROR - 0 2.000301956s"
     }
   }
   ```

   Falhou:

   ```json
   {
     "category": "AzureFirewallDnsProxy",
     "time": "2020-09-02T19:12:33.751Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallDnsProxyLog",
     "properties": {
         "msg": " Error: 2 time.windows.com.reddog.microsoft.com. A: read udp 10.0.1.5:49126->168.63.129.160:53: i/o timeout”
     }
   }
   ```

   formato msg:

   `[client’s IP address]:[client’s port] – [query ID] [type of the request] [class of the request] [name of the request] [protocol used] [request size in bytes] [EDNS0 DO (DNSSEC OK) bit set in the query] [EDNS0 buffer size advertised in the query] [response CODE] [response flags] [response size] [response duration]`

Tem três opções para armazenar os registos:

* **Conta de armazenamento**: as contas de armazenamento são ideais para os registos quando estes são armazenados durante um período mais longo e revistos quando necessário.
* **Hubs de eventos**: os Hubs de eventos são uma excelente opção para integrar com outras informações de segurança e ferramentas de gestão de eventos (SEIM) para obter alertas sobre os seus recursos.
* **Registos do Monitor Azure**: Os registos do Monitor Azure são mais utilizados para monitorizar em tempo real geral a sua aplicação ou analisar tendências.

## <a name="activity-logs"></a>Registos de atividade

   As entradas de registos de atividades são recolhidas por predefinição e pode visualizá-las no portal do Azure.

   Pode utilizar [registos de atividades da Azure](../azure-resource-manager/management/view-activity-logs.md) (anteriormente conhecidos como registos operacionais e registos de auditoria) para visualizar todas as operações submetidas à sua subscrição do Azure.

## <a name="metrics"></a>Métricas

As métricas no Monitor Azure são valores numéricos que descrevem algum aspeto de um sistema num determinado momento. As métricas são recolhidas a cada minuto, e são úteis para alertar porque podem ser amostradas com frequência. Um alerta pode ser disparado rapidamente com uma lógica relativamente simples.

As seguintes métricas estão disponíveis para Azure Firewall:

- **As regras de aplicação atingem** a contagem - O número de vezes que uma regra de candidatura foi atingida.

    Unidade: contagem

- **As regras da rede atingem a contagem** - O número de vezes que uma regra de rede foi atingida.

    Unidade: contagem

- **Dados processados** - Soma de dados que atravessam a firewall numa determinada janela de tempo.

    Unidade: bytes

- **Produção** - Taxa de dados que atravessam a firewall por segundo.

    Unidade: bits por segundo

- **Estado de saúde de firewall** - Indica a saúde da firewall com base na disponibilidade do porto SNAT.

    Unidade: por cento

   Esta métrica tem duas dimensões:
  - Estado: Os valores possíveis são *saudáveis,* *degradados,* *insalubres.*
  - Razão: Indica o motivo do estado correspondente da firewall. 

     Se as portas SNAT forem utilizadas > 95%, são consideradas exaustas e a saúde é de 50% com estatuto=**Degradada** e razão=**porta SNAT**. A firewall mantém o tráfego de processamento e as ligações existentes não são afetadas. No entanto, as novas ligações podem não ser estabelecidas intermitentemente.

     Se as portas SNAT forem utilizadas < 95%, então a firewall é considerada saudável e a saúde é mostrada como 100%.

     Se não for reportada qualquer utilização da portas SNAT, o estado de funcionamento será mostrada como 0%. 

- **Utilização da porta SNAT** - A percentagem de portas SNAT que foram utilizadas pela firewall.

    Unidade: por cento

   Quando adiciona mais IPs públicos à firewall, estão disponíveis mais portas SNAT, o que reduz a utilização das portas SNAT. Além do mais, quando a firewall aumenta horizontalmente por diferentes motivos (por exemplo, devido à CPU ou ao débito), também ficam disponíveis portas SNAT adicionais. Assim, efetivamente, uma dada percentagem de utilização das portas SNAT pode diminuir sem que você adicione quaisquer endereços IP públicos, apenas porque o serviço escalou para fora. Pode controlar diretamente o número de endereços IP públicos disponíveis para aumentar as portas disponíveis na sua firewall. Mas não se pode controlar diretamente a escala de firewall.


## <a name="next-steps"></a>Passos seguintes

- Para aprender a monitorizar registos e métricas do Azure Firewall, consulte [Tutorial: Monitor Azure Firewall .](./firewall-diagnostics.md)

- Para saber mais sobre as métricas no Azure Monitor, consulte [métricas no Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md).