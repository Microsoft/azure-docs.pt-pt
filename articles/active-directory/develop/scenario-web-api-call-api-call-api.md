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
ms.openlocfilehash: c7c54c7c4718cea479a812574e961ef2338a6be6
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701761"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Uma API Web que chama APIs da Web: chamar uma API

Depois de ter um token, você pode chamar uma API Web protegida. Faça isso no controlador de sua API Web do ASP.NET ou do ASP.NET Core.

## <a name="controller-code"></a>Código do controlador

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Uma API Web que chama APIs da Web: mover para produção](scenario-web-api-call-api-production.md)
