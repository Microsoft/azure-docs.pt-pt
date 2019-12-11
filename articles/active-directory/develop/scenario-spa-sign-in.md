---
title: Logon do aplicativo de página única-plataforma de identidade da Microsoft | Azure
description: Saiba como criar um aplicativo de página única (entrada)
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
ms.openlocfilehash: 5d2494a7ad89c9e9b3a525ddd04290a08eb3af58
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962428"
---
# <a name="single-page-application-sign-in"></a>Aplicativo de página única: entrar

Saiba como adicionar entrada ao código para seu aplicativo de página única.

Antes que você possa obter tokens para acessar APIs em seu aplicativo, você precisa de um contexto de usuário autenticado. Você pode conectar usuários ao seu aplicativo no MSAL. js de duas maneiras:

* [Janela pop-up](#sign-in-with-a-pop-up-window), usando o método `loginPopup`
* [Redirecionar](#sign-in-with-redirect), usando o método `loginRedirect`

Opcionalmente, você também pode passar os escopos das APIs para as quais você precisa que o usuário consenti no momento da entrada.

> [!NOTE]
> Se o seu aplicativo já tiver acesso a um contexto de usuário autenticado ou a um token de ID, você poderá ignorar a etapa de logon e adquirir tokens diretamente. Para obter detalhes, consulte [SSO sem logon MSAL. js](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Escolhendo entre uma experiência de pop-up ou de redirecionamento

Você não pode usar os métodos pop-up e Redirect em seu aplicativo. A escolha entre uma experiência de pop-up ou de redirecionamento depende do seu fluxo de aplicativo:

* Se você não quiser que os usuários se afastem da página principal do aplicativo durante a autenticação, recomendamos o método pop-up. Como o redirecionamento de autenticação ocorre em uma janela pop-up, o estado do aplicativo principal é preservado.

* Se os usuários tiverem restrições de navegador ou políticas em que as janelas pop-up estiverem desabilitadas, você poderá usar o método Redirect. Use o método redirecionar com o navegador Internet Explorer, pois há [problemas conhecidos com janelas pop-up no Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

## <a name="sign-in-with-a-pop-up-window"></a>Entrar com uma janela pop-up

### <a name="javascript"></a>JavaScript

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

### <a name="angular"></a>Angular

O wrapper angular MSAL permite que você proteja rotas específicas em seu aplicativo adicionando `MsalGuard` à definição de rota. Essa proteção invocará o método para entrar quando a rota for acessada.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Para uma experiência de janela pop-up, habilite a opção de configuração `popUp`. Você também pode passar os escopos que exigem consentimento da seguinte maneira:

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

## <a name="sign-in-with-redirect"></a>Entrar com redirecionamento

### <a name="javascript"></a>JavaScript

Os métodos de redirecionamento não retornam uma promessa devido à afastamento do aplicativo principal. Para processar e acessar os tokens retornados, você precisa registrar os retornos de chamada de êxito e erro antes de chamar os métodos de redirecionamento.

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

### <a name="angular"></a>Angular

O código aqui é o mesmo descrito anteriormente na seção sobre como entrar com uma janela pop-up. O fluxo padrão é redirecionar.

> [!NOTE]
> O token de ID não contém os escopos consentido e representa apenas o usuário autenticado. Os escopos consentidod são retornados no token de acesso, que será adquirido na próxima etapa.

## <a name="sign-out"></a>Terminar sessão

A biblioteca MSAL fornece um método `logout` que limpa o cache no armazenamento do navegador e envia uma solicitação de saída para Azure Active Directory (Azure AD). Após a saída, a biblioteca redireciona de volta para a página inicial do aplicativo por padrão.

Você pode configurar o URI para o qual ele deve ser redirecionado após a saída, definindo `postLogoutRedirectUri`. Esse URI também deve ser registrado como o URI de logout no registro do aplicativo.

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adquirindo um token para o aplicativo](scenario-spa-acquire-token.md)
