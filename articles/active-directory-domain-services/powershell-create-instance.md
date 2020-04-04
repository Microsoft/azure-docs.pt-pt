---
title: Ativar os serviços de domínio Azure DS utilizando powerShell / Microsoft Docs
description: Saiba como configurar e ativar os Serviços de Domínio de Diretório Ativo Azure utilizando o Azure AD PowerShell e o Azure PowerShell.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 09/05/2019
ms.author: iainfou
ms.openlocfilehash: e99ad2d53bc26b4e13a34097baaec929058a61a0
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654809"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Ativar serviços de domínio de diretório ativo Azure usando powerShell

Os Serviços de Domínio de Diretório Ativo Azure (Azure AD DS) fornecem serviços de domínio geridos, tais como a adesão ao domínio, política de grupo, autenticação LDAP, Kerberos/NTLM que é totalmente compatível com o Diretório Ativo do Windows Server. Você consome estes serviços de domínio sem implementar, gerir e remendar controladores de domínio por si mesmo. A Azure AD DS integra-se com o seu inquilino Azure AD existente. Esta integração permite que os utilizadores assinem usando as suas credenciais corporativas, e você pode usar grupos e contas de utilizador existentes para garantir o acesso aos recursos.

Este artigo mostra-lhe como permitir o Azure AD DS usando o PowerShell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este artigo, precisa dos seguintes recursos:

* Instalar e configurar o Azure PowerShell.
    * Se necessário, siga as instruções para [instalar o módulo PowerShell Azure e ligue-se à sua subscrição Azure](/powershell/azure/install-az-ps).
    * Certifique-se de que insere a sua subscrição Azure utilizando o cmdlet [Connect-AzAccount.][Connect-AzAccount]
* Instale e configure o Azure AD PowerShell.
    * Se necessário, siga as instruções para [instalar o módulo PowerShell Azure AD e ligue-se ao Azure AD](/powershell/azure/active-directory/install-adv2).
    * Certifique-se de que inscreveu o seu inquilino Azure AD utilizando o cmdlet [Connect-AzureAD.][Connect-AzureAD]
* Você precisa de privilégios *de administrador global* no seu inquilino Azure AD para permitir o Azure AD DS.
* Você precisa de privilégios *Contributivos* na sua subscrição Azure para criar os recursos DS Azure necessários.

## <a name="create-required-azure-ad-resources"></a>Criar recursos adatos necessários do Azure

A Azure AD DS requer um diretor de serviço e um grupo Azure AD. Estes recursos permitem que o Azure AD DS gerido sincronize os dados e defina quais os utilizadores que têm permissões administrativas no domínio gerido.

Em primeiro lugar, crie um serviço Azure AD principal para o Azure AD DS comunicar e autenticar-se. Um ID de aplicação específico é usado chamado *Serviços* de Controlador de Domínio com uma identificação de *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Não altere esta identificação da aplicação.

Crie um diretor de serviço Azure AD utilizando o cmdlet [New-AzureADServicePrincipal:][New-AzureADServicePrincipal]

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Agora crie um grupo Azure AD chamado *AAD DC Administrators*. Os utilizadores adicionados a este grupo são então autorizados a executar tarefas de administração no domínio gerido pelo Azure AD DS.

Crie o grupo de *administradores da AAD DC* utilizando o cmdlet [new-AzureADGroup:][New-AzureADGroup]

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Com o grupo de *administradores aAD DC* criado, adicione um utilizador ao grupo utilizando o Cmdlet [Add-AzureADGroupMember.][Add-AzureADGroupMember] Primeiro obtém o ID de objeto de grupo *aAD DC* administradores utilizando o cmdlet [Get-AzureADGroup,][Get-AzureADGroup] em seguida, o ID de objeto do utilizador desejado utilizando o cmdlet [Get-AzureADUser.][Get-AzureADUser]

No exemplo seguinte, o id do objeto do `admin@aaddscontoso.onmicrosoft.com`utilizador para a conta com uma UPN de . Substitua esta conta de utilizador pela UPN do utilizador que pretende adicionar ao grupo de *Administradores aAD DC:*

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@aaddscontoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-supporting-azure-resources"></a>Criar recursos azure de apoio

Em primeiro lugar, registe o fornecedor de recursos da Azure AD Domain Services utilizando o [cmdlet Register-AzResourceProvider:][Register-AzResourceProvider]

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Em seguida, crie um grupo de recursos utilizando o cmdlet [New-AzResourceGroup.][New-AzResourceGroup] No exemplo seguinte, o grupo de recursos é nomeado *myResourceGroup* e é criado na região *de Westus.* Use o seu próprio nome e região desejada:

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Crie a rede virtual e subnets para os Serviços de Domínio Azure AD. São criadas duas subredes - uma para *Serviços de Domínio*, e outra para *Cargas de Trabalho*. O Azure AD DS é implantado na subnet dedicada *domainServices.* Não implemente outras aplicações ou cargas de trabalho nesta sub-rede. Utilize as *cargas de trabalho separadas* ou outras subredes para o resto dos seus VMs.

Crie as subredes utilizando o [cmdlet New-AzVirtualNetworkSubnetConfig][New-AzVirtualNetworkSubnetConfig] e, em seguida, crie a rede virtual utilizando o cmdlet [New-AzVirtualNetwork.][New-AzVirtualNetwork]

```powershell
$VnetName = "myVnet"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet= New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```

## <a name="create-an-azure-ad-ds-managed-domain"></a>Criar um domínio gerido por AD DS azure

