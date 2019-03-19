---
title: Elevar o acesso para gerir todas as subscrições do Azure e grupos de gestão | Documentos da Microsoft
description: Descreve como elevar o acesso de Administrador Global gerir todas as subscrições e grupos de gestão no Azure Active Directory com o portal do Azure ou a REST API.
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
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ede7037aabc85739ee47636f1390c15e0b0d1639
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58106326"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Elevar o acesso para gerir todas as subscrições do Azure e grupos de gestão

Como Administrador Global no Azure Active Directory (Azure AD), poderá não ter acesso a todas as subscrições e grupos de gestão no seu diretório. Este artigo descreve as formas que pode elevar o acesso a todas as subscrições e grupos de gestão.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Por que precisaria elevar o acesso?

Se for um Administrador Global, haverá ocasiões em que deseja fazer o seguinte:

- Recuperar o acesso a um grupo de gestão ou de subscrição do Azure quando um utilizador perdeu o acesso
- Conceder a outro utilizador ou por conta própria aceder a um grupo de gestão ou de subscrição do Azure
- Ver todas as subscrições do Azure ou grupos de gestão numa organização
- Permitir que uma aplicação de automatização (por exemplo, uma aplicação de faturação ou de auditoria) para aceder a todas as subscrições do Azure ou grupos de gestão

## <a name="how-does-elevate-access-work"></a>Como efetuar a elevação de trabalho de acesso?

O Azure AD e recursos do Azure estão protegidos de forma independente umas das outras. Ou seja, atribuições de funções do Azure AD não concedem acesso aos recursos do Azure e as atribuições de funções do Azure não conceda acesso ao Azure AD. No entanto, se for um [Administrador Global](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator) no Azure AD, pode atribuir por conta própria acesso a todas as subscrições do Azure e grupos de gestão no seu diretório. Utilize esta capacidade se não tiver acesso a recursos de subscrição do Azure, como máquinas virtuais ou contas de armazenamento, e desejar usar seu privilégio de Administrador Global para obter acesso a esses recursos.

Quando elevar o acesso, será atribuído a [administrador de acesso de utilizador](built-in-roles.md#user-access-administrator) função no Azure no âmbito da raiz (`/`). Isto permite-lhe ver todos os recursos e atribuir acesso em qualquer subscrição ou grupo de gestão no diretório. Atribuições de funções de administrador de acesso de utilizador podem ser removidas com o PowerShell.

Depois de fazer as alterações, que tem de certificar-se no âmbito da raiz, deve remover este acesso elevado.

![Elevar o acesso](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Portal do Azure

Siga estes passos para elevar o acesso de Administrador Global no portal do Azure.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) ou o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) como um Administrador Global.

1. Na lista de navegação, clique em **do Azure Active Directory** e, em seguida, clique em **propriedades**.

   ![Propriedades do AD do Azure - captura de ecrã](./media/elevate-access-global-admin/aad-properties.png)

