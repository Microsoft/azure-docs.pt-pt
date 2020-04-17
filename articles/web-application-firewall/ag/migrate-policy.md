---
title: Políticas de WAF migrar para gateway de aplicação azure
description: Saiba como migrar as políticas de Firewall de aplicação web azure usando o Azure PowerShell.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 04/16/2020
ms.author: ant
ms.openlocfilehash: fb3b922b753b9696aa26ea189597589ecc5772db
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536629"
---
# <a name="migrate-web-application-firewall-policies-using-azure-powershell"></a>Migrar políticas de firewall de aplicações web usando O PowerShell Azure

Este script facilita a transição de uma config WAF ou de uma política waf personalizada apenas para uma política waf completa. Você pode ver um aviso no portal que diz *migrar para a política waf*, ou você pode querer as novas funcionalidades de pré-visualização pública WAF tais como regras personalizadas de Geomatch, política por site e per-URI WAF, ou o conjunto de regras de mitigação de bots. Para utilizar qualquer uma destas funcionalidades, necessita de uma política waf completa associada ao seu gateway de aplicação. 

Para obter mais informações sobre a criação de uma nova política waf, consulte [Create Web Application Firewall policies for Application Gateway](create-waf-policy-ag.md). Para obter informações sobre a migração, consulte [a política de Migrar para a WAF.](create-waf-policy-ag.md#migrate-to-waf-policy)

## <a name="to-migrate-to-waf-policy-using-the-migration-script"></a>Migrar para a política da WAF usando o roteiro de migração

Utilize os seguintes passos para executar o script de migração: 

1. Abra a seguinte janela de nuvens ou abra uma de dentro do portal.
2. Copie o guião para a janela da casca da nuvem e execute-o.
3. O script pede ID de subscrição, nome do Grupo de Recursos, o nome do Gateway de Aplicação com o qual o config WAF está associado, e o nome da nova política waf que criar. Uma vez que você introduz estas inputs, o script executa e cria a sua nova política WAF
4. Associe a nova política da WAF com o seu gateway de aplicação. Vá à política waf no portal e selecione o separador Gateways de **Aplicação Associado.** Selecione Associate um Gateway de **Aplicação** e, em seguida, selecione o Gateway de Aplicação para associar a política WAF.

> [!NOTE]
> O script não completa uma migração se existirem as seguintes condições:
> - Uma regra inteira é desativada. Para completar uma migração, certifique-se de que todo um grupo de regras não é desativado.
> - Uma entrada de exclusão com o *igual a qualquer* operador. Para completar uma migração, certifique-se de que as entradas de exclusão com *iguais qualquer* operador não está presente.
>
> Para mais informações, consulte a função *ValidateInput* no script.

```azurepowershell-interactive
<#PSScriptInfo
.DESCRIPTION
Will be used to migrate to the application-gateway to a top level waf policy experience.

.VERSION 1.0

.GUID b6fedd43-ebd0-41ed-9847-4f1c1c43be22

.AUTHOR Venkat.Krishnan

.PARAMETER subscriptionId 
Subscription Id of where the resources are present.
.PARAMETER resourceGroupName
Resource-group where the resources are present.
.PARAMETER applicationGatewayName
Application-Gateway name
.PARAMETER wafPolicyName
Name of the web application firewall policy

.EXAMPLE
./migrateToWafPolicy.ps1 -subscriptionId  <your-subscription-id> -applicationGatewayName <your-appgw-name> -resourceGroupName <your-resource-group-name> -wafPolicyName <new-waf-policy-name>
#>

param(
    [Parameter(Mandatory=$true)]
    [string] $subscriptionId,
    [Parameter(Mandatory=$true)]
    [string] $resourceGroupName,
    [Parameter(Mandatory=$true)]
    [string] $applicationGatewayName,
    [Parameter(Mandatory=$true)]
    [string] $wafPolicyName
)

function ValidateInput ($appgwName, $resourceGroupName) {
    # Obtain the application-gateway
    $appgw = Get-AzApplicationGateway -Name $applicationGatewayName -ResourceGroupName $resourceGroupName
    if (-not $appgw) {
        Write-Error "ApplicationGateway: $applicationGatewayName is not present in ResourceGroup: $resourceGroupName"
        return $false
    }

    # Check if already have a global firewall policy
    if ($appgw.FirewallPolicy) {
        $fp = Get-AzResource -ResourceId $appgw.FirewallPolicy.Id
        if ($fp.PolicySettings) {
            Write-Error "ApplicationGateway: $applicationGatewayName already has a global firewall policy: $fp.Name. Please use portal for changing the policy."
            return $false
        }
    }

    if ($appgw.WebApplicationFirewallConfiguration) {
        # Throw an error, since ruleGroup disabled case can't be migrated now.
        if ($appgw.WebApplicationFirewallConfiguration.DisabledRuleGroups) {
            foreach ($disabled in $appgw.WebApplicationFirewallConfiguration.DisabledRuleGroups) {
                if ($disabled.Rules.Count -eq 0) {
                    $ruleGroupName = $disabled.RuleGroupName
                    Write-Error "The ruleGroup '$ruleGroupName' is disabled. Currently we can't migrate to a firewall policy when an entire ruleGroup is disabled. This feature will be delivered shortly. To continue, kindly ensure the entire rulegroups are not disabled. "
                    return $false
                }
            }
        }

        # Throw an error when exclusion entry with 'EqualsAny' operator is present
        if ($appgw.WebApplicationFirewallConfiguration.Exclusions) {
            foreach ($excl in $appgw.WebApplicationFirewallConfiguration.Exclusions) {
                if ($null -ne $excl.MatchVariable -and $null -eq $excl.SelectorMatchOperator -and $null -eq $excl.Selector) {
                    Write-Error " You have an exclusion entry(s) with the 'Equals any' operator. Currently we can't migrate to a firewall policy with 'Equals Any' operator. This feature will be delivered shortly. To continue, kindly ensure exclusion entries with 'Equals Any' operator is not present. "
                    return $false
                }
            }
        }
    }

    if ($appgw.Sku.Name -ne "WAF_v2" -or $appgw.Sku.Tier -ne "WAF_v2") {
        Write-Error " Cannot associate a firewall policy to application gateway :$applicationGatewayName since the Sku is not on WAF_v2"
        return $false
    }

    return $true
}

function Login() {
    $context = Get-AzContext
    if ($null -eq $context -or $null -eq $context.Account) {
        Login-AzAccount
    }
}

function createNewTopLevelWafPolicy ($subscriptionId, $resourceGroupName, $applicationGatewayName, $wafPolicyName) {
    Select-AzSubscription -Subscription $subscriptionId
    $retVal = ValidateInput -appgwName $applicationGatewayName -resourceGroupName $resourceGroupName
    if (!$retVal) {
        return
    }

    $appgw = Get-AzApplicationGateway -Name $applicationGatewayName -ResourceGroupName $resourceGroupName

    # Get the managedRule and PolicySettings
    $managedRule = New-AzApplicationGatewayFirewallPolicyManagedRule
    $policySetting = New-AzApplicationGatewayFirewallPolicySetting
    if ($appgw.WebApplicationFirewallConfiguration) {
        $ruleGroupOverrides = [System.Collections.ArrayList]@()
        if ($appgw.WebApplicationFirewallConfiguration.DisabledRuleGroups) {
            foreach ($disabled in $appgw.WebApplicationFirewallConfiguration.DisabledRuleGroups) {
                $rules = [System.Collections.ArrayList]@()
                if ($disabled.Rules.Count -gt 0) {
                    foreach ($rule in $disabled.Rules) {
                        $ruleOverride = New-AzApplicationGatewayFirewallPolicyManagedRuleOverride -RuleId $rule
                        $_ = $rules.Add($ruleOverride)              
                    }
                }
                
                $ruleGroupOverride = New-AzApplicationGatewayFirewallPolicyManagedRuleGroupOverride -RuleGroupName $disabled.RuleGroupName -Rule $rules
                $_ = $ruleGroupOverrides.Add($ruleGroupOverride)
            }
        }

        $managedRuleSet = New-AzApplicationGatewayFirewallPolicyManagedRuleSet -RuleSetType $appgw.WebApplicationFirewallConfiguration.RuleSetType -RuleSetVersion $appgw.WebApplicationFirewallConfiguration.RuleSetVersion 
        if ($ruleGroupOverrides.Count -ne 0) {
            $managedRuleSet = New-AzApplicationGatewayFirewallPolicyManagedRuleSet -RuleSetType $appgw.WebApplicationFirewallConfiguration.RuleSetType -RuleSetVersion $appgw.WebApplicationFirewallConfiguration.RuleSetVersion -RuleGroupOverride $ruleGroupOverrides 
        }
    
        $exclusions = [System.Collections.ArrayList]@()  
        if ($appgw.WebApplicationFirewallConfiguration.Exclusions) {
            foreach ($excl in $appgw.WebApplicationFirewallConfiguration.Exclusions) {
                if ($excl.MatchVariable -and $excl.SelectorMatchOperator -and $excl.Selector) {
                    $exclusionEntry = New-AzApplicationGatewayFirewallPolicyExclusion -MatchVariable  $excl.MatchVariable -SelectorMatchOperator $excl.SelectorMatchOperator -Selector $excl.Selector
                    $_ = $exclusions.Add($exclusionEntry)               
                }
            }
        }
    
        $managedRule = New-AzApplicationGatewayFirewallPolicyManagedRule -ManagedRuleSet $managedRuleSet
        $exclCount = $exclusions.Count
        if ($exclCount -ne 0) {
            $managedRule = New-AzApplicationGatewayFirewallPolicyManagedRule -ManagedRuleSet $managedRuleSet -Exclusion $exclusions
        }

        
        $policySetting = New-AzApplicationGatewayFirewallPolicySetting -MaxFileUploadInMb $appgw.WebApplicationFirewallConfiguration.FileUploadLimitInMb -MaxRequestBodySizeInKb $appgw.WebApplicationFirewallConfiguration.MaxRequestBodySizeInKb -Mode Detection -State Disabled
        if ($appgw.WebApplicationFirewallConfiguration.FirewallMode -eq "Prevention") {
            $policySetting.Mode = "Prevention"
        }

        if ($appgw.WebApplicationFirewallConfiguration.Enabled) {
            $policySetting.State = "Enabled"
        }

        $policySetting.RequestBodyCheck = $appgw.WebApplicationFirewallConfiguration.RequestBodyCheck;
    }

    if ($appgw.FirewallPolicy) {
        $customRulePolicyId = $appgw.FirewallPolicy.Id
        $rg = Get-AzResourceGroup -Id $customRulePolicyId
        $crPolicyName = $customRulePolicyId.Substring($customRulePolicyId.LastIndexOf("/") + 1)
        $customRulePolicy = Get-AzApplicationGatewayFirewallPolicy -ResourceGroupName $rg.ResourceGroupName -Name $crPolicyName
        $wafPolicy = New-AzApplicationGatewayFirewallPolicy -ResourceGroupName $rg.ResourceGroupName -Name $wafPolicyName -CustomRule $customRulePolicy.CustomRules -ManagedRule $managedRule -PolicySetting $policySetting -Location $appgw.Location
    } else { 
        $wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name $wafPolicyName -ResourceGroupName $resourceGroupName -PolicySetting $policySetting -ManagedRule $managedRule -Location $appgw.Location
    }

    if (!$wafPolicy) {
        return
    }

    $appgw.FirewallPolicy = $wafPolicy
    $appgw = Set-AzApplicationGateway -ApplicationGateway $appgw
    Write-Host " firewallPolicy: $wafPolicyName has been created/updated successfully and applied to applicationGateway: $applicationGatewayName!"
    return $wafPolicy
}

function Main() {
    Login
    $policy = createNewTopLevelWafPolicy -subscriptionId $subscriptionId -resourceGroupName $resourceGroupName -applicationGatewayName $applicationGatewayName -wafPolicyName $wafPolicyName
    return $policy
}

Main
```
## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre grupos e regras de [regras CRS firewall de aplicação web](application-gateway-crs-rulegroups-rules.md).
