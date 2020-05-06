---
title: Elevate access to manage all Azure subscriptions and management groups (Elevar o acesso para gerir todas as subscrições e grupos de gestão do Azure)
description: Descreve como elevar o acesso a um Administrador Global para gerir todas as subscrições e grupos de gestão no Azure Ative Directory utilizando o portal Azure ou rest API.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: bagovind
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 6821e3de3bfec891d98e9291a479cbb7537364ca
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/03/2020
ms.locfileid: "82733669"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Elevate access to manage all Azure subscriptions and management groups (Elevar o acesso para gerir todas as subscrições e grupos de gestão do Azure)

Como Administrador Global em Azure Ative Directory (Azure AD), poderá não ter acesso a todas as subscrições e grupos de gestão no seu diretório. Este artigo descreve as formas como pode elevar o seu acesso a todas as subscrições e grupos de gestão.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Por que precisa elevar o seu acesso?

Se é administrador global, pode haver alturas em que pretende fazer as seguintes ações:

- Recuperar o acesso a um grupo de gestão ou subscrição do Azure quando um utilizador perde uinos
- Conceder a outro utilizador ou a si próprio acesso a uma subscrição ou grupo de gestão do Azure
- Ver todas as subscrições do Azure ou grupos de gestão numa organização
- Permitir que uma aplicação de automação (como uma aplicação de faturação ou auditoria) aceda a todas as subscrições ou grupos de gestão do Azure

## <a name="how-does-elevated-access-work"></a>Como funciona o acesso elevado?

Os recursos da Azure AD e azure são assegurados independentemente uns dos outros. Ou seja, as atribuições de funções da Azure AD não concedem acesso aos recursos do Azure, e as atribuições de funções da Azure não concedem acesso à AD Azure. No entanto, se for administrador [global](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) em Azure AD, pode atribuir-se acesso a todas as subscrições e grupos de gestão do Azure no seu diretório. Utilize esta capacidade se não tiver acesso aos recursos de subscrição do Azure, como máquinas virtuais ou contas de armazenamento, e pretende utilizar o privilégio do Administrador Global para ter acesso a esses recursos.

