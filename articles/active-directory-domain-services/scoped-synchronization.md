---
title: Sincronização âmbito para Serviços de Domínio AD Azure / Microsoft Docs
description: Saiba como configurar a sincronização scoped de Azure AD para um domínio gerido por Azure Ative Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: 097c894594987e92038beeaf4b17a3e67538fdad
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87046070"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>Configure sincronização âmbito de sincronização de Azure AD para Azure Ative Directory Domain Services

Para fornecer serviços de autenticação, o Azure Ative Directory Domain Services (Azure AD DS) sincroniza os utilizadores e grupos da Azure AD. Num ambiente híbrido, os utilizadores e grupos de um ambiente de Serviços de Domínio de Diretório Ativo (AD DS) podem ser primeiro sincronizados para Azure AD usando Azure AD Connect e, em seguida, sincronizados para Azure AD DS.

Por padrão, todos os utilizadores e grupos de um diretório AD Azure são sincronizados a um domínio gerido Azure AD DS. Se tiver necessidades específicas, pode optar por sincronizar apenas um conjunto definido de utilizadores.

Este artigo mostra-lhe como criar um domínio gerido que utiliza sincronização de âmbito e, em seguida, alterar ou desativar o conjunto de utilizadores com âmbito.

## <a name="scoped-synchronization-overview"></a>Visão geral da sincronização de âmbito

Por padrão, todos os utilizadores e grupos de um diretório AD Azure são sincronizados a um domínio gerido. Se apenas alguns utilizadores precisarem de aceder ao domínio gerido, pode sincronizar apenas essas contas de utilizador. Esta sincronização âmbito é baseada em grupo. Quando configurar a sincronização de âmbito de grupo, apenas as contas de utilizador que pertencem aos grupos especificados são sincronizadas com o domínio gerido.

O quadro que se segue descreve como utilizar a sincronização de âmbito:

| Estado atual | Estado desejado | Configuração necessária |
| --- | --- | --- |
| Um domínio gerido existente é configurado para sincronizar todas as contas e grupos de utilizadores. | Pretende sincronizar apenas contas de utilizador que pertencem a grupos específicos. | Não é possível mudar de sincronização de todos os utilizadores para usar sincronização de âmbito. [Elimine o domínio gerido existente,](delete-aadds.md)em seguida, siga os passos deste artigo para recriar um domínio gerido com sincronização de âmbito configurado. |
| Nenhum domínio gerido existente. | Pretende criar um novo domínio gerido e sincronizar apenas contas de utilizadores pertencentes a grupos específicos. | Siga os passos deste artigo para criar um domínio gerido com sincronização de âmbito configurado. |
| Um domínio gerido existente é configurado para sincronizar apenas contas que pertencem a grupos específicos. | Pretende modificar a lista de grupos cujos utilizadores devem ser sincronizados com o domínio gerido. | Siga os passos deste artigo para modificar a sincronização de âmbito. |

Utilize o portal Azure ou o PowerShell para configurar as definições de sincronização com âmbito:

