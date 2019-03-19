---
title: 'Azure Active Directory Domain Services: Sincronização de âmbito | Documentos da Microsoft'
description: Configurar a sincronização de âmbito do Azure AD para os domínios geridos
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: ergreenl
ms.openlocfilehash: ac11244b87c87285722b4922da69530fab98c299
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58117613"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-your-managed-domain"></a>Configurar a sincronização de âmbito do Azure AD para o seu domínio gerido
Este artigo mostra-lhe como configurar contas de usuários específicos a serem sincronizados do diretório do Azure AD ao seu domínio gerido do Azure AD Domain Services.


## <a name="group-based-scoped-synchronization"></a>Com base no grupo de sincronização de âmbito
Por predefinição, todos os utilizadores e grupos no seu diretório do Azure AD são sincronizados com o seu domínio gerido. Se apenas a alguns utilizadores utilizarem o domínio gerido, pode sincronizar apenas as contas de utilizador. Com base no grupo de sincronização âmbito permite-lhe fazê-lo. Quando configurado, as únicas contas de utilizador que pertencem aos grupos que especificou são sincronizadas com o domínio gerido.

A tabela seguinte ajuda-o a determinar como utilizar o âmbito de sincronização:

| **Estado atual** | **Estado pretendido** | **Configuração necessária** |
| --- | --- | --- |
| O domínio gerido existente está configurado para sincronizar todas as contas de utilizador e grupos. | Pretende sincronizar apenas as contas de utilizador que pertençam a grupos específicos ao seu domínio gerido. | [Eliminar o domínio gerido existente](active-directory-ds-disable-aadds.md). Em seguida, siga as instruções neste artigo para recriá-la com a sincronização de âmbito configurada. |
| Não tem um domínio gerido existente. | Pretende criar um novo domínio gerido e sincronizar apenas as contas de utilizador que pertencem a grupos específicos. | Siga as instruções neste artigo para criar um novo domínio gerido com a sincronização de âmbito configurada. |
| O domínio gerido existente está configurado para sincronizar apenas as contas que pertençam a grupos específicos. | Que pretende modificar a lista de grupos, cujos usuários devem ser sincronizados com o domínio de gerir. | Siga as instruções neste artigo para modificar o âmbito de sincronização. |

> [!WARNING]
> **A alteração do âmbito de sincronização faz com que o seu domínio gerido ir até a ressincronização.**
> 
>  * Quando altera o escopo de sincronização para um domínio gerido, ocorre uma ressincronização completa.
>  * Objetos que já não são necessárias no domínio gerido são eliminados. Novos objetos são criados no domínio gerido.
>  * A ressincronização pode demorar muito tempo a concluir, consoante o número de objetos (utilizadores, grupos e associações de grupo) no seu domínio gerido e de diretório do Azure AD. Para grandes diretórios com várias centenas de milhares de objetos, a ressincronização pode demorar alguns dias.


## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-azure-portal"></a>Criar um novo domínio gerido e ativar a sincronização âmbito com base em grupo através do portal do Azure

1. Siga os [guia de introdução](active-directory-ds-getting-started.md) para criar um domínio gerido.
2. Escolher **âmbito** durante a seleção de estilo de sincronização no Assistente de criação de serviços de domínio do Azure AD.

## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-powershell"></a>Criar um novo domínio gerido e ativar a sincronização âmbito com base em grupo com o PowerShell
Utilize o PowerShell para concluir este conjunto de passos. Consulte as instruções para [ativar o Azure Active Directory Domain Services com o PowerShell](active-directory-ds-enable-using-powershell.md). Alguns passos neste artigo são um pouco modificado para configurar a sincronização de âmbito.

Conclua os seguintes passos para configurar a sincronização de âmbito com base em grupo ao seu domínio gerido:

