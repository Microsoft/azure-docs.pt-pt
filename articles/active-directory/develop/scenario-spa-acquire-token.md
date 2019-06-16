---
title: Aplicação de página única (adquirir um token para chamar uma API) - plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação de página única (obter um token para chamar uma API)
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
ms.openlocfilehash: f4c842db8a0874d3619e0dc59b90aa12226cb984
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65138821"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>Aplicação de página única - adquirir um token para chamar uma API

O padrão para aquisição de tokens para APIs com msal é tentar primeiro um pedido de token silencioso através do `acquireTokenSilent` método. Quando esse método é chamado, a biblioteca primeiro verifica o cache no armazenamento do browser para ver se um token válido existe e devolve a mesma. Quando não houver nenhum token válido na cache, envia um pedido de token silencioso ao Azure Active Directory (Azure AD) de um iframe oculto. Esse método também permite que a biblioteca de renovar os tokens. Para obter mais detalhes sobre a sessão de início de sessão único e os valores de duração do token no Azure AD, consulte [durações de token](active-directory-configurable-token-lifetimes.md).

Os pedidos de token silenciosas para o Azure AD podem falhar por alguns motivos como uma sessão do AD de Azure expirada ou uma alteração de palavra-passe. Nesse caso, pode invocar um dos métodos interativos (que pedirá ao utilizador) para adquirir tokens.

* [Obter o token com uma janela de pop-up](#acquire-token-with-a-pop-up-window) usando `acquireTokenPopup`
* [Obter o token com redirecionamento](#acquire-token-with-redirect) usando `acquireTokenRedirect`

**Escolha entre uma experiência de pop-up ou de redirecionamento**

 Não é possível utilizar uma combinação dos métodos de pop-up e o redirecionamento em seu aplicativo. A escolha entre uma experiência de pop-up ou redirecionamento depende do fluxo do aplicativo.

* Se não pretender que o utilizador a sair de sua página principal do aplicativo durante a autenticação, é recomendado para utilizar os métodos de pop-up. Uma vez que o redirecionamento de autenticação acontece numa janela de pop-up, o estado do aplicativo principal é preservado.

* Há determinados casos em que poderá ter de utilizar os métodos de redirecionamento. Se os utilizadores da sua aplicação tiverem restrições do navegador ou políticas em que o windows de pop-ups estão desativadas, pode usar os métodos de redirecionamento. É também recomendado para utilizar os métodos de redirecionamento com o navegador Internet Explorer, uma vez que existem determinadas [problemas conhecidos com o Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) ao manipular janelas pop-up.

Pode definir os âmbitos da API que pretende que o token de acesso para incluir ao criar o pedido de token de acesso. Tenha em atenção que todos os âmbitos pedidos não pode ser concedido no token de acesso e depende de consentimento do usuário.

## <a name="acquire-token-with-a-pop-up-window"></a>Obter o token com uma janela de pop-up

### <a name="javascript"></a>JavaScript

O padrão acima usando os métodos para uma experiência de pop-up:

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

### <a name="angular"></a>Angular

O wrapper de MSAL Angular fornece a conveniência de adicionar o interceptor HTTP `MsalInterceptor` que irá adquirir silenciosamente os tokens de acesso e anexe-os para os pedidos HTTP para APIs automaticamente.

Pode especificar os âmbitos para as APIs no `protectedResourceMap` opção de configuração que o MsalInterceptor irá pedir quando automaticamente aquisição de tokens.

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                protectedResourceMap: {"https://graph.microsoft.com/v1.0/me", ["user.read", "mail.send"]}
            })]
         })

providers: [ ProductService, {
        provide: HTTP_INTERCEPTORS,
        useClass: MsalInterceptor,
        multi: true
    }
   ],
```

Para o êxito e falha da aquisição de token silenciosa, MSAL Angular fornece retornos de chamada que pode subscrever. Também é importante lembrar-se cancelar sua assinatura.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if(this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

Em alternativa, pode também explicitamente adquirir tokens utilizando os métodos de token de aquisição, conforme descrito na biblioteca de msal principal.

## <a name="acquire-token-with-redirect"></a>Adquirir token de redirecionamento

### <a name="javascript"></a>JavaScript

O padrão é como descrito acima, mas é mostrado com um método de redirecionamento para adquirir o token de forma interativa. Tenha em atenção que terá de registar o retorno de chamada de redirecionamento, tal como mencionado acima.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

### <a name="angular"></a>Angular

Este é o mesmo, tal como descrito acima.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Chamar uma API Web](scenario-spa-call-api.md)
