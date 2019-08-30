---
title: Aplicativo de página única (adquirir um token para chamar uma API)-plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo de página única (adquirir um token para chamar uma API)
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f49a6093194ef76a895f2a54f8a78a55da73e7e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135700"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>Aplicativo de página única – adquirir um token para chamar uma API

O padrão para adquirir tokens para APIs com MSAL. js é primeiro tentar uma solicitação de token silenciosa usando o `acquireTokenSilent` método. Quando esse método é chamado, a biblioteca verifica primeiro o cache no armazenamento do navegador para ver se existe um token válido e o retorna. Quando não há um token válido no cache, ele envia uma solicitação de token silenciosa para Azure Active Directory (Azure AD) de um iframe oculto. Esse método também permite que a biblioteca renove tokens. Para obter mais informações sobre a sessão de logon único e os valores de tempo de vida do token no Azure AD, consulte [tempos de vida do token](active-directory-configurable-token-lifetimes.md).

As solicitações de token silencioso para o Azure AD podem falhar por alguns motivos, como uma sessão expirada do Azure AD ou uma alteração de senha. Nesse caso, você pode invocar um dos métodos interativos (que solicitarão o usuário) para adquirir tokens.

* [Adquirir o token com uma janela pop-up](#acquire-token-with-a-pop-up-window) usando`acquireTokenPopup`
* [Adquirir token com](#acquire-token-with-redirect) redirecionamento usando`acquireTokenRedirect`

**Escolhendo entre uma experiência de pop-up ou de redirecionamento**

 Você não pode usar uma combinação dos métodos pop-up e Redirect em seu aplicativo. A escolha entre uma experiência de pop-up ou de redirecionamento depende do seu fluxo de aplicativo.

* Se você não quiser que o usuário navegue para fora da página principal do aplicativo durante a autenticação, é recomendável usar os métodos pop-up. Como o redirecionamento de autenticação ocorre em uma janela pop-up, o estado do aplicativo principal é preservado.

* Há certos casos em que talvez seja necessário usar os métodos de redirecionamento. Se os usuários do seu aplicativo tiverem restrições de navegador ou políticas em que janelas pop-up estiverem desabilitadas, você poderá usar os métodos de redirecionamento. Também é recomendável usar os métodos de redirecionamento com o navegador Internet Explorer, pois há certos [problemas conhecidos com o Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) ao lidar com janelas pop-up.

Você pode definir os escopos de API que deseja que o token de acesso inclua ao criar a solicitação de token de acesso. Observe que todos os escopos solicitados podem não ser concedidos no token de acesso e depende do consentimento do usuário.

## <a name="acquire-token-with-a-pop-up-window"></a>Adquirir token com uma janela pop-up

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

O wrapper angular MSAL fornece a conveniência de adicionar o interceptador HTTP, que adquirirá automaticamente tokens de acesso silenciosamente e os anexará às solicitações HTTP para APIs.

Você pode especificar os escopos para APIs na `protectedResourceMap` opção de configuração, que será solicitada pelo MsalInterceptor ao adquirir tokens automaticamente.

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

Para obter êxito e falha da aquisição de token silenciosa, o MSAL angular fornece retornos de chamada que você pode assinar. Também é importante lembrar-se de cancelar a assinatura.

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

Como alternativa, você também pode adquirir tokens explicitamente usando os métodos de token de aquisição, conforme descrito na biblioteca Core MSAL. js.

## <a name="acquire-token-with-redirect"></a>Adquirir token com redirecionamento

### <a name="javascript"></a>JavaScript

O padrão é conforme descrito acima, mas mostrado com um método de redirecionamento para adquirir o token de forma interativa. Será necessário registrar o retorno de chamada de redirecionamento conforme mencionado acima.

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

## <a name="request-for-optional-claims"></a>Solicitação de declarações opcionais
Você pode solicitar declarações opcionais em seu aplicativo para especificar quais declarações adicionais incluir em tokens para seu aplicativo. Para solicitar declarações opcionais no id_token, você pode enviar um objeto de declarações em cadeias para o campo claimsRequest da classe AuthenticationParameters. TS.

Você pode usar declarações opcionais para o seguinte propósito:

- Para incluir declarações adicionais em tokens para seu aplicativo.
- Altere o comportamento de determinadas declarações que o Azure AD retorna em tokens.
- Adicione e acesse declarações personalizadas para seu aplicativo.


### <a name="javascript"></a>JavaScript
```javascript
"optionalClaims":  
   {
      "idToken": [
            {
                  "name": "auth_time", 
                  "essential": true
             }
      ],

var request = {
    scopes: ["user.read"],
    claimsRequest: JSON.stringify(claims)
};

myMSALObj.acquireTokenPopup(request);
```
Para saber mais sobre declarações opcionais, faça checkout de [declarações opcionais](active-directory-optional-claims.md)


### <a name="angular"></a>Angular

Isso é o mesmo descrito acima.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Chamando uma API Web](scenario-spa-call-api.md)
