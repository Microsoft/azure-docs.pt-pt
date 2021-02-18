---
title: Configuração de inteligência de ameaça Azure Firewall
description: Saiba como configurar a filtragem baseada em ameaças baseada em inteligência para a sua política Azure Firewall para alertar e negar tráfego de e para endereços e domínios IP maliciosos conhecidos.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 7ede1c917bb44dd31aa59855a0b7c83eb478700a
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651731"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Configuração de inteligência de ameaça Azure Firewall

A filtragem baseada em informações de ameaça pode ser configurada para a sua política de Azure Firewall alertar e negar o tráfego de e para endereços e domínios IP maliciosos conhecidos. Os domínios e endereços IP são obtidos a partir do feed das Informações sobre Ameaças da Microsoft. [O Smart Security Graph](https://www.microsoft.com/security/operations/intelligence) alimenta a inteligência de ameaça da Microsoft e é usado por vários serviços, incluindo o Azure Security Center.<br>

Se configurar a filtragem baseada em informações de ameaça, as regras associadas são processadas antes de qualquer uma das regras da NAT, regras de rede ou regras de aplicação.

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="Política de inteligência de ameaça":::

## <a name="threat-intelligence-mode"></a>Modo de inteligência de ameaça

Pode configurar a inteligência de ameaças num dos três modos descritos na tabela seguinte. Por predefinição, a filtragem baseada em inteligência de ameaça é ativada no modo de alerta.

|Modo |Description  |
|---------|---------|
|`Off`     | A funcionalidade de inteligência de ameaça não está ativada para a sua firewall. |
|`Alert only`     | Receberá alertas de alta confiança para o tráfego que passa pela sua firewall de ou de endereços ip maliciosos conhecidos e domínios. |
|`Alert and deny`     | O tráfego está bloqueado e receberá alertas de alta confiança quando o tráfego for detetado tentando passar pela sua firewall de ou a partir de endereços e domínios IP maliciosos conhecidos. |

> [!NOTE]
> O modo de inteligência de ameaça é herdado das políticas dos pais às políticas infantis. Uma política infantil deve ser configurada com o mesmo modo ou um modo mais rigoroso do que a política dos pais.

## <a name="allowlist-addresses"></a>Endereços allowlist

A inteligência das ameaças pode desencadear falsos positivos e bloquear o tráfego que na verdade é válido. Pode configurar uma lista de endereços IP permitidos para que a inteligência de ameaça não filtrar nenhum dos endereços, intervalos ou sub-redes que especifique.  

![Endereços allowlist](media/threat-intelligence-settings/allow-list.png)

Pode atualizar a lista de admissões com múltiplas entradas ao mesmo tempo, carregando um ficheiro CSV. O ficheiro CSV só pode conter endereços IP e intervalos. O ficheiro não pode conter títulos.

> [!NOTE]
> Os endereços de identificação de ameaças são herdados das políticas dos pais às políticas infantis. Qualquer endereço IP ou alcance adicionado a uma política de pais também se aplicará a todas as políticas infantis.

## <a name="logs"></a>Registos

O seguinte excerto de registo mostra uma regra desencadeada para tráfego de saída para um site malicioso:

```json
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
