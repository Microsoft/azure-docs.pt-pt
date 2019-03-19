---
title: Ativar o Azure Active Directory Domain Services com o PowerShell | Documentos da Microsoft
description: Ativar o Azure Active Directory Domain Services com o PowerShell
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
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
ms.author: ergreenl
ms.openlocfilehash: f2c4f73af00e0093ce98f2de37e9c3a0ba381eda
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58119909"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Ativar o Azure Active Directory Domain Services com o PowerShell
Este artigo mostra como ativar os serviços de domínio do Azure Active Directory (AD) com o PowerShell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="task-1-install-the-required-powershell-modules"></a>Tarefa 1: Instalar os módulos necessários do PowerShell

### <a name="install-and-configure-azure-ad-powershell"></a>Instalar e configurar o Azure AD PowerShell
Siga as instruções no artigo para [instalar o módulo Azure AD PowerShell e ligar ao Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Instalar e configurar o Azure PowerShell
Siga as instruções no artigo para [instalar o módulo Azure PowerShell e ligue à sua subscrição do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="task-2-create-the-required-service-principal-in-your-azure-ad-directory"></a>Tarefa 2: Criar o principal de serviço necessário no diretório do Azure AD
Escreva o seguinte comando do PowerShell para criar o principal de serviço necessário para o Azure AD Domain Services no seu diretório do Azure AD.
```powershell
# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

## <a name="task-3-create-and-configure-the-aad-dc-administrators-group"></a>Tarefa 3: Criar e configurar o grupo "Administradores do AAD DC"
A próxima tarefa é criar o grupo de administrador que será utilizado para delegar tarefas de administração no seu domínio gerido.
```powershell
# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Agora que criou o grupo, adicione alguns utilizadores ao grupo.
```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso100.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="task-4-register-the-azure-ad-domain-services-resource-provider"></a>Tarefa 4: Registar o fornecedor de recursos do Azure AD Domain Services
Escreva o seguinte comando do PowerShell para registar o fornecedor de recursos para o Azure AD Domain Services:
```powershell
# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

## <a name="task-5-create-a-resource-group"></a>Tarefa 5: Criar um grupo de recursos
Escreva o seguinte comando do PowerShell para criar um grupo de recursos:
```powershell
$ResourceGroupName = "ContosoAaddsRg"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Pode criar a rede virtual e o domínio gerido do Azure AD Domain Services neste grupo de recursos.


## <a name="task-6-create-and-configure-the-virtual-network"></a>Tarefa 6: Criar e configurar a rede virtual
Agora, crie a rede virtual em que ativou o Azure AD Domain Services. Certifique-se de que criar uma sub-rede dedicada para o Azure AD Domain Services. Não implemente VMs de carga de trabalho para esta sub-rede dedicada.

Escreva os seguintes comandos do PowerShell para criar uma rede virtual com uma sub-rede dedicada para o Azure AD Domain Services.

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


## <a name="task-7-provision-the-azure-ad-domain-services-managed-domain"></a>Tarefa 7: Aprovisionar o domínio gerido do Azure AD Domain Services
Escreva o seguinte comando do PowerShell para ativar os serviços de domínio do Azure AD para o seu diretório:

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
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
> **Não se esqueça os passos de configuração adicionais depois de aprovisionar o seu domínio gerido.**
> Depois de seu domínio gerido está aprovisionado, ainda tem de concluir as seguintes tarefas:
> * **[Atualizar definições de DNS](active-directory-ds-getting-started-dns.md)**  para a rede virtual para que as máquinas virtuais pode encontrar o domínio gerido para associação a um domínio ou de autenticação.
> * **[Ativar a sincronização de palavra-passe para o Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md)**, pelo que os utilizadores finais podem iniciar sessão no domínio gerido utilizando as credenciais da empresa.


## <a name="powershell-script"></a>Script do PowerShell
O script do PowerShell utilizado para executar todas as tarefas apresentadas neste artigo é inferior. Copie o script e guarde-o para um ficheiro com uma extensão '. ps1'. Execute o script no PowerShell ou ao utilizar o PowerShell Integrated Scripting Environment (ISE).

> [!NOTE]
> **As permissões necessárias para executar este script** para ativar os serviços de domínio do Azure AD, tem de ser o administrador global do diretório do Azure AD. Além disso, terá de, pelo menos, privilégios de "Contribuinte" na rede virtual no qual ativar os serviços de domínio do Azure AD.
>

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso100.onmicrosoft.com"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
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
> **Não se esqueça os passos de configuração adicionais depois de aprovisionar o seu domínio gerido.**
> Depois de seu domínio gerido está aprovisionado, ainda tem de concluir as seguintes tarefas:
> * Atualize definições de DNS para a rede virtual para que as máquinas virtuais pode encontrar o domínio gerido para associação a um domínio ou de autenticação.
> * Ative a sincronização de palavra-passe para o Azure AD Domain Services para que os utilizadores finais podem iniciar sessão no domínio gerido utilizando as credenciais da empresa.

## <a name="next-steps"></a>Passos Seguintes
Depois de criado o seu domínio gerido, execute as seguintes tarefas de configuração, pelo que pode utilizar o domínio gerido:

* [Atualizar as definições do servidor DNS para a rede virtual para apontar para o seu domínio gerido](active-directory-ds-getting-started-dns.md)
* [Ativar a sincronização de palavra-passe para o seu domínio gerido](active-directory-ds-getting-started-password-sync.md)
