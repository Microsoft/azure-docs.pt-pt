---
title: Sincronização de telescópios para serviços de domínio azure ad  Microsoft Docs
description: Saiba como configurar a sincronização com âmbito de aplicação da Azure AD para um domínio gerido pelo Azure Ative Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: cc126af67a0d8627d61e595cee56f3df8973340d
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77613038"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>Configure sincronização de telescópios de Azure AD para Serviços de Domínio de Diretório Ativo Azure

Para fornecer serviços de autenticação, o Azure Ative Directory Domain Services (Azure AD DS) sincroniza utilizadores e grupos da Azure AD. Num ambiente híbrido, os utilizadores e grupos de um ambiente de Ative Directory Domain Services (AD DS) no local podem ser sincronizados primeiro para o Azure AD Connect utilizando o Azure AD Connect e, em seguida, sincronizados com o Azure AD DS.

Por padrão, todos os utilizadores e grupos de um diretório Azure AD são sincronizados para um domínio gerido pelo Azure AD DS. Se tiver necessidades específicas, pode optar por sincronizar apenas um conjunto definido de utilizadores.

Este artigo mostra-lhe como criar um domínio gerido pelo Azure AD DS que utiliza sincronização com âmbito e, em seguida, alterar ou desativar o conjunto de utilizadores com âmbito.

## <a name="scoped-synchronization-overview"></a>Visão geral da sincronização com âmbito

Por padrão, todos os utilizadores e grupos de um diretório Azure AD são sincronizados para um domínio gerido pelo Azure AD DS. Se apenas alguns utilizadores precisarem de aceder ao domínio gerido, pode sincronizar apenas essas contas de utilizador. Esta sincronização de âmbito é baseada em grupo. Quando configura a sincronização de âmbito de grupo, apenas as contas de utilizador que pertencem aos grupos que especifica são sincronizadas para o domínio gerido pelo Azure AD DS.

A tabela que se segue descreve como utilizar a sincronização com âmbito:

| Estado atual | Estado desejado | Configuração necessária |
| --- | --- | --- |
| Um domínio gerido existente está configurado para sincronizar todas as contas e grupos de utilizadores. | Pretende sincronizar apenas as contas de utilizador que pertencem a grupos específicos. | Não é possível mudar de sincronização de todos os utilizadores para utilizar sincronização com âmbito. [Elimine o domínio gerido existente,](delete-aadds.md)siga os passos deste artigo para recriar um domínio gerido pelo Azure AD DS com sincronização por via geminada configurada. |
| Nenhum domínio gerido existente. | Pretende criar um novo domínio gerido e sincronizar apenas as contas de utilizador pertencentes a grupos específicos. | Siga os passos deste artigo para criar um domínio gerido por AD DS Azure com sincronização de âmbito configurado. |
| Um domínio gerido existente é configurado para sincronizar apenas contas que pertencem a grupos específicos. | Pretende modificar a lista de grupos cujos utilizadores devem ser sincronizados com o domínio gerido pelo Azure AD DS. | Siga os passos deste artigo para modificar a sincronização scoped. |

Utiliza o portal Azure ou powerShell para configurar as definições de sincronização com âmbito:

