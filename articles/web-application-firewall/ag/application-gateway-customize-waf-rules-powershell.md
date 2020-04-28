---
title: Personalize as regras usando powerShell
titleSuffix: Azure Web Application Firewall
description: Este artigo fornece informações sobre como personalizar as regras de Firewall de aplicação web no Gateway de Aplicações com powerShell.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 55eea15da8c3a10b0421ff1576082d6b42fc7c56
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74048514"
---
# <a name="customize-web-application-firewall-rules-using-powershell"></a>Personalize as regras de firewall de aplicações web usando powerShell

O Firewall de aplicação web da Aplicação Gateway (WAF) da Aplicação Azure Application (WAF) fornece proteção para aplicações web. Estas proteções são fornecidas pelo Conjunto de Regras de Regra do Núcleo de Segurança de Aplicações Abertas (OWASP) (CRS). Algumas regras podem causar falsos positivos e bloquear o tráfego real. Por esta razão, o Application Gateway fornece a capacidade de personalizar grupos e regras de regras. Para obter mais informações sobre os grupos e regras de regras específicas, consulte [a lista de grupos e regras](application-gateway-crs-rulegroups-rules.md)da regra CRS firewall de aplicação web .

## <a name="view-rule-groups-and-rules"></a>Ver grupos de regras e regras

Os seguintes exemplos de código mostram como visualizar regras e grupos de regras que são configuráveis num gateway de aplicação ativado por WAF.

### <a name="view-rule-groups"></a>Ver grupos de regras

O exemplo que se segue mostra como ver os grupos de regras:

```powershell
Get-AzApplicationGatewayAvailableWafRuleSets
```

A seguinte saída é uma resposta truncada do exemplo anterior:

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

## <a name="disable-rules"></a>Regras de desativação

O exemplo seguinte `911011` desativa as regras e `911012` numa porta de aplicação:

```powershell
$disabledrules=New-AzApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="mandatory-rules"></a>Regras obrigatórias

A lista que se segue contém condições que fazem com que o WAF bloqueie o pedido enquanto está no Modo de Prevenção (no Modo de Deteção são registados como exceções). Estes não podem ser configurados ou desativados:

* A não análise do organismo de pedido resulta no bloqueio do pedido, a menos que a inspeção corporal seja desligada (XML, JSON, dados de formulário)
* O comprimento de dados do corpo de pedidos (sem ficheiros) é maior do que o limite configurado
* O organismo de pedido (incluindo ficheiros) é maior do que o limite
* Um erro interno aconteceu no motor WAF

CRS 3.x específico:

* Pontuação de anomalia de entrada excedida limiar

## <a name="next-steps"></a>Passos seguintes

Depois de configurar as suas regras de incapacidade, pode aprender a ver os seus registos WAF. Para mais informações, consulte O Diagnóstico de Gateway de [Aplicação](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
