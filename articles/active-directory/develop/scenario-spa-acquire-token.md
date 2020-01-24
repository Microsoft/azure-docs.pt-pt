---
title: Adquirir um token para chamar uma API Web (aplicativos de página única)-plataforma de identidade da Microsoft | Azure
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
ms.openlocfilehash: 290b0073ea6736141bca035f82f7aa37bdf364ef
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701931"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>Aplicativo de página única: adquirir um token para chamar uma API

O padrão para adquirir tokens para APIs com MSAL. js é primeiro tentar uma solicitação de token silenciosa usando o método `acquireTokenSilent`. Quando esse método é chamado, a biblioteca verifica primeiro o cache no armazenamento do navegador para ver se existe um token válido e o retorna. Quando nenhum token válido está no cache, ele envia uma solicitação de token silenciosa para Azure Active Directory (Azure AD) de um iframe oculto. Esse método também permite que a biblioteca renove tokens. Para obter mais informações sobre a sessão de logon único e os valores de tempo de vida do token no Azure AD, consulte [tempos de vida do token](active-directory-configurable-token-lifetimes.md).

As solicitações de token silencioso para o Azure AD podem falhar por motivos como uma sessão expirada do Azure AD ou uma alteração de senha. Nesse caso, você pode invocar um dos métodos interativos (que solicitarão o usuário) para adquirir tokens:

* [Janela pop-up](#acquire-a-token-with-a-pop-up-window), usando `acquireTokenPopup`
* [Redirecionar](#acquire-a-token-with-a-redirect), usando `acquireTokenRedirect`

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>Escolha entre uma experiência de pop-up ou de redirecionamento

 Você não pode usar os métodos pop-up e Redirect em seu aplicativo. A escolha entre uma experiência de pop-up ou de redirecionamento depende do seu fluxo de aplicativo:

* Se você não quiser que os usuários se afastem da página principal do aplicativo durante a autenticação, recomendamos o método pop-up. Como o redirecionamento de autenticação ocorre em uma janela pop-up, o estado do aplicativo principal é preservado.

* Se os usuários tiverem restrições de navegador ou políticas em que as janelas pop-ups estiverem desabilitadas, você poderá usar o método Redirect. Use o método redirecionar com o navegador Internet Explorer, pois há [problemas conhecidos com janelas pop-up no Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

Você pode definir os escopos de API que deseja que o token de acesso inclua ao criar a solicitação de token de acesso. Observe que todos os escopos solicitados podem não ser concedidos no token de acesso. Isso depende do consentimento do usuário.

## <a name="acquire-a-token-with-a-pop-up-window"></a>Adquirir um token com uma janela pop-up

### <a name="javascript"></a>Javascript

O código a seguir combina o padrão descrito anteriormente com os métodos para uma experiência de pop-up:

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
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

O wrapper angular MSAL fornece o interceptador HTTP, que automaticamente adquirirá tokens de acesso silenciosamente e os anexará às solicitações HTTP para APIs.

Você pode especificar os escopos para APIs na opção de configuração `protectedResourceMap`. `MsalInterceptor` solicitará esses escopos ao adquirir tokens automaticamente.

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

Como alternativa, você pode adquirir tokens explicitamente usando os métodos de aquisição-token conforme descrito na biblioteca do core MSAL. js.

## <a name="acquire-a-token-with-a-redirect"></a>Adquirir um token com um redirecionamento

### <a name="javascript"></a>Javascript

O padrão a seguir é conforme descrito anteriormente, mas mostrado com um método de redirecionamento para adquirir tokens interativamente. Você precisará registrar o retorno de chamada de redirecionamento, conforme mencionado anteriormente.

```javascript
function authCallback(error, response) {
    // Handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-optional-claims"></a>Solicitar declarações opcionais
Você pode usar declarações opcionais para as seguintes finalidades:

- Inclua declarações adicionais em tokens para seu aplicativo.
- Altere o comportamento de determinadas declarações que o Azure AD retorna em tokens.
- Adicione e acesse declarações personalizadas para seu aplicativo. 

Para solicitar declarações opcionais em `IdToken`, você pode enviar um objeto de declarações em cadeias para o campo `claimsRequest` da classe `AuthenticationParameters.ts`.

### <a name="javascript"></a>Javascript
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
Para saber mais, confira [declarações opcionais](active-directory-optional-claims.md).


### <a name="angular"></a>Angular

Esse código é o mesmo descrito anteriormente.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Chamando uma API Web](scenario-spa-call-api.md)
