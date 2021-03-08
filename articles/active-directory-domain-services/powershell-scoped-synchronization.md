---
title: Sincronização âmbito usando PowerShell para serviços de domínio AD Azure | Microsoft Docs
description: Saiba como utilizar o Azure AD PowerShell para configurar a sincronização telescópio do Azure AD para um domínio gerido por Serviços de Domínio do Diretório Ativo Azure
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: justinha
ms.openlocfilehash: f877a631fd3c89d74b9e3b47cf205bbcf173ebc0
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102453415"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-azure-ad-powershell"></a>Configure sincronização telescópio de Ad AD a Azure Ative Directory Domain Services usando Azure AD PowerShell

Para fornecer serviços de autenticação, o Azure Ative Directory Domain Services (Azure AD DS) sincroniza os utilizadores e grupos da Azure AD. Num ambiente híbrido, os utilizadores e grupos de um ambiente de Serviços de Domínio de Diretório Ativo (AD DS) podem ser primeiro sincronizados para Azure AD usando Azure AD Connect e, em seguida, sincronizados para Azure AD DS.

Por padrão, todos os utilizadores e grupos de um diretório AD Azure são sincronizados a um domínio gerido Azure AD DS. Se tiver necessidades específicas, pode optar por sincronizar apenas um conjunto definido de utilizadores.

Este artigo mostra-lhe como criar um domínio gerido que utiliza sincronização de âmbito e, em seguida, alterar ou desativar o conjunto de utilizadores com âmbito utilizando o Azure AD PowerShell. Também pode [completar estes passos utilizando o portal Azure.][scoped-sync]

## <a name="before-you-begin"></a>Antes de começar

Para completar este artigo, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um domínio de domínio do Azure Ative Directory Services gerido ativo e configurado no seu inquilino AZure AD.
    * Se necessário, complete o tutorial para criar e configurar um domínio gerido pelos [Serviços de Domínio do Diretório Ativo Azure][tutorial-create-instance].
* Você precisa de privilégios *de administrador global* no seu inquilino AZure AD para alterar o âmbito de sincronização Azure AD DS.

## <a name="scoped-synchronization-overview"></a>Visão geral da sincronização de âmbito

Por padrão, todos os utilizadores e grupos de um diretório AD Azure são sincronizados a um domínio gerido. Se apenas alguns utilizadores precisarem de aceder ao domínio gerido, pode sincronizar apenas essas contas de utilizador. Esta sincronização âmbito é baseada em grupo. Quando configurar a sincronização de âmbito de grupo, apenas as contas de utilizador que pertencem aos grupos especificados são sincronizadas com o domínio gerido. Os grupos aninhados não são sincronizados, apenas os grupos específicos que seleciona.

Pode alterar o âmbito de sincronização antes ou depois de criar o domínio gerido. O âmbito de sincronização é definido por um chefe de serviço com o identificador de aplicação 2565bd9d-da50-47d4-8b85-4c97f669dc36. Para evitar a perda de âmbito, não apague nem altere o principal de serviço. Se for acidentalmente eliminado, o âmbito de sincronização não pode ser recuperado. 

Tenha em mente as seguintes ressalvas se alterar o âmbito de sincronização:

- Ocorre uma sincronização completa.
- Os objetos que já não são necessários no domínio gerido são eliminados. Novos objetos são criados no domínio gerido.

Para saber mais sobre o processo de sincronização, consulte [a sincronização do Azure AD Domain Services][concepts-sync].

## <a name="powershell-script-for-scoped-synchronization"></a>Script PowerShell para sincronização de âmbito

Para configurar a sincronização de âmbito utilizando o PowerShell, guarde primeiro o seguinte script para um ficheiro chamado `Select-GroupsToSync.ps1` .

Este script configura Azure AD DS para sincronizar grupos selecionados do Azure AD. Todas as contas de utilizador que fazem parte dos grupos especificados são sincronizadas com o domínio gerido.

Este script é usado nos passos adicionais deste artigo.

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

## <a name="enable-scoped-synchronization"></a>Permitir a sincronização de âmbito

Para permitir a sincronização de âmbito baseado em grupo para um domínio gerido, complete os seguintes passos:

1. Primeiro conjunto *"filteredSync" = "Ativado"* no recurso Azure AD DS e, em seguida, atualize o domínio gerido.

    Quando solicitado, especifique as credenciais para *um administrador global* para iniciar seduca no seu inquilino AD Azure usando o cmdlet [Connect-AzureAD:][Connect-AzureAD]

    ```powershell
    # Connect to your Azure AD tenant
    Connect-AzureAD

    # Retrieve the Azure AD DS resource.
    $DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

    # Enable group-based scoped synchronization.
    $enableScopedSync = @{"filteredSync" = "Enabled"}

    # Update the Azure AD DS resource
    Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $enableScopedSync
    ```

1. Agora especifique a lista de grupos cujos utilizadores devem ser sincronizados com o domínio gerido.

    Executar o `Select-GroupsToSync.ps1` script e especificar a lista de grupos para sincronizar. No exemplo seguinte, os grupos a sincronizar são *o GroupName1* e *o GroupName2*.

    > [!WARNING]
    > Deve incluir o grupo *de administradores AAD DC* na lista de grupos para sincronização de âmbito. Se não incluir este grupo, o domínio gerido é inutilizável.

    ```powershell
    .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
    ```

Alterar o âmbito da sincronização faz com que o domínio gerido ressincronize todos os dados. Os objetos que já não são necessários no domínio gerido são eliminados e a ressincronização pode demorar muito tempo a ser concluída.

## <a name="modify-scoped-synchronization"></a>Modificar a sincronização âmbito

Para modificar a lista de grupos cujos utilizadores devem ser sincronizados com o domínio gerido, executar o `Select-GroupsToSync.ps1` script e especificar a nova lista de grupos para sincronizar.

No exemplo seguinte, os grupos a sincronizar já não incluem *o GroupName2,* e agora inclui *o GroupName3*.

> [!WARNING]
> Deve incluir o grupo *de administradores AAD DC* na lista de grupos para sincronização de âmbito. Se não incluir este grupo, o domínio gerido é inutilizável.

Quando solicitado, especifique as credenciais para *um administrador global* para iniciar seduca no seu inquilino AD Azure usando o cmdlet [Connect-AzureAD:][Connect-AzureAD]

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Alterar o âmbito da sincronização faz com que o domínio gerido ressincronize todos os dados. Os objetos que já não são necessários no domínio gerido são eliminados e a ressincronização pode demorar muito tempo a ser concluída.

## <a name="disable-scoped-synchronization"></a>Desativar a sincronização de âmbito

Para desativar a sincronização de âmbito baseado em grupo para um domínio gerido, desenrida *"FilteredSync" = "Desativado"* no recurso Azure AD DS e, em seguida, atualize o domínio gerido. Quando estiverem completos, todos os utilizadores e grupos estão programados para sincronizar a partir do Azure AD.

Quando solicitado, especifique as credenciais para *um administrador global* para iniciar seduca no seu inquilino AD Azure usando o cmdlet [Connect-AzureAD:][Connect-AzureAD]

```powershell
# Connect to your Azure AD tenant
Connect-AzureAD

# Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

# Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

# Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

Alterar o âmbito da sincronização faz com que o domínio gerido ressincronize todos os dados. Os objetos que já não são necessários no domínio gerido são eliminados e a ressincronização pode demorar muito tempo a ser concluída.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o processo de sincronização, consulte [a sincronização do Azure AD Domain Services](synchronization.md).

<!-- INTERNAL LINKS -->
[scoped-sync]: scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md

<!-- EXTERNAL LINKS -->
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