Quando elevar o seu acesso, será-lhe atribuída a função de`/`Administrador de Acesso ao [Utilizador](built-in-roles.md#user-access-administrator) em Azure no âmbito raiz ().Isto permite-lhe visualizar todos os recursos e atribuir acesso em qualquer subscrição ou grupo de gestão no diretório. As atribuições de funções do Administrador de Acesso ao Utilizador podem ser removidas utilizando o Azure PowerShell, o Azure CLI ou a API REST.

Deve remover este acesso elevado depois de ter feito as alterações que precisa de fazer no âmbito da raiz.

![Elevar o acesso](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Portal do Azure

### <a name="elevate-access-for-a-global-administrator"></a>Elevar o acesso a um Administrador Global

Siga estes passos para elevar o acesso a um Administrador Global utilizando o portal Azure.

1. Inscreva-se no [portal Azure](https://portal.azure.com) ou no centro de [administração do Azure Ative Directin](https://aad.portal.azure.com) como Administrador Global.

1. Diretório **Ativo Open Azure.**

1. Em **'Gerir',** selecione **Propriedades**.

   ![Selecione Propriedades para propriedades de Diretório Ativo Azure - screenshot](./media/elevate-access-global-admin/azure-active-directory-properties.png)

1. Sob **gestão de acesso para recursos Azure,** detete o toggle para **Sim**.

   ![Gestão de acesso para recursos Azure - screenshot](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Quando definir o toggle para **Sim,** é-lhe atribuída a função de Administrador de Acesso ao Utilizador no Azure RBAC no âmbito raiz (/). Isto concede-lhe permissão para atribuir funções em todas as subscrições e grupos de gestão azure associados a este diretório Azure AD. Este alternância só está disponível para utilizadores a quem seja atribuído o papel de Administrador Global em Azure AD.

   Quando definir o toggle para **Não,** a função de Administrador de Acesso ao Utilizador no Azure RBAC é removida da sua conta de utilizador. Já não é possível atribuir funções em todas as subscrições e grupos de gestão do Azure que estão associados a este diretório Azure AD. Pode ver e gerir apenas as subscrições e grupos de gestão azure a que lhe foi concedido acesso.

    > [!NOTE]
    > Se estiver a utilizar a [Azure AD Privileged Identity Management (PIM),](../active-directory/privileged-identity-management/pim-configure.md)desativar a sua atribuição de funções não altera este toggle para **No**. Para manter o acesso menos privilegiado, recomendamos que desative este toggle para **Não** antes de desativar a sua atribuição de funções.
    
1. Clique em **Guardar** para salvar a sua definição.

   Esta definição não é uma propriedade global e aplica-se apenas ao atualmente assinado no utilizador. Não se pode elevar o acesso a todos os membros do papel de Administrador Global.

1. Inscreva-se e volte a entrar para refrescar o seu acesso.

    Deverá agora ter acesso a todas as subscrições e grupos de gestão do seu diretório. Quando visualizar o painel de controlo de acesso (IAM), irá notar que lhe foi atribuída a função de Administrador de Acesso ao Utilizador no âmbito de raiz.

   ![Atribuições de funções de subscrição com âmbito de raiz - screenshot](./media/elevate-access-global-admin/iam-root.png)

1. Faça as alterações que precisa de fazer no acesso elevado.

    Para obter informações sobre a atribuição de funções, consulte Adicionar ou remover atribuições de [funções Azure utilizando o portal Azure](role-assignments-portal.md). Se estiver a utilizar a Azure AD Privileged Identity Management (PIM), consulte [os recursos do Discover Azure para gerir em](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) funções de recurso PIM ou [Atribuíris Azure na PIM.](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md)

### <a name="remove-elevated-access"></a>Remover o acesso elevado

Para remover a atribuição da função`/`de Administrador de Acesso ao Utilizador no âmbito raiz (), siga estes passos.

1. Inscreva-se como o mesmo utilizador que foi usado para elevar o acesso.

1. Na lista de navegação, clique em **Azure Ative Directory** e, em seguida, clique em **Propriedades**.

1. Desloque a **gestão de acesso dos recursos azure** para o **Nº**. Uma vez que se trata de uma definição por utilizador, deve ser inscrito como o mesmo utilizador que foi utilizado para elevar o acesso.

    Se tentar remover a atribuição da função de Administrador de Acesso ao Utilizador no painel de controlo de acesso (IAM), verá a seguinte mensagem. Para remover a atribuição de funções, deve repor o toggle para **No** ou utilizar o Azure PowerShell, O CLI Azure ou a API REST.

    ![Remover atribuições de funções com âmbito de raiz](./media/elevate-access-global-admin/iam-root-remove.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-root-scope-"></a>Atribuição de funções de lista no âmbito raiz (/)

Para listar a atribuição da função de`/`Administrador de Acesso ao Utilizador para um utilizador no âmbito de raiz (), utilize o comando [Get-AzRoleAssignment.](/powershell/module/az.resources/get-azroleassignment)

```azurepowershell
Get-AzRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : User
CanDelegate        : False
```

### <a name="remove-elevated-access"></a>Remover o acesso elevado

Para remover a atribuição da função de Administrador`/`de Acesso ao Utilizador para si ou para outro utilizador no âmbito da raiz (), siga estes passos.

1. Inscreva-se como utilizador que pode remover o acesso elevado. Este pode ser o mesmo utilizador que foi usado para elevar o acesso ou outro Administrador Global com acesso elevado no âmbito da raiz.

1. Utilize o comando [Remover-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) para remover a atribuição da função de Administrador de Acesso ao Utilizador.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="azure-cli"></a>CLI do Azure

### <a name="list-role-assignment-at-root-scope-"></a>Atribuição de funções de lista no âmbito raiz (/)

Para listar a atribuição da função de`/`Administrador de Acesso ao Utilizador para um utilizador no âmbito raiz (), utilize o comando da [lista de tarefas az.](/cli/azure/role/assignment#az-role-assignment-list)

```azurecli
az role assignment list --role "User Access Administrator" --scope "/"
```

```Example
[
  {
    "canDelegate": null,
    "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
    "name": "11111111-1111-1111-1111-111111111111",
    "principalId": "22222222-2222-2222-2222-222222222222",
    "principalName": "username@example.com",
    "principalType": "User",
    "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "roleDefinitionName": "User Access Administrator",
    "scope": "/",
    "type": "Microsoft.Authorization/roleAssignments"
  }
]

```

### <a name="remove-elevated-access"></a>Remover o acesso elevado

Para remover a atribuição da função de Administrador`/`de Acesso ao Utilizador para si ou para outro utilizador no âmbito da raiz (), siga estes passos.

1. Inscreva-se como utilizador que pode remover o acesso elevado. Este pode ser o mesmo utilizador que foi usado para elevar o acesso ou outro Administrador Global com acesso elevado no âmbito da raiz.

1. Utilize a atribuição de [funções az eliminar](/cli/azure/role/assignment#az-role-assignment-delete) o comando para remover a atribuição da função de Administrador de Acesso ao Utilizador.

    ```azurecli
    az role assignment delete --assignee username@example.com --role "User Access Administrator" --scope "/"
    ```

## <a name="rest-api"></a>API REST

### <a name="elevate-access-for-a-global-administrator"></a>Elevar o acesso a um Administrador Global

Utilize os seguintes passos básicos para elevar o acesso a um Administrador Global utilizando a API REST.

1. Utilização do `elevateAccess`REST, ligue , que lhe concede`/`o papel de Administrador de Acesso ao Utilizador no âmbito raiz ().

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Criar uma atribuição de [funções](/rest/api/authorization/roleassignments) para atribuir qualquer função em qualquer âmbito. O exemplo seguinte mostra as propriedades para atribuir a função`/`{roleDefinitionID} no âmbito raiz ():

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "11111111-1111-1111-1111-111111111111"
   }
   ```

1. Enquanto administrador de acesso ao utilizador, também pode`/`remover as atribuições de funções no âmbito da raiz ().

1. Remova os privilégios do Administrador de Acesso ao Utilizador até que sejam necessários novamente.

### <a name="list-role-assignments-at-root-scope-"></a>Atribuição de funções de lista no âmbito raiz (/)

Pode listar todas as atribuições de funções`/`para um utilizador no âmbito raiz ().

- Ligue para as `{objectIdOfUser}` [roleAssignments](/rest/api/authorization/roleassignments/listforscope) onde está o ID do objeto do utilizador cujas atribuições de funções pretende recuperar.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-root-scope-"></a>Lista nega atribuições no âmbito raiz (/)

Pode enumerar todas as atribuições de negação`/`para um utilizador no âmbito raiz ().

- Call GET denyAssignments onde `{objectIdOfUser}` está o ID do objeto do utilizador cujas atribuições de negação que pretende recuperar.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Remover o acesso elevado

Quando ligar, `elevateAccess`cria uma atribuição de funções para si mesmo, para revogar esses privilégios que`/`precisa para remover a atribuição de funções de Administrador de Acesso ao Utilizador para si mesmo no âmbito de raiz ().

1. Ligue para as `roleName` definições de [funções GET Onde](/rest/api/authorization/roledefinitions/get) é igual a Administrador de Acesso ao Utilizador para determinar o nome ID da função de Administrador de Acesso ao Utilizador.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
      "roleName": "User Access Administrator",
      "type": "BuiltInRole",
      "description": "Lets you manage user access to Azure resources.",
      "assignableScopes": [
        "/"
      ],
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Authorization/*",
            "Microsoft.Support/*"
          ],
          "notActions": []
        }
      ],
      "createdOn": "0001-01-01T08:00:00.0000000Z",
      "updatedOn": "2016-05-31T23:14:04.6964687Z",
      "createdBy": null,
      "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    Guarde a `name` identificação do parâmetro, neste caso. `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`

1. Também precisa de enumerar a atribuição de funções para o administrador de diretório no âmbito do diretório. Enumerar todas as atribuições `principalId` no âmbito do diretório para o administrador de diretório que elegou a chamada de acesso elevada. Isto listará todas as atribuições no diretório para o objectide.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
        
    >[!NOTE] 
    >Um administrador de diretório não deve ter muitas atribuições, se a consulta anterior devolver muitas atribuições, também pode consultar todas as atribuições apenas ao nível do âmbito de diretório, e depois filtrar os resultados:`GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
            
1. As chamadas anteriores devolvem uma lista de atribuições de papéis. Encontre a atribuição de `"/"` funções `roleDefinitionId` onde está o âmbito e termina `principalId` com o nome de identificação do papel que encontrou no passo 1 e corresponde ao objectid do administrador de diretório. 
    
    Atribuição de funções de amostra:
    
    ```json
    {
      "value": [
        {
          "properties": {
            "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
            "principalId": "{objectID}",
            "scope": "/",
            "createdOn": "2016-08-17T19:21:16.3422480Z",
            "updatedOn": "2016-08-17T19:21:16.3422480Z",
            "createdBy": "22222222-2222-2222-2222-222222222222",
            "updatedBy": "22222222-2222-2222-2222-222222222222"
          },
          "id": "/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
          "type": "Microsoft.Authorization/roleAssignments",
          "name": "11111111-1111-1111-1111-111111111111"
        }
      ],
      "nextLink": null
    }
    ```
    
    Mais uma vez, `name` guarde o ID do parâmetro, neste caso 11111111-1111-1111-1111-1111-1111111.

1. Por fim, utilize o ID de `elevateAccess`atribuição de funções para remover a atribuição adicionada por:

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Passos seguintes

- [Compreender as diferentes funções](rbac-and-directory-admin-roles.md)
- [Adicione ou remova atribuições de funções Azure utilizando a API REST](role-assignments-rest.md)
