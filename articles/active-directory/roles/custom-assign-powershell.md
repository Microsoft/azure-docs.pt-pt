---
title: Atribuir funções personalizadas usando Azure PowerShell - Azure AD / Microsoft Docs
description: Gerir membros de um papel personalizado de administrador AD Azure com a Azure PowerShell.
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
ms.openlocfilehash: d4695d0844ef8b707edce53a05de611c91223a46
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861957"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Atribuir funções personalizadas com âmbito de recursos utilizando o PowerShell no Azure Ative Directory

Este artigo descreve como criar uma atribuição de papel no âmbito da organização em Azure Ative Directory (Azure AD). Atribuir um papel no âmbito da organização concede acesso a toda a organização Azure AD. Para criar uma atribuição de funções com um âmbito de um único recurso AD Azure, consulte [Como criar uma função personalizada e atribuí-la no âmbito de recursos](custom-create.md). Este artigo utiliza o módulo [Azure Ative Directory PowerShell Version 2.](/powershell/module/azuread/#directory_roles)

Para obter mais informações sobre as funções de administrador da Azure AD, consulte [as funções de administrador de atribuição no Diretório Ativo Azure](permissions-reference.md).

## <a name="required-permissions"></a>Permissões obrigatórias

Ligue-se à sua organização Azure AD usando uma conta de administrador global para atribuir ou remover funções.

## <a name="prepare-powershell"></a>Preparar PowerShell

Instale o módulo Azure AD PowerShell da [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Em seguida, importe o módulo de pré-visualização Azure AD PowerShell, utilizando o seguinte comando:

``` PowerShell
import-module azureadpreview
```

Para verificar se o módulo está pronto a ser utilizado, combine a versão devolvida pelo seguinte comando ao indicado aqui:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

Agora pode começar a utilizar os cmdlets no módulo. Para obter uma descrição completa dos cmdlets no módulo AD Azure, consulte a documentação de referência on-line para o [módulo de pré-visualização AZure AD](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17).

## <a name="assign-a-role-to-a-user-or-service-principal-with-resource-scope"></a>Atribuir uma função a um utilizador ou principal de serviço com âmbito de recursos

1. Abra o módulo PowerShell de pré-visualização Azure AD.
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

Para atribuir a função a um principal de serviço em vez de um utilizador, utilize o [cmdlet Get-AzureADMSServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal).

## <a name="operations-on-roledefinition"></a>Operações em RoleDefinition

Os objetos de definição de função contêm a definição do papel incorporado ou personalizado, juntamente com as permissões que são concedidas por essa atribuição de funções. Este recurso exibe definições de funções personalizadas e diretórios incorporados (que são apresentados na forma equivalente de definição de função). Hoje, uma organização Azure AD pode ter um máximo de 30 RoleDefinitions personalizados únicos definidos.

### <a name="create-operations-on-roledefinition"></a>Criar Operações na Interpretação de Função

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/standard/read",
    "microsoft.directory/applications/credentials/update"
)
$rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}

# Create new custom admin role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-operations-on-roledefinition"></a>Ler Operações em RoleDefinition

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by objectId
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-operations-on-roledefinition"></a>Operações de atualização sobre adefinição de funções

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-operations-on-roledefinition"></a>Eliminar operações na RoleDefinition

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="operations-on-roleassignment"></a>Operações de Destacamento de RoleAssinamento

As atribuições de funções contêm informações que ligam um dado principal de segurança (um utilizador ou principal do serviço de aplicações) a uma definição de função. Se necessário, pode adicionar um âmbito de um único recurso AD Azure para as permissões atribuídas.  A restrição do âmbito das permissões é suportada para funções incorporadas e personalizadas.

### <a name="create-operations-on-roleassignment"></a>Criar Operações sobre RoleAssignment

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

### <a name="read-operations-on-roleassignment"></a>Ler Operações sobre RoleAssignment

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-operations-on-roleassignment"></a>Eliminar operações sobre a assinatura de roleAssignment

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>Passos seguintes

- Partilhe connosco no fórum de [funções administrativas da Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
- Para obter mais sobre funções e atribuições de funções de administrador azure, consulte [atribuições de funções de administrador](permissions-reference.md)
- Para obter permissões de utilizador predefinidos, consulte uma [comparação entre permissões de hóspedes e utilizadores de membros predefinidos](../fundamentals/users-default-permissions.md)
