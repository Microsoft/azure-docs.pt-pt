---
title: 'Azure Active Directory Domain Services: Sincronização com escopo | Microsoft Docs'
description: Configurar a sincronização com escopo do Azure AD para seus domínios gerenciados
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 7d3bd8c6c62c0b8a1be6203e426337fcee7d2126
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617119"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-your-managed-domain"></a>Configurar a sincronização com escopo do Azure AD para seu domínio gerenciado
Este artigo mostra como configurar apenas contas de usuário específicas a serem sincronizadas do seu diretório do Azure AD para seu Azure AD Domain Services domínio gerenciado.


## <a name="group-based-scoped-synchronization"></a>Sincronização com escopo baseado em grupo
Por padrão, todos os usuários e grupos no diretório do Azure AD são sincronizados com o domínio gerenciado. Se apenas alguns usuários usarem o domínio gerenciado, você poderá sincronizar apenas essas contas de usuário. A sincronização com escopo baseado em grupo permite que você faça isso. Quando configurado, somente as contas de usuário que pertencem aos grupos especificados são sincronizadas com o domínio gerenciado.

A tabela a seguir ajuda a determinar como usar a sincronização com escopo:

| **Estado atual** | **Estado desejado** | **Configuração necessária** |
| --- | --- | --- |
| O domínio gerenciado existente é configurado para sincronizar todas as contas de usuário e grupos. | Você deseja sincronizar apenas as contas de usuário que pertencem a grupos específicos para seu domínio gerenciado. | [Exclua o domínio gerenciado existente](delete-aadds.md). Em seguida, siga as instruções neste artigo para recriá-lo com a sincronização com escopo definido. |
| Você não tem um domínio gerenciado existente. | Você deseja criar um novo domínio gerenciado e sincronizar apenas as contas de usuário que pertencem a grupos específicos. | Siga as instruções neste artigo para criar um novo domínio gerenciado com a sincronização com escopo definido. |
| O domínio gerenciado existente é configurado para sincronizar somente as contas que pertencem a grupos específicos. | Você deseja modificar a lista de grupos cujos usuários devem ser sincronizados com o domínio de gerenciamento. | Siga as instruções neste artigo para modificar a sincronização com escopo definido. |

> [!WARNING]
> **Alterar o escopo da sincronização faz com que o domínio gerenciado passe pela ressincronização.**
> 
>  * Quando você altera o escopo de sincronização de um domínio gerenciado, ocorre uma ressincronização completa.
>  * Os objetos que não são mais necessários no domínio gerenciado são excluídos. Novos objetos são criados no domínio gerenciado.
>  * A ressincronização pode levar muito tempo para ser concluída, dependendo do número de objetos (usuários, grupos e associações de grupo) em seu domínio gerenciado e seu diretório do Azure AD. Para diretórios grandes com muitas centenas de milhares de objetos, a ressincronização pode levar alguns dias.


## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-azure-portal"></a>Criar um novo domínio gerenciado e habilitar a sincronização com escopo baseado em grupo usando portal do Azure

1. Siga o [Guia de introdução](tutorial-create-instance.md) para criar um domínio gerenciado.
2. Escolha o **escopo** durante a seleção de estilo de sincronização no assistente de criação de Azure AD Domain Services.

## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-powershell"></a>Criar um novo domínio gerenciado e habilitar a sincronização com escopo baseado em grupo usando o PowerShell
Use o PowerShell para concluir este conjunto de etapas. Consulte as instruções para [habilitar Azure Active Directory Domain Services usando o PowerShell](powershell-create-instance.md). Algumas etapas neste artigo são ligeiramente modificadas para configurar a sincronização com escopo.

Conclua as etapas a seguir para configurar a sincronização com escopo baseado em grupo para seu domínio gerenciado:

