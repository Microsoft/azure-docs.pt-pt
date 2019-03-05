---
title: Personalizar regras de firewall de aplicações web no Gateway de aplicação do Azure - PowerShell
description: Este artigo fornece informações sobre como personalizar regras de firewall de aplicações web no Gateway de aplicação com o PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 2/22/2019
ms.author: victorh
ms.openlocfilehash: f96395a54f66b787878faeee057f02818f956ade
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317004"
---
# <a name="customize-web-application-firewall-rules-through-powershell"></a>Personalizar regras de firewall de aplicações web através do PowerShell

A firewall de aplicações do Gateway de aplicação Azure web (WAF) fornece proteção para aplicativos web. Estas proteções são fornecidas pelo Open Web Application Security Project (OWASP) Core regra definido (CRS). Algumas regras podem causar falsos positivos e bloquear o tráfego real. Por esse motivo, o Gateway de aplicação fornece a capacidade para personalizar regras e grupos de regras. Para obter mais informações sobre os grupos de regras específicas e as regras, consulte [lista de grupos de regras de CRS da firewall de aplicação de web e regras](application-gateway-crs-rulegroups-rules.md).

## <a name="view-rule-groups-and-rules"></a>Grupos de regras de exibição e regras

Os exemplos de código seguintes mostram como ver as regras e grupos de regras que são configuráveis num gateway de aplicação com capacidade de WAF.

### <a name="view-rule-groups"></a>Grupos de regras do Vista

O exemplo seguinte mostra como ver grupos de regras:

```powershell
Get-AzApplicationGatewayAvailableWafRuleSets
```

O resultado seguinte é uma resposta truncada do exemplo anterior:

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

O exemplo seguinte desativa regras `911011` e `911012` num gateway de aplicação:

```powershell
$disabledrules=New-AzApplicationGatewayFirewallDisabledRuleGroupConfig -RuleGroupName REQUEST-911-METHOD-ENFORCEMENT -Rules 911011,911012
Set-AzApplicationGatewayWebApplicationFirewallConfiguration -ApplicationGateway $gw -Enabled $true -FirewallMode Detection -RuleSetVersion 3.0 -RuleSetType OWASP -DisabledRuleGroups $disabledrules
Set-AzApplicationGateway -ApplicationGateway $gw
```

## <a name="mandatory-rules"></a>Regras obrigatórias

A lista seguinte contém as condições que fazem com que o WAF bloquear o pedido no modo de prevenção (no modo de deteção tenham sessão iniciada como exceções). Estes não podem ser configurados ou desativados:

* Falha ao analisar o corpo do pedido resulta no pedido está a ser bloqueado, a menos que a inspeção de corpo está desativada (XML, JSON, dados de formulário)
* Comprimento de dados do pedido de corpo (com nenhum arquivo) é maior do que o limite configurado
* Corpo (inclusive arquivos) é maior do que o limite do pedido
* Ocorreu um erro interno no mecanismo de WAF

CRS 3.x específicas:

* Limiar de pontuação excedida de anomalias de entrada

## <a name="next-steps"></a>Passos Seguintes

Depois de configurar as regras do desativado, pode aprender a ver os registos da WAF. Para obter mais informações, consulte [Application Gateway Diagnostics](application-gateway-diagnostics.md#diagnostic-logging).

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
