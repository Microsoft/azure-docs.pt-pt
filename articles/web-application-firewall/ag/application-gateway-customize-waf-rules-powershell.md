---
title: Personalizar regras usando PowerShell
titleSuffix: Azure Web Application Firewall
description: Este artigo fornece informações sobre como personalizar as regras de Firewall de Aplicação Web em Gateway de aplicação com PowerShell.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 55eea15da8c3a10b0421ff1576082d6b42fc7c56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "74048514"
---
# <a name="customize-web-application-firewall-rules-using-powershell"></a>Personalize as regras de Firewall de aplicação web usando o PowerShell

O Azure Application Gateway Web Application Firewall (WAF) fornece proteção para aplicações web. Estas proteções são fornecidas pelo Open Web Application Security Project (OWASP) Core Rule set (CRS). Algumas regras podem causar falsos positivos e bloquear o tráfego real. Por esta razão, o Application Gateway fornece a capacidade de personalizar grupos de regras e regras. Para obter mais informações sobre os grupos e regras específicas de regras, consulte [a Lista de grupos e regras de regras do CRS rule do Firewall de Aplicação Web.](application-gateway-crs-rulegroups-rules.md)

## <a name="view-rule-groups-and-rules"></a>Ver grupos de regras e regras

Os seguintes exemplos de código mostram como ver regras e grupos de regras que são configuráveis num gateway de aplicações ativado pela WAF.

### <a name="view-rule-groups"></a>Ver grupos de regras

O exemplo a seguir mostra como ver grupos de regras:

```powershell
Get-AzApplicationGatewayAvailableWafRuleSets
```

Segue-se uma resposta truncada do exemplo anterior:

```
OWASP (Ver. 3.0):

    General:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            200004     Possible Multipart Unmatched Boundary.

    REQUEST-911-METHOD-ENFORCEMENT:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            911011     Rule 911011
            911012     Rule 911012
            911100     Method is not allowed by policy
            911013     Rule 911013
            911014     Rule 911014
            911015     Rule 911015
            911016     Rule 911016
            911017     Rule 911017
            911018     Rule 911018

    REQUEST-913-SCANNER-DETECTION:
        Description:

        Rules:
            RuleId     Description
            ------     -----------
            913011     Rule 913011
            913012     Rule 913012
            913100     Found User-Agent associated with security scanner
            913110     Found request header associated with security scanner
            913120     Found request filename/argument associated with security scanner
            913013     Rule 913013
            913014     Rule 913014
            913101     Found User-Agent associated with scripting/generic HTTP client
            913102     Found User-Agent associated with web crawler/bot
            913015     Rule 913015
            913016     Rule 913016
            913017     Rule 913017
            913018     Rule 913018

            ...        ...
```

## <a name="disable-rules"></a>Desativar regras

O exemplo a seguir desativa as regras `911011` e num gateway de `911012` aplicações:

```powershell
$disabledrules=New-AzApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="mandatory-rules"></a>Regras obrigatórias

A lista a seguir contém condições que fazem com que a WAF bloqueie o pedido durante o modo de prevenção (no Modo de Deteção são registados como exceções). Estes não podem ser configurados ou desativados:

* A não análise do organismo de pedido resulta no bloqueio do pedido, a menos que a inspeção corporal seja desligada (XML, JSON, dados do formulário)
* O comprimento dos dados do corpo do pedido (sem ficheiros) é maior do que o limite configurado
* O corpo de pedido (incluindo ficheiros) é maior do que o limite
* Um erro interno aconteceu no motor WAF

CRS 3.x específico:

* Pontuação de anomalia de entrada excedeu limiar

## <a name="next-steps"></a>Passos seguintes

Depois de configurar as suas regras desativadas, pode aprender a ver os seus registos WAF. Para mais informações, consulte [o Application Gateway Diagnostics](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
