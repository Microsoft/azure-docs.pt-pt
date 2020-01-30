---
title: API Web que chama APIs da Web-plataforma de identidade da Microsoft | Azure
description: Saiba como criar uma API Web que chama APIs da Web.
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
ms.openlocfilehash: d66a08d4e84a3771d6c3fa46b96c975869435452
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76833384"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Uma API Web que chama APIs da Web: chamar uma API

Depois de ter um token, você pode chamar uma API Web protegida. Faz isto a partir do controlador da sua Web API.

## <a name="controller-code"></a>Código do controlador

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

O código a seguir continua o código de exemplo mostrado em [uma API Web que chama APIs da Web: adquirir um token para o aplicativo](scenario-web-api-call-api-acquire-token.md). O código é chamado nas ações dos controladores de API. Ele chama uma API downstream chamada *ToDoList*.

Depois de adquirir o token, use-o como um token de portador para chamar a API downstream.

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

// After the token has been returned by Microsoft Authentication Library (MSAL), add it to the HTTP authorization header before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

O código a seguir continua o código de exemplo mostrado em [uma API Web que chama APIs da Web: adquirir um token para o aplicativo](scenario-web-api-call-api-acquire-token.md). O código é chamado nas ações dos controladores de API. Chama ao gráfico a jusante da API MS.

Depois de adquirir o token, use-o como um token de portador para chamar a API downstream.

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)
Uma amostra que demonstre este fluxo com a MSAL Python ainda não está disponível.

---

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Uma API Web que chama APIs da Web: mover para produção](scenario-web-api-call-api-production.md)
