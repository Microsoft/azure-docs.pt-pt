---
title: Elevate access to manage all Azure subscriptions and management groups (Elevar o acesso para gerir todas as subscrições e grupos de gestão do Azure)
description: Descreve como elevar o acesso a um Administrador Global para gerir todas as subscrições e grupos de gestão no Azure Ative Directory usando o portal Azure ou REST API.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 06/09/2020
ms.author: rolyon
ms.openlocfilehash: 6e57e495d34a265b5e0691106996206029656c5a
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371125"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Elevate access to manage all Azure subscriptions and management groups (Elevar o acesso para gerir todas as subscrições e grupos de gestão do Azure)

Como Administrador Global em Diretório Ativo Azure (Azure AD), poderá não ter acesso a todas as subscrições e grupos de gestão no seu diretório. Este artigo descreve as formas de elevar o seu acesso a todas as subscrições e grupos de gestão.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Por que precisa elevar o seu acesso?

Se for administrador global, pode haver momentos em que pretende fazer as seguintes ações:

- Recuperar o acesso a um grupo de subscrição ou gestão Azure quando um utilizador perdeu o acesso
- Conceder a outro utilizador ou a si próprio acesso a uma subscrição ou grupo de gestão do Azure
- Veja todas as subscrições ou grupos de gestão da Azure numa organização
- Permitir que uma aplicação de automação (como uma aplicação de faturação ou auditoria) aceda a todas as subscrições ou grupos de gestão da Azure

## <a name="how-does-elevated-access-work"></a>Como funciona o acesso elevado?

