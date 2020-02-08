---
title: Evite recargas de página (MSAL.js) / Azure
titleSuffix: Microsoft identity platform
description: Saiba como evitar recargas de página ao adquirir e renovar tokens silenciosamente utilizando a Biblioteca de Autenticação da Microsoft para JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/29/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 63944a5a9af34c2d4cf98eeb870a730df49654e5
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084950"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Evite recargas de página ao adquirir e renovar tokens silenciosamente usando MSAL.js
A Microsoft Authentication Library for JavaScript (MSAL.js) utiliza elementos `iframe` escondidos para adquirir e renovar tokens silenciosamente em segundo plano. A Azure AD devolve o token ao redirect_uri registado especificado no pedido simbólico (por padrão, esta é a página raiz da aplicação). Uma vez que a resposta é um 302, resulta no HTML correspondente ao `redirect_uri` ser carregado no `iframe`. Normalmente, o `redirect_uri` da aplicação é a página raiz e isso faz com que seja recarregado.

Noutros casos, se navegar para a página raiz da aplicação requer autenticação, pode levar a elementos `iframe` aninhadas ou `X-Frame-Options: deny` erro.

Uma vez que a MSAL.js não pode descartar os 302 emitidos pela Azure AD e é obrigado a processar o símbolo devolvido, não pode impedir que o `redirect_uri` seja carregado na `iframe`.

Para evitar a recarga de toda a aplicação ou outros erros causados por isso, siga estas seleções.

## <a name="specify-different-html-for-the-iframe"></a>Especificar HTML diferente para o iframe

Delineie a propriedade `redirect_uri` em config para uma página simples, que não requer autenticação. Tem de se certificar de que corresponde ao `redirect_uri` registado no portal Azure. Isto não afetará a experiência de login do utilizador, uma vez que o MSAL guarda a página de início quando o utilizador iniciar o processo de login e redireciona de volta para o local exato após o início do login.

## <a name="initialization-in-your-main-app-file"></a>Inicialização no seu ficheiro principal de aplicação

Se a sua aplicação estiver estruturada de modo a existir um ficheiro Javascript central que define a inicialização, encaminhamento e outras coisas da aplicação, pode carregar condicionalmente os módulos da sua aplicação com base no facto de a aplicação estar a carregar num `iframe` ou não. Por exemplo:

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
Saiba mais sobre [a construção de uma aplicação de uma única página (SPA)](scenario-spa-overview.md) utilizando MSAL.js.