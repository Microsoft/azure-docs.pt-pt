---
title: Atribuir funções personalizadas utilizando o Azure PowerShell - Azure AD [ Microsoft Docs
description: Gerencie os membros de um cargo personalizado de administrador da Azure AD com a Azure PowerShell.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a2096b7899039e7a9d3455bc0c6fb3ec84ebd1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025322"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Atribuir funções personalizadas com âmbito de recursos utilizando powerShell no Diretório Ativo Azure

Este artigo descreve como criar uma atribuição de papéis no âmbito da organização em Azure Ative Directory (Azure AD). Atribuir um papel no âmbito da organização concede acesso em toda a organização Azure AD. Para criar uma atribuição de funções com um âmbito de um único recurso Azure AD, consulte [como criar uma função personalizada e atribuí-la no âmbito](roles-create-custom.md)do recurso . Este artigo utiliza o módulo PowerShell Version 2 do [Diretório Ativo Azure.](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#directory_roles)

Para obter mais informações sobre as funções de administrador da Azure AD, consulte as funções de [administrador de atribuição no Diretório Ativo azure](directory-assign-admin-roles.md).

## <a name="required-permissions"></a>Permissões obrigatórias

Ligue-se ao seu inquilino Azure AD usando uma conta de administrador global para atribuir ou remover funções.

## <a name="prepare-powershell"></a>Preparar powerShell

Instale o módulo PowerShell Azure AD a partir da [Galeria PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Em seguida, importe o módulo de pré-visualização Azure AD PowerShell, utilizando o seguinte comando:

``` PowerShell
import-module azureadpreview
```

Para verificar se o módulo está pronto a ser utilizado, corresponda à versão devolvida pelo seguinte comando ao indicado aqui:

``` PowerShell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

Agora pode começar a utilizar os cmdlets no módulo. Para obter uma descrição completa dos cmdlets no módulo AD Azure, consulte a documentação de referência on-line para o módulo de [pré-visualização da AD Azure](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17).

## <a name="assign-a-role-to-a-user-or-service-principal-with-resource-scope"></a>Atribuir uma função a um utilizador ou diretor de serviço com âmbito de recurso

1. Abra o módulo powerShell de pré-visualização Da AD Azure.
1. Inscreva-se executando `Connect-AzureAD`o comando.
1. Crie uma nova função utilizando o seguinte script PowerShell.

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

Para atribuir a função a um diretor de serviço em vez de um utilizador, utilize o [cmdlet Get-AzureADMSServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0).

## <a name="operations-on-roledefinition"></a>Operações em RoleDefinition

Os objetos de definição de papel contêm a definição do papel incorporado ou personalizado, juntamente com as permissões que são concedidas por essa atribuição de funções. Este recurso apresenta definições de papéis personalizadas e diretórios incorporados (que são exibidos na forma equivalente roleDefinition). Hoje, uma organização azure ad pode ter um máximo de 30 Definições personalizadas únicas definidas.

### <a name="create-operations-on-roledefinition"></a>Criar operações na Definição de RoleDefinition

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

### <a name="read-operations-on-roledefinition"></a>Ler Operações sobre Definição de RoleDefinition

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by objectId
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-operations-on-roledefinition"></a>Atualizar operações na RoleDefinition

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-operations-on-roledefinition"></a>Eliminar operações em RoleDefinition

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="operations-on-roleassignment"></a>Operações em RoleAssignment

As atribuições de funções contêm informações que ligam um determinado diretor de segurança (um utilizador ou diretor de serviço de aplicação) a uma definição de função. Se necessário, pode adicionar um âmbito de um único recurso Azure AD para as permissões atribuídas.  Restringir o âmbito das permissões é suportado para funções incorporadas e personalizadas.

### <a name="create-operations-on-roleassignment"></a>Criar operações em RoleAssignment

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

### <a name="read-operations-on-roleassignment"></a>Ler Operações sobre Atribuição de Funções

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-operations-on-roleassignment"></a>Eliminar operações em RoleAssignment

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>Passos seguintes

- Partilhe connosco no fórum de [funções administrativas da Azure AD.](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
- Para mais informações sobre funções e atribuições de funções de administrador da AD azul, consulte [funções](directory-assign-admin-roles.md)de administrador de atribuição .
- Para obter permissões de utilizador predefinidas, consulte uma [comparação das permissões padrão dos hóspedes e dos utilizadores dos membros](../fundamentals/users-default-permissions.md).
