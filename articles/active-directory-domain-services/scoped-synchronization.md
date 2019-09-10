---
title: Sincronização com escopo para Azure AD Domain Services | Microsoft Docs
description: Saiba como configurar a sincronização com escopo do Azure AD para um Azure Active Directory Domain Services domínio gerenciado
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 09/06/2019
ms.author: iainfou
ms.openlocfilehash: 5fe19d3800883782187ae15c0a6fc0cd9709f0e9
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842677"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>Configurar a sincronização com escopo do Azure AD para Azure Active Directory Domain Services

Para fornecer serviços de autenticação, Azure Active Directory Domain Services (Azure AD DS) sincroniza usuários e grupos do Azure AD. Em um ambiente híbrido, os usuários e grupos de um ambiente de Active Directory Domain Services local (AD DS) podem ser sincronizados primeiro com o Azure AD usando Azure AD Connect e, em seguida, sincronizados com o Azure AD DS. Por padrão, todos os usuários e grupos de um diretório do AD do Azure são sincronizados com um domínio gerenciado do Azure AD DS. Se você tiver necessidades específicas, poderá optar por sincronizar apenas um conjunto definido de usuários.

Este artigo mostra como criar um domínio gerenciado AD DS do Azure que usa a sincronização com escopo e, em seguida, alterar ou desabilitar o conjunto de usuários com escopo.

## <a name="scoped-synchronization-overview"></a>Visão geral da sincronização com escopo

Por padrão, todos os usuários e grupos de um diretório do AD do Azure são sincronizados com um domínio gerenciado do Azure AD DS. Se apenas alguns usuários precisarem acessar o domínio gerenciado, você poderá sincronizar somente as contas de usuário. Essa sincronização com escopo é baseada em grupo. Quando você configura a sincronização com escopo baseado em grupo, somente as contas de usuário que pertencem aos grupos que você especifica são sincronizadas com o domínio gerenciado AD DS do Azure.

A tabela a seguir descreve como usar a sincronização com escopo:

| Estado atual | Estado pretendido | Configuração necessária |
| --- | --- | --- |
| Um domínio gerenciado existente é configurado para sincronizar todas as contas de usuário e grupos. | Você deseja sincronizar somente as contas de usuário que pertencem a grupos específicos. | Não é possível alterar a sincronização de todos os usuários para o uso da sincronização com escopo. [Exclua o domínio gerenciado existente](delete-aadds.md)e siga as etapas neste artigo para recriar um domínio gerenciado do Azure AD DS com a sincronização com escopo definido. |
| Nenhum domínio gerenciado existente. | Você deseja criar um novo domínio gerenciado e sincronizar apenas as contas de usuário que pertencem a grupos específicos. | Siga as etapas neste artigo para criar um domínio gerenciado do Azure AD DS com a sincronização com escopo definido. |
| Um domínio gerenciado existente é configurado para sincronizar somente as contas que pertencem a grupos específicos. | Você deseja modificar a lista de grupos cujos usuários devem ser sincronizados com o domínio gerenciado AD DS do Azure. | Siga as etapas neste artigo para modificar a sincronização com escopo definido. |

Use o portal do Azure ou o PowerShell para definir as configurações de sincronização com escopo definido:

