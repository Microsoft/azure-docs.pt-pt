---
title: Vários inquilinos no Azure Stack
description: Saiba como suportar vários diretórios do Azure Active Directory no Azure Stack
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: patricka
ms.openlocfilehash: a1c516ebbeb33d2aa92f6a0e3031a2b2d9fb4e9c
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50026165"
---
# <a name="multi-tenancy-in-azure-stack"></a>Vários inquilinos no Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Pode configurar o Azure Stack para dar suporte aos usuários a partir de múltiplos inquilinos do Azure Active Directory (Azure AD) para utilizar os serviços no Azure Stack. Por exemplo, considere o seguinte cenário:

 - É o administrador de serviço de contoso.onmicrosoft.com, onde o Azure Stack está instalado.
 - Maria é o administrador do diretório de fabrikam.onmicrosoft.com, onde estão localizados os utilizadores convidados. 
 - Empresa de Mary recebe serviços IaaS e PaaS da sua empresa e tem de permitir que os utilizadores do diretório de convidado (fabrikam.onmicrosoft.com) iniciar sessão e utilizar recursos do Azure Stack contoso.onmicrosoft.com.

Este guia fornece os passos necessários para configurar vários inquilinos no Azure Stack, no contexto deste cenário. Neste cenário, e a Maria tem de concluir os passos para permitir que os utilizadores da Fabrikam para iniciar sessão e consumir serviços de implementação do Azure Stack no Contoso.  

## <a name="enable-multi-tenancy"></a>Ativar multi-inquilinos

Existem alguns pré-requisitos para levar em conta antes de configurar vários inquilinos no Azure Stack:
  
 - E a Maria tem coordenar os passos administrativos o diretório que do Azure Stack está instalado no (Contoso) e o diretório de convidado (Fabrikam).  
 - Certifique-se de que [instalados](azure-stack-powershell-install.md) e [configurado](azure-stack-powershell-configure-admin.md) PowerShell para o Azure Stack.
 - [Baixe as ferramentas do Azure Stack](azure-stack-powershell-download.md)e importe os módulos do Connect e de identidade:

    ````PowerShell  
    Import-Module .\Connect\AzureStack.Connect.psm1
    Import-Module .\Identity\AzureStack.Identity.psm1
    ````

### <a name="configure-azure-stack-directory"></a>Configurar o diretório do Azure Stack

Nesta secção, irá configurar o Azure Stack para permitir inícios de sessão da Fabrikam do Azure AD directory inquilinos.

Carregar o inquilino de diretório de convidado (Fabrikam) para o Azure Stack através da configuração do Azure Resource Manager para aceitar os utilizadores e principais do inquilino do diretório de convidado de serviço.

````PowerShell  
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

## Replace the value below with the region location of the resource group. 
$location = "local"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location $location `
 -ResourceGroupName $ResourceGroupName
````

### <a name="configure-guest-directory"></a>Configurar o diretório de convidado

Depois de concluir os passos no diretório do Azure Stack, Mary tem de fornecer o consentimento para aceder ao diretório de convidado do Azure Stack e registar o Azure Stack com o diretório de convidado. 

#### <a name="registering-azure-stack-with-the-guest-directory"></a>Registar o Azure Stack com o diretório de convidado

Assim que o administrador do diretório de convidado tiverem dado consentimento para o Azure Stack aceder ao diretório da Fabrikam, Mary deve registrar o Azure Stack com o inquilino do diretório da Fabrikam.

````PowerShell
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
````

> [!IMPORTANT]
> Se o administrador do Azure Stack instalar novos serviços ou atualizações no futuro, terá de executar este script novamente.
>
> Execute este script novamente em qualquer altura para verificar o estado das aplicações no seu diretório do Azure Stack.
> 
> Se percebeu problemas com a criação de VMs em Managed Disks (introduzido na atualização 1808), uma nova **fornecedor de recursos de disco** foi adicionado, exigir que este script para ser executada novamente.

### <a name="direct-users-to-sign-in"></a>Direcionar os utilizadores para iniciar sessão

Agora que e Mary tem concluído os passos para o diretório de inclusão Mary, Mary pode direcionar o Fabrikam os utilizadores iniciem sessão.  A Fabrikam utilizadores (ou seja, os utilizadores com o sufixo de fabrikam.onmicrosoft.com) iniciarem sessão, visite a página https://portal.local.azurestack.external.  

Maria direcionará qualquer [principais externos](../role-based-access-control/rbac-and-directory-admin-roles.md) no diretório Fabrikam (ou seja, os utilizadores no diretório Fabrikam sem o sufixo de fabrikam.onmicrosoft.com) para iniciar sessão com https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.  Se não utilizar este URL, eles são enviados para o seu diretório padrão (Fabrikam) e recebem um erro que diz o que seu administrador não permitiu.

## <a name="disable-multi-tenancy"></a>Desativar a vários inquilinos

Se já não pretender vários inquilinos do Azure Stack, pode desativar multi-inquilinos efetuando os seguintes passos por ordem:

1. Como o administrador do diretório de convidado (Mary neste cenário), execute *Unregister-AzsWithMyDirectoryTenant*. O cmdlet desinstala todos os aplicativos do Azure Stack do novo diretório.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. Como o administrador de serviços do Azure Stack (neste cenário), execute *Unregister-AzSGuestDirectoryTenant*. 

    ``` PowerShell  
    ## The following Azure Resource Manaager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack directory
    $azureStackDirectoryTenant = "contoso.onmicrosoft.com"
    
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantToBeDecommissioned = "fabrikam.onmicrosoft.com"
    
    ## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
    $ResourceGroupName = "system.local"
    
    Unregister-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
     -DirectoryTenantName $azureStackDirectoryTenant `
     -GuestDirectoryTenantName $guestDirectoryTenantToBeDecommissioned `
     -ResourceGroupName $ResourceGroupName
    ```

    > [!WARNING]
    > Os passos de vários inquilinos desativar devem ser executados na ordem. Passo 1 de # falhar se passo #2 for concluído pela primeira vez.

## <a name="next-steps"></a>Passos Seguintes

- [Gerir fornecedores delegados](azure-stack-delegated-provider.md)
- [Conceitos-chave do Azure Stack](azure-stack-key-features.md)
