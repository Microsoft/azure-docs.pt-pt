---
title: API da web - configuração de código de aplicação protegida | O Azure Active Directory
description: Saiba como criar uma API web protegida e configurar o código de seu aplicativo.
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
ms.openlocfilehash: 23ff03316a1f9409d4d6e4b7ddf52d0c8cc7a909
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551544"
---
# <a name="protected-web-api-adding-authorization-to-your-api"></a>API web protegida: Adicionar autorização à sua API

Este artigo descreve como adicionar autorização para a API web. Esta proteção garante que a API é chamada apenas pelo:

- Aplicações em nome dos utilizadores que têm os âmbitos certos.
- Aplicações daemon com as funções de aplicação correta.

Para proteger uma API web ASP.NET/ASP.NET Core, precisará adicionar o `[Authorize]` atributo em um dos seguintes:

- O próprio, se pretender que todas as ações do controlador a ser protegido de controlador
- A ação de controlador individuais para a sua API

```CSharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Mas esta proteção não é suficiente. Esta ação garante apenas que ASP.NET/ASP.NET Core irá validar o token. A API tem de verificar que o token utilizado para chamar o web que API foi pedida com as declarações ele espera, em particular:

- O *âmbitos*, se a API é chamada em nome de um utilizador.
- O *funções de aplicação*, se a API pode ser chamada a partir de uma aplicação de daemon.

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>Verificação dos âmbitos em APIs chamados em nome dos utilizadores

Se a sua API é chamado por um aplicativo do cliente em nome de um utilizador, tem de solicitar um token de portador que tenha específicos âmbitos para a API. (Consulte [configuração de código | Token de portador](scenario-protected-web-api-app-configuration.md#bearer-token).)

```CSharp
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

O `VerifyUserHasAnyAcceptedScope` método faria algo semelhante ao seguinte:

- Certifique-se de que existe uma afirmação com o nome `http://schemas.microsoft.com/identity/claims/scope` ou `scp`.
- Certifique-se de que a afirmação tenha um valor que contém o âmbito esperado pela API.

```CSharp
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

Este código de exemplo é para o ASP.NET Core. Para o ASP.NET, basta substituir `HttpContext.User` com `ClaimsPrincipal.Current`e substituir o tipo de afirmação `"http://schemas.microsoft.com/identity/claims/scope"` com `"scp"`. (Consulte também o trecho de código neste artigo.)

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>Verificação de aplicação de funções, em APIs chamadas por aplicações daemon

Se a API web é chamada por um [aplicação de daemon](scenario-daemon-overview.md), essa aplicação deve exigir uma permissão de aplicação para a API web. Já vimos [expor as permissões de aplicação (funções da aplicação)](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles) que a API expõe este tipo de permissões (por exemplo, o `access_as_application` função de aplicação).
Agora tem de ter as suas APIs, certifique-se de que contém o token que recebeu o `roles` afirmação e que essa declaração tem o valor que ele espera. O código de fazer essa verificação é semelhante ao código que verifica permissões delegadas, exceto que, em vez de testar para `scopes`, a ação de controlador testará para `roles`:

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

Este código de exemplo é para o ASP.NET. Para o ASP.NET Core, Acabei de substituir `ClaimsPrincipal.Current` com `HttpContext.User`e substitua o `"roles"` afirmação nome com `"http://schemas.microsoft.com/identity/claims/roles"`. (Consulte também o trecho de código no início deste artigo.)

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Aceitação de tokens de só de aplicação se a API web deve ser chamada apenas por aplicações daemon

O `roles` afirmação também é utilizada para os utilizadores em padrões de atribuição do utilizador. (Consulte [como: Adicionar funções de aplicação na sua aplicação e recebê-las no token](howto-add-app-roles-in-azure-ad-apps.md).) Portanto, apenas a verificação de funções permitirá aplicações iniciar sessão como utilizadores e o caminho inverso, se as funções são atribuídas a ambos. Recomendamos que declarar funções diferentes para utilizadores e aplicações evitar essa confusão.

Se quiser permitir que apenas as aplicações de daemon de chamar a API web, adicione uma condição, ao validar a função de aplicação, que o token é um token de só de aplicação:

```CSharp
string oid = ClaimsPrincipal.Current.FindFirst("oid");
string sub = ClaimsPrincipal.Current.FindFirst("sub");
bool isAppOnlyToken = oid == sub;
```

A verificação da condição inversa permitirá apenas as aplicações que iniciam sessão de um utilizador para chamar a API.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Mover para produção](scenario-protected-web-api-production.md)