| Action | | |
|--|--|--|
| Criar um domínio gerenciado AD DS do Azure e configurar a sincronização com escopo | [Azure portal](#enable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#enable-scoped-synchronization-using-powershell) |
| Modificar sincronização com escopo | [Azure portal](#modify-scoped-synchronization-using-the-azure-portal) | [PowerShell](#modify-scoped-synchronization-using-powershell) |
| Desabilitar sincronização com escopo | [Azure portal](#disable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> Alterar o escopo da sincronização faz com que o domínio gerenciado AD DS do Azure sincronize novamente todos os dados.
> 
>  * Quando você altera o escopo de sincronização de um domínio gerenciado do Azure AD DS, ocorre uma ressincronização completa.
>  * Os objetos que não são mais necessários no domínio gerenciado AD DS do Azure são excluídos. Novos objetos são criados no domínio gerenciado.
>  * A ressincronização pode levar muito tempo para ser concluída. O tempo de sincronização depende do número de objetos, como usuários, grupos e associações de grupo no domínio gerenciado do Azure AD DS e no diretório do AD do Azure. Para diretórios grandes com muitas centenas de milhares de objetos, a ressincronização pode levar alguns dias.

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>Habilitar sincronização com escopo usando o portal do Azure

1. Siga o [tutorial para criar e configurar uma instância de AD DS do Azure](tutorial-create-instance.md). Conclua todos os pré-requisitos e as etapas de implantação diferentes de para o escopo de sincronização.
1. Escolha **escopo** na etapa de sincronização e selecione os grupos do Azure ad para sincronizar com a instância de AD DS do Azure.

O domínio gerenciado AD DS do Azure pode levar até uma hora para concluir a implantação. No portal do Azure, a página **visão geral** do domínio gerenciado do Azure AD DS mostra o status atual durante esse estágio de implantação.

Quando o portal do Azure mostra que o domínio gerenciado do Azure AD DS concluiu o provisionamento, as seguintes tarefas precisam ser concluídas:

* Atualize as configurações de DNS para a rede virtual para que as máquinas virtuais possam encontrar o domínio gerenciado para ingresso no domínio ou autenticação.
    * Para configurar o DNS, selecione seu domínio gerenciado AD DS do Azure no Portal. Na janela **visão geral** , é solicitado que você defina automaticamente essas configurações de DNS.
* [Habilite a sincronização de senha para Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) para que os usuários finais possam entrar no domínio gerenciado usando suas credenciais corporativas.

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>Modificar sincronização com escopo usando o portal do Azure

Para modificar a lista de grupos cujos usuários devem ser sincronizados com o domínio gerenciado AD DS do Azure, conclua as seguintes etapas:

1. Na portal do Azure, selecione sua instância do AD DS do Azure, como *contoso.com*.
1. Selecione **sincronização** no menu do lado esquerdo.
1. Para adicionar um grupo, escolha **+ Selecionar grupos** na parte superior e, em seguida, escolha os grupos a serem adicionados.
1. Para remover um grupo do escopo de sincronização, selecione-o na lista de grupos sincronizados no momento e escolha **remover grupos**.
1. Quando todas as alterações forem feitas, selecione **salvar escopo de sincronização**.

Alterar o escopo da sincronização faz com que o domínio gerenciado AD DS do Azure sincronize novamente todos os dados. Os objetos que não são mais necessários no domínio gerenciado AD DS do Azure são excluídos e a ressincronização pode levar muito tempo para ser concluída.

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>Desabilitar sincronização com escopo usando o portal do Azure

Para desabilitar a sincronização com escopo baseado em grupo para um domínio gerenciado do Azure AD DS, conclua as seguintes etapas:

1. Na portal do Azure, selecione sua instância do AD DS do Azure, como *contoso.com*.
1. Selecione **sincronização** no menu do lado esquerdo.
1. Defina o escopo de sincronização de **escopo** para **todos**e selecione **salvar escopo de sincronização**.

Alterar o escopo da sincronização faz com que o domínio gerenciado AD DS do Azure sincronize novamente todos os dados. Os objetos que não são mais necessários no domínio gerenciado AD DS do Azure são excluídos e a ressincronização pode levar muito tempo para ser concluída.

## <a name="powershell-script-for-scoped-synchronization"></a>Script do PowerShell para sincronização com escopo

Para configurar a sincronização com escopo usando o PowerShell, primeiro salve o script a seguir em `Select-GroupsToSync.ps1`um arquivo chamado. Esse script configura o Azure AD DS para sincronizar os grupos selecionados do Azure AD. Todas as contas de usuário que fazem parte dos grupos especificados são sincronizadas com o domínio gerenciado AD DS do Azure.

Esse script é usado nas etapas adicionais neste artigo.

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

## <a name="enable-scoped-synchronization-using-powershell"></a>Habilitar sincronização com escopo usando o PowerShell

Use o PowerShell para concluir este conjunto de etapas. Consulte as instruções para [habilitar Azure Active Directory Domain Services usando o PowerShell](powershell-create-instance.md). Algumas etapas neste artigo são ligeiramente modificadas para configurar a sincronização com escopo.

1. Conclua as seguintes tarefas do artigo para habilitar o Azure AD DS usando o PowerShell. Pare na etapa para realmente criar o domínio gerenciado. Você configura a sincronização com escopo criado o domínio gerenciado do Azure AD DS.

   * [Instale os módulos do PowerShell necessários](powershell-create-instance.md#prerequisites).
   * [Crie a entidade de serviço necessária e o grupo do Azure ad para acesso administrativo](powershell-create-instance.md#create-required-azure-ad-resources).
   * [Crie recursos do Azure de suporte como uma rede virtual e sub-redes](powershell-create-instance.md#create-supporting-azure-resources).

1. Determine os grupos e usuários que eles contêm que você deseja sincronizar do Azure AD. Faça uma lista dos nomes de exibição dos grupos para sincronizar com o Azure AD DS.

1. Execute o [script da seção anterior](#powershell-script-for-scoped-synchronization) e use o parâmetro *-groupsToAdd* para passar a lista de grupos a serem sincronizados.

   > [!WARNING]
   > Você deve incluir o grupo de *Administradores de DC do AAD* na lista de grupos para sincronização com escopo. Se você não incluir esse grupo, o domínio gerenciado AD DS do Azure será inutilizável.

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. Agora, crie o domínio gerenciado AD DS do Azure e habilite a sincronização com escopo baseado em grupo. Inclua *"filteredSync" = "Enabled"* no parâmetro *-Properties* .

    Defina sua ID de assinatura do Azure e forneça um nome para o domínio gerenciado, como *contoso.com*. Você pode obter sua ID de assinatura usando o cmdlet [Get-AzSubscription][Get-AzSubscription] . Defina o nome do grupo de recursos, o nome da rede virtual e a região para os valores usados nas etapas anteriores para criar os recursos de suporte do Azure:

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "contoso.com"
   $ResourceGroupName = "myResourceGroup"
   $VnetName = "myVnet"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -Force -Verbose
   ```

Leva alguns minutos para criar o recurso e retornar o controle para o prompt do PowerShell. O Azure AD DS domínio gerenciado continua a ser provisionado em segundo plano e pode levar até uma hora para concluir a implantação. No portal do Azure, a página **visão geral** do domínio gerenciado do Azure AD DS mostra o status atual durante esse estágio de implantação.

Quando o portal do Azure mostra que o domínio gerenciado do Azure AD DS concluiu o provisionamento, as seguintes tarefas precisam ser concluídas:

* Atualize as configurações de DNS para a rede virtual para que as máquinas virtuais possam encontrar o domínio gerenciado para ingresso no domínio ou autenticação.
    * Para configurar o DNS, selecione seu domínio gerenciado AD DS do Azure no Portal. Na janela **visão geral** , é solicitado que você defina automaticamente essas configurações de DNS.
* [Habilite a sincronização de senha para Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) para que os usuários finais possam entrar no domínio gerenciado usando suas credenciais corporativas.

## <a name="modify-scoped-synchronization-using-powershell"></a>Modificar sincronização com escopo usando o PowerShell

Para modificar a lista de grupos cujos usuários devem ser sincronizados com o domínio gerenciado AD DS do Azure, execute novamente o [script do PowerShell](#powershell-script-for-scoped-synchronization) e especifique a nova lista de grupos. No exemplo a seguir, os grupos para sincronizar não incluem mais *GroupName2*e agora incluem *GroupName3*.

> [!WARNING]
> Você deve incluir o grupo de *Administradores de DC do AAD* na lista de grupos para sincronização com escopo. Se você não incluir esse grupo, o domínio gerenciado AD DS do Azure será inutilizável.

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Alterar o escopo da sincronização faz com que o domínio gerenciado AD DS do Azure sincronize novamente todos os dados. Os objetos que não são mais necessários no domínio gerenciado AD DS do Azure são excluídos e a ressincronização pode levar muito tempo para ser concluída.

## <a name="disable-scoped-synchronization-using-powershell"></a>Desabilitar sincronização com escopo usando o PowerShell

Para desabilitar a sincronização com escopo baseado em grupo para um domínio gerenciado do Azure AD DS, defina *"filteredSync" = "Disabled"* no recurso de AD DS do Azure e, em seguida, atualize o domínio gerenciado. Ao concluir, todos os usuários e grupos serão definidos para sincronizar do Azure AD.

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

Alterar o escopo da sincronização faz com que o domínio gerenciado AD DS do Azure sincronize novamente todos os dados. Os objetos que não são mais necessários no domínio gerenciado AD DS do Azure são excluídos e a ressincronização pode levar muito tempo para ser concluída.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre o processo de sincronização, consulte [entender a sincronização no Azure AD Domain Services](synchronization.md).

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
