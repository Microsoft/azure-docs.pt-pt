---
title: Habilitar Azure Active Directory Domain Services usando o PowerShell | Microsoft Docs
description: Habilitar Azure Active Directory Domain Services usando o PowerShell
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: iainfou
ms.openlocfilehash: c6572ab8bc2a10039f327233f983c2e822fba3b0
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617222"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Habilitar Azure Active Directory Domain Services usando o PowerShell
Este artigo mostra como habilitar os serviços de domínio do Azure Active Directory (AD) usando o PowerShell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="task-1-install-the-required-powershell-modules"></a>Tarefa 1: Instalar os módulos necessários do PowerShell

### <a name="install-and-configure-azure-ad-powershell"></a>Instalar e configurar o PowerShell do Azure AD
Siga as instruções no artigo para [instalar o módulo do PowerShell do Azure AD e conectar-se ao Azure ad](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Instalar e configurar o Azure PowerShell
Siga as instruções no artigo para [instalar o módulo Azure PowerShell e conectar-se à sua assinatura do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="task-2-create-the-required-service-principal-in-your-azure-ad-directory"></a>Tarefa 2: Criar a entidade de serviço necessária em seu diretório do Azure AD
Digite o seguinte comando do PowerShell para criar a entidade de serviço necessária para Azure AD Domain Services em seu diretório do AD do Azure.
```powershell
# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

## <a name="task-3-create-and-configure-the-aad-dc-administrators-group"></a>Tarefa 3: Criar e configurar o grupo ' Administradores de controlador de domínio do AAD '
A próxima tarefa é criar o grupo de administradores que será usado para delegar tarefas de administração em seu domínio gerenciado.
```powershell
# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Agora que você criou o grupo, adicione alguns usuários ao grupo.
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

## <a name="task-4-register-the-azure-ad-domain-services-resource-provider"></a>Tarefa 4: Registrar o provedor de recursos de Azure AD Domain Services
Digite o seguinte comando do PowerShell para registrar o provedor de recursos para Azure AD Domain Services:
```powershell
# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

## <a name="task-5-create-a-resource-group"></a>Tarefa 5: Criar um grupo de recursos
Digite o seguinte comando do PowerShell para criar um grupo de recursos:
```powershell
$ResourceGroupName = "ContosoAaddsRg"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Você pode criar a rede virtual e o Azure AD Domain Services domínio gerenciado nesse grupo de recursos.


## <a name="task-6-create-and-configure-the-virtual-network"></a>Tarefa 6: Criar e configurar a rede virtual
Agora, crie a rede virtual na qual você habilita Azure AD Domain Services. Certifique-se de criar uma sub-rede dedicada para Azure AD Domain Services. Não implante VMs de carga de trabalho nessa sub-rede dedicada.

Digite os seguintes comandos do PowerShell para criar uma rede virtual com uma sub-rede dedicada para Azure AD Domain Services.

```powershell
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"

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
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```


## <a name="task-7-provision-the-azure-ad-domain-services-managed-domain"></a>Tarefa 7: Provisionar o Azure AD Domain Services domínio gerenciado
Digite o seguinte comando do PowerShell para habilitar Azure AD Domain Services para seu diretório:

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Não se esqueça das etapas de configuração adicionais depois de provisionar seu domínio gerenciado.**
> Depois que o domínio gerenciado for provisionado, você ainda precisará concluir as seguintes tarefas:
> * Atualize as configurações de DNS para a rede virtual para que as máquinas virtuais possam encontrar o domínio gerenciado para ingresso no domínio ou autenticação. Para configurar o DNS, selecione seu domínio gerenciado AD DS do Azure no Portal. Na janela **visão geral** , é solicitado que você defina automaticamente essas configurações de DNS.
> * Crie as regras necessárias do grupo de segurança de rede para restringir a entrada de tráfego para o domínio gerenciado. Para criar as regras do grupo de segurança de rede, selecione seu domínio gerenciado AD DS do Azure no Portal. Na janela **visão geral** , é solicitado que você crie automaticamente as regras apropriadas do grupo de segurança de rede.
> * **[Habilite a sincronização de senha para Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)** , para que os usuários finais possam entrar no domínio gerenciado usando suas credenciais corporativas.

## <a name="powershell-script"></a>Script do PowerShell
O script do PowerShell usado para executar todas as tarefas listadas neste artigo está abaixo. Copie o script e salve-o em um arquivo com uma extensão '. ps1 '. Execute o script no PowerShell ou usando o ISE (ambiente de script integrado) do PowerShell.

> [!NOTE]
> **Permissões necessárias para executar este script** Para habilitar Azure AD Domain Services, você precisa ser o administrador global do diretório do Azure AD. Além disso, você precisa de pelo menos privilégios de "colaborador" na rede virtual na qual habilitar Azure AD Domain Services.
>

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

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
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Não se esqueça das etapas de configuração adicionais depois de provisionar seu domínio gerenciado.**
> Depois que o domínio gerenciado for provisionado, você ainda precisará concluir as seguintes tarefas:
> * Atualize as configurações de DNS para a rede virtual para que as máquinas virtuais possam encontrar o domínio gerenciado para ingresso no domínio ou autenticação. Para configurar o DNS, selecione seu domínio gerenciado AD DS do Azure no Portal. Na janela **visão geral** , é solicitado que você defina automaticamente essas configurações de DNS.
> * Crie as regras necessárias do grupo de segurança de rede para restringir a entrada de tráfego para o domínio gerenciado. Para criar as regras do grupo de segurança de rede, selecione seu domínio gerenciado AD DS do Azure no Portal. Na janela **visão geral** , é solicitado que você crie automaticamente as regras apropriadas do grupo de segurança de rede.
> * **[Habilite a sincronização de senha para Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)** , para que os usuários finais possam entrar no domínio gerenciado usando suas credenciais corporativas.

## <a name="next-steps"></a>Passos Seguintes
Depois que o domínio gerenciado for criado, execute as seguintes tarefas de configuração para que você possa usar o domínio gerenciado:

* [Atualizar as configurações do servidor DNS para a rede virtual para apontar para seu domínio gerenciado](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)
* [Habilitar a sincronização de senha para seu domínio gerenciado](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)
