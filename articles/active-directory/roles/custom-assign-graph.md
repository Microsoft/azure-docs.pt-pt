---
title: Atribua funções de administração Azure AD com a Microsoft Graph API | Microsoft Docs
description: Atribuir e remover funções de administrador da AD Azure com API de gráfico no Diretório Ativo Azure
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67e77c2dd08386279beeb06ff9081fa2669afa73
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487839"
---
# <a name="assign-custom-admin-roles-using-the-microsoft-graph-api-in-azure-active-directory"></a>Atribuir funções de administração personalizadas utilizando a API do Gráfico microsoft em Azure Ative Directory 

Pode automatizar a forma como atribui funções a contas de utilizador utilizando a API do Microsoft Graph. Este artigo abrange as operações post, GET e DELETE sobre funções Assignments.

## <a name="required-permissions"></a>Permissões obrigatórias

Ligue-se à sua organização Azure AD utilizando um administrador global ou conta de administrador de funções privilegiada para atribuir ou remover funções.

## <a name="post-operations-on-roleassignment"></a>Operações POST sobre RoleAssignment

### <a name="example-1-create-a-role-assignment-between-a-user-and-a-role-definition"></a>Exemplo 1: Criar uma atribuição de funções entre um utilizador e uma definição de função

POST

``` HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
Content-type: application/json
```

Corpo

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "directoryScopeId":"/"  // Don't use "resourceScope" attribute in Azure AD role assignments. It will be deprecated soon.
}
```

Resposta

``` HTTP
HTTP/1.1 201 Created
```

### <a name="example-2-create-a-role-assignment-where-the-principal-or-role-definition-does-not-exist"></a>Exemplo 2: Criar uma atribuição de funções em que a definição principal ou de função não exista

POST

``` HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Corpo

``` HTTP
{
    "principalId":" 2142743c-a5b3-4983-8486-4532ccba12869",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "directoryScopeId":"/"  //Don't use "resourceScope" attribute in Azure AD role assignments. It will be deprecated soon.
}
```

Resposta

``` HTTP
HTTP/1.1 404 Not Found
```
### <a name="example-3-create-a-role-assignment-on-a-single-resource-scope"></a>Exemplo 3: Criar uma atribuição de funções num único âmbito de recursos

POST

``` HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Corpo

``` HTTP
{
    "principalId":" 2142743c-a5b3-4983-8486-4532ccba12869",
    "roleDefinitionId":"e9b2b976-1dea-4229-a078-b08abd6c4f84",    //role template ID of a custom role
    "directoryScopeId":"/13ff0c50-18e7-4071-8b52-a6f08e17c8cc"  //object ID of an application
}
```

Resposta

``` HTTP
HTTP/1.1 201 Created
```

### <a name="example-4-create-an-administrative-unit-scoped-role-assignment-on-a-built-in-role-definition-which-is-not-supported"></a>Exemplo 4: Criar uma unidade administrativa de função de âmbito de atribuição de funções numa definição de função incorporada que não seja suportada

POST

``` HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Corpo

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"29232cdf-9323-42fd-ade2-1d097af3e4de",    //role template ID of Exchange Administrator
    "directoryScopeId":"/administrativeUnits/13ff0c50-18e7-4071-8b52-a6f08e17c8cc"    //object ID of an administrative unit
}
```

Resposta

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "message":"The given built-in role is not supported to be assigned to a single resource scope."
        }
    }
}
```

Apenas um subconjunto de funções incorporadas está habilitado para a deteção da Unidade Administrativa. Consulte [esta documentação](admin-units-assign-roles.md) para a lista de funções incorporadas apoiadas sobre uma unidade administrativa.

## <a name="get-operations-on-roleassignment"></a>Get Operações no RoleAssignment

### <a name="example-5-get-role-assignments-for-a-given-principal"></a>Exemplo 5: Obtenha atribuições de funções para um dado diretor

GET

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=principalId+eq+'<object-id-of-principal>'
```

Resposta

``` HTTP
HTTP/1.1 200 OK
{
"value":[
            { 
                "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
                "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
                "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
                "directoryScopeId":"/"  
            } ,
            {
                "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
                "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
                "roleDefinitionId":"fe930be7-5e62-47db-91af-98c3a49a38b1",
                "directoryScopeId":"/"
            }
        ]
}
```

### <a name="example-6-get-role-assignments-for-a-given-role-definition"></a>Exemplo 6: Obtenha atribuições de funções para uma determinada definição de função.

GET

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=roleDefinitionId+eq+'<object-id-or-template-id-of-role-definition>'
```

Resposta

``` HTTP
HTTP/1.1 200 OK
{
"value":[
            {
                "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
                "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
                "roleDefinitionId":"fe930be7-5e62-47db-91af-98c3a49a38b1",
                "directoryScopeId":"/"
            }
     ]
}
```

### <a name="example-7-get-a-role-assignment-by-id"></a>Exemplo 7: Obter uma atribuição de papel por ID.

GET

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Resposta

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1",
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "directoryScopeId":"/"
}
```

### <a name="example-8-get-role-assignments-for-a-given-scope"></a>Exemplo 8: Obtenha atribuições de funções para um determinado âmbito


GET

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=directoryScopeId+eq+'/d23998b1-8853-4c87-b95f-be97d6c6b610'
```

Resposta

``` HTTP
HTTP/1.1 200 OK
{
"value":[
            { 
                "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
                "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
                "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
                "directoryScopeId":"/d23998b1-8853-4c87-b95f-be97d6c6b610"
            } ,
            {
                "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
                "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
                "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
                "directoryScopeId":"/d23998b1-8853-4c87-b95f-be97d6c6b610"
            }
        ]
}
```

## <a name="delete-operations-on-roleassignment"></a>EXCLUIR Operações sobre a assinatura de roleAssignment

### <a name="example-9-delete-a-role-assignment-between-a-user-and-a-role-definition"></a>Exemplo 9: Eliminar uma atribuição de funções entre um utilizador e uma definição de função.

DELETE

``` HTTP
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Resposta
``` HTTP
HTTP/1.1 204 No Content
```

### <a name="example-10-delete-a-role-assignment-that-no-longer-exists"></a>Exemplo 10: Eliminar uma atribuição de funções que já não existe

DELETE

``` HTTP
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Resposta

``` HTTP
HTTP/1.1 404 Not Found
```

### <a name="example-11-delete-a-role-assignment-between-self-and-global-administrator-role-definition"></a>Exemplo 11: Eliminar uma atribuição de funções entre definição de função de auto-administrador e administrador global

DELETE

``` HTTP
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Resposta

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "lang":"en",
            "value":"Removing self from Global Administrator built-in role is not allowed"},
            "values":null
        }
    }
}
```

Evitamos que os utilizadores apaguem o seu próprio papel de Administrador Global para evitar um cenário em que um inquilino tenha zero Administradores Globais. É permitido remover outras funções atribuídas a si mesmo.

## <a name="next-steps"></a>Passos seguintes

* Sinta-se livre para compartilhar conosco no fórum de [funções administrativas Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
* Para obter mais informações sobre funções e atribuição de funções de administrador, consulte [atribuições de funções de administrador](permissions-reference.md)
* Para obter permissões de utilizador predefinidos, consulte uma [comparação entre permissões de hóspedes e utilizadores de membros predefinidos](../fundamentals/users-default-permissions.md)