---
title: Habilitar os serviços de domínio do Azure DS usando o PowerShell | Microsoft Docs
description: Saiba como configurar e habilitar Azure Active Directory Domain Services usando o PowerShell do Azure AD e o Azure PowerShell.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: iainfou
ms.openlocfilehash: 961b54a4d7c9caee98497e5d2b8db86284084d15
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73023883"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Habilitar Azure Active Directory Domain Services usando o PowerShell

O Azure Active Directory Domain Services (Azure AD DS) fornece serviços de domínio gerenciados, como ingresso no domínio, diretiva de grupo, LDAP, autenticação Kerberos/NTLM que é totalmente compatível com o Windows Server Active Directory. Você consome esses serviços de domínio sem implantar, gerenciar e aplicar patches a controladores de domínio por conta própria. O Azure AD DS integra-se ao seu locatário existente do Azure AD. Essa integração permite que os usuários entrem usando suas credenciais corporativas e você pode usar grupos existentes e contas de usuário para proteger o acesso aos recursos.

Este artigo mostra como habilitar o Azure AD DS usando o PowerShell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa dos seguintes recursos:

* Instalar e configurar o Azure PowerShell.
    * Se necessário, siga as instruções para [instalar o módulo Azure PowerShell e conectar-se à sua assinatura do Azure](/powershell/azure/install-az-ps).
    * Certifique-se de entrar em sua assinatura do Azure usando o cmdlet [Connect-AzAccount][Connect-AzAccount] .
* Instalar e configurar o PowerShell do Azure AD.
    * Se necessário, siga as instruções para [instalar o módulo do PowerShell do Azure AD e conectar-se ao Azure ad](/powershell/azure/active-directory/install-adv2).
    * Certifique-se de entrar no seu locatário do Azure AD usando o cmdlet [Connect-AzureAD][Connect-AzureAD] .
* Você precisa de privilégios de *administrador global* em seu locatário do Azure ad para habilitar o Azure AD DS.
* Você precisa de privilégios de *colaborador* em sua assinatura do Azure para criar os recursos de AD DS do Azure necessários.

## <a name="create-required-azure-ad-resources"></a>Criar recursos necessários do Azure AD

O Azure AD DS requer uma entidade de serviço e um grupo do Azure AD. Esses recursos permitem que o Azure AD DS domínio gerenciado sincronize dados e defina quais usuários têm permissões administrativas no domínio gerenciado.

Primeiro, crie uma entidade de serviço do Azure AD para o Azure AD DS para se comunicar e se autenticar. Uma ID de aplicativo específica é usada chamada *serviços de controlador de domínio* com uma ID de *2565bd9d-DA50-47d4-8b85-4c97f669dc36*. Não altere essa ID de aplicativo.

Crie uma entidade de serviço do Azure AD usando o cmdlet [New-AzureADServicePrincipal][New-AzureADServicePrincipal] :

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Agora, crie um grupo do Azure AD chamado *Administradores de DC do AAD*. Os usuários adicionados a esse grupo recebem permissões para executar tarefas de administração no domínio gerenciado AD DS do Azure.

Crie o grupo de *Administradores de DC do AAD* usando o cmdlet [New-AzureADGroup][New-AzureADGroup] :

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Com o grupo de *Administradores do AAD DC* criado, adicione um usuário ao grupo usando o cmdlet [Add-AzureADGroupMember][Add-AzureADGroupMember] . Primeiro, você obtém a ID de objeto do grupo de *Administradores do AAD DC* usando o cmdlet [Get-AZUREADGROUP][Get-AzureADGroup] e a ID de objeto do usuário desejado usando o cmdlet [Get-AzureADUser][Get-AzureADUser] .

No exemplo a seguir, a ID de objeto de usuário para a conta com um UPN de `admin@contoso.onmicrosoft.com`. Substitua essa conta de usuário pelo UPN do usuário que você deseja adicionar ao grupo de *Administradores do AAD DC* :

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

## <a name="create-supporting-azure-resources"></a>Criar recursos de suporte do Azure

Primeiro, registre o provedor de recursos Azure AD Domain Services usando o cmdlet [Register-AzResourceProvider][Register-AzResourceProvider] :

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Em seguida, crie um grupo de recursos usando o cmdlet [New-AzResourceGroup][New-AzResourceGroup] . No exemplo a seguir, o grupo de recursos é denominado *MyResource* Group e é criado na região *westus* . Use seu próprio nome e a região desejada:

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Crie a rede virtual e as sub-redes para Azure AD Domain Services. Duas sub-redes são criadas-uma para *DomainServices*e outra para cargas de *trabalho*. O AD DS do Azure é implantado na sub-rede *DomainServices* dedicada. Não implante outros aplicativos ou cargas de trabalho nessa sub-rede. Use as *cargas de trabalho* separadas ou outras sub-redes para o restante de suas VMs.

Crie as sub-redes usando o cmdlet [New-AzVirtualNetworkSubnetConfig][New-AzVirtualNetworkSubnetConfig] e crie a rede virtual usando o cmdlet [New-AzVirtualNetwork][New-AzVirtualNetwork] .

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

## <a name="create-an-azure-ad-ds-managed-domain"></a>Criar um domínio gerenciado AD DS do Azure

