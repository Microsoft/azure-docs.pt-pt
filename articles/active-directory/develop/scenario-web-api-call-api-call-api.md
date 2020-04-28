---
title: Web API que chama APIs web - plataforma de identidade microsoft / Azure
description: Aprenda a construir uma API web que chama APIs web.
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
ms.openlocfilehash: 6bbd24978891efd147b0c317c1746d13961ce5e9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885094"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Uma API web que chama APIs web: Ligue para um API

Depois de ter um símbolo, pode chamar uma API protegida. Faz isto a partir do controlador da sua Web API.

## <a name="controller-code"></a>Código de comando

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

O seguinte código continua o código de exemplo que é mostrado em [API web que chama APIs web: Adquirir um símbolo para a aplicação](scenario-web-api-call-api-acquire-token.md). O código é chamado nas ações dos controladores DaPI. Chama a uma API a jusante chamada *todolista.*

Depois de adquirir o símbolo, use-o como um símbolo portador para chamar a API a jusante.

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

# <a name="java"></a>[Java](#tab/java)

O seguinte código continua o código de exemplo que é mostrado em [API web que chama APIs web: Adquirir um símbolo para a aplicação](scenario-web-api-call-api-acquire-token.md). O código é chamado nas ações dos controladores DaPI. Chama ao gráfico a jusante da API MS.

Depois de adquirir o símbolo, use-o como um símbolo portador para chamar a API a jusante.

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

# <a name="python"></a>[Python](#tab/python)
Uma amostra que demonstre este fluxo com a MSAL Python ainda não está disponível.

---

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Uma API web que chama APIs web: Mover-se para a produção](scenario-web-api-call-api-production.md)
