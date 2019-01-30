---
title: Ligar ao Azure Stack com o PowerShell como um operador | Documentos da Microsoft
description: Saiba como ligar ao Azure Stack com o PowerShell como um operador
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 01/24/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: cf7fe050d2c0521a84bd7f108b2a6d67bfbe09da
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55241632"
---
# <a name="connect-to-azure-stack-with-powershell-as-an-operator"></a>Ligar ao Azure Stack com o PowerShell como um operador

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Pode configurar o Azure Stack para utilizar o PowerShell para gerir os recursos como a criação de ofertas, planos, quotas e alertas. Este tópico ajuda-o a configurar o ambiente de operador.

## <a name="prerequisites"></a>Pré-requisitos

Execute os seguintes pré-requisitos a partir do [development kit do](./asdk/asdk-connect.md#connect-with-rdp) ou a partir de um baseado em Windows cliente externo se estiver [ligado ao ASDK através de VPN](./asdk/asdk-connect.md#connect-with-vpn). 

 - Instale [módulos do Azure Stack compatível com o Azure PowerShell](azure-stack-powershell-install.md).  
 - No desenvolvimento do [Azure Stack Kit, deve ser o blobEndpoint](azure-stack-powershell-download.md) .  

## <a name="connect-with-azure-ad"></a>Ligar ao Azure AD

Configure o ambiente de operador do Azure Stack com o PowerShell. Execute um dos seguintes scripts: Substitua o tenantName do Azure Active Directory (Azure AD) e valores de ponto final do Azure Resource Manager com a configuração do seu próprio ambiente. <!-- GraphAudience endpoint -->

```PowerShell  
    # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external"

    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>Ligar com o AD FS

Ligue-se para o ambiente de operador do Azure Stack com o PowerShell com o Azure Active Directory Federated Services (Azure AD FS). Kit de desenvolvimento do Azure Stack, este ponto de final do Azure Resource Manager está definido como `https://adminmanagement.local.azurestack.external`. Para obter o ponto de final do Azure Resource Manager para o Azure Stack de sistemas integrados, contacte o seu fornecedor de serviços.

<!-- GraphAudience endpoint -->

  ```PowerShell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
  Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external"

  # Sign in to your environment

  $cred = get-credential

  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -Credential $cred
  ```

> [!Note]  
> AD FS só suporta a autenticação interativa com identidades de utilizador. Se um objeto de credencial é necessário tem de utilizar um principal de serviço (SPN). Para obter mais informações sobre como configurar um principal de serviço com o Azure Stack e AS FS que o seu serviço de gestão de identidade, consulte [gerir principal de serviço para o AD FS](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

## <a name="test-the-connectivity"></a>Testar a conectividade

Agora que tem tudo o que configurar, utilizar o PowerShell para criar recursos no Azure Stack. Por exemplo, pode criar um grupo de recursos para uma aplicação e adicionar uma máquina virtual. Utilize o seguinte comando para criar um grupo de recursos chamado **MyResourceGroup**.

```PowerShell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Passos Seguintes

 - [Desenvolver modelos para o Azure Stack](user/azure-stack-develop-templates.md)
 - [Implementar modelos com o PowerShell](user/azure-stack-deploy-template-powershell.md)
