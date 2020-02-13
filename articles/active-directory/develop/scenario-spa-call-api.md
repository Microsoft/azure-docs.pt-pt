---
title: Construa uma aplicação de uma página chamada Web API - plataforma de identidade da Microsoft Azure
description: Saiba como construir uma aplicação de uma única página que chama a aPI web
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: ccece6c840033913ec6d96b446dcb98c4befb32f
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160003"
---
# <a name="single-page-application-call-a-web-api"></a>Aplicação de página única: Ligue para uma API web

Recomendamos que ligue para o método `acquireTokenSilent` para adquirir ou renovar um token de acesso antes de chamar uma API web. Depois de ter um token, você pode chamar uma API Web protegida.

## <a name="call-a-web-api"></a>Chamar uma API Web

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Utilize o token de acesso adquirido como portador num pedido http para ligar para qualquer API web, como a Microsoft Graph API. Por exemplo:

```javascript
    var headers = new Headers();
    var bearer = "Bearer " + access_token;
    headers.append("Authorization", bearer);
    var options = {
         method: "GET",
         headers: headers
    };
    var graphEndpoint = "https://graph.microsoft.com/v1.0/me";

    fetch(graphEndpoint, options)
        .then(function (response) {
             //do something with response
        }
```

# <a name="angulartabangular"></a>[Angular](#tab/angular)

O invólucro MSAL Angular aproveita o intercetor HTTP para adquirir automaticamente fichas de acesso silenciosamente e anexá-las aos pedidos http às APIs. Para mais informações, consulte [AdQuira um símbolo para chamar a API](scenario-spa-acquire-token.md).

---

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Passar para a produção](scenario-spa-production.md)
