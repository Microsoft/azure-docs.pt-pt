---
title: Atribuir funções Azure usando Azure PowerShell - Azure RBAC
description: Saiba como conceder acesso aos recursos Azure para utilizadores, grupos, principais serviços ou identidades geridas utilizando o controlo de acesso baseado em funções Azure PowerShell e Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 00f663b90f34f3b557329692f844bbbc1bf3207d
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556805"
---
# <a name="assign-azure-roles-using-azure-powershell"></a>Atribuir funções Azure usando Azure PowerShell

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Este artigo descreve como atribuir funções usando a Azure PowerShell.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para atribuir funções, deve ter:

- `Microsoft.Authorization/roleAssignments/write` permissões, tais como [Administrador de Acesso ao Utilizador](built-in-roles.md#user-access-administrator) ou [Proprietário](built-in-roles.md#owner)
- [PowerShell em Azure Cloud Shell](../cloud-shell/overview.md) ou [Azure PowerShell](/powershell/azure/install-az-ps)
- A conta que utiliza para executar o comando PowerShell deve ter a permissão do Microsoft `Directory.Read.All` Graph.

## <a name="steps-to-assign-an-azure-role"></a>Passos para atribuir um papel de Azure

Atribuir uma função consiste em três elementos: principal de segurança, definição de função e âmbito.

### <a name="step-1-determine-who-needs-access"></a>Passo 1: Determinar quem precisa de acesso

Pode atribuir uma função a um utilizador, grupo, principal de serviço ou identidade gerida. Para atribuir uma função, pode ser necessário especificar o ID único do objeto. O ID tem o formato: `11111111-1111-1111-1111-111111111111` . Pode obter o ID usando o portal Azure ou Azure PowerShell.

**Utilizador**

Para um utilizador AZure AD, obtenha o nome principal do utilizador, como *patlong \@ contoso.com* ou o ID do objeto de utilizador. Para obter o ID do objeto, pode utilizar [o Get-AzADUser](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <userName>
(Get-AzADUser -DisplayName <userName>).id
```

**Grupo**

Para um grupo AD Azure, você precisa da identificação do objeto de grupo. Para obter o ID do objeto, pode utilizar [o Get-AzADGroup](/powershell/module/az.resources/get-azadgroup).

```azurepowershell
Get-AzADGroup -SearchString <groupName>
(Get-AzADGroup -DisplayName <groupName>).id
```

**Service principal (Principal de serviço)**

Para um principal de serviço Azure AD (identidade utilizada por uma aplicação), precisa do iD principal do objeto de serviço. Para obter o ID do objeto, pode utilizar [o Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal). Para um principiante de serviço, utilize o ID do objeto e **não** o ID da aplicação.

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```

**Identidade gerida**

Para uma identidade gerida atribuída pelo sistema ou para uma identidade gerida atribuída pelo utilizador, precisa do ID do objeto. Para obter o ID do objeto, pode utilizar [o Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```
    
### <a name="step-2-select-the-appropriate-role"></a>Passo 2: Selecione a função adequada

As permissões são agrupadas em papéis. Pode selecionar a partir de uma lista de várias [funções incorporadas do Azure](built-in-roles.md) ou pode usar as suas próprias funções personalizadas. É uma boa prática conceder acesso com o menor privilégio que for necessário, por isso evite atribuir um papel mais amplo.

Para listar papéis e obter o ID de papel único, você pode usar [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom, Id
```

Aqui está como listar os detalhes de um papel particular.

```azurepowershell
Get-AzRoleDefinition <roleName>
```

Para obter mais informações, consulte as definições de [funções list Azure](role-definitions-list.md#azure-powershell).
 
### <a name="step-3-identify-the-needed-scope"></a>Passo 3: Identificar o âmbito necessário

O Azure fornece quatro níveis de âmbito: recurso, grupo de [recursos,](../azure-resource-manager/management/overview.md#resource-groups)subscrição e [grupo de gestão.](../governance/management-groups/overview.md) É uma boa prática conceder acesso com o menor privilégio que for necessário, por isso evite atribuir um papel num âmbito mais amplo. Para obter mais informações sobre o âmbito, consulte [o âmbito de aplicação.](scope-overview.md)

**Âmbito do recurso**

Para o âmbito de recursos, precisa do ID de recurso para o recurso. Pode encontrar o ID do recurso olhando para as propriedades do recurso no portal Azure. Um ID de recurso tem o seguinte formato.

```
/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

**Âmbito de grupo de recursos**

Para o âmbito do grupo de recursos, precisa do nome do grupo de recursos. Pode encontrar o nome na página **de grupos de recursos** no portal Azure ou pode utilizar o [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell
Get-AzResourceGroup
```

**Âmbito de subscrição** 

Para o âmbito de subscrição, precisa do ID de subscrição. Pode encontrar o ID na página **de Subscrições** no portal Azure ou pode utilizar [a subscrição Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```azurepowershell
Get-AzSubscription
```

**Âmbito do grupo de gestão** 

Para o âmbito do grupo de gestão, você precisa do nome do grupo de gestão. Pode encontrar o nome na página de **grupos de Gestão** no portal Azure ou pode utilizar o [Grupo Get-AzManagement](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
Get-AzManagementGroup
```
    
### <a name="step-4-assign-role"></a>Passo 4: Atribuir função

Para atribuir um papel, use o comando [new-AzRoleAssignment.](/powershell/module/az.resources/new-azroleassignment) Dependendo do âmbito, o comando tem normalmente um dos seguintes formatos.

**Âmbito do recurso**

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionId <roleId> `
-ResourceName <resourceName> `
-ResourceType <resourceType> `
-ResourceGroupName <resourceGroupName>
```

**Âmbito de grupo de recursos**

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

**Âmbito de subscrição** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

**Âmbito do grupo de gestão** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 
    
## <a name="assign-role-examples"></a>Atribuir exemplos de funções

#### <a name="assign-a-role-for-all-blob-containers-in-a-storage-account-resource-scope"></a>Atribuir uma função para todos os recipientes blob num âmbito de recursos de conta de armazenamento

Atribui a função [de Contribuinte de Dados Blob de Armazenamento](built-in-roles.md#storage-blob-data-contributor) a um titular de serviço com iD *55555555-5555-55555-555555555555555555555555555* numa área de recursos para uma conta de armazenamento denominada *armazenamento12345*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/providers/Microsoft.Authorization/roleAssignments/cccccccc-cccc-cccc-cccc-cccccccccccc
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-specific-blob-container-resource-scope"></a>Atribuir uma função para um âmbito específico de recurso de recipiente de bolhas

Atribui a função [de contribuinte de dados blob de armazenamento](built-in-roles.md#storage-blob-data-contributor) a um titular de serviço com o ID do objeto *5555555-5555-5555-555555555555555555555555555,* numa mira de recurso para um recipiente de bolhas denominado *blob-container-01*.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignm
                     ents/dddddddd-dddd-dddd-dddd-dddddddddddd
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-group-in-a-specific-virtual-network-resource-scope"></a>Atribuir um papel para um grupo num âmbito específico de recursos de rede virtual

Atribui o papel [de Contribuinte de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) ao grupo *Pharma Sales Admins* com ID aaaaaaa-aaaaa-aaaaa-aaaaa 

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceName pharma-sales-project-network `
-ResourceType Microsoft.Network/virtualNetworks `
-ResourceGroupName MyVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-at-a-resource-group-scope"></a>Atribuir uma função para um utilizador num âmbito de grupo de recursos

Atribui o papel [de Contribuinte de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) ao utilizador *\@ patlong contoso.com* no âmbito do grupo de recursos de vendas de *pharma.*

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

Alternadamente, pode especificar o grupo de recursos totalmente qualificado com o `-Scope` parâmetro:

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>Atribuir uma função para um utilizador que usa o ID de função único num âmbito de grupo de recursos

Há um par de vezes em que um nome pode mudar, por exemplo:

- Está a usar o seu próprio papel personalizado e decide mudar o nome.
- Está a utilizar um papel de pré-visualização que tem **(Pré-visualização)** no nome. Quando o papel é lançado, o papel é renomeado.

Mesmo que um papel seja renomeado, o iD do papel não muda. Se estiver a usar scripts ou automação para criar as suas atribuições de papéis, é uma boa prática usar o ID de função único em vez do nome de função. Portanto, se um papel for renomeado, os seus scripts são mais propensos a funcionar.

O exemplo a seguir atribui o papel [de Contribuinte de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) ao utilizador *\@ patlong contoso.com* no âmbito do grupo de recursos de vendas de *pharma.*

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 `
-RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="assign-a-role-for-an-application-at-a-resource-group-scope"></a>Atribuir uma função para uma aplicação num âmbito de grupo de recursos

Atribui a função [de Contribuinte de Máquina Virtual](built-in-roles.md#virtual-machine-contributor) a uma aplicação com o objeto principal de serviço ID 77777777-7777-7777-77777-777777777777777777 no âmbito do grupo de recursos de vendas de *pharma.*

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-at-a-subscription-scope"></a>Atribuir uma função para um utilizador num âmbito de subscrição

Atribui o papel [de Leitor](built-in-roles.md#reader) ao *utilizador annm \@ example.com* num âmbito de subscrição.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName annm@example.com `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Ann M
SignInName         : annm@example.com
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="assign-a-role-for-a-user-at-a-management-group-scope"></a>Atribuir uma função para um utilizador no âmbito de um grupo de gestão

Atribui o papel [de Leitor de Faturação](built-in-roles.md#billing-reader) ao *utilizador alain \@ example.com* num âmbito de grupo de gestão.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="next-steps"></a>Passos seguintes

- [List Azure fun assignments using Azure PowerShell](role-assignments-list-powershell.md)
- [Tutorial: Conceder a um grupo acesso aos recursos da Azure utilizando a Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Gerir recursos com a Azure PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)
