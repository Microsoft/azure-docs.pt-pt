---
title: Inscrição de aplicação de uma página única & inscrição - plataforma de identidade da Microsoft Azure
description: Saiba como construir uma aplicação de uma única página (iniciar sessão)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 7e809def048c95b6688a13ac99783615eb045d11
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885194"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>Aplicação de página única: Iniciar sessão e iniciar sessão

Saiba como adicionar sessão ao código para a sua aplicação de uma página única.

Antes de obter fichas para aceder a APIs na sua aplicação, precisa de um contexto de utilizador autenticado. Pode inscrever os utilizadores na sua aplicação em MSAL.js de duas formas:

* [Janela pop-up,](#sign-in-with-a-pop-up-window)usando `loginPopup` o método
* [Redirecione,](#sign-in-with-redirect) `loginRedirect` utilizando o método

Também pode passar opcionalmente os âmbitos das APIs para as quais necessita do utilizador para consentir no momento do início de sessão.

> [!NOTE]
> Se a sua aplicação já tiver acesso a um contexto de utilizador autenticado ou ficha de identificação, pode saltar o passo de login e adquirir fichas diretamente. Para mais detalhes, consulte [SSO sem login MSAL.js](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Escolher entre uma experiência pop-up ou redirecionamento

Não pode usar os métodos pop-up e redirecionamento na sua aplicação. A escolha entre uma experiência pop-up ou redirecionamento depende do fluxo de aplicação:

* Se não quiser que os utilizadores se afastem da sua página principal de aplicação durante a autenticação, recomendamos o método pop-up. Como o redirecionamento da autenticação acontece numa janela pop-up, o estado da aplicação principal é preservado.

* Se os utilizadores tiverem restrições ou políticas de navegador em que as janelas pop-up são desativadas, pode utilizar o método de redirecionamento. Utilize o método de redirecionamento com o navegador Internet Explorer, porque existem [problemas conhecidos com janelas pop-up no Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

## <a name="sign-in-with-a-pop-up-window"></a>Inscreva-se com uma janela pop-up

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="angular"></a>[Angular](#tab/angular)

O invólucro Angular MSAL permite-lhe assegurar rotas `MsalGuard` específicas na sua aplicação adicionando à definição de rota. Este guarda invocará o método para iniciar sessão quando essa rota for acedida.

```javascript
// In app-routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ProfileComponent } from './profile/profile.component';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';

const routes: Routes = [
  {
    path: 'profile',
    component: ProfileComponent,
    canActivate: [
      MsalGuard
    ]
  },
  {
    path: '',
    component: HomeComponent
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes, { useHash: false })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

Para uma experiência de janela `popUp` pop-up, ative a opção de configuração. Também pode passar os âmbitos que requerem consentimento da seguinte forma:

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
            }
        }, {
            popUp: true,
            consentScopes: ["https://graph.microsoft.com/User.ReadWrite"]
        })
    ]
})
```
---

## <a name="sign-in-with-redirect"></a>Iniciar sessão com redirecionamento

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="angular"></a>[Angular](#tab/angular)

O código aqui é o mesmo descrito anteriormente na secção sobre o início de sessão com uma janela pop-up. O fluxo predefinido é redirecionado.

> [!NOTE]
> O símbolo de identificação não contém os âmbitos de aplicação consentidoe e representa apenas o utilizador autenticado. Os âmbitos com consentimento são devolvidos no sinal de acesso, que adquirirá no próximo passo.

---

## <a name="sign-out"></a>Terminar sessão

A biblioteca MSAL `logout` fornece um método que limpa a cache no armazenamento do navegador e envia um pedido de inscrição para o Azure Ative Directory (Azure AD). Após a inscrição, a biblioteca redireciona para a página de início da aplicação por padrão.

Pode configurar o URI para o qual deve redirecionar após a inscrição, definindo `postLogoutRedirectUri`. Este URI também deve ser registado como o logout URI no seu registo de inscrição.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const userAgentApplication = new UserAgentApplication(config);
userAgentApplication.logout();

```

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
//In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            }
        })
    ]
})

// In app.component.ts
this.authService.logout();
```

---

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adquirir um token para a aplicação](scenario-spa-acquire-token.md)
