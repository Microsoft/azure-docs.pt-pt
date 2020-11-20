---
title: 'Tutorial: Criar uma app Angular que utilize a plataforma de identidade da Microsoft para autenticação Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste tutorial, você constrói uma aplicação de página única angular (SPA) que utiliza a plataforma de identidade da Microsoft para assinar nos utilizadores e obter um token de acesso para ligar para a Microsoft Graph API em seu nome.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 03/05/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: c4c7d021c7c3a5a32d537a50fa45449fdee7e817
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94979934"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application"></a>Tutorial: Inscreva-se nos utilizadores e ligue para a Microsoft Graph API a partir de uma aplicação de página única angular

Neste tutorial, você constrói uma aplicação angular de uma página única (SPA) que assina nos utilizadores e chama a API do Microsoft Graph.

Neste tutorial:

> [!div class="checklist"]
> * Criar um projeto angular com `npm`
> * Registe a inscrição no portal Azure
> * Adicione código para suportar a inscrição do utilizador e a s inscrição
> * Adicione código para ligar para a Microsoft Graph API
> * Testar a aplicação

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/download/) para gerir um servidor web local.
* [Código do Estúdio Visual](https://code.visualstudio.com/download) ou outro editor para modificar ficheiros de projeto.

## <a name="how-the-sample-app-works"></a>Como funciona a aplicação da amostra

![Diagrama que mostra como a app da amostra gerada neste tutorial funciona](./media/tutorial-v2-angular/diagram-auth-flow-spa-angular.svg)

A aplicação de amostra criada neste tutorial permite a um SPA angular consultar a API do Microsoft Graph ou uma API web que aceita fichas emitidas pela plataforma de identidade microsoft. Utiliza a Biblioteca de Autenticação microsoft (MSAL) para angular, um invólucro do núcleo MSAL.js biblioteca. O MSAL Angular permite que as aplicações Angular 6+ autentem a autenticação dos utilizadores empresariais utilizando o Azure Ative Directory (Azure AD), bem como utilizadores com contas da Microsoft e identidades sociais como facebook, Google e LinkedIn. A biblioteca também permite que as aplicações tenham acesso aos serviços de cloud da Microsoft e ao Microsoft Graph.

Neste cenário, após a indicação de um utilizador, é solicitado um token de acesso e adicionado aos pedidos HTTP através do cabeçalho de autorização. A aquisição e renovação da Token são tratadas pela MSAL.

### <a name="libraries"></a>Bibliotecas

Este tutorial utiliza a seguinte biblioteca:

|Biblioteca|Descrição|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Biblioteca de autenticação da Microsoft para invólucro angular JavaScript|

Pode encontrar o código fonte da biblioteca MSAL.js no repositório [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) no GitHub.

## <a name="create-your-project"></a>Criar o seu projeto

Gere uma nova aplicação Angular utilizando os seguintes comandos npm:

```bash
npm install -g @angular/cli@8                    # Install the Angular CLI
ng new my-application --routing=true --style=css # Generate a new Angular app
cd my-application                                # Change to the app directory
npm install @angular/material@8 @angular/cdk@8   # Install the Angular Material component library (optional, for UI)
npm install msal @azure/msal-angular             # Install MSAL and MSAL Angular in your application
ng generate component page-name                  # To add a new page (such as a home or profile page)
```

## <a name="register-your-application"></a>Registar a aplicação

Siga as [instruções para registar uma aplicação de uma página](./scenario-spa-app-registration.md) no portal Azure.

Na página **geral** da aplicação do seu registo, note o valor de **ID da Aplicação (cliente)** para posterior utilização.

Registe o seu valor **URI de redirecionamento** como **http://localhost:4200/** e permita configurações implícitas de concessão.

## <a name="configure-the-application"></a>Configurar a aplicação

1. Na pasta *src/app,* edite *app.module.ts* e adicione `MSALModule` a bem como a `imports` `isIE` constante:

    ```javascript
    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;
    @NgModule({
      declarations: [
        AppComponent
      ],
      imports: [
        BrowserModule,
        AppRoutingModule,
        MsalModule.forRoot({
          auth: {
            clientId: 'Enter_the_Application_Id_here', // This is your client ID
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', // This is your tenant ID
            redirectUri: 'Enter_the_Redirect_Uri_Here'// This is your redirect URI
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
          },
        }, {
          popUp: !isIE,
          consentScopes: [
            'user.read',
            'openid',
            'profile',
          ],
          unprotectedResources: [],
          protectedResourceMap: [
            ['https://graph.microsoft.com/v1.0/me', ['user.read']]
          ],
          extraQueryParameters: {}
        })
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    ```

    Substitua estes valores:

    |Nome do valor|Sobre|
    |---------|---------|
    |Enter_the_Application_Id_Here|Na página **geral** do registo da sua candidatura, este é o seu valor **de ID de candidatura (cliente).** |
    |Enter_the_Cloud_Instance_Id_Here|Este é o exemplo da nuvem Azure. Para a nuvem Azure principal ou global, **https://login.microsoftonline.com** entre. Para nuvens nacionais (por exemplo, China), ver [nuvens nacionais.](./authentication-national-cloud.md)|
    |Enter_the_Tenant_Info_Here| Definir uma das seguintes opções: Se a sua candidatura suporta *contas neste diretório organizacional,* substitua este valor pelo ID do diretório (inquilino) ou nome do inquilino (por exemplo, **contoso.microsoft.com).** Se a sua candidatura suporta *contas em qualquer diretório organizacional,* substitua este valor por **organizações.** Se a sua aplicação suportar *contas em qualquer diretório organizacional e contas pessoais da Microsoft,* substitua este valor por **comum**. Para restringir apenas o suporte às *contas pessoais da Microsoft,* substitua este valor pelos **consumidores**. |
    |Enter_the_Redirect_Uri_Here|Substitua-a por **http://localhost:4200** .|

    Para obter mais informações sobre as opções disponíveis, consulte [As aplicações do cliente Inicialize.](msal-js-initializing-client-applications.md)

2. No topo do mesmo ficheiro, adicione a seguinte declaração de importação:

    ```javascript
    import { MsalModule, MsalInterceptor } from '@azure/msal-angular';
    ```

3. Adicione as seguintes declarações de importação ao topo `src/app/app.component.ts` de:

    ```javascript
    import { MsalService, BroadcastService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';
    ```
## <a name="sign-in-a-user"></a>Inscreva-se num utilizador

Adicione o seguinte código `AppComponent` para iniciar sinsução num utilizador:

```javascript
export class AppComponent implements OnInit {
    constructor(private broadcastService: BroadcastService, private authService: MsalService) { }

    ngOnInit() { }

    login() {
        const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

        if (isIE) {
          this.authService.loginRedirect({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        } else {
          this.authService.loginPopup({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        }
    }
}
```

> [!TIP]
> Recomendamos a utilização `loginRedirect` para utilizadores do Internet Explorer.

## <a name="acquire-a-token"></a>Adquirir um token

### <a name="angular-interceptor"></a>Angular Interceptor

A MSAL Angular fornece uma `Interceptor` classe que adquire automaticamente fichas para pedidos de saída que usam o cliente Angular para recursos `http` protegidos conhecidos.

Em primeiro lugar, inclua a `Interceptor` classe como fornecedor à sua aplicação:

```javascript
import { MsalInterceptor, MsalModule } from "@azure/msal-angular";
import { HTTP_INTERCEPTORS, HttpClientModule } from "@angular/common/http";

@NgModule({
    // ...
    providers: [
        {
            provide: HTTP_INTERCEPTORS,
            useClass: MsalInterceptor,
            multi: true
        }
    ]
}
```

Em seguida, fornecer um mapa de recursos protegidos para `MsalModule.forRoot()` como e incluir esses `protectedResourceMap` âmbitos em `consentScopes` . Os URLs que fornece na `protectedResourceMap` coleção são sensíveis a casos.

```javascript
@NgModule({
  // ...
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_here', // This is your client ID
        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
      },
      cache: {
        cacheLocation: 'localStorage',
        storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
      },
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      unprotectedResources: [],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ],
      extraQueryParameters: {}
    })
  ],
});
```

Finalmente, recupere o perfil de um utilizador com um pedido HTTP:

```JavaScript
const graphMeEndpoint = "https://graph.microsoft.com/v1.0/me";

getProfile() {
  this.http.get(graphMeEndpoint).toPromise()
    .then(profile => {
      this.profile = profile;
    });
}
```

### <a name="acquiretokensilent-acquiretokenpopup-acquiretokenredirect"></a>adquirirTokenSilent, adquirirTokenPopup, adquirirTokenRedirect
A MSAL utiliza três métodos para adquirir fichas: `acquireTokenRedirect` `acquireTokenPopup` e `acquireTokenSilent` . No entanto, recomendamos a utilização da `MsalInterceptor` classe para aplicações Angular, como mostra a secção anterior.

#### <a name="get-a-user-token-silently"></a>Obter um token de utilizador automaticamente

O `acquireTokenSilent` método lida com aquisições simbólicas e renovação sem interação do utilizador. Após o `loginRedirect` ou método ser executado pela primeira `loginPopup` vez, é `acquireTokenSilent` comumente usado para obter fichas usadas para aceder a recursos protegidos em chamadas posteriores. As chamadas para solicitar ou renovar fichas são feitas silenciosamente.

```javascript
const requestObj = {
    scopes: ["user.read"]
};

this.authService.acquireTokenSilent(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

Nesse código, `scopes` solicita-se que sejam devolvidos âmbitos no token de acesso à API.

Por exemplo:

* `["user.read"]` para o Microsoft Graph
* `["<Application ID URL>/scope"]` para APIs web personalizado (isto é, `api://<Application ID>/access_as_user` )

#### <a name="get-a-user-token-interactively"></a>Obter um token de utilizador interativamente

Por vezes, é necessário que o utilizador interaja com o ponto final da plataforma de identidade da Microsoft. Por exemplo:

* Os utilizadores podem precisar de reentrar nas suas credenciais porque a sua palavra-passe expirou.
* A sua aplicação está a solicitar acesso a âmbitos de recursos adicionais aos quais o utilizador precisa de consentir.
* É necessária autenticação de dois fatores.

O padrão recomendado para a maioria das aplicações é ligar `acquireTokenSilent` primeiro, depois pegar a exceção e, em seguida, ligar `acquireTokenPopup` (ou `acquireTokenRedirect` ) para iniciar um pedido interativo.

A chamada `acquireTokenPopup` resulta numa janela de inscrição pop-up. Em alternativa, `acquireTokenRedirect` redireciona os utilizadores para o ponto final da plataforma de identidade da Microsoft. Nessa janela, os utilizadores precisam de confirmar as suas credenciais, dar consentimento ao recurso necessário ou completar a autenticação de dois fatores.

```javascript
  const requestObj = {
      scopes: ["user.read"]
  };

  this.authService.acquireTokenPopup(requestObj).then(function (tokenResponse) {
      // Callback code here
      console.log(tokenResponse.accessToken);
  }).catch(function (error) {
      console.log(error);
  });
```

> [!NOTE]
> Este quickstart utiliza os `loginRedirect` métodos e métodos com o Microsoft Internet Explorer devido a um problema `acquireTokenRedirect` [conhecido](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) relacionado com o manuseamento de janelas pop-up pelo Internet Explorer.

## <a name="log-out"></a>Registar-se

Adicione o seguinte código para iniciar sessão de um utilizador:

```javascript
logout() {
  this.authService.logout();
}
```

## <a name="add-ui"></a>Adicionar UI
Para um exemplo de como adicionar UI utilizando a biblioteca de componentes de material angular, consulte a [aplicação](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular)da amostra .

## <a name="test-your-code"></a>Teste o seu código

1.  Inicie o servidor web para ouvir a porta executando os seguintes comandos num pedido de linha de comando da pasta de aplicação:

    ```bash
    npm install
    npm start
    ```
1. No seu navegador, insira **http://localhost:4200** **http://localhost:{port}** ou, onde a *porta* é a porta que o seu servidor web está a ouvir.


### <a name="provide-consent-for-application-access"></a>Fornecer consentimento para acesso à aplicação

A primeira vez que começa a iniciar o seu contrato de inscrição, é-lhe pedido que lhe conceda acesso ao seu perfil e o deixe entrar:

![A janela "Permissões Solicitadas"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

## <a name="add-scopes-and-delegated-permissions"></a>Adicionar âmbitos e permissões delegadas

A API do Microsoft Graph requer que o âmbito de leitura do *utilizador.leia* para ler o perfil de um utilizador. Por predefinição, este âmbito é automaticamente adicionado em todas as aplicações registadas no portal de registo. Outros APIs para o Microsoft Graph, bem como APIs personalizados para o seu servidor back-end, podem necessitar de âmbitos adicionais. Por exemplo, a API do Gráfico microsoft requer o âmbito *calendários.Leia* o âmbito de aplicação para listar os calendários do utilizador.

Para aceder aos calendários do utilizador no contexto de uma aplicação, adicione os *Calendários.Leia* a permissão delegada na informação de registo de candidatura. Em seguida, adicione os *Calendários.Leia* o âmbito da `acquireTokenSilent` chamada.

>[!NOTE]
>O utilizador poderá ser solicitado para obter consentimentos adicionais à medida que aumenta o número de âmbitos.

Se uma API de back-end não necessitar de um âmbito (não recomendado), pode utilizar *o clientId* como o âmbito das chamadas para adquirir fichas.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

Aprofundar o desenvolvimento de aplicações de uma página única (SPA) na plataforma de identidade da Microsoft na nossa série de artigos multi-partes.

> [!div class="nextstepaction"]
> [Cenário: Aplicação de página única](scenario-spa-overview.md)
