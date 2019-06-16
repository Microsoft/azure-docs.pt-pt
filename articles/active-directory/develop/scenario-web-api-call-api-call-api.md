---
title: API Web que chama web APIs (chamada de APIs) - a plataforma de identidade da Microsoft
description: Saiba como compilar uma web API web a jusante esse chamadas APIs (chamar uma API web).
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
ms.openlocfilehash: 1cd093cc68a21558dc326221eeaa8c034c24f1c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074729"
---
# <a name="web-api-that-calls-web-apis---call-an-api"></a>API Web que chama a APIs - web chamar uma API

Depois de ter um token, pode chamar uma API web protegida. Isso é feito a partir do controlador da API web ASP.NET/ASP.NET Core.

## <a name="controller-code"></a>Código de controlador

Aqui é a continuação do código de exemplo mostrado na [APIs - adquirir um token de web de chamadas de web API protegido](scenario-web-api-call-api-acquire-token.md), denominado nas ações dos controladores da API, chamar uma API de downstream (com o nome todolist).

Assim que o utilizador tiver adquirido o token, utilizá-lo como um token de portador para chamar a API de downstream.

```CSharp
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

// Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Mover para produção](scenario-web-api-call-api-production.md)
