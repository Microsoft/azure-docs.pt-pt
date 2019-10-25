---
title: Evitar recargas de página (biblioteca de autenticação da Microsoft para JavaScript)
titleSuffix: Microsoft identity platform
description: Saiba como evitar recargas de página ao adquirir e renovar tokens silenciosamente usando a biblioteca de autenticação da Microsoft para JavaScript (MSAL. js).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29edafdc27a3835653f82ec36d576a4871e66155
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803098"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Evitar recargas de página ao adquirir e renovar tokens silenciosamente usando MSAL. js
A biblioteca de autenticação da Microsoft para JavaScript (MSAL. js) usa elementos de `iframe` ocultos para adquirir e renovar tokens silenciosamente em segundo plano. O Azure AD retorna o token de volta para o redirect_uri registrado especificado na solicitação de token (por padrão, essa é a página raiz do aplicativo). Como a resposta é um 302, ela resulta no HTML correspondente ao `redirect_uri` sendo carregado no `iframe`. Normalmente, a `redirect_uri` do aplicativo é a página raiz e isso faz com que ela seja recarregada.

Em outros casos, se navegar até a página raiz do aplicativo exigir autenticação, ele poderá levar a elementos `iframe` aninhados ou `X-Frame-Options: deny` erro.

Como o MSAL. js não pode ignorar o 302 emitido pelo Azure AD e é necessário para processar o token retornado, ele não pode impedir que o `redirect_uri` seja carregado no `iframe`.

Para evitar o recarregamento completo do aplicativo ou outros erros causados devido a isso, siga estas soluções alternativas.

## <a name="specify-different-html-for-the-iframe"></a>Especificar HTML diferente para o iframe

Defina a propriedade `redirect_uri` em config para uma página simples, que não requer autenticação. Você precisa se certificar de que ele corresponde ao `redirect_uri` registrado em portal do Azure. Isso não afetará a experiência de logon do usuário, pois o MSAL salva a página inicial quando o usuário inicia o processo de logon e redireciona de volta para o local exato após a conclusão do logon.

## <a name="initialization-in-your-main-app-file"></a>Inicialização no arquivo de aplicativo principal

Se seu aplicativo for estruturado de forma que haja um arquivo JavaScript central que define a inicialização, o roteamento e outras coisas do aplicativo, você poderá carregar condicionalmente seus módulos de aplicativo com base em se o aplicativo está sendo carregado em um `iframe` ou não. Por exemplo:

Em AngularJS: app. js

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

Em angular: app. Module. TS

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
Saiba mais sobre como [criar um aplicativo de página única (Spa) usando o](scenario-spa-overview.md) MSAL. js.