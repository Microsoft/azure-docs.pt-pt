---
title: Azure Firewall ameaça inteligência filtragem baseada em filtragem
description: A filtragem baseada nas informações sobre ameaças pode ser ativada para a firewall para alertar e negar o tráfego de/para domínios e endereços IP maliciosos conhecidos.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 05/12/2020
ms.author: victorh
ms.openlocfilehash: e51cc8905a7b4a88bb7f7dabaf24bb30159ff86c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83655082"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Filtragem baseada em ameaças de Azure Firewall

A filtragem baseada nas informações sobre ameaças pode ser ativada para a firewall para alertar e negar o tráfego de/para domínios e endereços IP maliciosos conhecidos. Os domínios e endereços IP são obtidos a partir do feed das Informações sobre Ameaças da Microsoft. [O Smart Security Graph](https://www.microsoft.com/security/operations/intelligence) alimenta a inteligência de ameaça da Microsoft e é usado por vários serviços, incluindo o Azure Security Center.<br>
<br>

:::image type="content" source="media/threat-intel/firewall-threat.png" alt-text="Inteligência de ameaça de firewall" border="false":::

Se tiver ativado a filtragem baseada em informações de ameaça, as regras associadas são processadas antes de qualquer uma das regras da NAT, regras de rede ou regras de aplicação.

Pode optar por registar um alerta quando uma regra é ativada, ou pode escolher o modo alerta e negar.

Por predefinição, a filtragem baseada em inteligência de ameaça é ativada no modo de alerta. Não é possível desativar esta funcionalidade ou alterar o modo até que a interface do portal fique disponível na sua região.

:::image type="content" source="media/threat-intel/threat-intel-ui.png" alt-text="Inteligência de ameaça de firewall":::

## <a name="logs"></a>Registos

O seguinte excerto de registo mostra uma regra desencadeada:

```
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Testar

- **Testes de saída** - Os alertas de tráfego de saída devem ser uma ocorrência rara, pois significa que o seu ambiente foi comprometido. Para ajudar a testar os alertas de saída, foi criado um teste FQDN que desencadeia um alerta. Utilize **testmaliciousdomain.eastus.cloudapp.azure.com** para os seus testes de saída.

- **Testes de entrada** - Pode esperar ver alertas sobre o tráfego de entrada se as regras de ADN forem configuradas na firewall. Isto é verdade mesmo que apenas fontes específicas sejam permitidas na regra do ADN e o tráfego seja negado de outra forma. A Azure Firewall não alerta em todos os scanners de portas conhecidos; apenas em scanners que são conhecidos por também se envolver em atividades maliciosas.

## <a name="next-steps"></a>Passos seguintes

- Ver [amostras do Azure Firewall Log Analytics](log-analytics-samples.md)
- Saiba como [implementar e configurar uma Firewall Azure](tutorial-firewall-deploy-portal.md)
- Reveja o [relatório de inteligência](https://www.microsoft.com/en-us/security/operations/security-intelligence-report) da Microsoft Security