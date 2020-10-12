---
title: Configuração de inteligência de ameaça Azure Firewall
description: Saiba como configurar a filtragem baseada em ameaças baseada em inteligência para a sua política Azure Firewall para alertar e negar tráfego de e para endereços e domínios IP maliciosos conhecidos.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: a663c5f3bcf3492c4a9bc74fe93c6ed6a86137ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530646"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Configuração de inteligência de ameaça Azure Firewall

A filtragem baseada em informações de ameaça pode ser configurada para a sua política de Azure Firewall alertar e negar o tráfego de e para endereços e domínios IP maliciosos conhecidos. Os domínios e endereços IP são obtidos a partir do feed das Informações sobre Ameaças da Microsoft. [O Smart Security Graph](https://www.microsoft.com/security/operations/intelligence) alimenta a inteligência de ameaça da Microsoft e é usado por vários serviços, incluindo o Azure Security Center.<br>

Se configurar a filtragem baseada em informações de ameaça, as regras associadas são processadas antes de qualquer uma das regras da NAT, regras de rede ou regras de aplicação.

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="Política de inteligência de ameaça":::

## <a name="threat-intelligence-mode"></a>Modo de inteligência de ameaça

Pode optar por registar apenas um alerta quando uma regra é ativada, ou pode escolher o modo alerta e negar.

Por predefinição, a filtragem baseada em inteligência de ameaça é ativada no modo de alerta.

## <a name="allowed-list-addresses"></a>Endereços de lista permitidos

Pode configurar uma lista de endereços IP permitidos para que a inteligência de ameaça não filtrar nenhum dos endereços, intervalos ou sub-redes que especifique.



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

- Reveja o [relatório de inteligência](https://www.microsoft.com/en-us/security/operations/security-intelligence-report) da Microsoft Security