---
title: Web API que chamadas outras APIs web (adquirir um token da aplicação) - plataforma de identidade da Microsoft
description: Saiba como compilar uma web API que chamadas outras web APIs (aquisição de um token da aplicação).
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
ms.openlocfilehash: 986e2e0f8a481d61dc870af2548290658b44d2d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65231106"
---
# <a name="web-api-that-calls-web-apis---acquire-a-token-for-the-app"></a>API Web que chama o web APIs - adquirir um token da aplicação

Depois de criar uma aplicação de cliente de objeto, utilizá-lo para adquirir um token que pode utilizar para chamar uma API web.

## <a name="code-in-the-controller"></a>No controlador de código

Eis um exemplo de código que será chamado nas ações dos controladores da API, chamar uma API de downstream (com o nome todolist).

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
}
```

`BuildConfidentialClient()` é semelhante ao que viu no artigo [APIs - configuração de aplicações de web de API Web que chama](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()` cria uma instância `IConfidentialClientApplication` com uma cache que contém apenas as informações para uma conta. A conta é fornecida pelo `GetAccountIdentifier` método.

O `GetAccountIdentifier` método utiliza as declarações associadas à identidade do utilizador para o qual a API web recebido o JWT:

```CSharp
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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Chamar uma API web](scenario-web-api-call-api-call-api.md)