| Ação | | |
|--|--|--|
| Criar um domínio gerido por AD DS azure e configurar a sincronização com o âmbito | [Portal do Azure](#enable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#enable-scoped-synchronization-using-powershell) |
| Modificar a sincronização com âmbito | [Portal do Azure](#modify-scoped-synchronization-using-the-azure-portal) | [PowerShell](#modify-scoped-synchronization-using-powershell) |
| Desativar a sincronização com âmbito | [Portal do Azure](#disable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> Alterar o âmbito da sincronização faz com que o domínio gerido pelo Azure AD DS ressincronizasse todos os dados.
> 
>  * Quando muda a margem de sincronização para um domínio gerido por DS Azure AD, ocorre uma resincronização completa.
>  * Os objetos que já não são necessários no domínio gerido pelo Azure AD DS são eliminados. Novos objetos são criados no domínio gerido.
>  * A ressincronização pode demorar muito tempo a ser concluída. O tempo de sincronização depende do número de objetos como utilizadores, grupos e membros do grupo no domínio gerido pelo Azure AD DS e no diretório Azure AD. Para grandes diretórios com muitas centenas de milhares de objetos, a ressincronização pode demorar alguns dias.

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>Ativar a sincronização com âmbito utilizando o portal Azure

1. Siga o [tutorial para criar e configurar uma instância Azure AD DS](tutorial-create-instance-advanced.md). Complete todos os pré-requisitos e passos de implantação que não o âmbito da sincronização.
1. Escolha **o Scopeed** na etapa de sincronização e, em seguida, selecione os grupos Azure AD para sincronizar com a instância Azure AD DS.

O domínio gerido pelo Azure AD DS pode demorar até uma hora para completar a implantação. No portal Azure, a página **de visão geral** do seu domínio gerido pelo Azure AD DS mostra o estado atual ao longo desta fase de implantação.

Quando o portal Azure mostrar que o domínio gerido pela AD DS azure terminou o provisionamento, as seguintes tarefas devem ser concluídas:

* Atualize as definições de DNS para a rede virtual para que as máquinas virtuais possam encontrar o domínio gerido para a adesão ou autenticação de domínio.
    * Para configurar o DNS, selecione o seu domínio gerido pelo Azure AD DS no portal. Na janela **Overview,** é-lhe solicitado que configure automaticamente estas definições de DNS.
* Ative a sincronização de [passwords para os Serviços de Domínio AD Do Azure para](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) que os utilizadores finais possam iniciar sessão no domínio gerido usando as suas credenciais corporativas.

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>Modificar a sincronização com o portal Azure

Para modificar a lista de grupos cujos utilizadores devem ser sincronizados com o domínio gerido pelo Azure AD DS, complete os seguintes passos:

1. No portal Azure, procure e selecione Serviços de **Domínio Azure AD**. Escolha a sua instância, como *aaddscontoso.com*.
1. Selecione **Sincronização** a partir do menu do lado esquerdo.
1. Para adicionar um grupo, escolha **+ Selecione grupos** na parte superior e, em seguida, escolha os grupos para adicionar.
1. Para remover um grupo do âmbito de sincronização, selecione-o a partir da lista de grupos atualmente sincronizados e escolha **remover grupos**.
1. Quando todas as alterações forem efetuadas, selecione Salvar o âmbito da **sincronização**.

Alterar o âmbito da sincronização faz com que o domínio gerido pelo Azure AD DS ressincronizasse todos os dados. Os objetos que já não são necessários no domínio gerido pelo Azure AD DS são eliminados e a ressincronização pode demorar muito tempo a ser concluída.

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>Desativar a sincronização com âmbito utilizando o portal Azure

Para desativar a sincronização de âmbito de grupo para um domínio gerido pelo Azure AD DS, complete os seguintes passos:

1. No portal Azure, procure e selecione Serviços de **Domínio Azure AD**. Escolha a sua instância, como *aaddscontoso.com*.
1. Selecione **Sincronização** a partir do menu do lado esquerdo.
1. Desloque o âmbito de sincronização de **Scoped** para **All**e, em seguida, selecione Salvar o âmbito de **sincronização**.

Alterar o âmbito da sincronização faz com que o domínio gerido pelo Azure AD DS ressincronizasse todos os dados. Os objetos que já não são necessários no domínio gerido pelo Azure AD DS são eliminados e a ressincronização pode demorar muito tempo a ser concluída.

## <a name="powershell-script-for-scoped-synchronization"></a>Script PowerShell para sincronização com âmbito

Para configurar a sincronização com o powerShell, guarde primeiro o seguinte script para um ficheiro chamado `Select-GroupsToSync.ps1`. Este script configura o Azure AD DS para sincronizar grupos selecionados a partir de Azure AD. Todas as contas de utilizador que fazem parte dos grupos especificados são sincronizadas com o domínio gerido pelo Azure AD DS.

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

## <a name="enable-scoped-synchronization-using-powershell"></a>Ativar a sincronização com âmbito utilizando o PowerShell

Utilize o PowerShell para completar este conjunto de passos. Consulte as instruções para ativar os Serviços de [Domínio de Diretório Ativo Azure utilizando powerShell](powershell-create-instance.md). Alguns passos neste artigo são modificados ligeiramente para configurar a sincronização scoped.

1. Complete as seguintes tarefas do artigo para permitir o Azure AD DS utilizando o PowerShell. Pare no degrau para criar o domínio gerido. Configura a sincronização com o âmbito de aplicação, cria-se o domínio gerido pelo Azure AD DS.

   * [Instale os módulos PowerShell necessários](powershell-create-instance.md#prerequisites).
   * [Crie o diretor de serviço sinuoso e o grupo Azure AD para acesso administrativo.](powershell-create-instance.md#create-required-azure-ad-resources)
   * [Criar recursos de apoio ao Azure como uma rede virtual e subredes.](powershell-create-instance.md#create-supporting-azure-resources)

1. Determine os grupos e utilizadores que contêm que pretende sincronizar a partir de Azure AD. Faça uma lista dos nomes de exibição dos grupos para sincronizar com O DS Azure.

1. Executar o [script a partir da secção anterior](#powershell-script-for-scoped-synchronization) e usar o parâmetro *-groupsToAdd* para passar a lista de grupos para sincronizar.

   > [!WARNING]
   > Deve incluir o grupo de administradores da *AAD DC* na lista de grupos para sincronização com âmbito. Se não incluir este grupo, o domínio gerido pelo Azure AD DS é inutilizável.

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. Agora crie o domínio gerido pelo Azure AD DS e permita a sincronização de âmbito de grupo. Incluir *"filteredSync" = "Ativado"* no parâmetro *-Propriedades.*

    Detete o seu ID de subscrição Azure e, em seguida, forneça um nome para o domínio gerido, como *aaddscontoso.com*. Pode obter o seu ID de subscrição utilizando o cmdlet [Get-AzSubscription.][Get-AzSubscription] Detete o nome do grupo de recursos, o nome da rede virtual e a região aos valores utilizados nas etapas anteriores para criar os recursos de apoio do Azure:

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "aaddscontoso.com"
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

Leva alguns minutos para criar o recurso e devolver o controlo ao pedido powerShell. O domínio gerido pela AD DS Azure continua a ser aprovisionado em segundo plano, podendo demorar até uma hora a concluir a implantação. No portal Azure, a página **de visão geral** do seu domínio gerido pelo Azure AD DS mostra o estado atual ao longo desta fase de implantação.

Quando o portal Azure mostrar que o domínio gerido pela AD DS azure terminou o provisionamento, as seguintes tarefas devem ser concluídas:

* Atualize as definições de DNS para a rede virtual para que as máquinas virtuais possam encontrar o domínio gerido para a adesão ou autenticação de domínio.
    * Para configurar o DNS, selecione o seu domínio gerido pelo Azure AD DS no portal. Na janela **Overview,** é-lhe solicitado que configure automaticamente estas definições de DNS.
* Se criou um domínio gerido pelo Azure AD DS numa região que suporta zonas de disponibilidade, crie um grupo de segurança de rede para restringir o tráfego na rede virtual para o domínio gerido pelo Azure AD DS. É criado um equilibrador de carga padrão Azure que exige que estas regras sejam criadas. Este grupo de segurança de rede protege o Azure AD DS e é necessário que o domínio gerido funcione corretamente.
    * Para criar o grupo de segurança da rede e as regras exigidas, selecione o seu domínio gerido pelo Azure AD DS no portal. Na janela **Overview,** é-lhe solicitado que crie e configure automaticamente o grupo de segurança da rede.
* Ative a sincronização de [passwords para os Serviços de Domínio AD Do Azure para](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) que os utilizadores finais possam iniciar sessão no domínio gerido usando as suas credenciais corporativas.

## <a name="modify-scoped-synchronization-using-powershell"></a>Modificar a sincronização com o alcance utilizando o PowerShell

Para modificar a lista de grupos cujos utilizadores devem ser sincronizados com o domínio gerido pelo Azure AD DS, reexecutar o [script PowerShell](#powershell-script-for-scoped-synchronization) e especificar a nova lista de grupos. No exemplo seguinte, os grupos para sincronizar já não inclui *o GroupName2*, e agora inclui *o GroupName3*.

> [!WARNING]
> Deve incluir o grupo de administradores da *AAD DC* na lista de grupos para sincronização com âmbito. Se não incluir este grupo, o domínio gerido pelo Azure AD DS é inutilizável.

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Alterar o âmbito da sincronização faz com que o domínio gerido pelo Azure AD DS ressincronizasse todos os dados. Os objetos que já não são necessários no domínio gerido pelo Azure AD DS são eliminados e a ressincronização pode demorar muito tempo a ser concluída.

## <a name="disable-scoped-synchronization-using-powershell"></a>Desativar a sincronização com âmbito utilizando o PowerShell

Para desativar a sincronização de âmbito de grupo para um domínio gerido por AD DS azure, desative *"filteredSync" = "Disabled"* no recurso Azure AD DS e, em seguida, atualize o domínio gerido. Quando estiver concluído, todos os utilizadores e grupos estão preparados para sincronizar a partir de Azure AD.

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

Alterar o âmbito da sincronização faz com que o domínio gerido pelo Azure AD DS ressincronizasse todos os dados. Os objetos que já não são necessários no domínio gerido pelo Azure AD DS são eliminados e a ressincronização pode demorar muito tempo a ser concluída.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o processo de sincronização, consulte [a sincronização do Understand nos Serviços de Domínio Azure AD.](synchronization.md)

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
