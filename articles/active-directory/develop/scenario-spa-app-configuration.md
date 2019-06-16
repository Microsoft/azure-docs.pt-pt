---
title: Aplicação de página única (configuração do código da aplicação) – plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação de página única (configuração de código da aplicação)
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
ms.openlocfilehash: b71454fc553a0f81c26426a6a9588f15d5311e38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65406420"
---
# <a name="single-page-application---code-configuration"></a>Aplicação de página única - configuração de código

Saiba como configurar o código para a sua aplicação de página única (SPA).

## <a name="msal-libraries-supporting-implicit-flow"></a>Fluxo implícito de suporte de bibliotecas MSAL

Plataforma de identidade da Microsoft fornece a biblioteca de msal para suportar o fluxo implícito usando o setor recomendado práticas de segurança.  

As bibliotecas de suporte a fluxo implícito são:

| Biblioteca MSAL | Descrição |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Biblioteca de JavaScript simples para utilização em qualquer aplicação web do lado de cliente criada com JavaScript ou SPA estruturas como Angular, VUE, react. js, etc. |
| ![MSAL Angular](media/sample-v2-code/logo_angular.png) <br/> [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Invólucro da biblioteca de msal core para simplificar o uso em aplicativos de única página criados com o Angular framework. Esta biblioteca está em pré-visualização e tem [problemas conhecidos](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) com determinadas versões Angular e navegadores. |

## <a name="application-code-configuration"></a>Configuração de código da aplicação

Na biblioteca MSAL, as informações de registo de aplicação são passadas como configuração durante a inicialização de biblioteca.

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
Para obter mais detalhes sobre as opções configuráveis disponíveis, consulte [ao inicializar o aplicativo com msal](msal-js-initializing-client-applications.md).

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientId: 'your_app_id'
            })]
         })

  export class AppModule { }
```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Início de sessão e fim de sessão](scenario-spa-sign-in.md)
