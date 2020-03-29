---
title: Obtenha um símbolo para uma API web que chama APIs web / Azure
titleSuffix: Microsoft identity platform
description: Aprenda a construir uma API web que chama APIs web que requerem a aquisição de um símbolo para a aplicação.
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
ms.openlocfilehash: 2721837459af24f39bb15ee17d394345cbb37eb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76834115"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Um API web que chama APIs web: Adquira um símbolo para a app

Depois de ter construído um objeto de aplicação de cliente, use-o para adquirir um símbolo que pode usar para chamar uma API web.

## <a name="code-in-the-controller"></a>Código no controlador

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Aqui está um exemplo de código que é chamado nas ações dos controladores DaPI. Chama a uma API a jusante chamada *todolista.*

```csharp
private async Task GetTodoList(bool isAppStarting)
{
 ...
 //
 // Get an access token to call the To Do service.
 //
 AuthenticationResult result = null;
 try
 {
  app = BuildConfidentialClient(HttpContext, HttpContext.User);
  result = await app.AcquireTokenSilent(Scopes, account)
                     .ExecuteAsync()
                     .ConfigureAwait(false);
 }
...
}
```

`BuildConfidentialClient()`é semelhante ao cenário em [AI web que chama APIs web: Configuração](scenario-web-api-call-api-app-configuration.md)de aplicações . `BuildConfidentialClient()`instantâneos `IConfidentialClientApplication` com uma cache que contém informação para apenas uma conta. A conta é `GetAccountIdentifier` fornecida pelo método.

O `GetAccountIdentifier` método utiliza as alegações associadas à identidade do utilizador para quem a Web API recebeu o JSON Web Token (JWT):

```csharp
public static string GetMsalAccountId(this ClaimsPrincipal claimsPrincipal)
{
 string userObjectId = GetObjectId(claimsPrincipal);
 string tenantId = GetTenantId(claimsPrincipal);

 if (    !string.IsNullOrWhiteSpace(userObjectId)
      && !string.IsNullOrWhiteSpace(tenantId))
 {
  return $"{userObjectId}.{tenantId}";
 }

 return null;
}
```

# <a name="java"></a>[Java](#tab/java)
Aqui está um exemplo de código que é chamado nas ações dos controladores DaPI. Chama a API a jusante - Microsoft Graph.

```java
@RestController
public class ApiController {

    @Autowired
    MsalAuthHelper msalAuthHelper;

    @RequestMapping("/graphMeApi")
    public String graphMeApi() throws MalformedURLException {

        String oboAccessToken = msalAuthHelper.getOboToken("https://graph.microsoft.com/.default");

        return callMicrosoftGraphMeEndpoint(oboAccessToken);
    }

}
```

# <a name="python"></a>[Pitão](#tab/python)

Uma API web Python terá de usar algum middleware para validar o token do portador recebido do cliente. A Web API pode então obter o token de acesso para [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) API a jusante usando a biblioteca MSAL Python, chamando o método. Uma amostra que demonstre este fluxo com a MSAL Python ainda não está disponível.

---

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Uma API web que chama APIs web: Ligue para um API](scenario-web-api-call-api-call-api.md)
