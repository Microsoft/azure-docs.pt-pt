---
title: Aplicação de página única (entrar) – plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação de página única (início de sessão)
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
ms.date: 05/06/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc9c46ae28960387e6f8efc1ade20afa1c77ef55
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138789"
---
# <a name="single-page-application---sign-in"></a>Aplicação de página única - início de sessão

Saiba como adicionar o início de sessão para o código para a sua aplicação de página única.

Antes de poder obter tokens para aceder a APIs na sua aplicação, terá um contexto de usuário autenticado. Pode iniciar sessão de utilizadores ao seu aplicativo no msal de duas formas:

* [Inicie sessão com uma janela de pop-up](#sign-in-with-a-pop-up-window) usando `loginPopup` método
* [Inicie sessão com o redirecionamento](#sign-in-with-redirect) usando `loginRedirect` método

Opcionalmente, também pode passar os âmbitos de APIs para que precisa de consentimento no momento de início de sessão ao utilizador.

> [!NOTE]
> Se a aplicação já tiver acesso a um contexto de utilizador autenticado ou o id de token, pode ignorar o passo de início de sessão e adquirir diretamente tokens. Para obter mais detalhes, consulte [sso sem início de sessão de msal](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Escolha entre uma experiência de pop-up ou de redirecionamento

Não é possível utilizar uma combinação dos métodos de pop-up e o redirecionamento em seu aplicativo. A escolha entre uma experiência de pop-up ou redirecionamento depende do fluxo do aplicativo.

* Se não pretender que o utilizador a sair de sua página principal do aplicativo durante a autenticação, é recomendado para utilizar os métodos de pop-up. Uma vez que o redirecionamento de autenticação acontece numa janela de pop-up, o estado do aplicativo principal é preservado.

* Há determinados casos em que poderá ter de utilizar os métodos de redirecionamento. Se os utilizadores da sua aplicação tiverem restrições do navegador ou políticas em que o windows de pop-ups estão desativadas, pode usar os métodos de redirecionamento. Utilizar os métodos de redirecionamento com o navegador Internet Explorer, uma vez que existem determinadas [problemas conhecidos com o Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) ao manipular janelas pop-up.

## <a name="sign-in-with-a-pop-up-window"></a>Inicie sessão com uma janela de pop-up

### <a name="javascript"></a>JavaScript

```javascript
const loginRequest = {
    scopes: ["user.read", "user.write"]
}

userAgentApplication.loginPopup(loginRequest).then(function (loginResponse) {
    //login success
    let idToken = loginResponse.idToken;
}).catch(function (error) {
    //login failure
    console.log(error);
});
```

### <a name="angular"></a>Angular

O wrapper de MSAL Angular permite-lhe assegurar rotas específicas em seu aplicativo adicionando apenas o `MsalGuard` para a definição de rota. Este guard invocará o método de iniciar sessão quando esse caminho é acessado.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Para uma experiência de janela pop-up, ativar o `popUp` opção de configuração. Também pode passar os âmbitos que requerem o consentimento da seguinte forma:

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["user.read", "user.write"]
            })]
         })
```

## <a name="sign-in-with-redirect"></a>Inicie sessão com o redirecionamento

### <a name="javascript"></a>JavaScript

Os métodos de redirecionamento não devolverem uma promessa devido a navegação para fora do aplicativo principal. Para processar e os retornado tokens de acesso, terá de registar os retornos de chamada de êxito e erro antes de chamar os métodos de redirecionamento.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["user.read", "user.write"]
}

userAgentApplication.loginRedirect(loginRequest);
```

### <a name="angular"></a>Angular

O código aqui é igual à descrita acima, sob a iniciar sessão com uma seção de janela pop-up. O fluxo de padrão é o redirecionamento.

> [!NOTE]
> O token de ID não contém os âmbitos autorizados e apenas representa o usuário autenticado. Os âmbitos autorizados são devolvidos no token de acesso que vão adquirir no próximo passo.

## <a name="sign-out"></a>Terminar sessão

A biblioteca MSAL fornece um `logout` método que irá limpar a cache no armazenamento do navegador e envia um pedido de sessão para o Azure AD. Depois de terminar, será redirecionado novamente para a página de início do aplicativo por predefinição.

Pode configurar o URI para o qual este deve redirecioná-após o início de sessão para fora, definindo o `postLogoutRedirectUri`. Este URI também deve ser registado como o URI de fim de sessão no seu registo de aplicação.

### <a name="javascript"></a>JavaScript

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

### <a name="angular"></a>Angular

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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Obter um token para a aplicação](scenario-spa-acquire-token.md)
