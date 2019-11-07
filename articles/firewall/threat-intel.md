---
title: Filtragem baseada em inteligência de ameaças do firewall do Azure
description: Saiba mais sobre a filtragem de inteligência contra ameaças do firewall do Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/05/2019
ms.author: victorh
ms.openlocfilehash: f6a60d7c29fc7e482e32233aa86d65a801e3f55c
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582258"
---
# <a name="azure-firewall-threat-intelligence-based-filtering"></a>Filtragem baseada em inteligência contra ameaças do firewall do Azure

A filtragem com base em informações sobre ameaças pode ser ativada para que a sua firewall alerte e negue tráfego de/para endereços e domínios de IP maliciosos. Os endereços e domínios de IP são obtidos no feed de Informações sobre Ameaças da Microsoft. [Gráfico de segurança inteligente](https://www.microsoft.com/en-us/security/operations/intelligence) capacita a inteligência contra ameaças da Microsoft e é usada por vários serviços, incluindo a central de segurança do Azure.

![Inteligência contra ameaças de firewall](media/threat-intel/firewall-threat.png)

Se você tiver habilitado a filtragem baseada em inteligência contra ameaças, as regras associadas serão processadas antes de qualquer uma das regras de NAT, regras de rede ou regras de aplicativo.

Você pode optar por apenas registrar um alerta quando uma regra é disparada ou pode escolher o modo de alerta e de negação.

Por padrão, a filtragem baseada em inteligência contra ameaças está habilitada no modo de alerta. Você não pode desativar esse recurso ou alterar o modo até que a interface do portal fique disponível em sua região.

![Interface do portal de filtragem baseada em inteligência contra ameaças](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>Registos

O trecho de log a seguir mostra uma regra disparada:

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

- **Teste de saída** -alertas de tráfego de saída devem ser uma ocorrência rara, pois isso significa que seu ambiente foi comprometido. Para ajudar a testar os alertas de saída estão funcionando, um FQDN de teste foi criado e dispara um alerta. Use **testmaliciousdomain.eastus.cloudapp.Azure.com** para seus testes de saída.

- **Teste de entrada** -você pode esperar ver alertas sobre o tráfego de entrada se as regras do DNAT estiverem configuradas no firewall. Isso é verdadeiro mesmo se apenas fontes específicas forem permitidas na regra DNAT e o tráfego for negado. O Firewall do Azure não alerta todos os scanners de porta conhecidos; somente em scanners que também são conhecidos por se envolver em atividades mal-intencionadas.

## <a name="next-steps"></a>Passos seguintes

- Consulte [exemplos de log Analytics de firewall do Azure](log-analytics-samples.md)
- Saiba como [implantar e configurar um firewall do Azure](tutorial-firewall-deploy-portal.md)
- Examinar o [relatório de inteligência de segurança da Microsoft](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)