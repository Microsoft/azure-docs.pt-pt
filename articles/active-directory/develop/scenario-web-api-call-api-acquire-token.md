---
title: Obtenha um símbolo para uma API web que chama APIs web Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma API web que chama APIs web que requerem a aquisição de um símbolo para a app.
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
ms.openlocfilehash: 79f8eb9e804502a7c0e61c18e4998fa05db10278
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885145"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Uma API web que chama APIs web: Adquira um símbolo para a app

Depois de ter construído um objeto de aplicação do cliente, use-o para adquirir um símbolo que pode usar para chamar uma API web.

## <a name="code-in-the-controller"></a>Código no controlador

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Aqui está um exemplo de código que é chamado nas ações dos controladores da API. Chama a abaixo a API chamada *lista de todos os.*

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

`BuildConfidentialClient()`é semelhante ao cenário em [Uma API web que chama APIs web: configuração de aplicação](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()`instantâneos `IConfidentialClientApplication` com uma cache que contém informação para apenas uma conta. A conta é fornecida pelo `GetAccountIdentifier` método.

O `GetAccountIdentifier` método utiliza as alegações associadas à identidade do utilizador para quem a API web recebeu o JSON Web Token (JWT):

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
Aqui está um exemplo de código que é chamado nas ações dos controladores da API. Chama a API a jusante - Microsoft Graph.

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

# <a name="python"></a>[Python](#tab/python)

Uma API web Python terá de usar algum middleware para validar o sinal do portador recebido do cliente. A API web pode então obter o token de acesso para a API a jusante usando a biblioteca MSAL Python, chamando o [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) método. Uma amostra que demonstra este fluxo com o MSAL Python ainda não está disponível.

---

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Uma API web que chama APIs web: Chame uma API](scenario-web-api-call-api-call-api.md)
