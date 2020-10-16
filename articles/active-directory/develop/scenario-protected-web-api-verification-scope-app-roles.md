---
title: Verifique os âmbitos e as funções de aplicações protegidas da API web Rio Azure
titleSuffix: Microsoft identity platform
description: Verifique se a API só é chamada por aplicações em nome de utilizadores que tenham os âmbitos adequados e por aplicações daemon que tenham as funções de aplicação certas.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1e5c8b788f1abbfbd46c4dfe6c7bb9d87adcee86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91257271"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>API web protegida: Verificar âmbitos e funções de aplicativo

Este artigo descreve como pode adicionar autorização à sua API web. Esta proteção garante que a API é chamada apenas por:

- Aplicações em nome de utilizadores que tenham as miras adequadas.
- Aplicações Daemon que têm as funções de aplicação certas.

> [!NOTE]
> Os fragmentos de código deste artigo são extraídos das seguintes amostras de código no GitHub:
>
> - [tutorial incremental da API web ASP.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs)
> - [ASP.NET amostra web de API](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/master/TodoListService/Controllers/TodoListController.cs)

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

### <a name="net-core"></a>.NET Core

#### <a name="verify-the-scopes-on-each-controller-action"></a>Verifique os âmbitos de cada ação do controlador

```csharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
         HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);
        // Do the work and return the result.
        // ...
    }
...
}
```

O `VerifyUserHasAnyAcceptedScope` método faz algo como os seguintes passos:

- Verifique se há uma reclamação chamada `http://schemas.microsoft.com/identity/claims/scope` ou `scp` .
- Verifique se a reclamação tem um valor que contém o âmbito esperado pela API.


#### <a name="verify-the-scopes-more-globally"></a>Verifique os âmbitos mais globalmente

Definir os âmbitos granulares para a sua API web e verificar os âmbitos de cada ação do controlador é a abordagem recomendada. No entanto, também é possível verificar os âmbitos ao nível da aplicação ou de um controlador utilizando ASP.NET Core. Para mais [informações,](/aspnet/core/security/authorization/claims) consulte a autorização baseada em reclamações na documentação principal ASP.NET.

### <a name="net-mvc"></a>.NET MVC

Para ASP.NET, basta substituir `HttpContext.User` por , e substituir o tipo de `ClaimsPrincipal.Current` reclamação por `"http://schemas.microsoft.com/identity/claims/scope"` `"scp"` . Consulte também o código mais tarde neste artigo.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Verifique as funções de aplicações em APIs chamadas por apps daemon

Se a sua API web for chamada por uma [aplicação daemon,](scenario-daemon-overview.md)essa aplicação deve exigir uma permissão de aplicação para a sua API web. Como mostrado na [Exposing permissões de aplicações (funções de aplicação)](./scenario-protected-web-api-app-registration.md#exposing-application-permissions-app-roles), a sua API expõe tais permissões. Um exemplo é o papel da `access_as_application` aplicação.

Precisa agora que a sua API verifique se o token que recebe contém a `roles` reclamação e que esta reclamação tem o valor esperado. O código de verificação é semelhante ao código que verifica as permissões delegadas, exceto que o seu controlador testa as funções em vez de âmbitos:

### <a name="aspnet-core"></a>Núcleo de ASP.NET

```csharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        HttpContext.ValidateAppRole("access_as_application");
        ...
    }
```

O `ValidateAppRole` método é definido em Microsoft.Identity.Web em [RolesRequiredHttpContextExtensions.cs](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/Resource/RolesRequiredHttpContextExtensions.cs#L28).

### <a name="aspnet-mvc"></a>ASP.NET MVC

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

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Aceitando fichas só de aplicativos se a API web deve ser chamada apenas por apps daemon

Os utilizadores também podem utilizar as alegações de funções nos padrões de atribuição do utilizador, como mostrado em [Como: Adicionar funções de aplicação na sua aplicação e recebê-las no token](howto-add-app-roles-in-azure-ad-apps.md). Se as funções forem atribuíveis a ambas, as funções de verificação permitirão que as aplicações entrem como utilizadores e utilizadores a iniciarem sessão como apps. Recomendamos que declare diferentes papéis para utilizadores e apps para evitar esta confusão.

Se quiser apenas que as aplicações daemon liguem para a sua API web, adicione a condição de que o token seja um símbolo apenas de aplicações quando validar o papel da app.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

A verificação da condição inversa permite apenas aplicações que assinam num utilizador para ligar para a sua API.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Mover para produção](scenario-protected-web-api-production.md)
