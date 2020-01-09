---
title: Verificar escopos & a API Web protegida de funções de aplicativo | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar uma API Web protegida e configurar o código do aplicativo.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eb9cdf68bf5103776d50db28e9e6facc89c9278
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423694"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>API Web protegida: verificar escopos e funções de aplicativo

Este artigo descreve como você pode adicionar autorização à sua API Web. Essa proteção garante que a API seja chamada apenas por:

- Aplicativos em nome dos usuários que têm os escopos corretos.
- Aplicativos de daemon que têm as funções de aplicativo corretas.

> [!NOTE]
> Os trechos de código deste artigo são extraídos dos exemplos a seguir, que são totalmente funcionais
>
> - [Tutorial incremental da API Web do ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) no github
> - [Exemplo de ASP.NET Web API](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

Para proteger uma API Web do ASP.NET/ASP.NET Core, você precisará adicionar o atributo `[Authorize]` em um destes:

- O próprio controlador, se você quiser que todas as ações do controlador sejam protegidas
- A ação do controlador individual para sua API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Mas essa proteção não é suficiente. Ele garante apenas que o ASP.NET/ASP.NET Core validará o token. Sua API precisa verificar se o token usado para chamar sua API Web foi solicitado com as declarações esperadas, em particular:

- Os *escopos*, se a API for chamada em nome de um usuário.
- As *funções de aplicativo*, se a API puder ser chamada de um aplicativo daemon.

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>Verificando escopos em APIs chamadas em nome dos usuários

Se sua API for chamada por um aplicativo cliente em nome de um usuário, precisará solicitar um token de portador que tenha escopos específicos para a API. (Consulte [configuração de código | Token de portador](scenario-protected-web-api-app-configuration.md#bearer-token).)

```csharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result.
        ...
    }
...
}
```

O método `VerifyUserHasAnyAcceptedScope` faria algo semelhante ao seguinte:

- Verifique se há uma declaração chamada `http://schemas.microsoft.com/identity/claims/scope` ou `scp`.
- Verifique se a declaração tem um valor que contém o escopo esperado pela API.

```csharp
    /// <summary>
    /// When applied to a <see cref="HttpContext"/>, verifies that the user authenticated in the 
    /// web API has any of the accepted scopes.
    /// If the authenticated user doesn't have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized error with a message noting which scopes are expected in the token.
    /// </summary>
    /// <param name="acceptedScopes">Scopes accepted by this API</param>
    /// <exception cref="HttpRequestException"/> with a <see cref="HttpResponse.StatusCode"/> set to 
    /// <see cref="HttpStatusCode.Unauthorized"/>
    public static void VerifyUserHasAnyAcceptedScope(this HttpContext context,
                                                     params string[] acceptedScopes)
    {
        if (acceptedScopes == null)
        {
            throw new ArgumentNullException(nameof(acceptedScopes));
        }
        Claim scopeClaim = HttpContext?.User
                                      ?.FindFirst("http://schemas.microsoft.com/identity/claims/scope");
        if (scopeClaim == null || !scopeClaim.Value.Split(' ').Intersect(acceptedScopes).Any())
        {
            context.Response.StatusCode = (int)HttpStatusCode.Unauthorized;
            string message = $"The 'scope' claim does not contain scopes '{string.Join(",", acceptedScopes)}' or was not found";
            throw new HttpRequestException(message);
        }
    }
```

Este [código de exemplo](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) é para ASP.NET Core. Para ASP.NET, basta substituir `HttpContext.User` por `ClaimsPrincipal.Current`e substituir o tipo de declaração `"http://schemas.microsoft.com/identity/claims/scope"` por `"scp"`. (Consulte também o trecho de código mais adiante neste artigo.)

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>Verificando funções de aplicativo em APIs chamadas por aplicativos de daemon

Se sua API Web for chamada por um [aplicativo de daemon](scenario-daemon-overview.md), esse aplicativo deverá exigir uma permissão de aplicativo para sua API da Web. Vimos na exposição de [permissões de aplicativo (funções de aplicativo)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles) que sua API expõe essas permissões (por exemplo, a função de aplicativo `access_as_application`).
Agora você precisa fazer com que suas APIs verifiquem se o token recebido contém a declaração de `roles` e que essa declaração tem o valor esperado. O código que faz essa verificação é semelhante ao código que verifica as permissões delegadas, exceto que, em vez de testar para `scopes`, a ação do controlador testará `roles`:

```csharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

O método `ValidateAppRole()` pode ser algo assim:

```csharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage 
        { StatusCode = HttpStatusCode.Unauthorized, 
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found" 
        });
    }
}
}
```

Desta vez, o trecho de código é para ASP.NET. Para ASP.NET Core, basta substituir `ClaimsPrincipal.Current` por `HttpContext.User`e substituir o nome da declaração de `"roles"` por `"http://schemas.microsoft.com/identity/claims/roles"`. (Consulte também o trecho de código anteriormente neste artigo.)

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Aceitando tokens somente de aplicativo se a API Web deve ser chamada somente por aplicativos de daemon

A declaração de `roles` também é usada para usuários em padrões de atribuição de usuário. (Consulte [como: adicionar funções de aplicativo em seu aplicativo e recebê-las no token](howto-add-app-roles-in-azure-ad-apps.md).) Portanto, apenas a verificação das funções permitirá que os aplicativos entrem como usuários e o contrário, se as funções forem atribuíveis a ambos. Recomendamos que você declare diferentes funções para usuários e aplicativos para evitar essa confusão.

Se você quiser permitir que somente aplicativos de daemon Chamem sua API da Web, adicione uma condição, ao validar a função de aplicativo, que o token é um token somente de aplicativo:

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

A verificação da condição inversa permitirá apenas aplicativos que se conectam a um usuário para chamar sua API.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Mover para produção](scenario-protected-web-api-production.md)
