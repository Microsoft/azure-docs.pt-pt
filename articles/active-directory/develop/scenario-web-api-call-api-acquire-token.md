---
title: Obter um token para uma API Web que chama APIs da Web | Azure
titleSuffix: Microsoft identity platform
description: Saiba como criar uma API Web que chama APIs da Web que exigem a aquisição de um token para o aplicativo.
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
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76834115"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Uma API Web que chama APIs da Web: adquirir um token para o aplicativo

Depois de criar um objeto de aplicativo cliente, use-o para adquirir um token que você pode usar para chamar uma API da Web.

## <a name="code-in-the-controller"></a>Código no controlador

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Aqui está um exemplo de código que é chamado nas ações dos controladores de API. Ele chama uma API downstream chamada *ToDoList*.

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

`BuildConfidentialClient()` é semelhante ao cenário em [uma API Web que chama APIs da Web: configuração de aplicativo](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()` instancia `IConfidentialClientApplication` com um cache que contém informações para apenas uma conta. A conta é fornecida pelo método de `GetAccountIdentifier`.

O método `GetAccountIdentifier` usa as declarações associadas à identidade do usuário para quem a API Web recebeu o token Web JSON (JWT):

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

# <a name="javatabjava"></a>[Java](#tab/java)
Aqui está um exemplo de código que é chamado nas ações dos controladores de API. Chama a API a jusante - Microsoft Graph.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Uma API web Python terá de usar algum middleware para validar o token do portador recebido do cliente. A Web API pode então obter o token de acesso para API a jusante usando a biblioteca MSAL Python, chamando o método [`acquire_token_on_behalf_of`.](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) Uma amostra que demonstre este fluxo com a MSAL Python ainda não está disponível.

---

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Uma API Web que chama APIs da Web: chamar uma API](scenario-web-api-call-api-call-api.md)
