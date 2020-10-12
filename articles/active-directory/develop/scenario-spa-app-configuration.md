---
title: Configure app de página única - Plataforma de identidade da Microsoft Rio Azure
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
ms.openlocfilehash: b42d720a425b92ec9002f7c2b9797a91f70dafe2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89003032"
---
# <a name="single-page-application-code-configuration"></a>Aplicação de uma página: Configuração de código

Saiba como configurar o código para a sua aplicação de uma página única (SPA).

## <a name="msal-libraries-for-spas-and-supported-authentication-flows"></a>Bibliotecas MSAL para SPAs e fluxos de autenticação suportados

A plataforma de identidade da Microsoft fornece a seguinte Biblioteca de Autenticação do Microsoft para o JavaScript (MSAL.js) para suportar o fluxo de código de fluxo e autorização implícito com o PKCE utilizando práticas de segurança recomendadas pela indústria:

| Biblioteca MSAL | Fluxo | Descrição |
|--------------|------|-------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (2.x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) | Fluxo de código de autorização (PKCE) | Biblioteca JavaScript simples para uso em qualquer aplicação web do lado do cliente que seja construída através de estruturas JavaScript ou SPA, tais como Angular, Vue.js e React.js. |
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (1.x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core) | Fluxo implícito | Biblioteca JavaScript simples para uso em qualquer aplicação web do lado do cliente que seja construída através de estruturas JavaScript ou SPA, tais como Angular, Vue.js e React.js. |
| ![MSAL Angular](media/sample-v2-code/logo_angular.png) <br/> [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Fluxo implícito | O invólucro do núcleo MSAL.js biblioteca para simplificar o uso em aplicações de uma só página que são construídas através da estrutura angular. |

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

> [!div class="nextstepaction"]
> [Iniciar e terminar sessão](scenario-spa-sign-in.md)
