---
title: Registos do Monitor Azure para Azure Firewall
description: Os registos do Azure Monitor podem ser utilizados para analisar o seu Azure Firewall. Um ficheiro de amostra é construído no View Designer em Azure Monitor.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 09/11/2020
ms.author: victorh
ms.openlocfilehash: 86538f6d0467eb15e549179166ca957902a2d0c3
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659561"
---
# <a name="azure-monitor-logs-for-azure-firewall"></a>Registos do Monitor Azure para Azure Firewall

As seguintes amostras de registos do Azure Monitor podem ser usadas para analisar os seus registos Azure Firewall. O ficheiro de amostra é construído no View Designer in Azure Monitor, o artigo [do View Designer em Azure Monitor](../azure-monitor/platform/view-designer.md) tem mais informações sobre o conceito de View Design.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="azure-monitor-logs-view"></a>Vista de registos do Monitor Azure

Eis como pode configurar um exemplo de visualização de registos do Azure Monitor. Você pode baixar o exemplo visualização do [repositório azure-docs-json-samples.](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-firewall/AzureFirewall.omsview) A maneira mais fácil é clicar com o botão certo na hiperligação desta página e escolher *guardar como* e fornecer um nome como **AzureFirewall.omsview**. 

Execute os seguintes passos para adicionar a vista ao seu espaço de trabalho Log Analytics:

1. Abra o espaço de trabalho Log Analytics no portal Azure.
2. Open **View Designer** abaixo **General**.
3. Clique **em Importar.**
4. Navegue e selecione o ficheiro **AzureFirewall.omsview** que descarregou anteriormente.
5. Clique em **Guardar**.

Eis como a vista procura os dados de registo de regras de aplicação:

![Dados de registo de regras de aplicação](./media/log-analytics-samples/azurefirewall-applicationrulelogstats.png)

E para os dados de registo de regras de rede:

![Dados de registo de regras de rede]( ./media/log-analytics-samples/azurefirewall-networkrulelogstats.png)

Azure Firewall regista dados abaixo da AzureDiagnostics com categoria como **AzureFirewallApplicationRule** ou **AzureFirewallNetworkRule**. Os dados que contêm os dados são armazenados no campo msg_s. Utilizando o operador [de parse podemos](/azure/kusto/query/parseoperator) extrair as várias propriedades interessantes do campo msg_s. As consultas abaixo extraem a informação para ambas as categorias.

## <a name="application-rules-log-data-query"></a>As regras de aplicação registam consulta de dados

A consulta abaixo analisa os dados de registo de regras de aplicação. Nas várias linhas de comentários há alguma orientação sobre como a consulta foi construída:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallApplicationRule"
//using :int makes it easier to pars but later we'll convert to string as we're not interested to do mathematical functions on these fields
//this first parse statement is valid for all entries as they all start with this format
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " " TempDetails
//case 1: for records that end with: "was denied. Reason: SNI TLS extension was missing."
| parse TempDetails with "was " Action1 ". Reason: " Rule1
//case 2: for records that end with
//"to ocsp.digicert.com:80. Action: Allow. Rule Collection: RC1. Rule: Rule1"
//"to v10.vortex-win.data.microsoft.com:443. Action: Deny. No rule matched. Proceeding with default action"
| parse TempDetails with "to " FQDN ":" TargetPortInt:int ". Action: " Action2 "." *
//case 2a: for records that end with:
//"to ocsp.digicert.com:80. Action: Allow. Rule Collection: RC1. Rule: Rule1"
| parse TempDetails with * ". Rule Collection: " RuleCollection2a ". Rule:" Rule2a
//case 2b: for records that end with:
//for records that end with: "to v10.vortex-win.data.microsoft.com:443. Action: Deny. No rule matched. Proceeding with default action"
| parse TempDetails with * "Deny." RuleCollection2b ". Proceeding with" Rule2b
| extend 
SourcePort = tostring(SourcePortInt)
|extend
TargetPort = tostring(TargetPortInt)
| extend
//make sure we only have Allowed / Deny in the Action Field
Action1 = case(Action1 == "Deny","Deny","Unknown Action")
| extend
    Action = case(Action2 == "",Action1,Action2),
    Rule = case(Rule2a == "",case(Rule1 == "",case(Rule2b == "","N/A", Rule2b),Rule1),Rule2a), 
    RuleCollection = case(RuleCollection2b == "",case(RuleCollection2a == "","No rule matched",RuleCollection2a),RuleCollection2b),
    FQDN = case(FQDN == "", "N/A", FQDN),
    TargetPort = case(TargetPort == "", "N/A", TargetPort)
