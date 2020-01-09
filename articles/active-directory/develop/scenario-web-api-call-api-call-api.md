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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5829ca41aaa4bd61f8878657e5eedbf6351b5df4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423568"
---
# <a name="web-api-that-calls-web-apis---call-an-api"></a>API Web que chama APIs da Web – chamar uma API

Depois de ter um token, você pode chamar uma API Web protegida. Isso é feito a partir do controlador de sua API Web do ASP.NET/ASP.NET Core.

## <a name="controller-code"></a>Código do controlador

Aqui está a continuação do código de exemplo mostrado em [API Web protegida chama APIs da Web-adquirindo um token](scenario-web-api-call-api-acquire-token.md), chamado nas ações dos controladores de API, chamando uma API downstream (chamada ToDoList).

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

// Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Mover para produção](scenario-web-api-call-api-production.md)
