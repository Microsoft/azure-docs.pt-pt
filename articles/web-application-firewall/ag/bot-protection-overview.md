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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "83714904"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Firewall de aplicação web Azure na visão geral da proteção bot do Gateway de aplicação Azure

Cerca de 20% de todo o tráfego de Internet provém de bots maus. Fazem coisas como raspar, digitalizar e procurar vulnerabilidades na sua aplicação web. Quando estes bots são parados na Firewall de Aplicação Web (WAF), não podem atacá-lo. Também não podem usar os seus recursos e serviços, como os seus backends e outras infraestruturas subjacentes.

Pode ativar um conjunto de regras de proteção contra bots gerido para a WAF bloquear ou registar pedidos de endereços IP maliciosos conhecidos. Os endereços IP são obtidos a partir do feed das Informações sobre Ameaças da Microsoft. O Grafo de Segurança Inteligente alimenta as informações sobre ameaças da Microsoft e é utilizado por vários serviços, incluindo o Centro de Segurança do Azure.

> [!IMPORTANT]
> O conjunto de regras de proteção de bots está atualmente em pré-visualização pública e é fornecido com um acordo de nível de serviço de pré-visualização. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Consulte os [Termos Complementares de Utilização para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)   para obter mais detalhes.

## <a name="use-with-owasp-rulesets"></a>Utilizar com regras OWASP

Pode utilizar o conjunto de regras da Proteção Contra Bots em conjunto com qualquer um dos conjuntos de regras OWASP (2.2.9, 3.0 e 3.1). Apenas um conjunto de regras OWASP pode ser utilizado num determinado momento. O conjunto de regras de proteção contra bots contém uma regra adicional que aparece no seu próprio conjunto de regras. É intitulado **Microsoft_BotManagerRuleSet_0.1**, e pode ativá-lo ou desativá-lo como as outras regras da OWASP.

![Bot ruleset](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>Atualização de regras

A lista de conjuntos de regras de mitigação de bot de endereços IP incorretos conhecidos é atualizada várias vezes por dia no feed das Informações sobre Ameaças da Microsoft para se manter em sincronização com os bots. As aplicações Web são continuamente protegidas, mesmo quando os vetores de ataques de bots mudam.

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

## <a name="next-steps"></a>Passos seguintes

- [Configurar a proteção contra bots para a Firewall de Aplicações Web no Gateway de Aplicação do Azure (Pré-visualização)](bot-protection.md)
