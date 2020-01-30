---
title: Verifique os âmbitos e as funções de aplicação protegidas pela Web API  Azure
titleSuffix: Microsoft identity platform
description: Aprenda a construir uma API web protegida e configure o código da sua aplicação.
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
ms.openlocfilehash: 816a9620a3486b534f9293084b7c4f5b4f748033
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768121"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>API da web protegida: Verificar os âmbitos e as funções da aplicação

Este artigo descreve como pode adicionar autorização à sua Web API. Esta proteção garante que a API é chamada apenas por:

- Aplicações em nome de utilizadores que tenham os âmbitos certos.
- Aplicativos Daemon que têm as funções de aplicação certas.

> [!NOTE]
> Os fragmentos de código deste artigo são extraídos das seguintes amostras, que estão totalmente funcionais:
>
> - [tutoria incremental da Web API de ASP.NET](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) no GitHub
> - [ASP.NET amostra de API web](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

Para proteger uma ASP.NET ou ASP.NET API web Core, deve adicionar o atributo `[Authorize]` a um dos seguintes itens:

- O controlador em si se quiser proteger todas as ações do controlador
- A ação do controlador individual para a sua API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Mas esta proteção não é suficiente. Garante apenas que ASP.NET e ASP.NET Core validam o símbolo. A Sua API precisa de verificar se o símbolo usado para ligar para a API é solicitado com as reclamações esperadas. Estas alegações, nomeadamente, necessitam de verificação:

- Os *âmbitos* se a API for chamada em nome de um utilizador.
- As funções da *aplicação* se a API puder ser chamada a partir de uma aplicação daemon.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Verificar os âmbitos nas APIs solicitados em nome dos utilizadores

Se uma aplicação de cliente ligar para a sua API em nome de um utilizador, a API precisa de solicitar um token ao portador que tenha âmbitos específicos para a API. Para mais informações, consulte a [configuração do Código  Símbolo do portador.](scenario-protected-web-api-app-configuration.md#bearer-token)

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

O método `VerifyUserHasAnyAcceptedScope` faz algo como os seguintes passos:

- Verifique se há uma reclamação chamada `http://schemas.microsoft.com/identity/claims/scope` ou `scp`.
- Verifique se a reclamação tem um valor que contém o âmbito esperado pela API.

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

O [código de amostra](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) anterior destina-se a ASP.NET Core. Para ASP.NET, substitua `HttpContext.User` por `ClaimsPrincipal.Current`e substitua o tipo de reclamação `"http://schemas.microsoft.com/identity/claims/scope"` por `"scp"`. Consulte também o código mais tarde neste artigo.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Verifique as funções da aplicação em APIs chamadas por aplicações daemon

Se a sua Web API for chamada por uma [aplicação daemon,](scenario-daemon-overview.md)essa aplicação deve exigir uma permissão de aplicação para a sua Web API. Como mostrado na [Exposing application permissions (funções de aplicação)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles), a sua API expõe tais permissões. Um exemplo é o papel `access_as_application` aplicação.

Agora precisa de ter a sua API a verificar se o símbolo que recebe contém a reclamação `roles` e que esta alegação tem o valor esperado. O código de verificação é semelhante ao código que verifica as permissões delegadas, exceto que o seu controlador testa funções em vez de âmbitos:

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

O método `ValidateAppRole` pode ser algo assim:

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

Desta vez, o código é para ASP.NET. Para ASP.NET Core, substitua `ClaimsPrincipal.Current` por `HttpContext.User`e substitua o nome de reivindicação `"roles"` por `"http://schemas.microsoft.com/identity/claims/roles"`. Consulte também o código mais cedo neste artigo.

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Aceitando tokens apenas para aplicações se a Web API deve ser chamada apenas por aplicações daemon

Os utilizadores também podem utilizar as funções nos padrões de atribuição do utilizador, como mostra em [Como: Adicionar funções de aplicação na sua aplicação e recebê-las no símbolo](howto-add-app-roles-in-azure-ad-apps.md). Se as funções forem atribuídas a ambas as peças, a verificação de funções permitirá que as aplicações instem como utilizadores e utilizadores para iniciar o seu insto como apps. Recomendamos que declare diferentes funções para utilizadores e apps para evitar esta confusão.

Se quiser apenas aplicações daemon para ligar para a sua Web API, adicione a condição de que o token seja apenas um símbolo de aplicação quando valida a função da aplicação.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Verificar a condição inversa permite que apenas as aplicações que assinam num utilizador liguem para a sua API.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Mover para produção](scenario-protected-web-api-production.md)
