---
title: Evitar recargas de página (biblioteca de autenticação da Microsoft para JavaScript) | Azure
description: Saiba como evitar recargas de página ao adquirir e tokens de renovação silenciosamente usando a biblioteca de autenticação da Microsoft para JavaScript (msal).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 162811221e6dde89ad11f358b2ec8f32f3c82522
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420472"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Evitar recargas de página ao adquirir e tokens de renovação silenciosamente com msal
Biblioteca de autenticação da Microsoft para utilizações de JavaScript (msal) ocultadas `iframe` elementos para adquirir e renovar tokens silenciosamente em segundo plano. O Azure AD devolve o token de volta para o redirect_uri registado especificado no pedido de token (por predefinição, é a página da aplicação raiz). Uma vez que a resposta é um 302, isso resulta em HTML correspondente para o `redirect_uri` ao carregar no `iframe`. Do normalmente, a aplicação `redirect_uri` é a página de raiz e isso faz com que ele volte a carregar.

Em outros casos, se navegar para a página da aplicação raiz requer autenticação, ele poderá levar para aninhados `iframe` elementos ou `X-Frame-Options: deny` erro.

Como msal não é possível dispensar 302 emitido pelo Azure AD e é necessária para processar o token retornado, ele não é possível impedir que o `redirect_uri` partir ao carregar no `iframe`.

Para evitar todo o aplicativo recarregar novamente ou outros erros causados por isso, siga estas soluções alternativas.

## <a name="specify-different-html-for-the-iframe"></a>Especificar diferente no código HTML iframe

Definir o `redirect_uri` propriedade de configuração para uma página simple, que não exija autenticação. Tem de certificar-se de que corresponde ao com o `redirect_uri` registado no portal do Azure. Isto não irá afetar a experiência de início de sessão do utilizador como MSAL guarda a página de início, quando inicia o processo de início de sessão de utilizador e redireciona para o local exato após a conclusão de início de sessão

## <a name="initialization-in-your-main-app-file"></a>Inicialização em seu arquivo de aplicativo principal

Se a sua aplicação é estruturada de modo que haja um arquivo Javascript central que define a inicialização do aplicativo, roteamento e outras coisas, pode carregar condicionalmente seus módulos de aplicação com base em se a aplicação está a carregar num `iframe` ou não. Por exemplo:

In AngularJS: app.js

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

No Angular: TS

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

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [criando um aplicativo de página única (SPA)](scenario-spa-overview.md) através de msal.