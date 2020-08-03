---
title: Ativar os Serviços de Domínio Azure DS utilizando o PowerShell Microsoft Docs
description: Aprenda a configurar e a capacitar os Serviços de Domínio do Diretório Ativo Azure utilizando a Azure AD PowerShell e a Azure PowerShell.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: 9c2345c93a163464ea735400c9269e2e3fc27ecf
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87488181"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Ativar os serviços de domínio do diretório ativo Azure utilizando o PowerShell

Azure Ative Directory Domain Services (Azure AD DS) fornece serviços de domínio geridos, tais como a junção de domínio, política de grupo, autenticação de LDAP, Kerberos/NTLM que é totalmente compatível com o Windows Server Ative Directory. Você consome estes serviços de domínio sem implantar, gerir e remendar controladores de domínio si mesmo. A Azure AD DS integra-se com o seu inquilino AZure AD existente. Esta integração permite que os utilizadores assinem a utilização das suas credenciais corporativas, podendo utilizar grupos e contas de utilizador existentes para garantir o acesso aos recursos.

Este artigo mostra-lhe como ativar o Azure AD DS utilizando o PowerShell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para completar este artigo, precisa dos seguintes recursos:

* Instalar e configurar o Azure PowerShell.
    * Se necessário, siga as instruções para [instalar o módulo Azure PowerShell e ligue-se à sua assinatura Azure](/powershell/azure/install-az-ps).
    * Certifique-se de que faz sedução na sua assinatura Azure utilizando o [cmdlet Connect-AzAccount.][Connect-AzAccount]
* Instale e configuure Azure AD PowerShell.
    * Se necessário, siga as instruções para [instalar o módulo Azure AD PowerShell e ligar-se ao Azure AD](/powershell/azure/active-directory/install-adv2).
    * Certifique-se de que faz sedundo ao seu inquilino AD Azure usando o cmdlet [Connect-AzureAD.][Connect-AzureAD]
* Você precisa de privilégios *de administrador global* no seu inquilino AZure AD para ativar Azure AD DS.
* Precisa de privilégios *colaboradores* na sua subscrição Azure para criar os recursos Azure AD DS necessários.

## <a name="create-required-azure-ad-resources"></a>Criar recursos AD AD necessários

A Azure AD DS requer um diretor de serviço e um grupo AZure AD. Estes recursos permitem que o Azure AD DS geriu dados sincronizados e definir quais os utilizadores que têm permissões administrativas no domínio gerido.

Em primeiro lugar, crie um principal de serviço Azure AD para a Azure AD DS comunicar e autenticar-se. Um ID de aplicação específico é usado chamado *Serviços de Controlador* de Domínio com um ID de *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Não mude esta identificação da aplicação.

Criar um diretor de serviço AZure AD utilizando o [cmdlet New-AzureADServicePrincipal:][New-AzureADServicePrincipal]

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Agora crie um grupo AZure AD chamado *AAD DC Administrators*. Os utilizadores adicionados a este grupo são então autorizados a executar tarefas de administração no domínio gerido.

Criar o grupo *de administradores AAD DC* utilizando o cmdlet [New-AzureADGroup:][New-AzureADGroup]

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Com o grupo *de administradores AAD DC* criado, adicione um utilizador ao grupo utilizando o cmdlet [Add-AzureADGroupMember.][Add-AzureADGroupMember] Primeiro obtém o ID do grupo *de administradores AAD DC* utilizando o cmdlet [Get-AzureADGroup,][Get-AzureADGroup] em seguida, o ID do objeto do utilizador desejado usando o cmdlet [Get-AzureADUser.][Get-AzureADUser]

No exemplo seguinte, o iD do objeto de utilizador para a conta com uma UPN de `admin@contoso.onmicrosoft.com` . Substitua esta conta de utilizador pela UPN do utilizador que pretende adicionar ao grupo *de administradores AAD DC:*

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-supporting-azure-resources"></a>Criar recursos de apoio Azure

Em primeiro lugar, registe o fornecedor de recursos de serviços de domínio Azure AD utilizando o cmdlet [Register-AzResourceProvider:][Register-AzResourceProvider]

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Em seguida, crie um grupo de recursos utilizando o cmdlet [New-AzResourceGroup.][New-AzResourceGroup] No exemplo seguinte, o grupo de recursos é nomeado *myResourceGroup* e é criado na região *oeste.* Use o seu próprio nome e região desejada:

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Crie a rede virtual e sub-redes para serviços de domínio Azure AD. São criadas duas sub-redes - uma para *Os Serviços de Domínios*e outra para *Cargas de Trabalho*. O Azure AD DS está implantado na sub-rede dedicada *do DomainServices.* Não coloque outras aplicações ou cargas de trabalho nesta sub-rede. Utilize as *cargas de trabalho separadas* ou outras sub-redes para o resto dos seus VMs.

Crie as sub-redes utilizando o cmdlet [New-AzVirtualNetworkSubnetConfig][New-AzVirtualNetworkSubnetConfig] e, em seguida, crie a rede virtual utilizando a cmdlet [New-AzVirtualNetwork.][New-AzVirtualNetwork]

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