1. Conclua as seguintes tarefas:
   * [Tarefa 1: Instalar os módulos do PowerShell necessários](active-directory-ds-enable-using-powershell.md#task-1-install-the-required-powershell-modules).
   * [Tarefa 2: Criar o principal de serviço necessário no diretório do Azure AD](active-directory-ds-enable-using-powershell.md#task-2-create-the-required-service-principal-in-your-azure-ad-directory).
   * [Tarefa 3: Criar e configurar o grupo "Administradores do AAD DC"](active-directory-ds-enable-using-powershell.md#task-3-create-and-configure-the-aad-dc-administrators-group).
   * [Tarefa 4: Registar o fornecedor de recursos do Azure AD Domain Services](active-directory-ds-enable-using-powershell.md#task-4-register-the-azure-ad-domain-services-resource-provider).
   * [Tarefa 5: Criar um grupo de recursos](active-directory-ds-enable-using-powershell.md#task-5-create-a-resource-group).
   * [Tarefa 6: Criar e configurar a rede virtual](active-directory-ds-enable-using-powershell.md#task-6-create-and-configure-the-virtual-network).

2. Selecione os grupos que pretende sincronizar e forneça o nome a apresentar dos grupos que pretende sincronizar o domínio gerido.

3. Guardar a [script na seção a seguir](active-directory-ds-scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) para um arquivo chamado ```Select-GroupsToSync.ps1```. Execute o script, conforme mostrado abaixo:

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

   > [!WARNING]
   > **Não se esqueça de incluir o grupo "Administradores do AAD DC".**
   >
   > Tem de incluir o grupo "Administradores do AAD DC" na lista de grupos configurados para a sincronização de âmbito. Se não incluir este grupo, o domínio gerido ficará inutilizável.
   >

4. Agora, crie o domínio gerido e ativar a sincronização de âmbito com base em grupo para o domínio gerido. Incluir a propriedade ```"filteredSync" = "Enabled"``` no ```Properties``` parâmetro. Por exemplo, consulte o seguinte fragmento de script, copiado do [Tarefa 7: Aprovisionar o domínio gerido do Azure AD Domain Services](active-directory-ds-enable-using-powershell.md#task-7-provision-the-azure-ad-domain-services-managed-domain).

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "contoso100.com"
   $ResourceGroupName = "ContosoAaddsRg"
   $VnetName = "DomainServicesVNet_WUS"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -ApiVersion 2017-06-01 -Force -Verbose
   ```

   > [!TIP]
   > Não se esqueça de incluir ```"filteredSync" = "Enabled"``` no ```-Properties``` parâmetro, para que o âmbito de sincronização está ativada para o domínio gerido.


## <a name="script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1"></a>Script para selecionar grupos para sincronizar com o domínio gerido (Select-GroupsToSync.ps1)
Guarde o seguinte script para um ficheiro (```Select-GroupsToSync.ps1```). Este script configura os serviços de domínio do Azure AD para sincronizar grupos selecionados para o domínio gerido. Todas as contas de utilizador que pertencem a grupos especificados serão sincronizadas com o domínio gerido.

```powershell
param (
    [Parameter(Position = 0)]
    [String[]]$groupsToAdd
)

Connect-AzureAD
$sp = Get-AzureADServicePrincipal -Filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
$role = $sp.AppRoles | where-object -FilterScript {$_.DisplayName -eq "User"}

Write-Output "`n****************************************************************************"

Write-Output "Total group-assignments need to be added: $($groupsToAdd.Count)"
$newGroupIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($groupName in $groupsToAdd)
{
    try
    {
        $group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
        $newGroupIds.Add($group.ObjectId)

        Write-Output "Group-Name: $groupName, Id: $($group.ObjectId)"
    }
    catch
    {
        Write-Error "Failed to find group: $groupName. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

$currentAssignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId
Write-Output "Total current group-assignments: $($currentAssignments.Count), SP-ObjectId: $($sp.ObjectId)"

$currAssignedObjectIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($assignment in $currentAssignments)
{
    Write-Output "Assignment-ObjectId: $($assignment.PrincipalId)"

    if ($newGroupIds.Contains($assignment.PrincipalId) -eq $false)
    {
        Write-Output "This assignment is not needed anymore. Removing it! Assignment-ObjectId: $($assignment.PrincipalId)"
        Remove-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -AppRoleAssignmentId $assignment.ObjectId
    }
    else
    {
        $currAssignedObjectIds.Add($assignment.PrincipalId)
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

foreach ($id in $newGroupIds)
{
    try
    {
        if ($currAssignedObjectIds.Contains($id) -eq $false)
        {
            Write-Output "Adding new group-assignment. Role-Id: $($role.Id), Group-Object-Id: $id, ResourceId: $($sp.ObjectId)"
            New-AzureADGroupAppRoleAssignment -Id $role.Id -ObjectId $id -PrincipalId $id -ResourceId $sp.ObjectId
        }
        else
        {
            Write-Output "Group-ObjectId: $id is already assigned."
        }
    }
    catch
    {
        Write-Error "Exception occurred assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```


## <a name="modify-group-based-scoped-synchronization"></a>Modificar baseado no grupo de sincronização de âmbito
Para modificar a lista de grupos, cujos usuários devem ser sincronizados com o seu domínio gerido, execute novamente o [script do PowerShell](active-directory-ds-scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) e especifique a nova lista de grupos. Lembre-se sempre especificar o grupo "Administradores do AAD DC" nesta lista.

> [!WARNING]
> **Não se esqueça de incluir o grupo "Administradores do AAD DC".**
>
> Tem de incluir o grupo "Administradores do AAD DC" na lista de grupos configurados para a sincronização de âmbito. Se não incluir este grupo, o domínio gerido ficará inutilizável.
>


## <a name="disable-group-based-scoped-synchronization"></a>Desativar sincronização de âmbito com base em grupo
Utilize o seguinte script do PowerShell para desativar a baseada em grupo no âmbito de sincronização para o seu domínio gerido:

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

## <a name="next-steps"></a>Passos Seguintes
* [Compreender a sincronização no Azure AD Domain Services](active-directory-ds-synchronization.md)
* [Ativar o Azure Active Directory Domain Services com o PowerShell](active-directory-ds-enable-using-powershell.md)