Agora, vamos criar um domínio gerenciado do Azure AD DS. Defina sua ID de assinatura do Azure e forneça um nome para o domínio gerenciado, como *contoso.com*. Você pode obter sua ID de assinatura usando o cmdlet [Get-AzSubscription][Get-AzSubscription] .

Se você escolher uma região com suporte a Zonas de Disponibilidade, os recursos de AD DS do Azure serão distribuídos entre zonas para redundância adicional.

As Zonas de Disponibilidade são localizações físicas exclusivas numa região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, refrigeração e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas.

Não há nada a ser configurado para o Azure AD DS ser distribuído entre zonas. A plataforma Azure manipula automaticamente a distribuição de zona de recursos. Para obter mais informações e ver a disponibilidade da região, consulte [o que são zonas de disponibilidade no Azure?][availability-zones].

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso.com"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

Leva alguns minutos para criar o recurso e retornar o controle para o prompt do PowerShell. O Azure AD DS domínio gerenciado continua a ser provisionado em segundo plano e pode levar até uma hora para concluir a implantação. No portal do Azure, a página **visão geral** do domínio gerenciado do Azure AD DS mostra o status atual durante esse estágio de implantação.

Quando o portal do Azure mostra que o domínio gerenciado do Azure AD DS concluiu o provisionamento, as seguintes tarefas precisam ser concluídas:

* Atualize as configurações de DNS para a rede virtual para que as máquinas virtuais possam encontrar o domínio gerenciado para ingresso no domínio ou autenticação.
    * Para configurar o DNS, selecione seu domínio gerenciado AD DS do Azure no Portal. Na janela **visão geral** , é solicitado que você defina automaticamente essas configurações de DNS.
* Se você criou um domínio gerenciado AD DS do Azure em uma região que dá suporte a Zonas de Disponibilidade, crie um grupo de segurança de rede para restringir o tráfego na rede virtual para o domínio gerenciado do Azure AD DS. Um balanceador de carga standard do Azure é criado e requer que essas regras sejam colocadas. Esse grupo de segurança de rede protege o Azure AD DS e é necessário para que o domínio gerenciado funcione corretamente.
    * Para criar o grupo de segurança de rede e as regras necessárias, selecione seu domínio gerenciado AD DS do Azure no Portal. Na janela **visão geral** , é solicitado que você crie e configure automaticamente o grupo de segurança de rede.
* [Habilite a sincronização de senha para Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) para que os usuários finais possam entrar no domínio gerenciado usando suas credenciais corporativas.

## <a name="complete-powershell-script"></a>Concluir script do PowerShell

O script completo do PowerShell a seguir combina todas as tarefas mostradas neste artigo. Copie o script e salve-o em um arquivo com uma extensão `.ps1`. Execute o script em um console do PowerShell local ou no [Azure cloud Shell][cloud-shell].

> [!NOTE]
> Para habilitar o Azure AD DS, você deve ser um administrador global para o locatário do Azure AD. Você também precisa de pelo menos privilégios de *colaborador* na assinatura do Azure.

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
$ResourceGroupName = "myResourceGroup"
$VnetName = "myVnet"
$AzureLocation = "westus"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso.com"

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

Leva alguns minutos para criar o recurso e retornar o controle para o prompt do PowerShell. O Azure AD DS domínio gerenciado continua a ser provisionado em segundo plano e pode levar até uma hora para concluir a implantação. No portal do Azure, a página **visão geral** do domínio gerenciado do Azure AD DS mostra o status atual durante esse estágio de implantação.

Quando o portal do Azure mostra que o domínio gerenciado do Azure AD DS concluiu o provisionamento, as seguintes tarefas precisam ser concluídas:

* Atualize as configurações de DNS para a rede virtual para que as máquinas virtuais possam encontrar o domínio gerenciado para ingresso no domínio ou autenticação.
    * Para configurar o DNS, selecione seu domínio gerenciado AD DS do Azure no Portal. Na janela **visão geral** , é solicitado que você defina automaticamente essas configurações de DNS.
* Se você criou um domínio gerenciado AD DS do Azure em uma região que dá suporte a Zonas de Disponibilidade, crie um grupo de segurança de rede para restringir o tráfego na rede virtual para o domínio gerenciado do Azure AD DS. Um balanceador de carga standard do Azure é criado e requer que essas regras sejam colocadas. Esse grupo de segurança de rede protege o Azure AD DS e é necessário para que o domínio gerenciado funcione corretamente.
    * Para criar o grupo de segurança de rede e as regras necessárias, selecione seu domínio gerenciado AD DS do Azure no Portal. Na janela **visão geral** , é solicitado que você crie e configure automaticamente o grupo de segurança de rede.
* [Habilite a sincronização de senha para Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) para que os usuários finais possam entrar no domínio gerenciado usando suas credenciais corporativas.

## <a name="next-steps"></a>Passos seguintes

Para ver o domínio gerenciado AD DS do Azure em ação, você pode [ingressar no domínio em uma VM do Windows][windows-join], [Configurar o LDAP seguro][tutorial-ldaps]e configurar a sincronização de hash de [senha][tutorial-phs].

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
