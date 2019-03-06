---
title: Ligar ao Azure Stack com o PowerShell como um utilizador | Documentos da Microsoft
description: Passos para ligar à instância do Azure Stack do utilizador.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: 6839d46efdcd9b6a09e8b2c98e0334f6e6ea1620
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57405603"
---
# <a name="connect-to-azure-stack-with-powershell-as-a-user"></a>Ligar ao Azure Stack com o PowerShell como um utilizador

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Este artigo fornece os passos para ligar à sua instância do Azure Stack. Tem de ligar para gerir os recursos do Azure Stack com o PowerShell. Por exemplo, pode utilizar o PowerShell para subscrever ofertas, criar máquinas virtuais e implementar modelos Azure Resource Manager. para executar cmdlets do PowerShell.

Para configurar:
  - Certifique-se de que tem os requisitos.
  - Ligar com o Azure Active Directory (Azure AD) ou serviços de Federação do Active Directory (AD FS). 
  - Registe fornecedores de recursos.
  - Teste a conectividade.

## <a name="prerequisites"></a>Pré-requisitos

Pode configurar estes pré-requisitos do [kit de desenvolvimento](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), ou a partir de um baseado em Windows cliente externo se estiver [ligados através de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Instale [módulos do Azure Stack compatível com o Azure PowerShell](azure-stack-powershell-install.md).
* No desenvolvimento do [Azure Stack Kit, deve ser o blobEndpoint](azure-stack-powershell-download.md) .

Certifique-se de que substitua as seguintes variáveis do script pelos valores da sua configuração do Azure Stack:

- **Nome de inquilino do Azure AD**  
  O nome do inquilino do Azure AD utilizado para gerir o Azure Stack, por exemplo, yourdirectory.onmicrosoft.com.
- **Ponto final do Gestor de recursos do Azure**  
  Kit de desenvolvimento do Azure Stack, este valor é definido como https://management.local.azurestack.external. Para obter este valor para o Azure Stack de sistemas integrados, contacte o seu fornecedor de serviços.

## <a name="connect-with-azure-ad"></a>Ligar ao Azure AD

```PowerShell  
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"
    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackUser" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>Ligar com o AD FS

  ```PowerShell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"

  # Sign in to your environment
  Login-AzureRmAccount -EnvironmentName "AzureStackUser"
  ```

## <a name="register-resource-providers"></a>Registar fornecedores de recursos

Fornecedores de recursos não são automaticamente registrados para novas subscrições de utilizador que não têm quaisquer recursos implementados através do portal. Explicitamente pode registar um fornecedor de recursos ao executar o seguinte script:

```PowerShell  
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    }
```

## <a name="test-the-connectivity"></a>Testar a conectividade

Quando tem tudo o que configurar, teste a conectividade com o PowerShell para criar recursos no Azure Stack. Como um teste, crie um grupo de recursos para uma aplicação e adicionar uma máquina virtual. Execute o seguinte comando para criar um grupo de recursos com o nome "MyResourceGroup":

```PowerShell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Passos Seguintes

- [Desenvolver modelos para o Azure Stack](azure-stack-develop-templates.md)  
- [Implementar modelos com o PowerShell](azure-stack-deploy-template-powershell.md)  
- [Referência do módulo do Azure Stack](https://docs.microsoft.com/en-us/powershell/azure/azure-stack/overview)  
- Se quiser configurar o PowerShell para o ambiente de operador de nuvem, consulte a [configurar o ambiente do PowerShell da operadora do Azure Stack](../azure-stack-powershell-configure-admin.md) artigo.
