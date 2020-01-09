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
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc92fb7bc5ddf451279e6c157f9e93aa7fe9a12a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423627"
---
# <a name="web-api-that-calls-web-apis---acquire-a-token-for-the-app"></a>API Web que chama APIs da Web – adquirir um token para o aplicativo

Depois de criar um objeto de aplicativo cliente, use-o para adquirir um token que você pode usar para chamar uma API da Web.

## <a name="code-in-the-controller"></a>Código no controlador

Aqui está um exemplo de código que será chamado nas ações dos controladores de API, chamando uma API downstream (chamada ToDoList).

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

`BuildConfidentialClient()` é semelhante ao que você viu no artigo [API Web que chama as APIs da Web-configuração de aplicativo](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()` instancia `IConfidentialClientApplication` com um cache que contém apenas informações para uma conta. A conta é fornecida pelo método de `GetAccountIdentifier`.

O método `GetAccountIdentifier` usa as declarações associadas à identidade do usuário para a qual a API Web recebeu o JWT:

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Chamando uma API Web](scenario-web-api-call-api-call-api.md)
