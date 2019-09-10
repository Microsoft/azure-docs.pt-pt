---
title: Aplicativo Web que chama APIs da Web (adquirir um token para o aplicativo)-plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo Web que chama APIs da Web (adquirindo um token para o aplicativo)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3aa144c76fb0a8e479658efdb5d43361fbbc085c
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860617"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Aplicativo Web que chama APIs da Web – adquirir um token para o aplicativo

Agora que você criou o objeto de aplicativo cliente, você o usará para adquirir um token para chamar uma API da Web. Em ASP.NET ou ASP.NET Core, a chamada a uma API da Web é feita no controlador. É sobre:

- Obter um token para a API Web usando o cache de token. Para obter esse token, você chama `AcquireTokenSilent`.
- Chamar a API protegida com o token de acesso.

## <a name="aspnet-core"></a>Núcleo de ASP.NET

Os métodos do controlador são protegidos por `[Authorize]` um atributo que força os usuários que estão sendo autenticados a usar o aplicativo Web. Aqui está o código que chama Microsoft Graph.

```CSharp
[Authorize]
public class HomeController : Controller
{
 ...
}
```

Aqui está um código simplificado da ação do HomeController, que obtém um token para chamar o Microsoft Graph.

```CSharp
public async Task<IActionResult> Profile()
{
 var application = BuildConfidentialClientApplication(HttpContext, HttpContext.User);
 string accountIdentifier = claimsPrincipal.GetMsalAccountId();
 string loginHint = claimsPrincipal.GetLoginHint();

 // Get the account
 IAccount account = await application.GetAccountAsync(accountIdentifier);

 // Special case for guest users as the Guest iod / tenant id are not surfaced.
 if (account == null)
 {
  var accounts = await application.GetAccountsAsync();
  account = accounts.FirstOrDefault(a => a.Username == loginHint);
 }

 AuthenticationResult result;
 result = await application.AcquireTokenSilent(new []{"user.read"}, account)
                            .ExecuteAsync();
 var accessToken = result.AccessToken;
 ...
 // use the access token to call a web API
}
```

Para entender com mais detalhes o código necessário para esse cenário, consulte a etapa 2 ([2-1-chamadas de aplicativo Web Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) do tutorial [MS-Identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) .

Há muitas complexidades adicionais, como:

- Implementando um cache de token para o aplicativo Web (o tutorial apresenta várias implementações)
- Removendo a conta do cache quando o usuário sair
- Chamar várias APIs, incluindo ter consentimento incremental

## <a name="aspnet"></a>ASP.NET

As coisas são semelhantes em ASP.NET:

- Uma ação do controlador protegida por um atributo [autorizar] extrai a ID do locatário e a ID `ClaimsPrincipal` de usuário do membro do controlador. (O ASP.NET `HttpContext.User`usa.)
- A partir daí, ele cria um `IConfidentialClientApplication`MSAL.net.
- Por fim, ele chama `AcquireTokenSilent` o método do aplicativo cliente confidencial.

O código é semelhante ao código mostrado para ASP.NET Core.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Chamar uma API Web](scenario-web-app-call-api-call-api.md)
