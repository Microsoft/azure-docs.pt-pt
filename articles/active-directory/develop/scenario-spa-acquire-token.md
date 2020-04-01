---
title: Adquirir um símbolo para chamar um API web (aplicações de página única) - Plataforma de identidade da Microsoft Azure
description: Saiba como construir uma aplicação de uma única página (adquira um símbolo para chamar a API)
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
ms.openlocfilehash: 393c3a06a2366a7d6947faf8bbfe038d6c5982fc
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419667"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>Aplicação de página única: Adquira um símbolo para chamar a API

O padrão para adquirir fichas para APIs com MSAL.js é primeiro tentar `acquireTokenSilent` um pedido de token silencioso usando o método. Quando este método é chamado, a biblioteca verifica primeiro a cache no armazenamento do navegador para ver se existe um token válido e devolve-o. Quando não há ficha válida na cache, envia um pedido de ficha silenciosa ao Azure Ative Directory (Azure AD) a partir de um iframe oculto. Este método também permite que a biblioteca renove fichas. Para obter mais informações sobre a sessão de inscrição única e os valores de vida simbólicos em Azure AD, consulte [token lifetimes](active-directory-configurable-token-lifetimes.md).

Os pedidos de ficha silenciosa para a AD Azure podem falhar por razões como uma sessão de Anúncio Saque Azure expirada ou uma alteração de senha. Nesse caso, pode invocar um dos métodos interativos (o que irá levar o utilizador) a adquirir fichas:

* [Janela pop-up,](#acquire-a-token-with-a-pop-up-window)usando`acquireTokenPopup`
* [Redirecione,](#acquire-a-token-with-a-redirect)utilizando`acquireTokenRedirect`

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>Escolha entre uma experiência pop-up ou redirecionamento

 Não pode usar os métodos pop-up e redirecionamento na sua aplicação. A escolha entre uma experiência pop-up ou redirecionamento depende do fluxo de aplicação:

* Se não quiser que os utilizadores se afastem da sua página principal de aplicação durante a autenticação, recomendamos o método pop-up. Como o redirecionamento da autenticação acontece numa janela pop-up, o estado da aplicação principal é preservado.

* Se os utilizadores tiverem restrições ou políticas de navegador em que as janelas pop-ups são desativadas, pode utilizar o método de redirecionamento. Utilize o método de redirecionamento com o navegador Internet Explorer, porque existem [problemas conhecidos com janelas pop-up no Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

Você pode definir os âmbitos da API que você quer que o token de acesso inclua quando está construindo o pedido de acesso. Note que todos os âmbitos solicitados podem não ser concedidos no token de acesso. Depende do consentimento do utilizador.

## <a name="acquire-a-token-with-a-pop-up-window"></a>Adquira um símbolo com uma janela pop-up

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O seguinte código combina o padrão previamente descrito com os métodos para uma experiência pop-up:

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

# <a name="angular"></a>[Angular](#tab/angular)

O invólucro MSAL Angular fornece o intercetor HTTP, que adquirirá automaticamente fichas de acesso silenciosamente e as ligará aos pedidos http às APIs.

Pode especificar os âmbitos para `protectedResourceMap` APIs na opção de configuração. `MsalInterceptor`solicitará estes âmbitos ao adquirir automaticamente fichas.

```javascript
// app.module.ts
@NgModule({
  declarations: [
    // ...
  ],
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_Here',
      }
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ]
    })
  ],
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MsalInterceptor,
      multi: true
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

Para o sucesso e falha da aquisição de token silencioso, a MSAL Angular fornece callbacks que pode subscrever. Também é importante lembrar de cancelar a inscrição.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if (this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

Alternativamente, pode adquirir explicitamente fichas utilizando os métodos de aquisição de token, conforme descrito na biblioteca core MSAL.js.

---

## <a name="acquire-a-token-with-a-redirect"></a>Adquirir um símbolo com um redirecionamento

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O seguinte padrão é descrito anteriormente, mas mostrado com um método de redirecionamento para adquirir fichas interativamente. Terá de registar a redirecção, como mencionado anteriormente.

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

## <a name="request-optional-claims"></a>Solicitar reclamações opcionais

Pode utilizar reclamações opcionais para os seguintes fins:

- Inclua reclamações adicionais em fichas para a sua aplicação.
- Mude o comportamento de certas alegações que a AD Azure devolve em fichas.
- Adicione e aceda a reclamações personalizadas para a sua aplicação.

Para solicitar reclamações `IdToken`opcionais, pode enviar `claimsRequest` um `AuthenticationParameters.ts` objeto de reclamações stringified para o campo da classe.

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

Para saber mais, consulte [As reclamações opcionais.](active-directory-optional-claims.md)

# <a name="angular"></a>[Angular](#tab/angular)

Este código é o mesmo descrito anteriormente.

---

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Chamar uma API web](scenario-spa-call-api.md)
