---
title: API da web - configuração de código de aplicação protegida | O Azure Active Directory
description: Saiba como criar uma API Web protegida e configurar o código de seu aplicativo.
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
ms.openlocfilehash: b700825be9a7fe23fe4b50a2d69d4de71f7dc038
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116457"
---
# <a name="protected-web-api---adding-authorization-to-your-api"></a>API - adicionar autorização à sua API web protegida

Este artigo descreve como adicionar autorização à sua API Web. Esta proteção garante que apenas é chamado por:

- aplicações em nome dos utilizadores com os âmbitos certos 
- ou por aplicações daemon com as funções de aplicação correta.

Para um ASP.NET / ASP.NET Core API Web para ser protegido, terá de adicionar o `[Authorize]` atributo:

- o próprio se quiser que todas as ações do controlador a ser protegido de controlador
- ou a ação de controlador individuais para a sua API.

```CSharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Mas esta proteção não é suficiente. Ele apenas guaranties que o ASP.NET / ASP.NET Core irá validar o token. A API tem de verificar que o token utilizado para chamar a API Web foi pedido com as declarações de espera, em particular:

- o **âmbitos** se a API é chamada em nome de um utilizador
- o **funções de aplicação** se a API pode ser chamada a partir de uma aplicação de daemon.

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>Verificação dos âmbitos em APIs chamados em nome dos utilizadores

Se a sua API é chamado por uma aplicação de cliente em nome de um utilizador, então ela precisa para solicitar um token de portador com âmbitos específicos para a API (consulte [configuração de código | Token de portador](scenario-protected-web-api-app-configuration.md#bearer-token))

```CSharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The Web API will only accept tokens 1) for users, 2) having the `access_as_user` scope for
    /// this API
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result
        ...
    }
...
}
```

O `VerifyUserHasAnyAcceptedScope` método faria algo semelhante ao seguinte:

- Certifique-se de que existe um afirmações com o nome `http://schemas.microsoft.com/identity/claims/scope` ou `scp`
- Certifique-se de que a afirmação tem um valor que contém o âmbito esperado pela API.

```CSharp
    /// <summary>
    /// When applied to an <see cref="HttpContext"/>, verifies that the user authenticated in the 
    /// Web API has any of the accepted scopes.
    /// If the authenticated user does not have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized with the message telling which scopes are expected in the token
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

Este código de exemplo é para o ASP.NET Core. Para substituir apenas do ASP.NET `HttpContext.User` pela `ClaimsPrincipal.Current`e o tipo de afirmação `"http://schemas.microsoft.com/identity/claims/scope"` por `"scp"` (Veja também o trecho de código abaixo)

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>Verificação de aplicação de funções, em APIs chamadas por aplicações daemon

Se a sua API Web é chamada por um [aplicação de Daemon](scenario-daemon-overview.md), em seguida, esse aplicativo deve exigir uma permissão de aplicação para a API Web. Já vimos [scenario-protected-web-api-app-registration.md#how-to-expose-application-permissions--app-roles-] que a API expõe este tipo de permissões (por exemplo, como o `access_as_application` função de aplicação).
Agora tem de ter as suas APIs, certifique-se de que contém o token que recebeu o `roles` afirmações e que essa declaração tem o valor que ele espera. O código de fazer essa verificação é semelhante ao código que verifica permissões delegadas, exceto que, em vez de testar para `scopes`, a ação de controlador testará para `roles`:

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

O `ValidateAppRole()` método pode ser algo semelhante a isto:

```CSharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case we look for a `role` value of `access_as_application`
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

Este código de exemplo é para o ASP.NET. Para o ASP.NET Core, Acabei de substituir `ClaimsPrincipal.Current` pela `HttpContext.User` e o `"roles"` afirmação nome por `"http://schemas.microsoft.com/identity/claims/roles"` (Veja também o trecho de código acima)

### <a name="accepting-app-only-tokens-if-the-web-api-should-only-be-called-by-daemon-apps"></a>Aceitar tokens de aplicação apenas se a API Web só deve ser chamada por aplicações daemon

O `roles` afirmação também é utilizada para os utilizadores em padrões de atribuição de utilizador (consulte [como: Adicionar funções de aplicação na sua aplicação e recebê-las no token](howto-add-app-roles-in-azure-ad-apps.md)). Portanto, apenas a verificação de funções permitirá aplicações iniciar sessão como utilizadores e o caminho inverso, se as funções são atribuídas a ambos. Recomendamos ter diferentes funções declaradas para utilizadores e aplicações evitar essa confusão.

Se quiser apenas permitir que os aplicativos de daemon de chamar a API Web, poderá ser útil adicionar uma condição, ao validar a função de aplicação, que o token é um token de só de aplicação:

```CSharp
string oid = ClaimsPrincipal.Current.FindFirst("oid");
string sub = ClaimsPrincipal.Current.FindFirst("sub");
bool isAppOnlyToken = oid == sub;
```

A verificação da condição inversa permitirá apenas as aplicações que iniciam sessão de um utilizador, para chamar a API.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Mover para produção](scenario-protected-web-api-production.md)
