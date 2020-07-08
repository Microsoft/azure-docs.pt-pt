---
title: Verifique os âmbitos e as funções de aplicações protegidas da API web Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma API web protegida e configurar o código da sua aplicação.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: a4ee2679da5065ab9e9b02d4ddb313fab75e78f7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83845140"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>API web protegida: Verificar âmbitos e funções de aplicativo

Este artigo descreve como pode adicionar autorização à sua API web. Esta proteção garante que a API é chamada apenas por:

- Aplicações em nome de utilizadores que tenham as miras adequadas.
- Aplicações Daemon que têm as funções de aplicação certas.

> [!NOTE]
> Os fragmentos de código deste artigo são extraídos das seguintes amostras, que estão totalmente funcionais:
>
> - [tutorial incremental da API web ASP.NET core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs#L37) no GitHub
> - [ASP.NET amostra web de API](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/dfd0115533d5a230baff6a3259c76cf117568bd9/TodoListService/Controllers/TodoListController.cs#L48)

Para proteger uma API web ASP.NET ou ASP.NET Core, deve adicionar o `[Authorize]` atributo a um dos seguintes itens:

- O controlador em si se quiser que todas as ações do controlador sejam protegidas
- A ação do controlador individual para a sua API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Mas esta proteção não é suficiente. Garante apenas que ASP.NET e ASP.NET Core validam o token. A sua API precisa de verificar se o token usado para ligar para a API é solicitado com as reclamações esperadas. Estas alegações, em especial, necessitam de ser verificadas:

- Os *âmbitos* se a API for chamada em nome de um utilizador.
- As funções da *aplicação* se a API puder ser chamada a partir de uma aplicação da daemon.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Verificar âmbitos em APIs chamados em nome dos utilizadores

Se uma aplicação de cliente ligar para a sua API em nome de um utilizador, a API precisa de solicitar um token ao portador que tenha âmbitos específicos para a API. Para mais informações, consulte [a configuração do Código / Ficha do portador.](scenario-protected-web-api-app-configuration.md#bearer-token)

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

O `VerifyUserHasAnyAcceptedScope` método faz algo como os seguintes passos:

- Verifique se há uma reclamação chamada `http://schemas.microsoft.com/identity/claims/scope` ou `scp` .
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

O código de [amostra](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/02352945c1c4abb895f0b700053506dcde7ed04a/Microsoft.Identity.Web/Resource/ScopesRequiredByWebAPIExtension.cs#L47) anterior é para ASP.NET Core. Para ASP.NET, basta substituir `HttpContext.User` por , e substituir o tipo de `ClaimsPrincipal.Current` reclamação por `"http://schemas.microsoft.com/identity/claims/scope"` `"scp"` . Consulte também o código mais tarde neste artigo.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Verifique as funções de aplicações em APIs chamadas por apps daemon

Se a sua API web for chamada por uma [aplicação daemon,](scenario-daemon-overview.md)essa aplicação deve exigir uma permissão de aplicação para a sua API web. Como mostrado na [Exposing permissões de aplicações (funções de aplicação)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles), a sua API expõe tais permissões. Um exemplo é o papel da `access_as_application` aplicação.

Precisa agora que a sua API verifique se o token que recebe contém a `roles` reclamação e que esta reclamação tem o valor esperado. O código de verificação é semelhante ao código que verifica as permissões delegadas, exceto que o seu controlador testa as funções em vez de âmbitos:

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

O `ValidateAppRole` método pode ser algo assim:

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

Desta vez, o código é para ASP.NET. Para ASP.NET Core, basta substituir `ClaimsPrincipal.Current` por , e substituir o nome de `HttpContext.User` `"roles"` reclamação por `"http://schemas.microsoft.com/ws/2008/06/identity/claims/role"` . Consulte também o código snippet mais cedo neste artigo.

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Aceitando fichas só de aplicativos se a API web deve ser chamada apenas por apps daemon

Os utilizadores também podem utilizar as alegações de funções nos padrões de atribuição do utilizador, como mostrado em [Como: Adicionar funções de aplicação na sua aplicação e recebê-las no token](howto-add-app-roles-in-azure-ad-apps.md). Se as funções forem atribuíveis a ambas, as funções de verificação permitirão que as aplicações entrem como utilizadores e utilizadores a iniciarem sessão como apps. Recomendamos que declare diferentes papéis para utilizadores e apps para evitar esta confusão.

Se quiser apenas que as aplicações daemon liguem para a sua API web, adicione a condição de que o token seja um símbolo apenas de aplicações quando validar o papel da app.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

A verificação da condição inversa permite apenas aplicações que assinam num utilizador para ligar para a sua API.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Mover-se para a produção](scenario-protected-web-api-production.md)
