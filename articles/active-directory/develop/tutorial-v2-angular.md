---
title: Tutorial de aplicativo de uma página única angular - Azure
titleSuffix: Microsoft identity platform
description: Saiba como as aplicações angulares do SPA podem chamar uma API que requer acesso a fichas do ponto final da plataforma de identidade da Microsoft.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 03/05/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: c645ab45711698e4a6f582678e2a850e15dea62a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82181601"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application"></a>Tutorial: Inscreva-se nos utilizadores e ligue para a Microsoft Graph API a partir de uma aplicação de uma página única angular

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspetos desta funcionalidade podem mudar antes da disponibilidade geral (GA).

Este tutorial demonstra como uma aplicação angular de uma única página (SPA) pode:
- Inscreva-se em contas pessoais, contas de trabalho ou contas escolares.
- Adquira um sinal de acesso.
- Ligue para o Microsoft Graph API ou outros APIs que requerem fichas de acesso do ponto final da *plataforma de identidade*da Microsoft .

>[!NOTE]
>Este tutorial explica-o como criar um novo SPA angular utilizando a Microsoft Authentication Library (MSAL). Se quiser descarregar uma aplicação de amostra, consulte o [arranque rápido](quickstart-v2-angular.md).

## <a name="how-the-sample-app-works"></a>Como funciona a aplicação da amostra

