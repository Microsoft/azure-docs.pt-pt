---
title: Evite recargas de página (MSAL.js) Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como evitar recargas de página ao adquirir e renovar tokens silenciosamente utilizando a Biblioteca de Autenticação da Microsoft para JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/29/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5eb30f7dcf4b459b0af0bd8de965971fbbe44863
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85477656"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Evite recargas de página ao adquirir e renovar tokens silenciosamente utilizando MSAL.js
A Microsoft Authentication Library for JavaScript (MSAL.js) utiliza `iframe` elementos ocultos para adquirir e renovar tokens silenciosamente em segundo plano. A Azure AD devolve o símbolo ao redirect_uri registado especificado no pedido simbólico (por padrão, esta é a página raiz da aplicação). Uma vez que a resposta é um 302, resulta no HTML correspondente ao `redirect_uri` carregamento no `iframe` . Normalmente, a aplicação `redirect_uri` é a página raiz e isto faz com que recarregue.

Noutros casos, se navegar na página raiz da aplicação necessitar de autenticação, pode levar a `iframe` elementos aninhados ou `X-Frame-Options: deny` erros.

Uma vez que MSAL.js não pode descartar o 302 emitido pela Azure AD e é obrigado a processar o token devolvido, não pode impedir que `redirect_uri` o carregamento seja carregado no `iframe` .

Para evitar que toda a aplicação volte a recarregar ou outros erros causados por isso, por favor siga estas soluções alternativas.

## <a name="specify-different-html-for-the-iframe"></a>Especificar HTML diferente para o iframe

Desafie o `redirect_uri` imóvel em config para uma página simples, que não requer autenticação. Tem de se certificar de que corresponde ao `redirect_uri` registado no portal Azure. Isto não afetará a experiência de login do utilizador, uma vez que o MSAL guarda a página inicial quando o utilizador iniciar o processo de login e redireciona de volta para a localização exata após o início de sessão.

## <a name="initialization-in-your-main-app-file"></a>Inicialização no seu ficheiro principal de aplicações

Se a sua aplicação estiver estruturada de tal forma que exista um ficheiro Javascript central que define a inicialização, encaminhamento e outras coisas da aplicação, pode carregar condicionalmente os seus módulos de aplicação com base no carregamento ou não da `iframe` app. Por exemplo:

Em AngularJS: app.js

```javascript
// Check that the window is an iframe and not popup
if (window !== window.parent && !window.opener) {
angular.module('todoApp', ['ui.router', 'MsalAngular'])
    .config(['$httpProvider', 'msalAuthenticationServiceProvider','$locationProvider', function ($httpProvider, msalProvider,$locationProvider) {
        msalProvider.init(
            // msal configuration
        );

        $locationProvider.html5Mode(false).hashPrefix('');
    }]);
}
else {
    angular.module('todoApp', ['ui.router', 'MsalAngular'])
        .config(['$stateProvider', '$httpProvider', 'msalAuthenticationServiceProvider', '$locationProvider', function ($stateProvider, $httpProvider, msalProvider, $locationProvider) {
            $stateProvider.state("Home", {
                url: '/Home',
                controller: "homeCtrl",
                templateUrl: "/App/Views/Home.html",
            }).state("TodoList", {
                url: '/TodoList',
                controller: "todoListCtrl",
                templateUrl: "/App/Views/TodoList.html",
                requireLogin: true
            })

            $locationProvider.html5Mode(false).hashPrefix('');

            msalProvider.init(
                // msal configuration
            );
        }]);
}
```

Em Angular: app.module.ts

```javascript
// Imports...
@NgModule({
  declarations: [
    AppComponent,
    MsalComponent,
    MainMenuComponent,
    AccountMenuComponent,
    OsNavComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    ServiceWorkerModule.register('ngsw-worker.js', { enabled: environment.production }),
    MsalModule.forRoot(environment.MsalConfig),
    SuiModule,
    PagesModule
  ],
  providers: [
    HttpServiceHelper,
    {provide: HTTP_INTERCEPTORS, useClass: MsalInterceptor, multi: true},
    AuthService
  ],
  entryComponents: [
    AppComponent,
    MsalComponent
  ]
})
export class AppModule {
  constructor() {
    console.log('APP Module Constructor!');
  }

  ngDoBootstrap(ref: ApplicationRef) {
    if (window !== window.parent && !window.opener)
    {
      console.log("Bootstrap: MSAL");
      ref.bootstrap(MsalComponent);
    }
    else
    {
    //this.router.resetConfig(RouterModule);
      console.log("Bootstrap: App");
      ref.bootstrap(AppComponent);
    }
  }
}
```

MsalComponent:

```javascript
import { Component} from '@angular/core';
import { MsalService } from '@azure/msal-angular';

// This component is used only to avoid Angular reload
// when doing acquireTokenSilent()

@Component({
  selector: 'app-root',
  template: '',
})
export class MsalComponent {
  constructor(private Msal: MsalService) {
  }
}
```

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a construção de uma aplicação de uma página (SPA)](scenario-spa-overview.md) utilizando MSAL.js.