| project TimeGenerated, msg_s, Protocol, SourceIP, SourcePort, FQDN, TargetPort, Action ,RuleCollection, Rule
```

A mesma consulta num formato mais condensado:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallApplicationRule"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " " TempDetails
| parse TempDetails with "was " Action1 ". Reason: " Rule1
| parse TempDetails with "to " FQDN ":" TargetPortInt:int ". Action: " Action2 "." *
| parse TempDetails with * ". Rule Collection: " RuleCollection2a ". Rule:" Rule2a
| parse TempDetails with * "Deny." RuleCollection2b ". Proceeding with" Rule2b
| extend SourcePort = tostring(SourcePortInt)
| extend TargetPort = tostring(TargetPortInt)
| extend Action1 = case(Action1 == "Deny","Deny","Unknown Action")
| extend Action = case(Action2 == "",Action1,Action2),Rule = case(Rule2a == "", case(Rule1 == "",case(Rule2b == "","N/A", Rule2b),Rule1),Rule2a), 
RuleCollection = case(RuleCollection2b == "",case(RuleCollection2a == "","No rule matched",RuleCollection2a), RuleCollection2b),FQDN = case(FQDN == "", "N/A", FQDN),TargetPort = case(TargetPort == "", "N/A", TargetPort)
| project TimeGenerated, msg_s, Protocol, SourceIP, SourcePort, FQDN, TargetPort, Action ,RuleCollection, Rule
```

## <a name="network-rules-log-data-query"></a>As regras de rede registam consulta de dados

A consulta seguinte analisa os dados de registo de regras de rede. Nas várias linhas de comentários há alguma orientação sobre como a consulta foi construída:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallNetworkRule"
//using :int makes it easier to pars but later we'll convert to string as we're not interested to do mathematical functions on these fields
//case 1: for records that look like this:
//TCP request from 10.0.2.4:51990 to 13.69.65.17:443. Action: Deny//Allow
//UDP request from 10.0.3.4:123 to 51.141.32.51:123. Action: Deny/Allow
//TCP request from 193.238.46.72:50522 to 40.119.154.83:3389 was DNAT'ed to 10.0.2.4:3389
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
//case 1a: for regular network rules
//TCP request from 10.0.2.4:51990 to 13.69.65.17:443. Action: Deny//Allow
//UDP request from 10.0.3.4:123 to 51.141.32.51:123. Action: Deny/Allow
| parse msg_s with * ". Action: " Action1a
//case 1b: for NAT rules
//TCP request from 193.238.46.72:50522 to 40.119.154.83:3389 was DNAT'ed to 10.0.2.4:3389
| parse msg_s with * " was " Action1b " to " NatDestination
//case 2: for ICMP records
//ICMP request from 10.0.2.4 to 10.0.3.4. Action: Allow
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend
SourcePort = tostring(SourcePortInt),
TargetPort = tostring(TargetPortInt)
| extend 
    Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),
    Protocol = case(Protocol == "", Protocol2, Protocol),
    SourceIP = case(SourceIP == "", SourceIP2, SourceIP),
    TargetIP = case(TargetIP == "", TargetIP2, TargetIP),
    //ICMP records don't have port information
    SourcePort = case(SourcePort == "", "N/A", SourcePort),
    TargetPort = case(TargetPort == "", "N/A", TargetPort),
    //Regular network rules don't have a DNAT destination
    NatDestination = case(NatDestination == "", "N/A", NatDestination)
| project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination
```

A mesma consulta num formato mais condensado:

```Kusto
AzureDiagnostics
| where Category == "AzureFirewallNetworkRule"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
| parse msg_s with * ". Action: " Action1a
| parse msg_s with * " was " Action1b " to " NatDestination
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt)
| extend Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort),NatDestination = case(NatDestination == "", "N/A", NatDestination)
| project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination
```

## <a name="threat-intelligence-log-data-query"></a>Consulta de dados de registo de informação de ameaça

A seguinte consulta analisa os dados de registo da regra de Inteligência de Ameaça:

```Kusto
AzureDiagnostics
| where OperationName  == "AzureFirewallThreatIntelLog"
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int *
| parse msg_s with * ". Action: " Action "." Message
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt)
| extend Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort)
| sort by TimeGenerated desc | project TimeGenerated, msg_s, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action,Message
```

## <a name="sample-logs"></a>Registos de exemplo

As seguintes amostras de registo mostram os dados incluídos numa entrada de registo.

:::image type="content" source="media/log-analytics-samples/log1.png" alt-text="Screenshot de uma entrada de registo. São visíveis vários valores, tais como um carimbo temporal, um protocolo, um número de porta, uma ação, uma coleção de regras e uma regra." border="false":::

:::image type="content" source="media/log-analytics-samples/log2.png" alt-text="Screenshot de uma entrada de registo. São visíveis vários valores, como um carimbo temporal, um protocolo, uma fonte e endereços I P alvo, e uma ação." border="false":::

:::image type="content" source="media/log-analytics-samples/log3.png" alt-text="Screenshot de uma entrada de registo. São visíveis vários valores, tais como um carimbo de tempo, um protocolo, endereços e portas I P de origem e alvo, e uma mensagem." border="false":::
## <a name="next-steps"></a>Próximos passos

Para saber mais sobre a monitorização e diagnósticos do Azure Firewall, consulte [Tutorial: Monitor Azure Firewall registos e métricas](./firewall-diagnostics.md).