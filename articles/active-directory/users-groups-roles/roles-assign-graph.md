---
title: Atribuir funções de administração Azure AD com a Microsoft Graph API Microsoft Docs
description: Atribuir e remover funções de administrador da AD Azure com API de gráfico no Diretório Ativo Azure
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 04/29/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44299a55424f9b0338ee49d2742aeedf16db22e8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84732094"
---
# <a name="assign-custom-admin-roles-using-the-microsoft-graph-api-in-azure-active-directory"></a>Atribuir funções de administração personalizadas utilizando a API do Gráfico microsoft em Azure Ative Directory 

Pode automatizar a forma como atribui funções a contas de utilizador utilizando a API do Microsoft Graph. Este artigo abrange as operações post, GET e DELETE sobre funções Assignments.

## <a name="required-permissions"></a>Permissões obrigatórias

Conecte-se à sua organização Azure AD usando uma conta de administrador global ou administrador de identidade privilegiada para atribuir ou remover funções.

## <a name="post-operations-on-roleassignment"></a>Operações POST sobre RoleAssignment

HTTP solicitação para criar uma atribuição de papel entre um utilizador e uma definição de função.

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
    "resourceScopes":"/"
}
```

Resposta

``` HTTP
HTTP/1.1 201 Created
```

HTTP solicitação para criar uma atribuição de função onde a definição principal ou de função não existe

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Corpo

``` HTTP
{
    "principalId":" 2142743c-a5b3-4983-8486-4532ccba12869",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":"/"
}
```

Resposta

``` HTTP
HTTP/1.1 404 Not Found
```

HTTP solicita a criação de uma atribuição de papel de âmbito único de recursos numa definição de função incorporada.

> [!NOTE] 
> As funções incorporadas têm hoje uma limitação em que podem ser apenas a nível da organização ou do âmbito "/AU/*". A deteção de recursos únicos não funciona para funções incorporadas, mas funciona para funções personalizadas.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Corpo

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "resourceScopes":"/ab2e1023-bddc-4038-9ac1-ad4843e7e539"
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
            "lang":"en",
            "value":"Provided authorization scope is not supported for built-in role definitions."},
            "values":
            [
                {
                    "item":"scope",
                    "value":"/ab2e1023-bddc-4038-9ac1-ad4843e7e539"
                }
            ]
        }
    }
}
```

## <a name="get-operations-on-roleassignment"></a>Get Operações no RoleAssignment

HTTP solicita para obter uma atribuição de papel para um dado diretor

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=principalId eq ‘<object-id-of-principal>’
```

Resposta

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "resourceScopes":"/"
} ,
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":"/"
}
```

HTTP solicita para obter uma atribuição de papel para uma determinada definição de função.

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Resposta

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":"/"
}
```

HTTP solicita para obter uma atribuição de papel por ID.

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
    "resourceScopes":"/"
}
```

## <a name="delete-operations-on-roleassignment"></a>EXCLUIR Operações sobre a assinatura de roleAssignment

HTTP solicita para eliminar uma atribuição de funções entre um utilizador e uma definição de função.

DELETE

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Resposta
``` HTTP
HTTP/1.1 204 No Content
```

HTTP solicita para apagar uma atribuição de função que já não existe

DELETE

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Resposta

``` HTTP
HTTP/1.1 404 Not Found
```

HTTP solicita para apagar uma atribuição de papel entre definição de função auto e incorporada

DELETE

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
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
            "value":"Cannot remove self from built-in role definitions."},
            "values":null
        }
    }
}
```

## <a name="next-steps"></a>Próximos passos

* Sinta-se livre para compartilhar conosco no fórum de [funções administrativas Azure AD.](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
* Para obter mais informações sobre funções e atribuição de funções de Administrador, consulte [as funções de administrador de atribuição](directory-assign-admin-roles.md).
* Para obter permissões de utilizador predefinidos, consulte uma [comparação entre as permissões de utilizador e de hóspedes predefinidos](../fundamentals/users-default-permissions.md).
