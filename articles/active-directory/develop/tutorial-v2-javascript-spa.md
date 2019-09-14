---
title: Instalação guiada do aplicativo de página única (SPA) do JavaScript do Azure AD v 2.0 | Microsoft Docs
description: Como os aplicativos JavaScript SPA podem chamar uma API que exige tokens de acesso por Azure Active Directory ponto de extremidade v 2.0
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61790954393923bbf330ad3a534d1d33d1a44bbc
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983485"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Conectar usuários e chamar a API de Microsoft Graph de um aplicativo de página única do JavaScript (SPA)

Este guia demonstra como um aplicativo de página única (SPA) JavaScript pode:
- Entrar em contas pessoais, bem como contas corporativas e de estudante 
- Adquirir um token de acesso
- Chamar a API de Microsoft Graph ou outras APIs que exigem tokens de acesso do *ponto de extremidade da plataforma Microsoft Identity*

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como o aplicativo de exemplo gerado por este guia funciona

![Mostra como o aplicativo de exemplo gerado por este tutorial funciona](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Mais informações

O aplicativo de exemplo criado por este guia permite que um SPA JavaScript consulte a API de Microsoft Graph ou uma API Web que aceita tokens do ponto de extremidade da plataforma Microsoft Identity. Nesse cenário, depois que um usuário entra, um token de acesso é solicitado e adicionado às solicitações HTTP por meio do cabeçalho de autorização. A aquisição e a renovação de tokens são manipuladas pela MSAL (biblioteca de autenticação da Microsoft).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliotecas

Este guia usa a biblioteca a seguir:

|Biblioteca|Descrição|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Visualização da biblioteca de autenticação da Microsoft para JavaScript|

> [!NOTE]
> O *MSAL. js* tem como alvo o ponto de extremidade da plataforma Microsoft Identity, que permite contas pessoais e contas corporativas e de estudante para entrar e adquirir tokens. O ponto de extremidade da plataforma de identidade da Microsoft tem [algumas limitações](azure-ad-endpoint-comparison.md#limitations).
> Para entender as diferenças entre os pontos de extremidade v 1.0 e v 2.0, consulte o [Guia de comparação do ponto de extremidades](azure-ad-endpoint-comparison.md).

<!--end-collapse-->

## <a name="set-up-your-web-server-or-project"></a>Configurar seu servidor Web ou projeto

> Prefere baixar este projeto de exemplo? Efetue um dos seguintes procedimentos:
> 
> - Para executar o projeto usando um servidor Web local, como node. js, [Baixe os arquivos de projeto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> - Adicional Para executar o projeto usando o servidor do Microsoft Serviços de Informações da Internet (IIS), [Baixe o projeto do Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).
>
> Para configurar o exemplo de código antes de executá-lo, pule para a [etapa de configuração](#register-your-application).

## <a name="prerequisites"></a>Pré-requisitos

* Para executar este tutorial, você precisa de um servidor Web local, como o [node. js](https://nodejs.org/en/download/), o [.NET Core](https://www.microsoft.com/net/core)ou a integração do IIS Express com o [Visual Studio 2017](https://www.visualstudio.com/downloads/).

* Se você estiver usando o Node. js para executar o projeto, instale um IDE (ambiente de desenvolvimento integrado), como [Visual Studio Code](https://code.visualstudio.com/download), para editar os arquivos de projeto.

* As instruções neste guia se baseiam no node. js e no Visual Studio 2017, mas você pode usar qualquer outro ambiente de desenvolvimento ou servidor Web.

## <a name="create-your-project"></a>Criar seu projeto

> ### <a name="option-1-nodejs-or-other-web-servers"></a>Opção 1: Node. js ou outros servidores Web
> Verifique se você tem o [node. js](https://nodejs.org/en/download/) instalado e, em seguida, crie uma pasta para hospedar seu aplicativo.
>
> ### <a name="option-2-visual-studio"></a>Opção 2: Visual Studio
> Se você estiver usando o Visual Studio e estiver criando um novo projeto, siga estas etapas:
> 1. No Visual Studio, selecione **arquivo** > **novo** > **projeto**.
> 1. Em **Visual C#\Web**,selecione **Aplicação Web ASP.NET (.NET Framework)** .
> 1. Insira um nome para seu aplicativo e, em seguida, selecione **OK**.
> 1. Em **novo aplicativo Web ASP.net**, selecione **vazio**.

## <a name="create-the-spa-ui"></a>Criar a interface do usuário do SPA
1. Crie um arquivo *index. html* para o seu JavaScript Spa. Se você estiver usando o Visual Studio, selecione o projeto (pasta raiz do projeto). Clique com o botão direito do mouse e selecione **Adicionar** > **novo item** > **HTML página**e nomeie o arquivo *index. html*.

1. No arquivo *index. html* , adicione o seguinte código:

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```

   > [!TIP]
   > Você pode substituir a versão do MSAL. js no script anterior pela versão lançada mais recente em [versões do MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Usar a MSAL (biblioteca de autenticação da Microsoft) para conectar o usuário

Adicione o seguinte código ao seu `index.html` arquivo dentro das `<script></script>` marcas:

   ```JavaScript
   var msalConfig = {
       auth: {
           clientId: "Enter_the_Application_Id_here",
           authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
       },
       cache: {
           cacheLocation: "localStorage",
           storeAuthStateInCookie: true
       }
   };

   var graphConfig = {
       graphMeEndpoint: "https://graph.microsoft.com/v1.0/me"
   };

   // this can be used for login or token request, however in more complex situations
   // this can have diverging options
   var requestObj = {
        scopes: ["user.read"]
   };

   var myMSALObj = new Msal.UserAgentApplication(msalConfig);
   // Register Callbacks for redirect flow
   myMSALObj.handleRedirectCallback(authRedirectCallBack);


   function signIn() {

       myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
           //Login Success
           showWelcomeMessage();
           acquireTokenPopupAndCallMSGraph();
       }).catch(function (error) {
           console.log(error);
       });
   }

   function acquireTokenPopupAndCallMSGraph() {
       //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
       myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
       }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenPopup(popup window)
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                    callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
                }).catch(function (error) {
                    console.log(error);
                });
            }
       });
   }


   function graphAPICallback(data) {
       document.getElementById("json").innerHTML = JSON.stringify(data, null, 2);
   }


   function showWelcomeMessage() {
       var divWelcome = document.getElementById('WelcomeMessage');
       divWelcome.innerHTML = 'Welcome ' + myMSALObj.getAccount().userName + "to Microsoft Graph API";
       var loginbutton = document.getElementById('SignIn');
       loginbutton.innerHTML = 'Sign Out';
       loginbutton.setAttribute('onclick', 'signOut();');
   }


   //This function can be removed if you do not need to support IE
   function acquireTokenRedirectAndCallMSGraph() {
        //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
        myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
        }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenRedirect
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenRedirect(requestObj);
            }
        });
   }


   function authRedirectCallBack(error, response) {
       if (error) {
           console.log(error);
       }
       else {
           if (response.tokenType === "access_token") {
               callMSGraph(graphConfig.graphEndpoint, response.accessToken, graphAPICallback);
           } else {
               console.log("token type is:" + response.tokenType);
           }
       }
   }

   function requiresInteraction(errorCode) {
       if (!errorCode || !errorCode.length) {
           return false;
       }
       return errorCode === "consent_required" ||
           errorCode === "interaction_required" ||
           errorCode === "login_required";
   }

   // Browser check variables
   var ua = window.navigator.userAgent;
   var msie = ua.indexOf('MSIE ');
   var msie11 = ua.indexOf('Trident/');
   var msedge = ua.indexOf('Edge/');
   var isIE = msie > 0 || msie11 > 0;
   var isEdge = msedge > 0;
   //If you support IE, our recommendation is that you sign-in using Redirect APIs
   //If you as a developer are testing using Edge InPrivate mode, please add "isEdge" to the if check
   // can change this to default an experience outside browser use
   var loginType = isIE ? "REDIRECT" : "POPUP";

   if (loginType === 'POPUP') {
        if (myMSALObj.getAccount()) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }
   }
   else if (loginType === 'REDIRECT') {
       document.getElementById("SignIn").onclick = function () {
            myMSALObj.loginRedirect(requestObj);
       };
       if (myMSALObj.getAccount() && !myMSALObj.isCallback(window.location.hash)) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenRedirectAndCallMSGraph();
        }
   } else {
       console.error('Please set a valid login type');
   }
   ```

<!--start-collapse-->
### <a name="more-information"></a>Mais informações

Depois que um usuário seleciona o botão **entrar** pela primeira vez, o `signIn` método chama `loginPopup` para entrar no usuário. Esse método abre uma janela pop-up com o *ponto de extremidade da plataforma Microsoft Identity* para solicitar e validar as credenciais do usuário. Após uma entrada bem-sucedida, o usuário é Redirecionado de volta para a página *index. html* original. Um token é recebido, processado pelo `msal.js`e as informações contidas no token são armazenadas em cache. Esse token é conhecido como o *token de ID* e contém informações básicas sobre o usuário, como o nome de exibição do usuário. Se você planeja usar todos os dados fornecidos por esse token para qualquer finalidade, você precisará certificar-se de que esse token seja validado pelo servidor de back-end para garantir que o token foi emitido para um usuário válido para seu aplicativo.

O Spa gerado por este guia chama `acquireTokenSilent` e/ou `acquireTokenPopup` para adquirir um *token de acesso* usado para consultar a API de Microsoft Graph para obter informações de perfil de usuário. Se você precisar de um exemplo que valida o token de ID, dê uma olhada [nesse]aplicativo de exemplo de exemplo do(https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub Active-Directory-JavaScript-singlepageapp-dotnet-webAPI-v2") no github. O exemplo usa um ASP.NET Web API para validação de token.

#### <a name="getting-a-user-token-interactively"></a>Obter um token de utilizador interativamente

Após a entrada inicial, você não deseja solicitar que os usuários façam a autenticação novamente sempre que precisarem solicitar um token para acessar um recurso. Portanto, *acquireTokenSilent* deve ser usado na maior parte do tempo para adquirir tokens. No entanto, há situações em que você precisa forçar os usuários a interagir com o ponto de extremidade da plataforma de identidade da Microsoft. Os exemplos incluem:

- Os usuários precisam inserir novamente suas credenciais porque a senha expirou.
- Seu aplicativo está solicitando acesso a um recurso e você precisa do consentimento do usuário.
- A autenticação de dois fatores é necessária.

Chamar *acquireTokenPopup* abre uma janela pop-up (ou *acquireTokenRedirect* redireciona os usuários para o ponto de extremidade da plataforma Microsoft Identity). Nessa janela, os usuários precisam interagir confirmando suas credenciais, dando consentimento ao recurso necessário ou concluindo a autenticação de dois fatores.

#### <a name="getting-a-user-token-silently"></a>Obter um token de utilizador automaticamente

O `acquireTokenSilent` método manipula a aquisição e a renovação de tokens sem qualquer interação do usuário. Após `loginPopup` (ou `loginRedirect`) ser executado pela primeira vez, `acquireTokenSilent` é o método normalmente usado para obter tokens usados para acessar recursos protegidos para chamadas subsequentes. (As chamadas para solicitar ou renovar tokens são feitas silenciosamente.) `acquireTokenSilent` pode falhar em alguns casos. Por exemplo, a senha do usuário pode ter expirado. Seu aplicativo pode manipular essa exceção de duas maneiras:

1. Faça uma chamada para `acquireTokenPopup` imediatamente, que dispara um prompt de entrada do usuário. Esse padrão é comumente usado em aplicativos online em que não há nenhum conteúdo não autenticado no aplicativo disponível para o usuário. O exemplo gerado por essa instalação interativa usa esse padrão.

2. Os aplicativos também podem fazer uma indicação visual para o usuário de que uma entrada interativa é necessária e, portanto, o usuário pode selecionar o horário certo para entrar ou o aplicativo pode tentar `acquireTokenSilent` novamente mais tarde. Isso é normalmente usado quando o usuário pode usar outra funcionalidade do aplicativo sem ser interrompido. Por exemplo, pode haver conteúdo não autenticado disponível no aplicativo. Nessa situação, o usuário pode decidir quando deseja entrar para acessar o recurso protegido ou para atualizar as informações desatualizadas.

> [!NOTE]
> Este guia de início `loginRedirect` rápido `acquireTokenRedirect` usa os métodos e quando o Internet Explorer é usado pelo navegador. Seguimos essa prática devido a um [problema conhecido](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) relacionado à maneira como o Internet Explorer lida com janelas pop-up.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Chamar a API de Microsoft Graph usando o token que você acabou de adquirir

Adicione o seguinte código ao seu `index.html` arquivo dentro das `<script></script>` marcas:

```javascript
function callMSGraph(theUrl, accessToken, callback) {
    var xmlHttp = new XMLHttpRequest();
    xmlHttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200)
            callback(JSON.parse(this.responseText));
    }
    xmlHttp.open("GET", theUrl, true); // true for asynchronous
    xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
    xmlHttp.send();
}
```
<!--start-collapse-->

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Mais informações sobre como fazer uma chamada REST em uma API protegida

No aplicativo de exemplo criado por este guia, o `callMSGraph()` método é usado para fazer uma solicitação `GET` http em um recurso protegido que requer um token. Em seguida, a solicitação retorna o conteúdo para o chamador. Esse método adiciona o token adquirido no *cabeçalho de autorização http*. Para o aplicativo de exemplo criado por este guia, o recurso é o Microsoft Graph ponto de extremidade do API *me* , que exibe as informações de perfil do usuário.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Adicionar um método para desconectar o usuário

Adicione o seguinte código ao seu `index.html` arquivo dentro das `<script></script>` marcas:

```javascript
/**
 * Sign out the user
 */
 function signOut() {
     myMSALObj.logout();
 }
