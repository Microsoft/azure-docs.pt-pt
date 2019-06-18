---
title: Aplicação de página única (chamada de uma API web) – plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação de página única (chamada de uma API web)
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
ms.openlocfilehash: 77a4ed01ac55a1153a62c672b33056a543b912ed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545599"
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
