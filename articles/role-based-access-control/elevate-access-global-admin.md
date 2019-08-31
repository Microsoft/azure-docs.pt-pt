---
title: Elevar o acesso para gerenciar todas as assinaturas e grupos de gerenciamento do Azure | Microsoft Docs
description: Descreve como elevar o acesso de um administrador global para gerenciar todas as assinaturas e grupos de gerenciamento no Azure Active Directory usando o portal do Azure ou a API REST.
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
ms.openlocfilehash: 984284fa185d4d8454b1689a62ca9e08c342e33b
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195115"
---
# <a name="elevate-access-to-manage-all-azure-subscriptions-and-management-groups"></a>Elevar o acesso para gerenciar todas as assinaturas e grupos de gerenciamento do Azure

Como administrador global no Azure Active Directory (AD do Azure), talvez você não tenha acesso a todas as assinaturas e grupos de gerenciamento em seu diretório. Este artigo descreve as maneiras que você pode elevar seu acesso a todas as assinaturas e grupos de gerenciamento.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="why-would-you-need-to-elevate-your-access"></a>Por que você precisaria elevar seu acesso?

Se você for um administrador global, pode haver ocasiões em que você deseja fazer o seguinte:

- Obter acesso a uma assinatura do Azure ou grupo de gerenciamento quando um usuário tiver perdido o acesso
- Conceder a outro usuário ou a você mesmo acesso a uma assinatura do Azure ou a um grupo de gerenciamento
- Ver todas as assinaturas do Azure ou grupos de gerenciamento em uma organização
- Permitir que um aplicativo de automação (como um aplicativo de faturamento ou de auditoria) acesse todas as assinaturas do Azure ou grupos de gerenciamento

## <a name="how-does-elevate-access-work"></a>Como a elevação do acesso funciona?

Os recursos do Azure AD e do Azure são protegidos independentemente um do outro. Ou seja, as atribuições de função do Azure AD não concedem acesso aos recursos do Azure e as atribuições de função do Azure não concedem acesso ao Azure AD. No entanto, se você for um [administrador global](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) no Azure AD, você poderá atribuir a si mesmo acesso a todas as assinaturas e grupos de gerenciamento do Azure em seu diretório. Use essa funcionalidade se você não tiver acesso aos recursos de assinatura do Azure, como máquinas virtuais ou contas de armazenamento, e desejar usar o privilégio de administrador global para obter acesso a esses recursos.

