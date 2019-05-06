---
title: Aplicação de página única (chamada de uma API web) – plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação de página única (chamada de uma API web)
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: CelesteDG
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01f141a5374c0e794b264f6e0135ca3e15ff8359
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074849"
---
# <a name="single-page-application---call-a-web-api"></a>Aplicação de página única - chamada de uma API web

Recomendamos que chamar o `acquireTokenSilent` método para comprar ou renovar um token de acesso antes de chamar uma API web. Depois de ter um token, pode chamar uma API web protegida.

## <a name="call-a-web-api"></a>Chamar uma API Web

### <a name="javascript"></a>JavaScript

Utilize o token de acesso adquirido como um portador numa solicitação HTTP para chamar qualquer API web, como o Microsoft Graph API. Por exemplo:

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

Conforme mencionado na [adquirir a seção tokens](scenario-spa-acquire-token.md), o invólucro de MSAL Angular aproveita o interceptor HTTP para adquirir silenciosamente os tokens de acesso e anexe-os para os pedidos HTTP para APIs automaticamente.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Mover para produção](scenario-spa-production.md)