| Ação | Utilização<br>Portal do Azure | Utilização<br>PowerShell |
| ------ | ------------------- | ----------------- |
| Criar um domínio gerido e configurar sincronização âmbitoda | [Portal do Azure](#enable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#enable-scoped-synchronization-using-powershell) |
| Modificar a sincronização âmbito | [Portal do Azure](#modify-scoped-synchronization-using-the-azure-portal) | [PowerShell](#modify-scoped-synchronization-using-powershell) |
| Desativar a sincronização de âmbito | [Portal do Azure](#disable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> Alterar o âmbito da sincronização faz com que o domínio gerido ressincronize todos os dados. As seguintes considerações são aplicáveis:
> 
>  * Quando altera o âmbito de sincronização para um domínio gerido, ocorre uma ressincronização completa.
>  * Os objetos que já não são necessários no domínio gerido são eliminados. Novos objetos são criados no domínio gerido.
>  * A ressincronização pode demorar muito tempo a ser completada. O tempo de sincronização depende do número de objetos como utilizadores, grupos e membros do grupo no domínio gerido e diretório AD AZure. Para grandes diretórios com muitas centenas de milhares de objetos, a ressincronização pode demorar alguns dias.

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>Permitir a sincronização com âmbito utilizando o portal Azure

Para permitir a sincronização no portal Azure, complete os seguintes passos:

1. Siga o [tutorial para criar e configurar um domínio gerido](tutorial-create-instance-advanced.md). Preencha todos os pré-requisitos e etapas de implantação que não seja para o âmbito de sincronização.
1. Escolha **Scoped** no passo de sincronização e, em seguida, selecione os grupos AD Azure para sincronizar com o domínio gerido.

O domínio gerido pode demorar até uma hora para completar a implementação. No portal Azure, a página **'Visão Geral'** para o seu domínio gerido mostra o estado atual ao longo desta fase de implantação.

Quando o portal Azure mostrar que o domínio gerido terminou o provisionamento, as seguintes tarefas devem ser concluídas:

* Atualize as definições de DNS para a rede virtual para que as máquinas virtuais possam encontrar o domínio gerido para a junção de domínio ou autenticação.
    * Para configurar o DNS, selecione o seu domínio gerido no portal. Na janela **'Vista Geral',** é solicitado que configufique automaticamente estas definições de DNS.
* [Ativar a sincronização de palavras-passe nos Serviços de Domínio AD Azure para](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) que os utilizadores finais possam iniciar súm na área gerida utilizando as suas credenciais corporativas.

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>Modificar a sincronização âmbito através do portal Azure

Para modificar a lista de grupos cujos utilizadores devem ser sincronizados com o domínio gerido, complete os seguintes passos:

1. No portal Azure, procure e selecione **serviços de domínio Azure AD**. Escolha o seu domínio gerido, como *aaddscontoso.com*.
1. Selecione **a sincronização** do menu do lado esquerdo.
1. Para adicionar um grupo, escolha **+ Selecione os grupos** no topo e, em seguida, escolha os grupos a adicionar.
1. Para remover um grupo do âmbito de sincronização, selecione-o da lista de grupos atualmente sincronizados e escolha **grupos Remover**.
1. Quando todas as alterações forem efetuadas, **selecione Guardar o âmbito de sincronização**.

Alterar o âmbito da sincronização faz com que o domínio gerido ressincronize todos os dados. Os objetos que já não são necessários no domínio gerido são eliminados e a ressincronização pode demorar muito tempo a ser concluída.

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>Desativar a sincronização com o portal Azure

Para desativar a sincronização de âmbito de grupo para um domínio gerido, complete os seguintes passos:

1. No portal Azure, procure e selecione **serviços de domínio Azure AD**. Escolha o seu domínio gerido, como *aaddscontoso.com*.
1. Selecione **a sincronização** do menu do lado esquerdo.
1. Desloque o âmbito de sincronização de **Scoped** para **All**e, em seguida, selecione **Guardar o âmbito de sincronização**.

Alterar o âmbito da sincronização faz com que o domínio gerido ressincronize todos os dados. Os objetos que já não são necessários no domínio gerido são eliminados e a ressincronização pode demorar muito tempo a ser concluída.

## <a name="powershell-script-for-scoped-synchronization"></a>Script PowerShell para sincronização de âmbito

Para configurar a sincronização de âmbito utilizando o PowerShell, guarde primeiro o seguinte script para um ficheiro chamado `Select-GroupsToSync.ps1` . Este script configura Azure AD DS para sincronizar grupos selecionados do Azure AD. Todas as contas de utilizador que fazem parte dos grupos especificados são sincronizadas com o domínio gerido.

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

## <a name="enable-scoped-synchronization-using-powershell"></a>Permitir a sincronização de âmbito utilizando o PowerShell

Utilize o PowerShell para completar o seguinte conjunto de passos. Consulte as instruções para ativar os [Serviços de Domínio do Diretório Ativo Azure utilizando o PowerShell](powershell-create-instance.md). Alguns passos deste artigo são ligeiramente modificados para configurar a sincronização telescópio.

1. Preencha as seguintes tarefas do artigo para ativar o Azure AD DS utilizando o PowerShell. Pare no degrau para realmente criar o domínio gerido. Configura a sincronização de âmbito que cria o domínio gerido.

   * [Instale os módulos PowerShell necessários.](powershell-create-instance.md#prerequisites)
   * [Crie o principal serviço obrigatório e o grupo Azure AD para acesso administrativo.](powershell-create-instance.md#create-required-azure-ad-resources)
   * [Criar recursos Azure de suporte como uma rede virtual e sub-redes.](powershell-create-instance.md#create-supporting-azure-resources)

1. Determine os grupos e utilizadores que contêm que pretende sincronizar a partir de Azure AD. Faça uma lista dos nomes de exibição dos grupos para sincronizar com Azure AD DS.

1. Executar o [script a partir da secção anterior](#powershell-script-for-scoped-synchronization) e usar o parâmetro *-groupsToAdd* para passar a lista de grupos para sincronizar.

   > [!WARNING]
   > Deve incluir o grupo *de administradores AAD DC* na lista de grupos para sincronização de âmbito. Se não incluir este grupo, o domínio gerido é inutilizável.

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. Agora crie o domínio gerido e permita a sincronização baseada em grupo. Incluir *"FilteredSync" = "Ativado"* no parâmetro *-Propriedades.*

    Desconfie o iD de subscrição do Azure e, em seguida, forneça um nome para o domínio gerido, como *aaddscontoso.com*. Pode obter o seu ID de subscrição utilizando o cmdlet [Get-AzSubscription.][Get-AzSubscription] Desaprova o nome do grupo de recursos, o nome da rede virtual e a região para os valores utilizados nas etapas anteriores para criar os recursos Azure de suporte:

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

Demora alguns minutos a criar o recurso e a devolver o controlo à solicitação PowerShell. O domínio gerido continua a ser a provisionado em segundo plano, podendo demorar até uma hora para completar a implementação. No portal Azure, a página **'Visão Geral'** para o seu domínio gerido mostra o estado atual ao longo desta fase de implantação.

Quando o portal Azure mostrar que o domínio gerido terminou o provisionamento, as seguintes tarefas devem ser concluídas:

* Atualize as definições de DNS para a rede virtual para que as máquinas virtuais possam encontrar o domínio gerido para a junção de domínio ou autenticação.
    * Para configurar o DNS, selecione o seu domínio gerido no portal. Na janela **'Vista Geral',** é solicitado que configufique automaticamente estas definições de DNS.
* Se criou um domínio gerido numa região que suporta Zonas de Disponibilidade, crie um grupo de segurança de rede para restringir o tráfego na rede virtual para o domínio gerido. É criado um equilibrador de carga padrão Azure que exige que estas regras sejam postas em prática. Este grupo de segurança de rede assegura o Azure AD DS e é necessário para que o domínio gerido funcione corretamente.
    * Para criar o grupo de segurança da rede e as regras necessárias, selecione o seu domínio gerido no portal. Na janela **'Vista Geral',** é-lhe pedido que crie e configuure automaticamente o grupo de segurança da rede.
* [Ativar a sincronização de palavras-passe nos Serviços de Domínio AD Azure para](tutorial-create-instance-advanced.md#enable-user-accounts-for-azure-ad-ds) que os utilizadores finais possam iniciar súm na área gerida utilizando as suas credenciais corporativas.

## <a name="modify-scoped-synchronization-using-powershell"></a>Modificar a sincronização âmbitota usando o PowerShell

Para modificar a lista de grupos cujos utilizadores devem ser sincronizados com o domínio gerido, reencamúndir o [script PowerShell](#powershell-script-for-scoped-synchronization) e especificar a nova lista de grupos. No exemplo seguinte, os grupos a sincronizar já não incluem *o GroupName2,* e agora inclui *o GroupName3*.

> [!WARNING]
> Deve incluir o grupo *de administradores AAD DC* na lista de grupos para sincronização de âmbito. Se não incluir este grupo, o domínio gerido é inutilizável.

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Alterar o âmbito da sincronização faz com que o domínio gerido ressincronize todos os dados. Os objetos que já não são necessários no domínio gerido são eliminados e a ressincronização pode demorar muito tempo a ser concluída.

## <a name="disable-scoped-synchronization-using-powershell"></a>Desativar a sincronização com recurso ao PowerShell

Para desativar a sincronização de âmbito baseado em grupo para um domínio gerido, desenrida *"FilteredSync" = "Desativado"* no recurso Azure AD DS e, em seguida, atualize o domínio gerido. Quando estiverem completos, todos os utilizadores e grupos estão programados para sincronizar a partir do Azure AD.

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

Alterar o âmbito da sincronização faz com que o domínio gerido ressincronize todos os dados. Os objetos que já não são necessários no domínio gerido são eliminados e a ressincronização pode demorar muito tempo a ser concluída.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o processo de sincronização, consulte [a sincronização do Azure AD Domain Services](synchronization.md).

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
