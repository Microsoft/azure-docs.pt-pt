---
title: Atribua uma identidade gerida a uma função de aplicação utilizando PowerShell - Azure AD
description: Instruções passo a passo para atribuir um acesso de identidade gerido ao papel de outra aplicação, utilizando o PowerShell.
services: active-directory
documentationcenter: ''
author: johndowns
manager: ''
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: jodowns
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2d844054e3744aafa01f43ded354a5b5d9f8b4b
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732104"
---
# <a name="assign-a-managed-identity-access-to-an-application-role-using-powershell"></a>Atribuir um acesso de identidade gerido a uma função de aplicação utilizando o PowerShell

Identidades geridas para recursos Azure fornecem aos serviços Azure uma identidade no Azure Ative Directory. Funcionam sem precisar de credenciais no seu código. Os serviços Azure utilizam esta identidade para autenticar serviços que suportem a autenticação AZURE AD. As funções de aplicação fornecem uma forma de controlo de acesso baseado em funções e permitem que um serviço implemente regras de autorização.

Neste artigo, aprende-se a atribuir uma identidade gerida a uma função de aplicação exposta por outra aplicação utilizando o Azure AD PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Se não está familiarizado com as identidades geridas para os recursos da Azure, consulte a [secção de visão geral.](overview.md) **Certifique-se de rever a [diferença entre uma identidade gerida atribuída ao sistema e atribuída ao utilizador](overview.md#managed-identity-types)**.
- Se ainda não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para executar os scripts de exemplo, tem duas opções:
    - Utilize o [Azure Cloud Shell,](../../cloud-shell/overview.md)que pode abrir utilizando o botão **Try It** no canto superior direito dos blocos de código.
    - Executar scripts localmente instalando a versão mais recente do [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

## <a name="assign-a-managed-identity-access-to-another-applications-app-role"></a>Atribuir um acesso de identidade gerido à função de app de outra aplicação

1. Ativar a identidade gerida num recurso Azure, [como um Azure VM](qs-configure-powershell-windows-vm.md).

1. Encontre a identificação do objeto do principal de serviço da identidade gerida.

   **Para uma identidade gerida atribuída ao sistema,** pode encontrar o ID do objeto no portal Azure na página **identidade** do recurso. Também pode utilizar o seguinte script PowerShell para encontrar o ID do objeto. Você precisará do ID de recursos do recurso que criou no passo 1, que está disponível no portal Azure na página **propriedades** do recurso.

    ```powershell
    $resourceIdWithManagedIdentity = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.Compute/virtualMachines/{my virtual machine name}'
    (Get-AzResource -ResourceId $resourceIdWithManagedIdentity).Identity.PrincipalId
    ```

    **Para uma identidade gerida atribuída pelo utilizador,** pode encontrar o ID do objeto de identidade gerida no portal Azure na **página** geral do recurso. Também pode utilizar o seguinte script PowerShell para encontrar o ID do objeto. Vai precisar da identificação de recursos da identidade gerida atribuída pelo utilizador.

    ```powershell
    $userManagedIdentityResourceId = '/subscriptions/{my subscription ID}/resourceGroups/{my resource group name}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{my managed identity name}'
    (Get-AzResource -ResourceId $userManagedIdentityResourceId).Properties.PrincipalId
    ```

1. Crie um novo registo de inscrição para representar o serviço ao que a sua identidade gerida enviará um pedido. Se a API ou o serviço que expõe a subvenção de função da app à identidade gerida já tem um diretor de serviço no seu inquilino AZure AD, ignore este passo. Por exemplo, se pretender conceder o acesso de identidade gerido à API do Gráfico microsoft, pode saltar este passo.

1. Encontre a identificação do objeto do principal serviço da aplicação de serviço. Pode encontrar isto usando o portal Azure. Vá ao Azure Ative Directory e abra a página **de aplicações** da Enterprise, em seguida, encontre a aplicação e procure o **ID do objeto**. Também pode encontrar o ID do objeto do principal do serviço pelo seu nome de exibição utilizando o seguinte script PowerShell:

    ```powershell
    $serverServicePrincipalObjectId = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$applicationName'").ObjectId
    ```

    > [!NOTE]
    > Os nomes de exibição para aplicações não são únicos, pelo que deve verificar se obtém o principal de serviço da aplicação correta.

1. Adicione um [papel de aplicação](../develop/howto-add-app-roles-in-azure-ad-apps.md) à aplicação que criou no passo 3. Pode criar a função utilizando o portal Azure ou utilizando o Microsoft Graph. Por exemplo, pode adicionar um papel de aplicação como este:

    ```json
    {
        "allowedMemberTypes": [
            "Application"
        ],
        "displayName": "Read data from MyApi",
        "id": "0566419e-bb95-4d9d-a4f8-ed9a0f147fa6",
        "isEnabled": true,
        "description": "Allow the application to read data as itself.",
        "value": "MyApi.Read.All"
    }
    ```

1. Atribua o papel da aplicação à identidade gerida. Necessitará das seguintes informações para atribuir o papel da aplicação:
    * `managedIdentityObjectId`: o iD do objeto do principal de serviço da identidade gerida, que encontrou no passo 2.
    * `serverServicePrincipalObjectId`: o iD do objeto do principal de serviço da aplicação do servidor, que encontrou no passo 4.
    * `appRoleId`: o ID da função da aplicação exposta pela aplicação do servidor, que gerou no passo 5 - no exemplo, o ID da função da aplicação é `0566419e-bb95-4d9d-a4f8-ed9a0f147fa6` .
   
   Execute o seguinte script PowerShell para adicionar a atribuição de funções:

    ```powershell
    New-AzureADServiceAppRoleAssignment -ObjectId $managedIdentityObjectId -Id $appRoleId -PrincipalId $managedIdentityObjectId -ResourceId $serverServicePrincipalObjectId
    ```

## <a name="complete-script"></a>Script completo

Este script de exemplo mostra como atribuir a identidade gerida de uma aplicação web Azure a um papel de app.

```powershell
# Install the module. (You need admin on the machine.)
# Install-Module AzureAD

# Your tenant ID (in the Azure portal, under Azure Active Directory > Overview).
$tenantID = '<tenant-id>'

# The name of your web app, which has a managed identity that should be assigned to the server app's app role.
$webAppName = '<web-app-name>'
$resourceGroupName = '<resource-group-name-containing-web-app>'

# The name of the server app that exposes the app role.
$serverApplicationName = '<server-application-name>' # For example, MyApi

# The name of the app role that the managed identity should be assigned to.
$appRoleName = '<app-role-name>' # For example, MyApi.Read.All

# Look up the web app's managed identity's object ID.
$managedIdentityObjectId = (Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $webAppName).identity.principalid

Connect-AzureAD -TenantId $tenantID

# Look up the details about the server app's service principal and app role.
$serverServicePrincipal = (Get-AzureADServicePrincipal -Filter "DisplayName eq '$serverApplicationName'")
$serverServicePrincipalObjectId = $serverServicePrincipal.ObjectId
$appRoleId = ($serverServicePrincipal.AppRoles | Where-Object {$_.Value -eq $appRoleName }).Id

# Assign the managed identity access to the app role.
New-AzureADServiceAppRoleAssignment `
    -ObjectId $managedIdentityObjectId `
    -Id $appRoleId `
    -PrincipalId $managedIdentityObjectId `
    -ResourceId $serverServicePrincipalObjectId
```

## <a name="next-steps"></a>Próximos passos

- [Identidade gerida para visão geral dos recursos da Azure](overview.md)
- Para permitir a identidade gerida num Azure VM, consulte [identidades geridas configure para recursos Azure num VM Azure utilizando o PowerShell](qs-configure-powershell-windows-vm.md).