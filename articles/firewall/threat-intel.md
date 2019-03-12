---
title: Inteligência de ameaças do Azure do Firewall filtragem com base em
description: Saiba mais sobre a filtragem de inteligência de ameaças do Firewall do Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 4ef9089c94d9e806cc519c4f8243cdcb7e73953a
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57730524"
---
# <a name="azure-firewall-threat-intelligence-based-filtering---public-preview"></a>Filtragem de baseados em inteligência contra ameaças de Firewall do Azure - pré-visualização pública

Filtragem de baseada em inteligência de ameaças pode ser ativada para a firewall para o alertar e negar o tráfego de/para endereços IP maliciosos e conhecidos domínios. Os endereços IP e os domínios são originados da Microsoft informações sobre ameaças do feed. [Gráfico de segurança inteligente](https://www.microsoft.com/en-us/security/operations/intelligence) alimenta sobre ameaças da Microsoft e é utilizado por vários serviços, incluindo o Centro de segurança do Azure.

![Informações de ameaças de firewall](media/threat-intel/firewall-threat.png)

> [!IMPORTANT]
> Threat intelligence filtragem com base em está atualmente em pré-visualização pública e é fornecido com um contrato de nível de serviço de pré-visualização. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.  Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.

Se a filtragem de baseada em inteligência de ameaças estiver ativada, as regras associadas são processadas antes de qualquer um dos regras NAT, regras de rede ou regras de aplicações. Durante a pré-visualização, apenas registos de confiança de mais altos são incluídos.

Pode optar por apenas um alerta de registo quando é acionada uma regra, ou pode escolher o alerta e negar modo.

Por predefinição, a filtragem de baseada em inteligência de ameaças está ativada no modo de alerta. Não é possível desativar esta funcionalidade ou altere o modo de até que a interface do portal fica disponível na sua região.

![Interface de portal filtragem com base de informações sobre ameaças](media/threat-intel/threat-intel-ui.png)

## <a name="logs"></a>Registos

O resumo de log a seguir mostra uma regra acionada:

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

## <a name="testing"></a>Testes

- **A testar saída** -alertas de tráfego de saída devem ser uma ocorrência rara, pois significa que seu ambiente estiver comprometido. Para ajudar a alertas de saída de teste estiver a trabalhar, um teste FQDN criou que aciona um alerta. Uso **testmaliciousdomain.eastus.cloudapp.azure.com** para seus testes de saída.

- **Teste de entrada** -pode esperar ver alertas sobre o tráfego de entrada se as regras DNAT estão configuradas na firewall. Isso vale mesmo que apenas as fontes específicas são permitidas na regra DNAT e tráfego caso contrário, é negado. Firewall do Azure não alerta sobre todos os scanners de porta conhecida; apenas em scanners que são conhecidos por também participa em atividades maliciosas.

## <a name="next-steps"></a>Passos Seguintes

- Consulte [exemplos de Firewall do Azure Log Analytics](log-analytics-samples.md)
- Saiba como [implementar e configurar uma Firewall do Azure](tutorial-firewall-deploy-portal.md)
- Reveja o [relatório do Microsoft Security intelligence](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)