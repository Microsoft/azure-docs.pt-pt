---
title: Configurar aplicativos de uma só página | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma aplicação de uma página (configuração de código da aplicação)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: fe73832ec5eaee62a2dc2d397c12f82334e2efd8
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103010712"
---
# <a name="single-page-application-code-configuration"></a>Aplicação de uma página: Configuração de código

Saiba como configurar o código para a sua aplicação de uma página única (SPA).

## <a name="microsoft-libraries-supporting-single-page-apps"></a>Bibliotecas da Microsoft que suportam aplicações de página única 

As seguintes bibliotecas da Microsoft suportam aplicações de uma só página:

[!INCLUDE [active-directory-develop-libraries-spa](../../../includes/active-directory-develop-libraries-spa.md)]

## <a name="application-code-configuration"></a>Configuração do código de aplicação

Numa biblioteca MSAL, a informação de registo de pedidos é passada como configuração durante a inicialização da biblioteca.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

Para obter mais informações sobre as opções configuráveis, consulte [a aplicação De Inicialização com MSAL.js](msal-js-initializing-client-applications.md).

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
// App.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id'
            }
        })
    ]
})

export class AppModule { }
```

---

## <a name="next-steps"></a>Passos seguintes

Passe para o próximo artigo neste cenário, [iniciar sômis e assinar](scenario-spa-sign-in.md).
