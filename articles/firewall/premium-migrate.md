---
title: Migrar para Azure Firewall Premium Preview
description: Saiba como migrar do Azure Firewall Standard para a Azure Firewall Premium Preview.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: ffdcad33568af9955dbdf5aafb1b6ffe4a51681d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100549856"
---
# <a name="migrate-to-azure-firewall-premium-preview"></a>Migrar para Azure Firewall Premium Preview

Pode migrar o Azure Firewall Standard para a Azure Firewall Premium Preview para tirar partido das novas capacidades Premium. Para obter mais informações sobre as funcionalidades de pré-visualização Azure Firewall Premium, consulte [as funcionalidades de pré-visualização do Azure Firewall Premium](premium-features.md).

Os dois exemplos que se seguem mostram como:
- Migrar uma política padrão existente usando a Azure PowerShell
- Migrar uma firewall padrão existente (com regras clássicas) para Azure Firewall Premium com uma política Premium.

## <a name="migrate-an-existing-policy-using-azure-powershell"></a>Migrar uma política existente usando a Azure PowerShell

`Transform-Policy.ps1` é um script Azure PowerShell que cria uma nova política Premium a partir de uma política Standard existente.

Dado um ID de política de firewall padrão, o script transforma-o numa política premium Azure Firewall. O script liga-se primeiro à sua conta Azure, puxa a política, transforma/adiciona vários parâmetros e, em seguida, envia uma nova política Premium. A nova política de prémios chama-se `<previous_policy_name>_premium` .

Exemplo de utilização:

`Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name`

> [!IMPORTANT]
> O guião não migra as definições de Inteligência ameaçadora. Terá de observar essas definições antes de prosseguir e migrar manualmente.

```azurepowershell
<#
    .SYNOPSIS
        Given an Azure firewall policy id the script will transform it to a Premium Azure firewall policy. 
        The script will first pull the policy, transform/add various parameters and then upload a new premium policy. 
        The created policy will be named <previous_policy_name>_premium if no new name provided else new policy will be named as the parameter passed.  
    .Example
        Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name -NewPolicyName <optional param for the new policy name>
#>

param (
    #Resource id of the azure firewall policy. 
    [Parameter(Mandatory=$true)]
    [string]
    $PolicyId,

    # #new filewallpolicy name, if not specified will be the previous name with the '_premium' suffix
    [Parameter(Mandatory=$false)]
    [string]
    $NewPolicyName = ""
)
$ErrorActionPreference = "Stop"
$script:PolicyId = $PolicyId
$script:PolicyName = $NewPolicyName

function ValidatePolicy {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Object]
        $Policy
    )

    Write-Host "Validating resource is as expected"

    if ($null -eq $Policy) {
        Write-Error "Recived null policy"
        exit(1)
    }
    if ($Policy.GetType().Name -ne "PSAzureFirewallPolicy") {
        Write-Host "Resource must be of type Microsoft.Network/firewallPolicies" -ForegroundColor Red
        exit(1)
    }

    if ($Policy.Sku.Tier -eq "Premium") {
        Write-Host "Policy is already premium"
        exit(1)
    }
}

function GetPolicyNewName {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )

    if (-not [string]::IsNullOrEmpty($script:PolicyName)) {
        return $script:PolicyName
    }

    return $Policy.Name + "_premium"
}

function TransformPolicyToPremium {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )    
    $NewPolicyParameters = @{
                        Name = (GetPolicyNewName -Policy $Policy) 
                        ResourceGroupName = $Policy.ResourceGroupName 
                        Location = $Policy.Location 
                        ThreatIntelMode = $Policy.ThreatIntelMode 
                        BasePolicy = $Policy.BasePolicy 
                        DnsSetting = $Policy.DnsSettings 
                        Tag = $Policy.Tag 
                        SkuTier = "Premium" 
    }

    Write-Host "Creating new policy"
    $premiumPolicy = New-AzFirewallPolicy @NewPolicyParameters

    Write-Host "Populating rules in new policy"
    foreach ($ruleCollectionGroup in $Policy.RuleCollectionGroups) {
        $ruleResource = Get-AzResource -ResourceId $ruleCollectionGroup.Id
        $ruleToTransfom = Get-AzFirewallPolicyRuleCollectionGroup -AzureFirewallPolicy $Policy -Name $ruleResource.Name
        $ruleCollectionGroup = @{
            FirewallPolicyObject = $premiumPolicy
            Priority = $ruleToTransfom.Properties.Priority
            Name = $ruleToTransfom.Name
        }

        if ($ruleToTransfom.Properties.RuleCollection.Count) {
            $ruleCollectionGroup["RuleCollection"] = $ruleToTransfom.Properties.RuleCollection
        }

        Set-AzFirewallPolicyRuleCollectionGroup @ruleCollectionGroup
    }
}

function ValidateAzNetworkModuleExists {
    Write-Host "Validating needed module exists"
    $networkModule = Get-InstalledModule -Name "Az.Network" -ErrorAction SilentlyContinue
    if (($null -eq $networkModule) -or ($networkModule.Version -lt 4.5)){
        Write-Host "Please install Az.Network module version 4.5.0 or higher, see instructions: https://github.com/Azure/azure-powershell#installation"
        exit(1)
    }
    Import-Module Az.Network -MinimumVersion 4.5.0
}

ValidateAzNetworkModuleExists
$policy = Get-AzFirewallPolicy -ResourceId $script:PolicyId
ValidatePolicy -Policy $policy
TransformPolicyToPremium -Policy $policy
```

