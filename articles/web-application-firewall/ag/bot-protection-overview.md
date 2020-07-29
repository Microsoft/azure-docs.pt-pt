---
title: WAF na visão geral da proteção bot do Gateway de aplicação Azure
titleSuffix: Azure Web Application Firewall
description: Este artigo fornece uma visão geral da firewall de aplicações web (WAF) na proteção bot do Gateway de Aplicação
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 05/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: e01f9ac8966223e11ad218af7bf6fbb2462f28f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83714904"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Firewall de aplicação web Azure na visão geral da proteção bot do Gateway de aplicação Azure

Cerca de 20% de todo o tráfego de Internet provém de bots maus. Fazem coisas como raspar, digitalizar e procurar vulnerabilidades na sua aplicação web. Quando estes bots são parados na Firewall de Aplicação Web (WAF), não podem atacá-lo. Também não podem usar os seus recursos e serviços, como os seus backends e outras infraestruturas subjacentes.

Pode ativar uma regra de proteção de bot gerida definida para o seu WAF bloquear ou registar pedidos de endereços IP maliciosos conhecidos. Os endereços IP são obtidos a partir do feed da Microsoft Threat Intelligence. O Smart Security Graph alimenta a inteligência de ameaça da Microsoft e é usado por vários serviços, incluindo o Azure Security Center.

> [!IMPORTANT]
> O conjunto de regras de proteção de bots está atualmente em pré-visualização pública e é fornecido com um acordo de nível de serviço de pré-visualização. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Consulte os [Termos Complementares de Utilização para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)   para obter mais detalhes.

## <a name="use-with-owasp-rulesets"></a>Utilizar com regras OWASP

Pode utilizar o bot protection ruleset ao lado de qualquer um dos rulesets OWASP (2.2.9, 3.0 e 3.1). Apenas um ruleset OWASP pode ser usado a qualquer momento. O bot protection ruleset contém uma regra adicional que aparece no seu próprio ruleset. É intitulado **Microsoft_BotManagerRuleSet_0.1**, e pode ativá-lo ou desativá-lo como as outras regras da OWASP.

![Bot ruleset](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>Atualização de regras

A lista de regras de mitigação de bots de endereços IP maus conhecidos atualiza várias vezes por dia a partir do feed da Microsoft Threat Intelligence para se manter em sintonia com os bots. As suas aplicações web são continuamente protegidas, mesmo à medida que os vetores de ataque de bot mudam.

## <a name="log-example"></a>Exemplo de registo

Aqui está um registo de exemplo para proteção de bot:

```
{
        "timeStamp": "0000-00-00T00:00:00+00:00",
            "resourceId": "appgw",
            "operationName": "ApplicationGatewayFirewall",
            "category": "ApplicationGatewayFirewallLog",
            "properties": {
            "instanceId": "vm1",
                "clientIp": "1.2.3.4",
                "requestUri": "/hello.php?arg1=aaaaaaabccc",
                "ruleSetType": "MicrosoftBotProtection",
                "message": "IPReputationTriggered",
                "action": "Blocked",
                "hostname": "example.com",
                "transactionId": "abc",
                "policyId": "waf policy 1",
                "policyScope": "Global",
                "policyScopeName": "Default Policy",
                "engine": "Azwaf"
        }
    }
```

## <a name="next-steps"></a>Próximos passos

- [Configure a proteção de bot para firewall de aplicação web no Gateway de aplicações Azure (pré-visualização)](bot-protection.md)
