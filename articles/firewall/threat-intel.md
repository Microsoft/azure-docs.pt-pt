---
title: Filtragem baseada em inteligência de ameaça de Firewall Azure
description: A filtragem baseada em informações de ameaça pode ser ativada para que a sua firewall alerte e negue tráfego de/para endereços e domínios IP maliciosos conhecidos.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: c291dbe9c1eb37e68174a2353e296a376c7d0896
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74168670"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Filtragem baseada em inteligência de ameaça de Firewall Azure

A filtragem baseada em informações de ameaça pode ser ativada para que a sua firewall alerte e negue tráfego de/para endereços e domínios IP maliciosos conhecidos. Os endereços e domínios IP são obtidos a partir do feed da Microsoft Threat Intelligence. [O Smart Security Graph](https://www.microsoft.com/en-us/security/operations/intelligence) alimenta a inteligência de ameaça da Microsoft e é usado por vários serviços, incluindo o Azure Security Center.

![Inteligência de ameaça de firewall](media/threat-intel/firewall-threat.png)

Se permitiu a filtragem baseada em ameaças baseadas em inteligência, as regras associadas são processadas antes de qualquer uma das regras da NAT, regras de rede ou regras de aplicação.

Pode optar por registar um alerta quando uma regra é acionada, ou pode escolher o alerta e negar o modo.

Por predefinição, a filtragem baseada em informações de ameaça está ativada no modo de alerta. Não é possível desligar esta funcionalidade ou alterar o modo até que a interface do portal fique disponível na sua região.

![Interface do portal de filtragem baseado em inteligência de ameaça](media/threat-intel/threat-intel-ui.png)

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

- **Testes** de saída - Os alertas de trânsito de saída devem ser uma ocorrência rara, pois significa que o seu ambiente foi comprometido. Para ajudar a testar os alertas de saída estão a funcionar, foi criado um teste FQDN que desencadeia um alerta. Use **testmaliciousdomain.eastus.cloudapp.azure.com** para os seus testes de saída.

- **Teste de entrada** - Pode esperar ver alertas sobre o tráfego que está a chegar se as regras de ADN estiverem configuradas na firewall. Isto é verdade, mesmo que apenas sejam permitidas fontes específicas na regra do ADN e o tráfego seja negado de outra forma. O Azure Firewall não alerta sobre todos os scanners de porta conhecidos; apenas em scanners que são conhecidos por também se envolverem em atividades maliciosas.

## <a name="next-steps"></a>Passos seguintes

- Ver amostras de [Log Analytics de Firewall Azure](log-analytics-samples.md)
- Saiba como [implementar e configurar uma Firewall Azure](tutorial-firewall-deploy-portal.md)
- Reveja o relatório de inteligência da [Microsoft Security](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)