## <a name="migrate-an-existing-standard-firewall-using-the-azure-portal"></a>Migrar uma firewall padrão existente usando o portal Azure

Este exemplo mostra como usar o portal Azure para migrar uma firewall padrão (regras clássicas) para Azure Firewall Premium com uma política Premium.

1. A partir do portal Azure, selecione a sua firewall padrão. Na página **'Visão Geral',** selecione **Migrar para a política de firewall**.

   :::image type="content" source="media/premium-migrate/firewall-overview-migrate.png" alt-text="Migrar para a política de firewall":::

1. Na página **política de Migração para firewall,** selecione **Review + create**.
1. Selecione **Criar**.

   A implantação leva alguns minutos para ser concluída.
1. Utilize o `Transform-Policy.ps1` [script Azure PowerShell](#migrate-an-existing-policy-using-azure-powershell) para transformar esta nova política padrão numa política Premium.
1. No portal, selecione o seu recurso de firewall padrão. 
1. Em **Automação,** selecione **modelo de exportação.** Mantenha este separador de navegador aberto. Voltará mais tarde.
   > [!TIP]
   > Para garantir que não perde o modelo, descarregue e guarde-o caso o separador do navegador seja fechado ou atualizado.
1. Abra um novo separador de navegador, navegue até ao portal Azure e abra o grupo de recursos que contém a sua firewall.
1. Elimine a instância de firewall padrão existente.

   Este processo demora alguns minutos para ser concluído.

1. Volte ao separador do navegador com o modelo exportado.
1. Selecione **Implementar** e, em seguida, no modelo de **edição** de página **de implementação personalizada.**
1. Editar o texto do modelo:
   
   1. No `Microsoft.Network/azureFirewalls` recurso, em `Properties` , em , , alterar o de `sku` `tier` "Standard" para "Premium".
   1. Sob o `Parameters` modelo, alteração `defaultValue` para o a `firewallPolicies_FirewallPolicy_,<your policy name>_externalid` partir de:
      
       `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>"`

      para:

      `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>_premium"`
1. Selecione **Guardar**.
1. Selecione **Rever + Criar**.
1. Selecione **Criar**.

Quando a implementação estiver concluída, pode agora configurar todas as novas funcionalidades de Pré-visualização Azure Firewall Premium.

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre as funcionalidades Azure Firewall Premium](premium-features.md)