![Diagrama que mostra como a aplicação de amostra gerada neste tutorial funciona](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

### <a name="more-information"></a>Mais informações

A aplicação de amostra criada neste tutorial permite que um SPA angular questione o Microsoft Graph API ou um API web que aceita tokens do ponto final da plataforma de identidade da Microsoft. O MSAL para biblioteca angular é um invólucro da biblioteca core MSAL.js. Permite que aplicações Angulares (6+) atentiquem os utilizadores da empresa utilizando o Microsoft Azure Ative Directory, os utilizadores da conta microsoft e os utilizadores de identidade social (como facebook, Google e LinkedIn). A biblioteca também permite que as aplicações tenham acesso aos serviços de nuvem da Microsoft ou ao Microsoft Graph.

Neste cenário, após a adesão de um utilizador, é solicitado um sinal de acesso e adicionado aos pedidos http através do cabeçalho de autorização. A aquisição e renovação simbólicas são tratadas pela MSAL.

### <a name="libraries"></a>Bibliotecas

Este tutorial utiliza a seguinte biblioteca:

|Biblioteca|Descrição|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Biblioteca de autenticação da Microsoft para invólucro angular JavaScript|

Pode encontrar o código fonte para a biblioteca MSAL.js no repositório [AzureAD/microsoft-autenticação-biblioteca-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) no GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Para executar este tutorial, precisa de:

* Um servidor web local, como [Node.js](https://nodejs.org/en/download/). As instruções neste tutorial baseiam-se no Node.js.
* Um ambiente de desenvolvimento integrado (IDE), como [o Visual Studio Code,](https://code.visualstudio.com/download)para editar os ficheiros do projeto.

## <a name="create-your-project"></a>Crie o seu projeto

Gerar uma nova aplicação Angular utilizando os seguintes comandos npm:

```Bash
npm install -g @angular/cli@8                    # Install the Angular CLI
npm install @angular/material@8 @angular/cdk@8   # Install the Angular Material component library (optional, for UI)
ng new my-application --routing=true --style=css # Generate a new Angular app
npm install msal @azure/msal-angular             # Install MSAL and MSAL Angular in your application
ng generate component page-name                  # To add a new page (such as a home or profile page)
```

## <a name="register-your-application"></a>Registar a sua aplicação

Siga as [instruções para registar uma aplicação de uma página única](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration) no portal Azure.

Na página de **visão geral** da sua inscrição, note o valor de ID da **Aplicação (cliente)** para posterior utilização.

Registe o seu **http://localhost:4200/** valor **URI redirecionamento** e ative as definições implícitas de subvenção.

## <a name="configure-the-application"></a>Configurar a aplicação

1. Na pasta *src/app,* editar *app.module.ts* e adicionar `MSALModule` a, `imports` bem como a `isIE` constante:

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

    |Nome do valor|Acerca de|
    |---------|---------|
    |Enter_the_Application_Id_Here|Na página **de síntese** do registo da sua candidatura, este é o seu valor de ID de **Aplicação (cliente).** |
    |Enter_the_Cloud_Instance_Id_Here|Este é o caso da nuvem Azure. Para a nuvem azure principal **https://login.microsoftonline.com**ou global, entre . Para as nuvens nacionais (por exemplo, China), veja [as nuvens nacionais.](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)|
    |Enter_the_Tenant_Info_Here| Definir para uma das seguintes opções: Se a sua candidatura apoiar *contas neste diretório organizacional,* substitua este valor pelo nome de ID de diretório (inquilino) ou nome de inquilino (por exemplo, **contoso.microsoft.com).** Se a sua aplicação apoiar *contas em qualquer diretório organizacional,* substitua este valor por **organizações.** Se a sua aplicação suportar *contas em qualquer diretório organizacional e contas pessoais*da Microsoft, substitua este valor por **comum**. Para restringir o suporte apenas às *contas pessoais*da Microsoft, substitua este valor pelos **consumidores.** |
    |Enter_the_Redirect_Uri_Here|Substitua **http://localhost:4200**por .|

    Para obter mais informações sobre as opções configuráveis disponíveis, consulte [Inicialize as aplicações do cliente.](msal-js-initializing-client-applications.md)

2. No topo do mesmo ficheiro, adicione a seguinte declaração de importação:

    ```javascript
    import { MsalModule, MsalInterceptor } from '@azure/msal-angular';
    ```

3. Adicione as seguintes declarações `src/app/app.component.ts`de importação ao topo de:

    ```javascript
    import { MsalService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';
    ```
## <a name="sign-in-a-user"></a>Inscreva-se num utilizador

Adicione o seguinte `AppComponent` código para iniciar sessão num utilizador:

```javascript
export class AppComponent implements OnInit {
    constructor(private broadcastService: BroadcastService, private authService: MsalService) { }

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
> Recomendamos `loginRedirect` a utilização para utilizadores do Internet Explorer.

## <a name="acquire-a-token"></a>Adquirir um token

### <a name="angular-interceptor"></a>Angular Interceptor

A MSAL Angular `Interceptor` fornece uma classe que adquire automaticamente fichas para `http` pedidos de saída que utilizam o cliente Angular para recursos protegidos conhecidos.

Em primeiro `Interceptor` lugar, inclua a classe como fornecedor a sua aplicação:

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

Em seguida, forneça um mapa `MsalModule.forRoot()` `protectedResourceMap` dos recursos protegidos `consentScopes`para incluir esses âmbitos em:

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

Por fim, recupere o perfil de um utilizador com um pedido HTTP:

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
A MSAL utiliza três métodos `acquireTokenRedirect` `acquireTokenPopup`para `acquireTokenSilent`adquirir fichas: , e . No entanto, `MsalInterceptor` recomendamos a utilização da classe em vez de aplicações Angulares, como mostra a secção anterior.

#### <a name="get-a-user-token-silently"></a>Obter um token de utilizador automaticamente

O `acquireTokenSilent` método lida com aquisições simbólicas e renovação sem interação do utilizador. Após `loginRedirect` a `loginPopup` execução ou método pela `acquireTokenSilent` primeira vez, é comumente usado para obter fichas usadas para aceder a recursos protegidos em chamadas posteriores. Chamadas para solicitar ou renovar fichas são feitas silenciosamente.

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

Nesse código, `scopes` os âmbitos que estão a ser solicitados para serem devolvidos no sinal de acesso à API.

Por exemplo:

* `["user.read"]`para o Microsoft Graph
* `["<Application ID URL>/scope"]`para APIs web personalizados `api://<Application ID>/access_as_user`(isto é, )

#### <a name="get-a-user-token-interactively"></a>Obter um token de utilizador interativamente

Por vezes, é necessário que o utilizador interaja com o ponto final da plataforma de identidade da Microsoft. Por exemplo:

* Os utilizadores podem ter de reintroduzir as suas credenciais porque a sua palavra-passe expirou.
* A sua aplicação está a solicitar o acesso a âmbitos de recursos adicionais que o utilizador necessita de consentir.
* É necessária autenticação de dois fatores.

O padrão recomendado para a `acquireTokenSilent` maioria das aplicações é ligar `acquireTokenPopup` primeiro, depois pegar a exceção, e depois ligar (ou) `acquireTokenRedirect`para iniciar um pedido interativo.

A `acquireTokenPopup` chamada resulta numa janela pop-up. Em alternativa, `acquireTokenRedirect` redireciona os utilizadores para o ponto final da plataforma de identidade da Microsoft. Nessa janela, os utilizadores precisam de confirmar as suas credenciais, dar o seu consentimento ao recurso necessário ou concluir a autenticação de dois fatores.

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
> Este quickstart `loginRedirect` usa `acquireTokenRedirect` os métodos e métodos com o Microsoft Internet Explorer devido a um [problema conhecido](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) relacionado com o manuseamento de janelas pop-up pelo Internet Explorer.

## <a name="log-out"></a>Iniciar sessão

Adicione o seguinte código para iniciar sessão com um utilizador:

```javascript
logout() {
  this.authService.logout();
}
```

## <a name="add-ui"></a>Adicionar UI
Para um exemplo de como adicionar UI utilizando a biblioteca de componentes de Materiais Angulares, consulte a [aplicação](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular)da amostra .

## <a name="test-your-code"></a>Teste o seu código

1.  Inicie o servidor web para ouvir a porta executando os seguintes comandos numa solicitação de linha de comando a partir da pasta de aplicação:

    ```bash
    npm install
    npm start
    ```
1. No seu navegador, entre **http://localhost:4200** ou **http://localhost:{port}**, onde a *porta* é a porta que o seu servidor web está a ouvir.


### <a name="provide-consent-for-application-access"></a>Fornecer consentimento para acesso à aplicação

A primeira vez que começa a iniciar o início da sua inscrição, é-lhe pedido que lhe conceda acesso ao seu perfil e permita que o inscreva:

![A janela "Permissões solicitadas"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

## <a name="add-scopes-and-delegated-permissions"></a>Adicionar âmbitos e permissões delegadas

O Microsoft Graph API requer que o *utilizador.leia* o âmbito para ler o perfil de um utilizador. Por predefinição, este âmbito é adicionado automaticamente em todas as aplicações registadas no portal de registo. Outras APIs para o Microsoft Graph, bem como APIs personalizadas para o seu servidor back-end, podem requerer âmbitos adicionais. Por exemplo, a Microsoft Graph API requer os *Calendários.Leia* o âmbito para listar os calendários do utilizador.

Para aceder aos calendários do utilizador no contexto de uma aplicação, adicione os *Calendários.Leia* a permissão delegada à informação de registo de aplicações. Em seguida, adicione os *Calendários.Leia* o âmbito da `acquireTokenSilent` chamada.

>[!NOTE]
>O utilizador pode ser solicitado para obter consentimentos adicionais à medida que aumenta o número de âmbitos.

Se uma API de back-end não necessitar de um âmbito (não recomendado), pode utilizar o *clienteId* como âmbito nas chamadas para adquirir fichas.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

Em seguida, aprenda a assinar um utilizador e adquira fichas no tutorial angular:

> [!div class="nextstepaction"]
> [Tutorial angular](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-angular)