1. Sob **Access management para recursos do Azure**, defina o seletor para **Sim**.

   ![Gestão de acesso para recursos do Azure - captura de ecrã](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Se definir o botão de alternar como **Sim**, é-lhe atribuída a função de administrador de acesso de utilizador no RBAC do Azure no âmbito da raiz (/). Isso lhe concede permissão para atribuir funções em todas as subscrições do Azure e grupos de gestão associados a este diretório do Azure AD. Este botão só está disponível para utilizadores que estão atribuídos a função de Administrador Global no Azure AD.

   Se definir o botão de alternar como **não**, a função de administrador de acesso de utilizador no Azure RBAC é removida da sua conta de utilizador. Já não pode atribuir funções em todas as subscrições do Azure e grupos de gestão que estão associados este diretório do Azure AD. Pode ver e gerir apenas as subscrições do Azure e os grupos de gestão ao qual lhe foi concedido acesso.

1. Clique em **guardar** para salvar sua configuração.

   Esta definição não é uma propriedade global e aplica-se apenas ao utilizador atualmente com sessão iniciado. Não é possível elevar o acesso para todos os membros da função de Administrador Global.

1. Termine sessão e inicie sessão novamente para atualizar o acesso do utilizador.

    Agora, deve ter acesso a todas as subscrições e grupos de gestão no seu diretório. Perceberá que lhe foi atribuída a função de administrador de acesso de utilizador no âmbito da raiz.

   ![Atribuições de funções de subscrição com âmbito de raiz - captura de ecrã](./media/elevate-access-global-admin/iam-root.png)

1. Faça as alterações que precisa fazer no acesso elevado.

    Para obter informações sobre a atribuição de funções, consulte [gerir o acesso com RBAC e o portal do Azure](role-assignments-portal.md). Se estiver a utilizar o Azure AD Privileged Identity Management (PIM), veja [recursos do Azure detetar para gerir no PIM](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) ou [funções de recursos do Azure atribuir no PIM](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md).

1. Quando tiver terminado, definir o **Access management para recursos do Azure** alternar de volta ao **não**. Uma vez que esta é uma definição de por utilizador, precisa estar conectado como o mesmo utilizador que foi utilizada para elevar o acesso.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-the-root-scope-"></a>Lista de atribuição de função no âmbito da raiz (/)

Para listar a atribuição de função de administrador de acesso de utilizador para um utilizador no âmbito da raiz (`/`), utilize o [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) comando.

```azurepowershell
Get-AzRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc
ObjectType         : User
CanDelegate        : False
```

### <a name="remove-a-role-assignment-at-the-root-scope-"></a>Remover uma atribuição de função no âmbito da raiz (/)

Para remover uma atribuição de função de administrador de acesso de utilizador para um utilizador no âmbito da raiz (`/`), siga estes passos.

1. Inicie sessão como um utilizador que pode remover o acesso elevado. Isso pode ser o mesmo utilizador que foi utilizado para elevar o acesso ou outro administrador Global com acesso elevado no âmbito da raiz.


1. Utilize o [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) comando para remover a atribuição de função de administrador de acesso do utilizador.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="rest-api"></a>API REST

### <a name="elevate-access-for-a-global-administrator"></a>Elevar o acesso de Administrador Global

Utilize os seguintes passos básicos para elevar o acesso de Administrador Global com a API REST.

1. Utilizar o REST, chamar `elevateAccess`, que concede a função de administrador de acesso de utilizador no âmbito da raiz (`/`).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Criar uma [atribuição de função](/rest/api/authorization/roleassignments) para atribuir qualquer função em qualquer âmbito. O exemplo seguinte mostra as propriedades para atribuir a função {roleDefinitionID} no âmbito da raiz (`/`):

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
   }
   ```

1. Enquanto administrador de acesso de utilizador, também pode remover atribuições de funções no âmbito da raiz (`/`).

1. Remova os privilégios de administrador de acesso de utilizador até que forem novamente necessários.

### <a name="list-role-assignments-at-the-root-scope-"></a>Lista de atribuições de funções no âmbito da raiz (/)

Pode listar todas as atribuições de funções para um utilizador no âmbito da raiz (`/`).

- Chamar [roleAssignments GET](/rest/api/authorization/roleassignments/listforscope) onde `{objectIdOfUser}` é o ID de objeto do utilizador cujas atribuições de função que pretende recuperar.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-the-root-scope-"></a>Lista negar atribuições no âmbito de raiz (/)

Pode listar todas as atribuições de negação para um utilizador no âmbito da raiz (`/`).

- Chame GET denyAssignments onde `{objectIdOfUser}` é o ID de objeto do utilizador cujo negar atribuições de que pretende recuperar.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Remover acesso elevado

Quando chama `elevateAccess`, crie uma atribuição de função para si, portanto, para revogar esses privilégios tem de remover a atribuição.

1. Chamar [roleDefinitions GET](/rest/api/authorization/roledefinitions/get) onde `roleName` é igual ao administrador de acesso de utilizador para determinar o ID de nome da função de administrador de acesso do utilizador.

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

    Guarde o ID do `name` parâmetro, neste caso `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`.

2. Também precisa de listar a atribuição de função para o administrador do diretório no âmbito do diretório. Listar todas as atribuições no âmbito de diretório para o `principalId` do administrador do diretório que efetuou o acesso de elevação chamar. Isso listará todas as atribuições no diretório para o objectid.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >Um administrador do diretório não deve ter muitas atribuições, se a consulta anterior devolve demasiados atribuições, também pode consultar para todas as atribuições de apenas ao nível do âmbito de diretório, em seguida, filtrar os resultados: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
   1. As chamadas anteriores retornam uma lista de atribuições de funções. Encontrar a atribuição de função em que é o âmbito `"/"` e o `roleDefinitionId` termina com o ID de nome de função indicada no passo 1 e `principalId` corresponde o objectId do administrador do diretório. 
    
      Atribuição de função de exemplo:

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
               "createdBy": "93ce6722-3638-4222-b582-78b75c5c6d65",
               "updatedBy": "93ce6722-3638-4222-b582-78b75c5c6d65"
             },
             "id": "/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
             "type": "Microsoft.Authorization/roleAssignments",
             "name": "e7dd75bc-06f6-4e71-9014-ee96a929d099"
           }
         ],
         "nextLink": null
       }
       ```
        
      Mais uma vez, guarde o ID do `name` parâmetro, neste caso e7dd75bc-06f6-4e71-9014-ee96a929d099.

   1. Por último, utilize o ID de atribuição de função para remover a atribuição adicionada pelo `elevateAccess`:

      ```http
      DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
      ```

## <a name="next-steps"></a>Passos Seguintes

- [Compreender as diferentes funções no Azure](rbac-and-directory-admin-roles.md)
- [Gerir o acesso aos recursos do Azure através do RBAC e a API REST](role-assignments-rest.md)
