---
title: Adquira um token para chamar uma API web (aplicações de uma página) | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma aplicação de uma página (adquira um símbolo para chamar uma API)
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.openlocfilehash: 83896b2599f03961b2dcaf34ea9b55fe16c13b9e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98756438"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>Aplicação de uma página: Adquirir um símbolo para chamar uma API

O padrão para adquirir fichas para APIs com MSAL.js é primeiro tentar um pedido de token silencioso usando o `acquireTokenSilent` método. Quando este método é chamado, a biblioteca verifica primeiro a cache no armazenamento do navegador para ver se existe um token válido e devolve-o. Quando não existe nenhum token válido na cache, envia um pedido de token silencioso ao Azure Ative Directory (Azure AD) a partir de um iframe oculto. Este método também permite que a biblioteca renove fichas. Para obter mais informações sobre a sessão de sessão de sessão de inscrição única e os valores de vida útil simbólicos em Azure AD, consulte [as vidas de Token](active-directory-configurable-token-lifetimes.md).

Os pedidos de token silenciosos para Azure AD podem falhar por razões como uma sessão AD Azure expirada ou uma alteração de senha. Nesse caso, pode invocar um dos métodos interativos (que levarão o utilizador) a adquirir fichas:

* [Janela pop-up,](#acquire-a-token-with-a-pop-up-window)usando `acquireTokenPopup`
* [Redirecionamento,](#acquire-a-token-with-a-redirect)utilizando `acquireTokenRedirect`

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>Escolha entre uma experiência pop-up ou redirecionando

 Não pode utilizar os métodos pop-up e redirecionamento na sua aplicação. A escolha entre uma experiência pop-up ou de redirecionamento depende do fluxo da sua aplicação:

* Se não quiser que os utilizadores se afastem da sua página principal de aplicação durante a autenticação, recomendamos o método pop-up. Como o redirecionamento de autenticação acontece numa janela pop-up, o estado da aplicação principal é preservado.

* Se os utilizadores tiverem restrições de navegador ou políticas onde as janelas pop-ups são desativadas, pode utilizar o método de redirecionamento. Utilize o método de redirecionamento com o navegador Internet Explorer, porque existem [problemas conhecidos com janelas pop-up no Internet Explorer.](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)

Pode definir os âmbitos da API que pretende que o token de acesso inclua quando estiver a construir o pedido de acesso ao símbolo. Note que todos os âmbitos solicitados podem não ser concedidos no token de acesso. Depende do consentimento do utilizador.

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

O invólucro ANGULAR MSAL fornece o intercetor HTTP, que adquirirá automaticamente os tokens de acesso silenciosamente e os anexará aos pedidos HTTP às APIs.

Pode especificar os âmbitos de APIs na `protectedResourceMap` opção de configuração. `MsalInterceptor` solicitará estes âmbitos quando adquirir automaticamente fichas.

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

Para o sucesso e insucesso da aquisição de token silencioso, a MSAL Angular fornece chamadas que pode subscrever. Também é importante lembrar de cancelar a subscrição.

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

Em alternativa, pode adquirir explicitamente fichas utilizando os métodos de aquisição-token, conforme descrito na biblioteca core MSAL.js.

---

## <a name="acquire-a-token-with-a-redirect"></a>Adquira um token com um redirecionamento

# <a name="javascript"></a>[JavaScript](#tab/javascript)

O padrão a seguir é descrito anteriormente, mas mostrado com um método de redirecionamento para adquirir tokens interativamente. Terá de registar o retoque de retoque como mencionado anteriormente.

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
- Alterar o comportamento de certas alegações de que a Azure AD regressa em fichas.
- Adicione e aceda reclamações personalizadas para a sua aplicação.

Para solicitar reclamações opcionais `IdToken` em , pode enviar um objeto de reclamaçõesplificado para o campo da `claimsRequest` `AuthenticationParameters.ts` classe.

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

Para saber mais, consulte [as reclamações opcionais.](active-directory-optional-claims.md)

# <a name="angular"></a>[Angular](#tab/angular)

Este código é o mesmo que descrito anteriormente.

---

## <a name="next-steps"></a>Passos seguintes

Passe para o próximo artigo neste cenário, [Chamando uma API web](scenario-spa-call-api.md).