Ao elevar seu acesso, você receberá a função de [administrador de acesso do usuário](built-in-roles.md#user-access-administrator) no Azure no escopo raiz (`/`). Isso permite que você exiba todos os recursos e atribua acesso em qualquer assinatura ou grupo de gerenciamento no diretório. As atribuições da função Administrador de acesso do usuário podem ser removidas usando o PowerShell.

Você deve remover esse acesso elevado após ter feito as alterações que precisa fazer no escopo raiz.

![Elevar o acesso](./media/elevate-access-global-admin/elevate-access.png)

## <a name="azure-portal"></a>Portal do Azure

Siga estas etapas para elevar o acesso para um administrador global usando o portal do Azure.

1. Entre no [portal do Azure](https://portal.azure.com) ou no centro de [Administração do Azure Active Directory](https://aad.portal.azure.com) como um administrador global.

1. Na lista de navegação, clique em **Azure Active Directory** e em **Propriedades**.

   ![Propriedades do Azure AD – captura de tela](./media/elevate-access-global-admin/aad-properties.png)

1. Em **Gerenciamento de acesso para recursos do Azure**, defina a alternância para **Sim**.

   ![Gerenciamento de acesso para recursos do Azure – captura de tela](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Quando você define a alternância para **Sim**, você recebe a função Administrador de acesso do usuário no RBAC do Azure no escopo raiz (/). Isso concede permissão para atribuir funções em todas as assinaturas do Azure e grupos de gerenciamento associados a este diretório do Azure AD. Essa alternância só está disponível para usuários que recebem a função de administrador global no Azure AD.

   Quando você define a alternância para **não**, a função Administrador de acesso do usuário no RBAC do Azure é removida da sua conta de usuário. Você não pode mais atribuir funções em todas as assinaturas do Azure e grupos de gerenciamento associados a este diretório do Azure AD. Você pode exibir e gerenciar somente as assinaturas do Azure e os grupos de gerenciamento aos quais você recebeu acesso.

1. Clique em **salvar** para salvar sua configuração.

   Essa configuração não é uma propriedade global e aplica-se somente ao usuário conectado no momento. Você não pode elevar o acesso para todos os membros da função de administrador global.

1. Saia e entre novamente para atualizar seu acesso.

    Agora você deve ter acesso a todas as assinaturas e grupos de gerenciamento em seu diretório. Você observará que recebeu a função Administrador de acesso do usuário no escopo raiz.

   ![Atribuições de função de assinatura com escopo raiz – captura de tela](./media/elevate-access-global-admin/iam-root.png)

1. Faça as alterações que você precisa fazer no acesso elevado.

    Para obter informações sobre como atribuir funções, consulte [gerenciar o acesso usando RBAC e o portal do Azure](role-assignments-portal.md). Se você estiver usando Azure AD Privileged Identity Management (PIM), consulte [descobrir recursos do Azure para gerenciar no PIM](../active-directory/privileged-identity-management/pim-resource-roles-discover-resources.md) ou [atribuir funções de recurso do Azure no PIM](../active-directory/privileged-identity-management/pim-resource-roles-assign-roles.md).

1. Quando terminar, defina a alternância de **Gerenciamento de acesso para recursos do Azure** de volta para **não**. Como essa é uma configuração por usuário, você deve estar conectado como o mesmo usuário que foi usado para elevar o acesso.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

### <a name="list-role-assignment-at-the-root-scope-"></a>Listar atribuição de função no escopo raiz (/)

Para listar a atribuição de função de administrador de acesso do usuário para um usuário`/`no escopo raiz (), use o comando [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) .

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

### <a name="remove-a-role-assignment-at-the-root-scope-"></a>Remover uma atribuição de função no escopo raiz (/)

Para remover uma atribuição de função de administrador de acesso de usuário para um usuário no`/`escopo raiz (), siga estas etapas.

1. Entre como um usuário que pode remover o acesso elevado. Esse pode ser o mesmo usuário que foi usado para elevar o acesso ou outro administrador global com acesso elevado no escopo raiz.


1. Use o comando [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) para remover a atribuição de função de administrador de acesso do usuário.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName <username@example.com> `
      -RoleDefinitionName "User Access Administrator" -Scope "/"
    ```

## <a name="rest-api"></a>API REST

### <a name="elevate-access-for-a-global-administrator"></a>Elevar o acesso para um administrador global

Use as etapas básicas a seguir para elevar o acesso para um administrador global usando a API REST.

1. Usando REST, chame `elevateAccess`, que concede a você a função de administrador de acesso do usuário no`/`escopo raiz ().

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Crie uma [atribuição de função](/rest/api/authorization/roleassignments) para atribuir qualquer função a qualquer escopo. O exemplo a seguir mostra as propriedades para atribuir a função {roleDefinitionID} no escopo raiz (`/`):

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

1. Enquanto um administrador de acesso de usuário, você também pode remover atribuições de função no`/`escopo raiz ().

1. Remova seus privilégios de administrador de acesso do usuário até que eles sejam necessários novamente.

### <a name="list-role-assignments-at-the-root-scope-"></a>Listar atribuições de função no escopo raiz (/)

Você pode listar todas as atribuições de função para um usuário no escopo raiz`/`().

- Chame [Get roleAssignments](/rest/api/authorization/roleassignments/listforscope) , `{objectIdOfUser}` em que é a ID de objeto do usuário cujas atribuições de função você deseja recuperar.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

### <a name="list-deny-assignments-at-the-root-scope-"></a>Listar atribuições de negação no escopo raiz (/)

Você pode listar todas as atribuições de negação para um usuário no escopo`/`raiz ().

- Chame Get denyAssignments, `{objectIdOfUser}` em que é a ID de objeto do usuário cujas atribuições de negação você deseja recuperar.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter=gdprExportPrincipalId+eq+'{objectIdOfUser}'
   ```

### <a name="remove-elevated-access"></a>Remover acesso elevado

Ao chamar `elevateAccess`, você cria uma atribuição de função para si mesmo, para revogar os privilégios necessários para remover a atribuição.

1. Chame [Get roleDefinitions](/rest/api/authorization/roledefinitions/get) , `roleName` em que é igual a administrador de acesso do usuário para determinar a ID de nome da função de administrador de acesso do usuário.

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

    Salve a ID do `name` parâmetro, nesse caso. `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`

2. Você também precisa listar a atribuição de função para o administrador de diretório no escopo do diretório. Listar todas as atribuições no escopo `principalId` do diretório para o do administrador de diretório que fez a chamada de elevação de acesso. Isso listará todas as atribuições no diretório para o ObjectID.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >Um administrador de diretório não deve ter muitas atribuições, se a consulta anterior retornar muitas atribuições, você também poderá consultar todas as atribuições apenas no nível de escopo do diretório e, em seguida, filtrar os resultados:`GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
   1. As chamadas anteriores retornam uma lista de atribuições de função. Localize a atribuição de função em que o `"/"` escopo é `roleDefinitionId` e termina com a ID de nome de função encontrada na etapa `principalId` 1 e corresponde à ObjectID do administrador de diretório. 
    
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
        
      Novamente, salve a ID do `name` parâmetro, neste caso, e7dd75bc-06f6-4e71-9014-ee96a929d099.

   1. Por fim, use a ID de atribuição de função para remover a `elevateAccess`atribuição adicionada por:

      ```http
      DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
      ```

## <a name="next-steps"></a>Passos seguintes

- [Compreender as diferentes funções no Azure](rbac-and-directory-admin-roles.md)
- [Gerenciar o acesso aos recursos do Azure usando o RBAC e a API REST](role-assignments-rest.md)
