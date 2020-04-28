---
title: Configure aplicativo de página única - Plataforma de identidade da Microsoft / Azure
description: Saiba como construir uma aplicação de uma única página (configuração de código da aplicação)
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
ms.openlocfilehash: f159105046231ba5fb4e458cdd70d930a411a920
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80882340"
---
# <a name="single-page-application-code-configuration"></a>Aplicação de página única: Configuração do código

Saiba como configurar o código para a sua aplicação de uma página única (SPA).

## <a name="msal-libraries-that-support-implicit-flow"></a>Bibliotecas MSAL que suportam fluxo implícito

A plataforma de identidade da Microsoft fornece as seguintes bibliotecas da Microsoft Authentication Library (MSAL) para suportar o fluxo implícito utilizando práticas de segurança recomendadas pela indústria:

| Biblioteca MSAL | Descrição |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Biblioteca JavaScript simples para uso em qualquer aplicação web do lado do cliente que seja construída através de quadros JavaScript ou SPA tais como Angular, Vue.js e React.js. |
| ![MSAL Angular](media/sample-v2-code/logo_angular.png) <br/> [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Invólucro da biblioteca core MSAL.js para simplificar o uso em aplicações de uma página única que são construídas através da estrutura angular. |

## <a name="application-code-configuration"></a>Configuração do código de aplicação

Numa biblioteca da MSAL, a informação de inscrição da aplicação é passada como configuração durante a inicialização da biblioteca.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

Para obter mais informações sobre as opções configuráveis, consulte [a aplicação inicialização com mSAL.js](msal-js-initializing-client-applications.md).

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
