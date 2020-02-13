---
title: Inscrição e inscrição de aplicativo de página única - plataforma de identidade da Microsoft Azure
description: Saiba como construir uma aplicação de uma única página (iniciar sessão)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: eb75aa53051e7e3c424ffe131cda61324fe86b1a
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159969"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>Aplicação de página única: Iniciar sessão e iniciar sessão

Saiba como adicionar sessão ao código para a sua aplicação de uma página única.

Antes de obter fichas para aceder a APIs na sua aplicação, precisa de um contexto de utilizador autenticado. Pode inscrever os utilizadores na sua aplicação em MSAL.js de duas formas:

* [Janela pop-up,](#sign-in-with-a-pop-up-window)usando o método `loginPopup`
* [Redirecione,](#sign-in-with-redirect)utilizando o método `loginRedirect`

Também pode passar opcionalmente os âmbitos das APIs para as quais necessita do utilizador para consentir no momento do início de sessão.

> [!NOTE]
> Se a sua aplicação já tiver acesso a um contexto de utilizador autenticado ou ficha de identificação, pode saltar o passo de login e adquirir fichas diretamente. Para mais detalhes, consulte [SSO sem login MSAL.js](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Escolher entre uma experiência pop-up ou redirecionamento

Não pode usar os métodos pop-up e redirecionamento na sua aplicação. A escolha entre uma experiência pop-up ou redirecionamento depende do fluxo de aplicação:

* Se não quiser que os utilizadores se afastem da sua página principal de aplicação durante a autenticação, recomendamos o método pop-up. Como o redirecionamento da autenticação acontece numa janela pop-up, o estado da aplicação principal é preservado.

* Se os utilizadores tiverem restrições ou políticas de navegador em que as janelas pop-up são desativadas, pode utilizar o método de redirecionamento. Utilize o método de redirecionamento com o navegador Internet Explorer, porque existem [problemas conhecidos com janelas pop-up no Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

## <a name="sign-in-with-a-pop-up-window"></a>Inscreva-se com uma janela pop-up

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginPopup(loginRequest).then(function (loginResponse) {
    //login success
    let idToken = loginResponse.idToken;
}).catch(function (error) {
    //login failure
    console.log(error);
});
```

# <a name="angulartabangular"></a>[Angular](#tab/angular)

O invólucro Angular MSAL permite-lhe assegurar rotas específicas na sua aplicação adicionando `MsalGuard` à definição de rota. Este guarda invocará o método para iniciar sessão quando essa rota for acedida.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Para uma experiência de janela pop-up, ative a opção de configuração `popUp`. Também pode passar os âmbitos que requerem consentimento da seguinte forma:

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["https://graph.microsoft.com/User.ReadWrite"]
            })]
         })
```
---

## <a name="sign-in-with-redirect"></a>Iniciar sessão com redirecionamento

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Os métodos de redirecionamento não devolvem uma promessa por causa da mudança para longe da aplicação principal. Para processar e aceder aos tokens devolvidos, precisa de registar chamadas de sucesso e erros antes de ligar para os métodos de redirecionamento.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginRedirect(loginRequest);
```

# <a name="angulartabangular"></a>[Angular](#tab/angular)

O código aqui é o mesmo descrito anteriormente na secção sobre o início de sessão com uma janela pop-up. O fluxo predefinido é redirecionado.

> [!NOTE]
> O símbolo de identificação não contém os âmbitos de aplicação consentidoe e representa apenas o utilizador autenticado. Os âmbitos com consentimento são devolvidos no sinal de acesso, que adquirirá no próximo passo.

---

## <a name="sign-out"></a>Terminar sessão

A biblioteca MSAL fornece um método `logout` que limpa a cache no armazenamento do navegador e envia um pedido de inscrição para o Azure Ative Directory (Azure AD). Após a inscrição, a biblioteca redireciona para a página de início da aplicação por padrão.

Pode configurar o URI para o qual deve redirecionar após a inscrição, definindo `postLogoutRedirectUri`. Este URI também deve ser registado como o logout URI no seu registo de inscrição.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const config = {

    auth: {
        clientID: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const userAgentApplication = new UserAgentApplication(config);
userAgentApplication.logout();

```

# <a name="angulartabangular"></a>[Angular](#tab/angular)

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            })]
         })

// In app.component.ts
this.authService.logout();
```

---

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adquirir um símbolo para a app](scenario-spa-acquire-token.md)