Agora vamos criar um domínio gerido azure AD DS. Detete o seu ID de subscrição Azure e, em seguida, forneça um nome para o domínio gerido, como *aaddscontoso.com*. Pode obter o seu ID de subscrição utilizando o cmdlet [Get-AzSubscription.][Get-AzSubscription]

Se escolher uma região que apoie zonas de disponibilidade, os recursos Da DS Azure são distribuídos por zonas para despedimentoadicional.

As Zonas de Disponibilidade são localizações físicas exclusivas numa região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões ativadas.

Não há nada para configurar para que o Azure AD DS seja distribuído por zonas. A plataforma Azure lida automaticamente com a distribuição de recursos da zona. Para mais informações e para ver disponibilidade da região, consulte [As Zonas de Disponibilidade em Azure?][availability-zones]

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

Leva alguns minutos para criar o recurso e devolver o controlo ao pedido powerShell. O domínio gerido pela AD DS Azure continua a ser aprovisionado em segundo plano, podendo demorar até uma hora a concluir a implantação. No portal Azure, a página **de visão geral** do seu domínio gerido pelo Azure AD DS mostra o estado atual ao longo desta fase de implantação.

Quando o portal Azure mostrar que o domínio gerido pela AD DS azure terminou o provisionamento, as seguintes tarefas devem ser concluídas:

* Atualize as definições de DNS para a rede virtual para que as máquinas virtuais possam encontrar o domínio gerido para a adesão ou autenticação de domínio.
    * Para configurar o DNS, selecione o seu domínio gerido pelo Azure AD DS no portal. Na janela **Overview,** é-lhe solicitado que configure automaticamente estas definições de DNS.
* Se criou um domínio gerido pelo Azure AD DS numa região que suporta zonas de disponibilidade, crie um grupo de segurança de rede para restringir o tráfego na rede virtual para o domínio gerido pelo Azure AD DS. É criado um equilibrador de carga padrão Azure que exige que estas regras sejam criadas. Este grupo de segurança de rede protege o Azure AD DS e é necessário que o domínio gerido funcione corretamente.
    * Para criar o grupo de segurança da rede e as regras exigidas, selecione o seu domínio gerido pelo Azure AD DS no portal. Na janela **Overview,** é-lhe solicitado que crie e configure automaticamente o grupo de segurança da rede.
* Ative a sincronização de [passwords para os Serviços de Domínio AD Do Azure para](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) que os utilizadores finais possam iniciar sessão no domínio gerido usando as suas credenciais corporativas.

## <a name="complete-powershell-script"></a>Script Completo PowerShell

O seguinte roteiro completo da PowerShell combina todas as tarefas mostradas neste artigo. Copie o script e guarde-o para um ficheiro com uma `.ps1` extensão. Execute o script numa consola local powerShell ou na [Azure Cloud Shell][cloud-shell].

> [!NOTE]
> Para permitir o Azure AD DS, você deve ser um administrador global para o inquilino da AD Azure. Você também precisa de pelo menos privilégios *Contributivos* na subscrição Azure.

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@aaddscontoso.onmicrosoft.com"
$ResourceGroupName = "myResourceGroup"
$VnetName = "myVnet"
$AzureLocation = "westus"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"

# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"

# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq '$AaddsAdminUserUpn'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId

# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

Leva alguns minutos para criar o recurso e devolver o controlo ao pedido powerShell. O domínio gerido pela AD DS Azure continua a ser aprovisionado em segundo plano, podendo demorar até uma hora a concluir a implantação. No portal Azure, a página **de visão geral** do seu domínio gerido pelo Azure AD DS mostra o estado atual ao longo desta fase de implantação.

Quando o portal Azure mostrar que o domínio gerido pela AD DS azure terminou o provisionamento, as seguintes tarefas devem ser concluídas:

* Atualize as definições de DNS para a rede virtual para que as máquinas virtuais possam encontrar o domínio gerido para a adesão ou autenticação de domínio.
    * Para configurar o DNS, selecione o seu domínio gerido pelo Azure AD DS no portal. Na janela **Overview,** é-lhe solicitado que configure automaticamente estas definições de DNS.
* Se criou um domínio gerido pelo Azure AD DS numa região que suporta zonas de disponibilidade, crie um grupo de segurança de rede para restringir o tráfego na rede virtual para o domínio gerido pelo Azure AD DS. É criado um equilibrador de carga padrão Azure que exige que estas regras sejam criadas. Este grupo de segurança de rede protege o Azure AD DS e é necessário que o domínio gerido funcione corretamente.
    * Para criar o grupo de segurança da rede e as regras exigidas, selecione o seu domínio gerido pelo Azure AD DS no portal. Na janela **Overview,** é-lhe solicitado que crie e configure automaticamente o grupo de segurança da rede.
* Ative a sincronização de [passwords para os Serviços de Domínio AD Do Azure para](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) que os utilizadores finais possam iniciar sessão no domínio gerido usando as suas credenciais corporativas.

## <a name="next-steps"></a>Passos seguintes

Para ver o domínio gerido pelo Azure AD DS em ação, pode [juntar-se a um Windows VM,][windows-join] [configurar lDAP seguro][tutorial-ldaps]e configurar a sincronização de hash de [palavra-passe][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[New-AzVirtualNetworkSubnetConfig]: /powershell/module/Az.Network/New-AzVirtualNetworkSubnetConfig
[New-AzVirtualNetwork]: /powershell/module/Az.Network/New-AzVirtualNetwork
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users
[availability-zones]: ../availability-zones/az-overview.md
