---
title: Sindes de sing-in de aplicações de uma só página & sismo - Plataforma de identidade da Microsoft Rio Azure
description: Saiba como construir uma aplicação de uma página (iniciar sê-in)
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
ms.openlocfilehash: 53a84bd970d564411ec9a56b54159e5a96717a6e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84558752"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>Aplicação de uma página: Iniciar sção e sindes

Saiba como adicionar o s-in ao código para a sua aplicação de uma página única.

Antes de obter fichas para aceder a APIs na sua aplicação, precisa de um contexto autenticado do utilizador. Pode iniciar sôms nas suas aplicações MSAL.js de duas formas:

* [Janela pop-up,](#sign-in-with-a-pop-up-window)usando o `loginPopup` método
* [Redirecione,](#sign-in-with-redirect)utilizando o `loginRedirect` método

Também pode passar opcionalmente os âmbitos das APIs para as quais precisa que o utilizador consinta no momento da sua inscrição.

> [!NOTE]
> Se a sua aplicação já tiver acesso a um contexto de utilizador autenticado ou ficha de ID, pode saltar o passo de login e adquirir diretamente fichas. Para mais informações, consulte [sSO sem MSAL.js login](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Escolher entre uma experiência pop-up ou redirecionamento

Não pode utilizar os métodos pop-up e redirecionamento na sua aplicação. A escolha entre uma experiência pop-up ou de redirecionamento depende do fluxo da sua aplicação:

* Se não quiser que os utilizadores se afastem da sua página principal de aplicações durante a autenticação, recomendamos o método pop-up. Como o redirecionamento de autenticação acontece numa janela pop-up, o estado da aplicação principal é preservado.

* Se os utilizadores tiverem restrições de navegador ou políticas onde as janelas pop-up são desativadas, pode utilizar o método de redirecionamento. Utilize o método de redirecionamento com o navegador Internet Explorer, porque existem [problemas conhecidos com janelas pop-up no Internet Explorer.](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)

## <a name="sign-in-with-a-pop-up-window"></a>Iniciar s-in com uma janela pop-up

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new userAgentApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        //login success
        let idToken = loginResponse.idToken;
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="angular"></a>[Angular](#tab/angular)

O invólucro ANGULAR MSAL permite-lhe garantir rotas específicas na sua aplicação adicionando `MsalGuard` à definição de rota. Este guarda invocará o método para iniciar seduca quando essa rota for acedida.

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

Para uma experiência de janela pop-up, ative a `popUp` opção de configuração. Também pode passar os âmbitos que requerem consentimento da seguinte forma:

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
            consentScopes: ["User.ReadWrite"]
        })
    ]
})
```
---

## <a name="sign-in-with-redirect"></a>Iniciar s-in com redirecionamento

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Os métodos de redirecionamento não devolvem uma promessa por causa da mudança para longe da aplicação principal. Para processar e aceder às fichas devolvidas, é necessário registar chamadas de sucesso e erro antes de ligar para os métodos de redirecionamento.

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new userAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

myMsal.handleRedirectCallback(authCallback);

myMsal.loginRedirect(loginRequest);
```

# <a name="angular"></a>[Angular](#tab/angular)

O código aqui é o mesmo descrito anteriormente na secção sobre o início com uma janela pop-up. O fluxo predefinido é redirecionado.

> [!NOTE]
> O token de ID não contém os âmbitos consentidos e representa apenas o utilizador autenticado. Os âmbitos consentidos são devolvidos no token de acesso, que adquirirá no próximo passo.

---

## <a name="sign-out"></a>Terminar sessão

A biblioteca MSAL fornece um `logout` método que limpa a cache no armazenamento do navegador e envia um pedido de sinalização para O Diretório Ativo Azure (Azure AD). Após a sposição, a biblioteca redireciona de volta para a página inicial da aplicação por predefinição.

Pode configurar o URI para o qual deve redirecionar após a definição `postLogoutRedirectUri` . Este URI também deve ser registado como o logout URI no seu registo de inscrição.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new UserAgentApplication(config);

myMsal.logout();
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

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Adquirir um token para a aplicação](scenario-spa-acquire-token.md)