```

## <a name="register-your-application"></a>Registar a sua aplicação

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Se sua conta fornece acesso a mais de um locatário, selecione a conta no canto superior direito e, em seguida, defina a sessão do portal para o locatário do Azure AD que você deseja usar.
1. Acesse a página da plataforma de identidade da Microsoft para desenvolvedores [registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Quando a página **registrar um aplicativo** for exibida, insira um nome para seu aplicativo.
1. Em **tipos de conta com suporte**, selecione **contas em qualquer diretório organizacional e contas pessoais da Microsoft**.
1. Na seção **URI de redirecionamento** , selecione a plataforma **Web** na lista suspensa e defina o valor para a URL do aplicativo que se baseia em seu servidor Web.

   Para obter informações sobre como definir e obter a URL de redirecionamento para o Node. js e o Visual Studio, consulte a seção "definir uma URL de redirecionamento para o Node. js" e [definir uma URL de redirecionamento para o Visual Studio](#set-a-redirect-url-for-visual-studio).

1. Selecione **Registar**.
1. Na página **visão geral** do aplicativo, observe o valor da **ID do aplicativo (cliente)** para uso posterior.
1. Este início rápido requer que o [fluxo de concessão implícita](v2-oauth2-implicit-grant-flow.md) esteja habilitado. No painel esquerdo do aplicativo registrado, selecione **autenticação**.
1. Em **Configurações avançadas**, em **concessão implícita**, marque as caixas de seleção **tokens de ID** e **tokens de acesso** . Tokens de ID e tokens de acesso são necessários porque esse aplicativo deve conectar usuários e chamar uma API.
1. Selecione **Guardar**.

> #### <a name="set-a-redirect-url-for-nodejs"></a>Definir uma URL de redirecionamento para node. js
> Para node. js, você pode definir a porta do servidor Web no arquivo *Server. js* . Este tutorial usa a porta 30662, mas você pode usar qualquer outra porta disponível.
>
> Para configurar uma URL de redirecionamento nas informações de registro do aplicativo, alterne de volta para o painel de **registro do aplicativo** e siga um destes procedimentos:
>
> - Defina *`http://localhost:30662/`* como a **URL de redirecionamento**.
> - Se você estiver usando uma porta TCP personalizada, use *`http://localhost:<port>/`* (onde  *\<a porta >* é o número da porta TCP personalizada).
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Definir uma URL de redirecionamento para o Visual Studio
> Para obter a URL de redirecionamento para o Visual Studio, siga estas etapas:
> 1. Em Gerenciador de Soluções, selecione o projeto.
>
>    A janela **Propriedades** é aberta. Se não estiver, pressione F4.
>
>    ![O projeto JavaScriptSPA janela Propriedades](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. Copie o valor da **URL** .
> 1. Volte para o painel de **registro do aplicativo** e cole o valor copiado como a URL de **redirecionamento**.

#### <a name="configure-your-javascript-spa"></a>Configurar seu JavaScript SPA

1. No arquivo *index. html* que você criou durante a configuração do projeto, adicione as informações de registro do aplicativo. Na parte superior do arquivo, dentro das `<script></script>` marcas, adicione o seguinte código:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Em que:
    - Enter_the_Application_Id_here > é a **ID do aplicativo (cliente)** para o aplicativo que você registrou.  *\<*
    - Enter_the_Tenant_info_here > é definido como uma das seguintes opções:  *\<*
       - Se seu aplicativo der suporte a *contas nesse diretório organizacional*, substitua esse valor pela **ID do locatário** ou pelo **nome do locatário** (por exemplo, *contoso.Microsoft.com*).
       - Se seu aplicativo oferecer suporte a *contas em qualquer diretório organizacional*, substitua esse valor pelas **organizações**.
       - Se seu aplicativo oferecer suporte a *contas em qualquer diretório organizacional e contas pessoais da Microsoft*, substitua esse valor por **comum**. Para restringir o suporte *somente a contas pessoais da Microsoft*, substitua esse valor por **consumidores**.

## <a name="test-your-code"></a>Testar seu código

Teste seu código usando qualquer um dos ambientes a seguir.

### <a name="test-with-nodejs"></a>Teste com node. js

Se você não estiver usando o Visual Studio, verifique se o servidor Web foi iniciado.

1. Configure o servidor para escutar uma porta TCP com base no local do arquivo *index. html* . Para o Node. js, inicie o servidor Web para escutar a porta executando os seguintes comandos em um prompt de linha de comando da pasta do aplicativo:

    ```bash
    npm install
    node server.js
    ```
1. No navegador, insira **http://\<span\<>/span > localhost: 30662** ou **http://\<span >\</span > localhost: {Port}** , em que *Port* é a porta que o servidor Web está ouvindo. Você deve ver o conteúdo do arquivo *index. html* e o botão **entrar** .

### <a name="test-with-visual-studio"></a>Testar com o Visual Studio

Se você estiver usando o Visual Studio, selecione a solução de projeto e, em seguida, pressione F5 para executar seu projeto. O navegador é aberto no local<span></span>http://localhost: {Port} e o botão **entrar** deve estar visível.

## <a name="test-your-application"></a>Testar seu aplicativo

Depois que o navegador carregar o arquivo *index. html* , selecione **entrar**. Você será solicitado a entrar com o ponto de extremidade da plataforma de identidade da Microsoft:

![A janela de entrada da conta SPA do JavaScript](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Fornecer consentimento para acesso ao aplicativo

Na primeira vez que você entrar em seu aplicativo, você será solicitado a conceder acesso ao seu perfil e conectá-lo:

![A janela "permissões solicitadas"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Exibir resultados do aplicativo

Depois de entrar, suas informações de perfil de usuário são retornadas na Microsoft Graph resposta da API exibida:

![Resultados da chamada à API de Microsoft Graph](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre escopos e permissões delegadas

A API de Microsoft Graph requer que o escopo *User. Read* Leia o perfil de um usuário. Por padrão, esse escopo é adicionado automaticamente em cada aplicativo registrado no portal de registro. Outras APIs para Microsoft Graph, bem como APIs personalizadas para seu servidor back-end, podem exigir escopos adicionais. Por exemplo, a API de Microsoft Graph requer o escopo *calendários. Read* para listar os calendários do usuário.

Para acessar os calendários do usuário no contexto de um aplicativo, adicione a permissão delegada *calendários. Read* para as informações de registro do aplicativo. Em seguida, adicione o escopo *calendários. Read* à `acquireTokenSilent` chamada.

>[!NOTE]
>O usuário pode ser solicitado a fornecer consentirs adicionais à medida que você aumenta o número de escopos.

Se uma API de back-end não exigir um escopo (não recomendado), você poderá usar *clientId* como o escopo nas chamadas para adquirir tokens.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Ajude-nos a melhorar a plataforma Microsoft Identity. Diga-nos o que você imagina ao concluir uma pesquisa curta de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa sobre plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
