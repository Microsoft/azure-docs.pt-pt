---
title: Remova as contas do cache simbólico no sign-out - plataforma de identidade da Microsoft / Azure
description: Saiba como remover uma conta da cache simbólica no sign-out
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 934b756329065c466f21fca1480247065bdea28b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881617"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Uma aplicação web que chama APIs web: Remova as contas da cache simbólica no sign-out global

Aprendeu a adicionar sessão na sua aplicação web na [aplicação Web que assina nos utilizadores: Iniciar](scenario-web-app-sign-user-sign-in.md)sessão e iniciar sessão .

A inscrição é diferente para uma aplicação web que chama apis web. Quando o utilizador se signa da sua aplicação, ou de qualquer aplicação, deve remover as fichas associadas a esse utilizador da cache simbólica.

## <a name="intercept-the-callback-after-single-sign-out"></a>Intercete a chamada após a inscrição individual

Para limpar a entrada de cache de token associada à conta `logout` que assinou, a sua aplicação pode intercetar o evento seguinte. As aplicações web armazenam fichas de acesso para cada utilizador numa cache simbólica. Intercetando o `logout` backback posterior, a sua aplicação web pode remover o utilizador da cache.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Para ASP.NET Core, o mecanismo de `AddMsal()` interceção é ilustrado no método do [WebAppServiceCollectionExtensions.cs#L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157).

O URL de Logout que registou anteriormente para a sua aplicação permite-lhe implementar uma única inscrição. O ponto `logout` final da plataforma de identidade da Microsoft chama o seu URL de Logout. Esta chamada acontece se o sign-out ter começado a partir da sua aplicação web, ou de outra aplicação web ou do navegador. Para mais informações, consulte [a inscrição individual](v2-protocols-oidc.md#single-sign-out).

```csharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: Remove the account from MSAL.NET cache.
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache.
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

O código `RemoveAccountAsync` para está disponível em [Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A amostra ASP.NET não remove contas da cache no sinal global.

# <a name="java"></a>[Java](#tab/java)

A amostra de Java não remove contas da cache na aprovação global.

# <a name="python"></a>[Python](#tab/python)

A amostra python não remove contas da cache na aprovação global.

---

## <a name="next-steps"></a>Passos seguintes

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Adquirir um símbolo para a aplicação web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Adquirir um símbolo para a aplicação web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Adquirir um símbolo para a aplicação web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Adquirir um símbolo para a aplicação web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