1. Conclua as seguintes tarefas:
   * [Tarefa 1: Instale os módulos](powershell-create-instance.md#task-1-install-the-required-powershell-modules)do PowerShell necessários.
   * [Tarefa 2: Crie a entidade de serviço necessária em seu diretório](powershell-create-instance.md#task-2-create-the-required-service-principal-in-your-azure-ad-directory)do Azure AD.
   * [Tarefa 3: Crie e configure o ' grupo de administradores de controlador de domínio do AAD '] PowerShell-Create-instance. MD # tarefa-3-Create-and-configure-The-AAD-DC-Administrators-Group).
   * [Tarefa 4: Registre o provedor](powershell-create-instance.md#task-4-register-the-azure-ad-domain-services-resource-provider)de recursos de Azure AD Domain Services.
   * [Tarefa 5: Crie um grupo](powershell-create-instance.md#task-5-create-a-resource-group)de recursos.
   * [Tarefa 6: Crie e configure a rede](powershell-create-instance.md#task-6-create-and-configure-the-virtual-network)virtual.

2. Selecione os grupos que você deseja sincronizar e forneça o nome de exibição dos grupos que você deseja sincronizar com o domínio gerenciado.

3. Salve o [script na seção a seguir](scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) em um arquivo chamado ```Select-GroupsToSync.ps1```. Execute o script como abaixo:

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

   > [!WARNING]
   > **Não se esqueça de incluir o grupo ' Administradores do controlador de domínio do AAD '.**
   >
   > Você deve incluir o grupo "administradores do controlador de domínio do AAD" na lista de grupos configurados para sincronização com escopo. Se você não incluir esse grupo, o domínio gerenciado será inutilizável.
   >

4. Agora, crie o domínio gerenciado e habilite a sincronização com escopo baseado em grupo para o domínio gerenciado. Inclua a propriedade ```"filteredSync" = "Enabled"``` ```Properties``` no parâmetro. Por exemplo, consulte o fragmento de script a seguir, [copiado da tarefa 7: Provisionar o Azure AD Domain Services](powershell-create-instance.md#task-7-provision-the-azure-ad-domain-services-managed-domain)domínio gerenciado.

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "contoso.com"
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
   > Não se esqueça de incluir ```"filteredSync" = "Enabled"``` ```-Properties``` no parâmetro, portanto, a sincronização com escopo está habilitada para o domínio gerenciado.


## <a name="script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1"></a>Script para selecionar grupos para sincronizar com o domínio gerenciado (Select-GroupsToSync. ps1)
Salve o script a seguir em um arquivo```Select-GroupsToSync.ps1```(). Esse script configura Azure AD Domain Services para sincronizar os grupos selecionados com o domínio gerenciado. Todas as contas de usuário pertencentes aos grupos especificados serão sincronizadas com o domínio gerenciado.

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


## <a name="modify-group-based-scoped-synchronization"></a>Modificar sincronização com escopo baseado em grupo
Para modificar a lista de grupos cujos usuários devem ser sincronizados com o domínio gerenciado, execute novamente o [script do PowerShell](scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) e especifique a nova lista de grupos. Lembre-se de sempre especificar o grupo "administradores do controlador de domínio do AAD" nesta lista.

> [!WARNING]
> **Não se esqueça de incluir o grupo ' Administradores do controlador de domínio do AAD '.**
>
> Você deve incluir o grupo "administradores do controlador de domínio do AAD" na lista de grupos configurados para sincronização com escopo. Se você não incluir esse grupo, o domínio gerenciado será inutilizável.
>


## <a name="disable-group-based-scoped-synchronization"></a>Desabilitar sincronização com escopo baseado em grupo
Use o seguinte script do PowerShell para desabilitar a sincronização com escopo baseado em grupo para seu domínio gerenciado:

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

## <a name="next-steps"></a>Passos seguintes
* [Entender a sincronização no Azure AD Domain Services](synchronization.md)
* [Habilitar Azure Active Directory Domain Services usando o PowerShell](powershell-create-instance.md)
