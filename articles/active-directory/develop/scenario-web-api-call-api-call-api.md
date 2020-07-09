---
title: API web que chama APIs web - plataforma de identidade microsoft / Rio Azure
description: Saiba como construir uma API web que chama APIs web.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885094"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Uma API web que chama APIs web: Chame uma API

Depois de ter um token, pode chamar uma API web protegida. Faça isso a partir do controlador da sua API web.

## <a name="controller-code"></a>Código do controlador

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

O seguinte código continua o código de exemplo que é mostrado numa [API web que chama APIs web: Adquira um símbolo para a aplicação](scenario-web-api-call-api-acquire-token.md). O código é chamado nas ações dos controladores da API. Chama a abaixo a API chamada *lista de todos os.*

Depois de ter adquirido o símbolo, use-o como símbolo portador para chamar a API a jusante.

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

O seguinte código continua o código de exemplo que é mostrado numa [API web que chama APIs web: Adquira um símbolo para a aplicação](scenario-web-api-call-api-acquire-token.md). O código é chamado nas ações dos controladores da API. Chama o gráfico a jusante da API MS.

Depois de ter adquirido o símbolo, use-o como símbolo portador para chamar a API a jusante.

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
Uma amostra que demonstra este fluxo com o MSAL Python ainda não está disponível.

---

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Uma API web que chama APIs web: Mover para a produção](scenario-web-api-call-api-production.md)
