---
title: Aplicativo de página única (configuração de código do aplicativo)-plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo de página única (configuração de código do aplicativo)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7b4fba03f9edf8a3f4e42b23c6a1b5e06518863
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891537"
---
# <a name="single-page-application---code-configuration"></a>Aplicativo de página única-configuração de código

Saiba como configurar o código para seu aplicativo de página única (SPA).

## <a name="msal-libraries-supporting-implicit-flow"></a>Bibliotecas MSAL que dão suporte ao fluxo implícito

A plataforma de identidade da Microsoft fornece a biblioteca MSAL. js para dar suporte ao fluxo implícito usando as práticas seguras recomendadas do setor.  

As bibliotecas que dão suporte ao fluxo implícito são:

| Biblioteca MSAL | Descrição |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Biblioteca JavaScript simples para uso em qualquer aplicativo Web do lado do cliente criado usando estruturas JavaScript ou SPA, como angular, Vue. js, reajam. js, etc. |
| ![MSAL angular](media/sample-v2-code/logo_angular.png) <br/> [MSAL angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Wrapper da biblioteca MSAL. js principal para simplificar o uso em aplicativos de página única criados com a estrutura angular. Esta biblioteca está em visualização e tem [problemas conhecidos](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) com determinadas versões e navegadores angulares. |

## <a name="application-code-configuration"></a>Configuração de código do aplicativo

Na biblioteca MSAL, as informações de registro do aplicativo são passadas como configuração durante a inicialização da biblioteca.

### <a name="javascript"></a>JavaScript

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
Para obter mais detalhes sobre as opções configuráveis disponíveis, consulte [inicializando aplicativo com MSAL. js](msal-js-initializing-client-applications.md).

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id'
            })]
         })

  export class AppModule { }
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Entrada e saída](scenario-spa-sign-in.md)