## <a name="create-a-managed-domain"></a>Criar um domínio gerido

Agora vamos criar um domínio gerido. Desconfie o iD de subscrição do Azure e, em seguida, forneça um nome para o domínio gerido, como *aaddscontoso.com*. Pode obter o seu ID de subscrição utilizando o cmdlet [Get-AzSubscription.][Get-AzSubscription]

Se escolher uma região que suporte Zonas de Disponibilidade, os recursos Azure AD DS são distribuídos por zonas para redundância adicional.

As Zonas de Disponibilidade são localizações físicas exclusivas numa região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões ativadas.

Não há nada que possa configurar para que o Azure AD DS seja distribuído por zonas. A plataforma Azure lida automaticamente com a distribuição de recursos na zona. Para mais informações e para ver a disponibilidade da região, consulte [quais são as Zonas de Disponibilidade em Azure?][availability-zones]

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

Demora alguns minutos a criar o recurso e a devolver o controlo à solicitação PowerShell. O domínio gerido continua a ser a provisionado em segundo plano, podendo demorar até uma hora para completar a implementação. No portal Azure, a página **'Visão Geral'** para o seu domínio gerido mostra o estado atual ao longo desta fase de implantação.

Quando o portal Azure mostrar que o domínio gerido terminou o provisionamento, as seguintes tarefas devem ser concluídas:

* Atualize as definições de DNS para a rede virtual para que as máquinas virtuais possam encontrar o domínio gerido para a junção de domínio ou autenticação.
    * Para configurar o DNS, selecione o seu domínio gerido no portal. Na janela **'Vista Geral',** é solicitado que configufique automaticamente estas definições de DNS.
* Criar um grupo de segurança de rede para restringir o tráfego na rede virtual para o domínio gerido. É criado um equilibrador de carga padrão Azure que exige que estas regras sejam postas em prática. Este grupo de segurança de rede assegura o Azure AD DS e é necessário para que o domínio gerido funcione corretamente.
    * Para criar o grupo de segurança da rede e as regras exigidas, primeiro instale o `New-AzureAddsNetworkSecurityGroup` script utilizando o comando `Install-Script -Name New-AaddsNetworkSecurityGroup` e, em seguida, corra `New-AaddsNetworkSecurityGroup` . As regras necessárias para o domínio gerido são criadas para si.
* [Ativar a sincronização de palavras-passe para Azure AD DS para](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) que os utilizadores finais possam iniciar sação no domínio gerido utilizando as suas credenciais corporativas.

## <a name="complete-powershell-script"></a>Script completo do PowerShell

O seguinte script completo powerShell combina todas as tarefas mostradas neste artigo. Copie o script e guarde-o num ficheiro com uma `.ps1` extensão. Execute o script numa consola local da PowerShell ou na [Azure Cloud Shell][cloud-shell].

> [!NOTE]
> Para ativar a Azure AD DS, você deve ser um administrador global para o inquilino AZURE AD. Também precisa de pelo menos privilégios *de contribuinte* na subscrição do Azure.

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
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

Demora alguns minutos a criar o recurso e a devolver o controlo à solicitação PowerShell. O domínio gerido continua a ser a provisionado em segundo plano, podendo demorar até uma hora para completar a implementação. No portal Azure, a página **'Visão Geral'** para o seu domínio gerido mostra o estado atual ao longo desta fase de implantação.

Quando o portal Azure mostrar que o domínio gerido terminou o provisionamento, as seguintes tarefas devem ser concluídas:

* Atualize as definições de DNS para a rede virtual para que as máquinas virtuais possam encontrar o domínio gerido para a junção de domínio ou autenticação.
    * Para configurar o DNS, selecione o seu domínio gerido no portal. Na janela **'Vista Geral',** é solicitado que configufique automaticamente estas definições de DNS.
* Criar um grupo de segurança de rede para restringir o tráfego na rede virtual para o domínio gerido. É criado um equilibrador de carga padrão Azure que exige que estas regras sejam postas em prática. Este grupo de segurança de rede assegura o Azure AD DS e é necessário para que o domínio gerido funcione corretamente.
    * Para criar o grupo de segurança da rede e as regras exigidas, primeiro instale o `New-AzureAddsNetworkSecurityGroup` script utilizando o comando `Install-Script -Name New-AaddsNetworkSecurityGroup` e, em seguida, corra `New-AaddsNetworkSecurityGroup` . As regras necessárias para o domínio gerido são criadas para si.
* [Ativar a sincronização de palavras-passe para Azure AD DS para](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) que os utilizadores finais possam iniciar sação no domínio gerido utilizando as suas credenciais corporativas.

## <a name="next-steps"></a>Passos seguintes

Para ver o domínio gerido em ação, pode [juntar-se a um VM do Windows,][windows-join] [configurar LDAP seguro][tutorial-ldaps]e [configurar a sincronização de haxixe de palavra-passe][tutorial-phs].

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
