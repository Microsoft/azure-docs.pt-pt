---
title: Aplicação Web que chama o web APIs (adquirir um token da aplicação) – plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação Web que chama o web APIs (aquisição de um token da aplicação)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 653db995000308bb3ef78a9183696cd9d8ed1056
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074804"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Aplicação Web que chama o web APIs - adquirir um token da aplicação

Agora que criou o objeto de aplicativo de cliente, serão utilizadas para adquirir um token que, em seguida, usará para chamar um web APIs. No ASP.NET ou ASP.NET Core, chamar uma API, em seguida, é feita no controlador da web. Trata-se:

- Obter um token para utilizar a cache de token de API web. Para isso, chama `AcquireTokenSilent`
- Chamar a API protegida com o token de acesso

## <a name="aspnet-core"></a>Núcleo de ASP.NET

Os métodos de controlador estão protegidos por um `[Authorize]` atributo que força os usuários sejam autenticados para utilizar a aplicação Web. Aqui está o código que chama o Microsoft Graph

```CSharp
[Authorize]
public class HomeController : Controller
{
 ...
}
```

Aqui está um código simplificada da ação de HomeController, que obtém um token para chamar o Microsoft Graph.

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

Se quiser compreender em detalhes o total do código necessário para este cenário, consulte o artigo da fase 2 [2-1-Web-App chamadas Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph) passo do [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) tutorial

Existem inúmeras complexidades adicionais, tais como:

- implementar uma cache de tokens para a aplicação Web (o tutorial apresenta várias implementações)
- Remover a conta de cache quando o utilizador inicia sessão-out
- Chamar várias APIs, incluindo ter consentimento incremental

## <a name="aspnet"></a>ASP.NET

As coisas são semelhantes no ASP.NET:

- Uma ação de controlador protegida por um atributo [Authorize], extrairá o inquilino, ID e o ID de utilizador do `ClaimsPrincipal` membro do controlador (o ASP.NET usa `HttpContext.User`)
- a partir daí, ele cria um MSAL.NET `IConfidentialClientApplication`
- Chamada IT a `AcquireTokenSilent` método do aplicativo cliente confidencial 

o código é semelhante ao código mostrado para o ASP.NET Core

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Chamar uma API Web](scenario-web-app-call-api-call-api.md)
