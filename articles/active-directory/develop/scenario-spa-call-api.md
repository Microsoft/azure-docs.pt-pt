---
title: Compilar aplicativo de página única chamando uma API Web-plataforma de identidade da Microsoft | Azure
description: Saiba como criar um aplicativo de página única que chama uma API da Web
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1171d8c3bc28c7b325cc8daf6cc072965363339c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965336"
---
# <a name="single-page-application-call-a-web-api"></a>Aplicativo de página única: chamar uma API da Web

Recomendamos que você chame o método `acquireTokenSilent` para adquirir ou renovar um token de acesso antes de chamar uma API da Web. Depois de ter um token, você pode chamar uma API Web protegida.

## <a name="call-a-web-api"></a>Chamar uma API Web

### <a name="javascript"></a>JavaScript

Use o token de acesso adquirido como um portador em uma solicitação HTTP para chamar qualquer API da Web, como Microsoft Graph API. Por exemplo:

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

### <a name="angular"></a>Angular

O wrapper angular MSAL aproveita o interceptador HTTP para adquirir automaticamente tokens de acesso silenciosamente e anexá-los às solicitações HTTP para APIs. Para obter mais informações, consulte [adquirir um token para chamar uma API](scenario-spa-acquire-token.md).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Mover para produção](scenario-spa-production.md)
