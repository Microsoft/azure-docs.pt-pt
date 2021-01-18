---
title: Atribua funções personalizadas usando Azure AD PowerShell - Azure AD | Microsoft Docs
description: Gerir membros de um papel personalizado de administrador AD Azure com a Azure AD PowerShell.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b155ccd7f8f0d7f6d63d906d7d0baaa3243512b
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562782"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Atribuir funções personalizadas com âmbito de recursos utilizando o PowerShell no Azure Ative Directory

Este artigo descreve como criar uma atribuição de papel no âmbito da organização em Azure Ative Directory (Azure AD). Atribuir um papel no âmbito da organização concede acesso a toda a organização Azure AD. Para criar uma atribuição de funções com um âmbito de um único recurso AD Azure, consulte [Como criar uma função personalizada e atribuí-la no âmbito de recursos](custom-create.md). Este artigo utiliza o módulo [Azure Ative Directory PowerShell Version 2.](/powershell/module/azuread/#directory_roles)

Para obter mais informações sobre as funções de administrador da Azure AD, consulte [as funções de administrador de atribuição no Diretório Ativo Azure](permissions-reference.md).

## <a name="required-permissions"></a>Permissões obrigatórias

Ligue-se à sua organização Azure AD usando uma conta de administrador global para atribuir ou remover funções.

## <a name="prepare-powershell"></a>Preparar PowerShell

Instale o módulo Azure AD PowerShell da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview). Em seguida, importe o módulo de pré-visualização Azure AD PowerShell, utilizando o seguinte comando:

``` PowerShell
Import-Module AzureADPreview
```

Para verificar se o módulo está pronto a ser utilizado, combine a versão devolvida pelo seguinte comando ao indicado aqui:

``` PowerShell
Get-Module AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

Agora pode começar a utilizar os cmdlets no módulo. Para obter uma descrição completa dos cmdlets no módulo AD Azure, consulte a documentação de referência on-line para o [módulo de pré-visualização AZure AD](https://www.powershellgallery.com/packages/AzureADPreview).

## <a name="assign-a-directory-role-to-a-user-or-service-principal-with-resource-scope"></a>Atribuir uma função de diretório a um utilizador ou principal de serviço com âmbito de recursos

1. Carregue o módulo Azure AD PowerShell (Preview).
1. Inscreva-se executando o comando `Connect-AzureAD` .
1. Crie um novo papel utilizando o seguinte script PowerShell.

``` PowerShell
## Assign a role to a user or service principal with resource scope
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

Para atribuir a função a um principal de serviço em vez de um utilizador, utilize o [cmdlet Get-AzureADMSServicePrincipal.](/powershell/module/azuread/get-azureadserviceprincipal)

## <a name="role-definitions"></a>Definições de função

Os objetos de definição de função contêm a definição do papel incorporado ou personalizado, juntamente com as permissões que são concedidas por essa atribuição de funções. Este recurso exibe definições de funções personalizadas e funções de diretório incorporadas (que são exibidas na forma equivalente de definição de função). Hoje, uma organização AZure AD pode ter um máximo de 30 definições de papel personalizado únicas definidas.

### <a name="create-a-role-definition"></a>Criar uma definição de papel

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to include
$rolePermissions = @{
    "allowedResourceActions" = @(
        "microsoft.directory/applications/standard/read",
        "microsoft.directory/applications/credentials/update"
    )
}

# Create new custom directory role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-and-list-role-definitions"></a>Ler e listar definições de funções

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by ID
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-a-role-definition"></a>Atualizar uma definição de função

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-a-role-definition"></a>Eliminar uma definição de função

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="role-assignments"></a>Atribuições de funções

As atribuições de funções contêm informações que ligam um dado principal de segurança (um utilizador ou principal do serviço de aplicações) a uma definição de função. Se necessário, pode adicionar um âmbito de um único recurso AD Azure para as permissões atribuídas.  Restringir o âmbito de uma atribuição de funções é suportado para funções incorporadas e personalizadas.

### <a name="create-a-role-assignment"></a>Criar uma atribuição de funções

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

### <a name="read-and-list-role-assignments"></a>Ler e listar atribuições de funções

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-a-role-assignment"></a>Excluir uma atribuição de funções

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>Passos seguintes

- Partilhe connosco no fórum de [funções administrativas da Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
- Para obter mais sobre funções e atribuições de funções de administrador AD Azure, consulte [atribuições de funções de administrador](permissions-reference.md)
- Para obter permissões de utilizador predefinidos, consulte uma [comparação entre permissões de hóspedes e utilizadores de membros predefinidos](../fundamentals/users-default-permissions.md)