Os recursos Azure AD e Azure são protegidos independentemente uns dos outros. Ou seja, as atribuições de funções da Azure AD não concedem acesso aos recursos da Azure, e as atribuições de funções da Azure não concedem acesso à Azure AD. No entanto, se for [administrador global](../active-directory/roles/permissions-reference.md#company-administrator-permissions) em Azure AD, pode atribuir-se acesso a todas as subscrições e grupos de gestão da Azure no seu diretório. Utilize esta capacidade se não tiver acesso aos recursos de subscrição do Azure, como máquinas virtuais ou contas de armazenamento, e pretende utilizar o seu privilégio de Administrador Global para ter acesso a esses recursos.

Quando elevar o seu acesso, será atribuída a função [de Administrador de Acesso](built-in-roles.md#user-access-administrator) ao Utilizador em Azure no âmbito raiz `/` ().Isto permite-lhe visualizar todos os recursos e atribuir acesso em qualquer grupo de subscrição ou gestão no diretório. As atribuições de funções de administrador de acesso ao utilizador podem ser removidas usando Azure PowerShell, Azure CLI ou a API REST.

Deve remover este acesso elevado depois de ter escoado as alterações necessárias para fazer no âmbito da raiz.

![Elevar o acesso](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Portal do Azure

### <a name="elevate-access-for-a-global-administrator"></a>Elevar o acesso a um administrador global

Siga estes passos para elevar o acesso a um Administrador Global utilizando o portal Azure.

1. Inscreva-se no [portal Azure](https://portal.azure.com) ou no [Centro de Administração Azure Ative](https://aad.portal.azure.com) Como Administrador Global.

    Se estiver a utilizar a Gestão de Identidade Privilegiada AZure AD, [ative a sua atribuição de função de Administrador Global](../active-directory/privileged-identity-management/pim-how-to-activate-role.md).

1. Abra **o Diretório Ativo Azure**.

1. Em **Gestão**, selecione **Propriedades**.

   ![Selecione Propriedades para propriedades do Azure Ative Directory - screenshot](./media/elevate-access-global-admin/azure-active-directory-properties.png)

1. Sob **a gestão de Acesso para recursos Azure,** definir o toggle para **Sim**.

   ![Gestão de acesso para recursos Azure - screenshot](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Quando define o toggle para **Sim,** é-lhe atribuída a função de Administrador de Acesso ao Utilizador em Azure RBAC no âmbito raiz (/). Isto permite-lhe atribuir funções em todas as subscrições e grupos de gestão Azure associados a este diretório AZure AD. Este toggle só está disponível para utilizadores a quem for atribuído o papel de Administrador Global em AZure AD.

   Quando define o toggle para **Nº,** a função de Administrador de Acesso ao Utilizador no RBAC Azure é removida da sua conta de utilizador. Já não é possível atribuir funções em todas as subscrições e grupos de gestão da Azure que estejam associados a este diretório AD Azure. Pode ver e gerir apenas as assinaturas e grupos de gestão Azure aos quais lhe foi concedido acesso.

    > [!NOTE]
    > Se estiver a utilizar a [Gestão de Identidade Privilegiada,](../active-directory/privileged-identity-management/pim-configure.md)desativar a sua função não altera a **gestão access for Azure** para alternar para **Nº**. Para manter o acesso menos privilegiado, recomendamos que desative este toggle para **O** antes de desativar a sua atribuição de funções.
    
1. Clique **em Guardar** para guardar a sua definição.

   Esta definição não é uma propriedade global e aplica-se apenas ao utilizador atualmente assinado. Não se pode elevar o acesso a todos os membros do papel de Administrador Global.

1. Inscreva-se e inscreva-se de volta para refrescar o seu acesso.

    Deve agora ter acesso a todas as subscrições e grupos de gestão do seu diretório. Quando visualizar o painel de controlo de acesso (IAM), irá notar que lhe foi atribuída a função de Administrador de Acesso ao Utilizador no âmbito raiz.

   ![Atribuições de funções de subscrição com âmbito de raiz - screenshot](./media/elevate-access-global-admin/iam-root.png)

1. Faça as alterações necessárias para fazer no acesso elevado.

    Para obter informações sobre a atribuição de funções, consulte [adicionar ou remover atribuições de funções Azure utilizando o portal Azure](role-assignments-portal.md). Se estiver a utilizar a Gestão de Identidade Privilegiada, consulte [os recursos do Discover Azure para gerir](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) ou atribuir [funções de recursos da Azure.](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md)

1. Execute os passos na secção seguinte para remover o seu acesso elevado.

### <a name="remove-elevated-access"></a>Remover acesso elevado

Para remover a atribuição de função de administrador de acesso ao utilizador no âmbito raiz `/` (), siga estes passos.

1. Inscreva-se como o mesmo utilizador que foi usado para elevar o acesso.

1. Na lista de navegação, clique em **Azure Ative Directory** e, em seguida, clique em **Propriedades**.

1. Desconfiem da **gestão de Acesso para os recursos Azure** para alternar de volta para **o Nº**. Uma vez que esta é uma definição por utilizador, deve ser assinado como o mesmo utilizador que foi usado para elevar o acesso.

    Se tentar remover a atribuição de função de administrador de acesso ao utilizador no painel de controlo de acesso (IAM), verá a seguinte mensagem. Para remover a atribuição de funções, deve redobrar o toggle de volta para **No** ou utilizar Azure PowerShell, Azure CLI ou a API REST.

    ![Remover atribuições de funções com âmbito de raiz](./media/elevate-access-global-admin/iam-root-remove.png)

1. Inscreva-se como Administrador Global.

    Se estiver a utilizar a Gestão de Identidade Privilegiada, desative a sua atribuição de função de Administrador Global.

    > [!NOTE]
    > Se estiver a utilizar a [Gestão de Identidade Privilegiada,](../active-directory/privileged-identity-management/pim-configure.md)desativar a sua função não altera a **gestão access for Azure** para alternar para **Nº**. Para manter o acesso menos privilegiado, recomendamos que desative este toggle para **O** antes de desativar a sua atribuição de funções.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-root-scope-"></a>Atribuição de funções de lista no âmbito raiz (/)

Para listar a atribuição de função de administrador de acesso ao utilizador para um utilizador no âmbito raiz `/` (), utilize o comando [Get-AzRoleAssignment.](/powershell/module/az.resources/get-azroleassignment)

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

### <a name="remove-elevated-access"></a>Remover acesso elevado

Para remover a atribuição de função do Administrador de Acesso ao Utilizador para si ou para outro utilizador no âmbito raiz `/` (), siga estes passos.

1. Inscreva-se como um utilizador que pode remover acesso elevado. Este pode ser o mesmo utilizador que foi usado para elevar o acesso ou outro Administrador Global com acesso elevado no âmbito raiz.

1. Utilize o comando [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) para remover a atribuição de função de administrador de acesso ao utilizador.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="azure-cli"></a>CLI do Azure

### <a name="elevate-access-for-a-global-administrator"></a>Elevar o acesso a um administrador global

Utilize os seguintes passos básicos para elevar o acesso a um Administrador Global utilizando o Azure CLI.

1. Utilize o comando [de repouso az](/cli/azure/reference-index?view=azure-cli-latest#az-rest) para ligar para o `elevateAccess` ponto final, o que lhe confere a função de Administrador de Acesso ao Utilizador no âmbito raiz `/` ().

    ```azurecli
    az rest --method post --url "/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01"
    ```

1. Faça as alterações necessárias para fazer no acesso elevado.

    Para obter informações sobre a atribuição de funções, consulte [adicionar ou remover atribuições de funções Azure utilizando o CLI Azure](role-assignments-cli.md).

1. Execute os passos numa secção posterior para remover o seu acesso elevado.

### <a name="list-role-assignment-at-root-scope-"></a>Atribuição de funções de lista no âmbito raiz (/)

Para listar a atribuição de funções do Administrador de Acesso ao Utilizador para um utilizador no âmbito raiz `/` (), utilize o comando [da lista de atribuições de funções az.](/cli/azure/role/assignment#az-role-assignment-list)

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

### <a name="remove-elevated-access"></a>Remover acesso elevado

Para remover a atribuição de função do Administrador de Acesso ao Utilizador para si ou para outro utilizador no âmbito raiz `/` (), siga estes passos.

1. Inscreva-se como um utilizador que pode remover acesso elevado. Este pode ser o mesmo utilizador que foi usado para elevar o acesso ou outro Administrador Global com acesso elevado no âmbito raiz.

1. Utilize o comando [de eliminação de funções az](/cli/azure/role/assignment#az-role-assignment-delete) para remover a atribuição de função de Administrador de Acesso ao Utilizador.

    ```azurecli
    az role assignment delete --assignee username@example.com --role "User Access Administrator" --scope "/"
    ```

## <a name="rest-api"></a>API REST

### <a name="elevate-access-for-a-global-administrator"></a>Elevar o acesso a um administrador global

Utilize os seguintes passos básicos para elevar o acesso a um Administrador Global utilizando a API REST.

1. Utilizando o REST, `elevateAccess` ligue, que lhe concede a função de Administrador de Acesso ao Utilizador no âmbito raiz `/` ().

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Faça as alterações necessárias para fazer no acesso elevado.

    Para obter informações sobre a atribuição de funções, consulte [Adicionar ou remover atribuições de funções Azure utilizando a API REST](role-assignments-rest.md).

1. Execute os passos numa secção posterior para remover o seu acesso elevado.

### <a name="list-role-assignments-at-root-scope-"></a>Atribuições de funções de lista no âmbito raiz (/)

Pode listar todas as atribuições de funções para um utilizador no âmbito raiz `/` ().

- Ligue [para o papel GETAsignments](/rest/api/authorization/roleassignments/listforscope) onde está o `{objectIdOfUser}` ID do objeto do utilizador cujas atribuições de funções pretende recuperar.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-root-scope-"></a>Lista negar atribuições no âmbito raiz (/)

Pode listar todas as atribuições de negação para um utilizador no âmbito raiz `/` ().

- Ligue para GET negar Assignments onde `{objectIdOfUser}` está o objeto ID do utilizador cujas atribuições de negação que pretende recuperar.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Remover acesso elevado

Quando `elevateAccess` ligar, crie uma atribuição de função para si mesmo, para revogar os privilégios necessários para remover a atribuição de função de Administrador de Acesso ao Utilizador para si mesmo no âmbito raiz `/` ().

1. Chamada [função GETDefinitions](/rest/api/authorization/roledefinitions/get) onde `roleName` é igual a Administrador de Acesso ao Utilizador para determinar o nome ID da função de Administrador de Acesso ao Utilizador.

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

    Guarde o ID do `name` parâmetro, neste `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9` caso.

1. Também precisa de listar a função para o administrador de diretório no âmbito do diretório. Lista todas as atribuições no âmbito do diretório para `principalId` o administrador do diretório que fez a chamada de acesso elevado. Isto listará todas as atribuições no diretório para o objectid.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
        
    >[!NOTE] 
    >Um administrador de diretório não deve ter muitas atribuições, se a consulta anterior devolver demasiadas atribuições, também pode consultar todas as atribuições apenas ao nível do diretório, e depois filtrar os resultados: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
            
1. As chamadas anteriores devolvem uma lista de atribuições de papéis. Encontre a atribuição de funções onde está o âmbito `"/"` e o fim com o `roleDefinitionId` iD de nome de função que encontrou no passo 1 e `principalId` corresponda ao objectId do administrador de diretório. 
    
    Atribuição de função de amostra:
    
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
    
    Mais uma vez, salve o ID do `name` parâmetro, neste caso 11111111-1111-1111-1111-1111-1111111111111111.

1. Finalmente, utilize o ID de atribuição de funções para remover a atribuição adicionada `elevateAccess` por:

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Passos seguintes

- [Compreender as diferentes funções](rbac-and-directory-admin-roles.md)
- [Utilizar a API REST para adicionar ou remover atribuições de funções do Azure](role-assignments-rest.md)
