---
title: Tutorial de aplicação de uma página única JavaScript - plataforma de identidade da Microsoft Azure
description: Como as aplicações javaScript SPA podem chamar uma API que requer acesso a fichas por Azure Ative Directory v2.0 endpoint
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
ms.openlocfilehash: 51a456a05e1d41057e80b92e44f997e1b52cb132
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239127"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Inscreva-se nos utilizadores e ligue para a Microsoft Graph API a partir de uma aplicação de uma página única JavaScript (SPA)

Este guia demonstra como uma aplicação de uma página única javaScript (SPA) pode:
- Inscreva-se em contas pessoais, bem como trabalho e contas escolares 
- Adquirir um token de acesso
- Ligue para a Microsoft Graph API ou outras APIs que requerem fichas de acesso do ponto final da *plataforma de identidade* da Microsoft

>[!NOTE]
> Se for novidade na plataforma de identidade da Microsoft, recomendamos que comece com o [Sign in users e obtenha um sinal de acesso num quickstart javaScript SPA](quickstart-v2-javascript.md).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como funciona a aplicação de amostragerada por este guia

![Mostra como funciona a aplicação de amostragerada por este tutorial](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Mais informações

A aplicação de amostra criada por este guia permite que um JavaScript SPA questione o Microsoft Graph API ou um API web que aceita tokens do ponto final da plataforma de identidade da Microsoft. Neste cenário, após a adesão de um utilizador, é solicitado um sinal de acesso e adicionado aos pedidos http através do cabeçalho de autorização. A aquisição e renovação simbólicas são tratadas pela Microsoft Authentication Library (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliotecas

Este guia utiliza a seguinte biblioteca:

|Biblioteca|Descrição|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Biblioteca de autenticação da Microsoft para pré-visualização javaScript|

> [!NOTE]
> *Msal.js* tem como alvo o ponto final da plataforma de identidade da Microsoft, que permite que contas pessoais e contas escolares e de trabalho entrem e adquiram fichas. O ponto final da plataforma de identidade da Microsoft tem [algumas limitações.](../azuread-dev/azure-ad-endpoint-comparison.md#limitations)
> Para compreender as diferenças entre os pontos finais v1.0 e v2.0, consulte o guia de [comparação de pontos finais](../azuread-dev/azure-ad-endpoint-comparison.md).

<!--end-collapse-->

## <a name="set-up-your-web-server-or-project"></a>Configurar o seu servidor web ou projeto

> Prefere baixar o projeto desta amostra? Efetue um dos seguintes procedimentos:
> 
> - Para executar o projeto utilizando um servidor web local, como node.js, [descarregue os ficheiros do projeto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> - (Opcional) Para executar o projeto utilizando o servidor microsoft Internet Information Services (IIS), [descarregue o projeto Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).
>
> Para configurar a amostra de código antes de executá-la, salte para o [passo de configuração](#register-your-application).

## <a name="prerequisites"></a>Pré-requisitos

* Para executar este tutorial, você precisa de um servidor web local, como [Node.js,](https://nodejs.org/en/download/) [.NET Core](https://www.microsoft.com/net/core), ou integração IIS Express com [visual studio 2017](https://www.visualstudio.com/downloads/).

* Se estiver a usar o Node.js para executar o projeto, instale um ambiente de desenvolvimento integrado (IDE), como o [Visual Studio Code,](https://code.visualstudio.com/download)para editar os ficheiros do projeto.

* As instruções neste guia baseiam-se tanto no Node.js como no Visual Studio 2017, mas pode utilizar qualquer outro ambiente de desenvolvimento ou servidor web.

## <a name="create-your-project"></a>Crie o seu projeto

> ### <a name="option-1-nodejs-or-other-web-servers"></a>Opção 1: Node.js ou outros servidores web
> Certifique-se de que tem [o Node.js](https://nodejs.org/en/download/) instalado e, em seguida, crie uma pasta para hospedar a sua aplicação.
>
> ### <a name="option-2-visual-studio"></a>Opção 2: Estúdio Visual
> Se estiver a usar o Visual Studio e estiver a criar um novo projeto, siga estes passos:
> 1. No Estúdio Visual, selecione **File** > **New** > **Project**.
> 1. Em **Visual C#\Web**,selecione **Aplicação Web ASP.NET (.NET Framework)** .
> 1. Introduza um nome para a sua aplicação e, em seguida, selecione **OK**.
> 1. Em **Nova ASP.NET Aplicação Web,** selecione **Empty**.

## <a name="create-the-spa-ui"></a>Criar o SPA UI
1. Crie um ficheiro *index.html* para o seu JavaScript SPA. Se estiver a utilizar o Visual Studio, selecione o projeto (pasta raiz do projeto). Clique à direita e selecione **Adicionar** > **Novo Item** > **página HTML**, e nomeie o índice de *ficheiros.html*.

1. No ficheiro *index.html,* adicione o seguinte código:

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
   > Pode substituir a versão de MSAL.js no script anterior pela versão mais recente lançada em [versão MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Utilize a Biblioteca de Autenticação da Microsoft (MSAL) para iniciar sessão no utilizador

Adicione o seguinte código ao seu ficheiro `index.html` dentro das etiquetas `<script></script>`:

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

Depois de um utilizador selecionar o botão **Iniciar** sessão pela primeira vez, o método `signIn` chama `loginPopup` para assinar no utilizador. Este método abre uma janela pop-up com o ponto final da *plataforma de identidade da Microsoft* para solicitar e validar as credenciais do utilizador. Após um inserido bem sucedido, o utilizador é redirecionado para a página original *do índice.html.* Um símbolo é recebido, processado por `msal.js`, e a informação contida no token é emcached. Esta ficha é conhecida como ficha *de identificação* e contém informações básicas sobre o utilizador, como o nome do utilizador. Se planeia utilizar quaisquer dados fornecidos por este token para qualquer finalidade, tem de se certificar de que este token é validado pelo seu servidor backend para garantir que o token foi emitido a um utilizador válido para a sua aplicação.

O SPA gerado por este guia chama `acquireTokenSilent` e/ou `acquireTokenPopup` para adquirir um *token* de acesso usado para consultar a API do Microsoft Graph para obter informações sobre o perfil do utilizador. Se precisar de uma amostra que valide o símbolo de identificação, veja [esta](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub active-directy-javascript-singlepageapp-dotnet-webapi-v2") aplicação de amostra no GitHub. A amostra utiliza uma ASP.NET Web API para validação de tokens.

#### <a name="getting-a-user-token-interactively"></a>Obter um token de utilizador interativamente

Após o início do início, não pretende pedir aos utilizadores que se reatentem sempre que precisam de solicitar um sinal de acesso a um recurso. Assim, *adquirirTokenSilent* deve ser usado a maior parte do tempo para adquirir fichas. Existem, no entanto, situações em que é necessário forçar os utilizadores a interagir com o ponto final da plataforma de identidade da Microsoft. Os exemplos incluem:

- Os utilizadores precisam de reintroduzir as suas credenciais porque a palavra-passe expirou.
- A sua aplicação está a solicitar acesso a um recurso e precisa do consentimento do utilizador.
- É necessária autenticação de dois fatores.

A *chamada adquiridaTokenPopup* abre uma janela pop-up (ou *adquireTokenRedirect redireciona* os utilizadores para o ponto final da plataforma de identidade da Microsoft). Nessa janela, os utilizadores precisam de interagir confirmando as suas credenciais, dando consentimento ao recurso necessário, ou completando a autenticação de dois fatores.

#### <a name="getting-a-user-token-silently"></a>Obter um token de utilizador automaticamente

O método `acquireTokenSilent` trata da aquisição e renovação simbólicas sem qualquer interação do utilizador. Após `loginPopup` (ou `loginRedirect`) é executado pela primeira vez, `acquireTokenSilent` é o método comumente usado para obter fichas usadas para aceder a recursos protegidos para chamadas subsequentes. (As chamadas para solicitar ou renovar fichas são feitas silenciosamente.) `acquireTokenSilent` pode falhar em alguns casos. Por exemplo, a palavra-passe do utilizador pode ter expirado. A sua aplicação pode lidar com esta exceção de duas formas:

1. Faça uma chamada para `acquireTokenPopup` imediatamente, o que aciona um pedido de entrada do utilizador. Este padrão é comumente utilizado em aplicações online onde não existe conteúdo não autenticado na aplicação disponível para o utilizador. A amostra gerada por esta configuração guiada utiliza este padrão.

2. As aplicações também podem indicar ao utilizador que é necessário um sinal de inscrição interativo, para que o utilizador possa selecionar o momento certo para iniciar sessão, ou a aplicação pode voltar a tentar `acquireTokenSilent` mais tarde. Isto é comumente usado quando o utilizador pode usar outra funcionalidade da aplicação sem ser interrompido. Por exemplo, pode haver conteúdo não autenticado disponível na aplicação. Nesta situação, o utilizador pode decidir quando pretende iniciar sessão para aceder ao recurso protegido ou atualizar a informação desatualizada.

> [!NOTE]
> Este quickstart utiliza os métodos `loginRedirect` e `acquireTokenRedirect` quando o Internet Explorer é o navegador utilizado. Acompanhamos esta prática devido a um [problema conhecido](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) relacionado com a forma como o Internet Explorer lida com as janelas pop-up.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Ligue para a Microsoft Graph API usando o símbolo que acabou de adquirir

Adicione o seguinte código ao seu ficheiro `index.html` dentro das etiquetas `<script></script>`:

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

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Mais informações sobre fazer uma chamada rest contra uma API protegida

Na aplicação da amostra criada por este guia, o método `callMSGraph()` é utilizado para fazer um pedido de `GET` HTTP contra um recurso protegido que requer um símbolo. O pedido devolve então o conteúdo ao chamador. Este método adiciona o token adquirido no *cabeçalho*de autorização http . Para a aplicação de amostra criada por este guia, o recurso é o *ponto* final do Microsoft Graph API Me, que exibe as informações de perfil do utilizador.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Adicione um método para assinar o utilizador

Adicione o seguinte código ao seu ficheiro `index.html` dentro das etiquetas `<script></script>`:

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

1. Se a sua conta lhe der acesso a mais de um inquilino, selecione a conta no canto superior direito e, em seguida, marque a sua sessão do portal para o inquilino da AD Azure que pretende utilizar.
1. Vá à plataforma de identidade da Microsoft para programar página de registos de [Aplicações.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. Quando aparecer uma página de **inscrição,** insira um nome para a sua candidatura.
1. Nos tipos de **conta suportados,** selecione **Contas em qualquer diretório organizacional e contas pessoais**da Microsoft .
1. Na secção **Redirecionamento URI,** selecione a plataforma **Web** a partir da lista de drop-down e, em seguida, delineie o valor para o URL da aplicação que é baseado no seu servidor web.

   Para obter informações sobre como definir e obter o URL de redirecionamento para Node.js e Visual Studio, consulte a seguinte secção "Delineie um URL de redirecionamento para Node.js" e [delineie um URL de redirecionamento para O Estúdio Visual](#set-a-redirect-url-for-visual-studio).

1. Selecione **Registar**.
1. Na página de **visão geral** da aplicação, note o valor de ID da **Aplicação (cliente)** para posterior utilização.
1. Este arranque rápido requer que o fluxo de [subvenção implícita](v2-oauth2-implicit-grant-flow.md) seja ativado. No painel esquerdo da aplicação registada, **selecione Autenticação**.
1. Em **configurações avançadas**, sob **a subvenção implícita,** selecione as **fichas de identificação** e as **fichas** de acesso caixas de verificação. São necessários tokens de identificação e fichas de acesso porque esta aplicação deve inscrever os utilizadores e chamar uma API.
1. Selecione **Guardar**.

> #### <a name="set-a-redirect-url-for-nodejs"></a>Delineie um URL de redirecionamento para Node.js
> Para node.js, pode definir a porta do servidor web no ficheiro *server.js.* Este tutorial utiliza a porta 30662, mas pode utilizar qualquer outra porta disponível.
>
> Para configurar um URL redireccionador nas informações de registo de aplicação, volte para o painel de registo de **aplicações** e faça qualquer um dos seguintes:
>
> - *Delineie`http://localhost:30662/`* como URL **de redirecionamento**.
> - Se estiver a utilizar uma porta TCP personalizada, utilize *`http://localhost:<port>/`* (onde *\<port>* é o número de porta TCP personalizado).
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Delineie um URL de redirecionamento para o Estúdio Visual
> Para obter o URL de redirecionamento para O Estúdio Visual, siga estes passos:
> 1. No Solution Explorer, selecione o projeto.
>
>    A janela **Properties** abre. Se não, prima F4.
>
>    ![A janela JavaScriptSPA Project Properties](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. Copie o valor **url.**
> 1. Volte para o painel de registo de **aplicações** e colhe o valor copiado como **URL de redirecionamento**.

#### <a name="configure-your-javascript-spa"></a>Configure o seu JavaScript SPA

1. No ficheiro *index.html* que criou durante a configuração do projeto, adicione as informações de registo de aplicação. Na parte superior do ficheiro, dentro das etiquetas `<script></script>`, adicione o seguinte código:

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
    - *\<Enter_the_Application_Id_here>* é o ID de **Aplicação (cliente)** para a aplicação que registou.
    - *\<Enter_the_Tenant_info_here>* está definido para uma das seguintes opções:
       - Se a sua candidatura apoiar *Contas neste diretório organizacional,* substitua este valor pelo **nome** de Id do **Arrendatário** (por exemplo, *contoso.microsoft.com).*
       - Se a sua aplicação apoiar *Contas em qualquer diretório organizacional,* substitua este valor por **organizações.**
       - Se a sua aplicação apoiar *contas em qualquer diretório organizacional e contas pessoais*da Microsoft, substitua este valor por **comum**. Para restringir o suporte apenas às *contas pessoais*da Microsoft, substitua este valor pelos **consumidores.**

## <a name="test-your-code"></a>Teste o seu código

Teste o seu código utilizando qualquer um dos seguintes ambientes.

### <a name="test-with-nodejs"></a>Teste com Nó.js

Se não estiver a utilizar o Visual Studio, certifique-se de que o seu servidor web está iniciado.

1. Configure o servidor para ouvir uma porta TCP baseada na localização do seu ficheiro *index.html.* Para node.js, inicie o servidor web para ouvir a porta executando os seguintes comandos numa linha de comando saem da pasta de aplicação:

    ```bash
    npm install
    node server.js
    ```
1. No seu navegador, introduza **http://\<span>\</span>localhost:30662** ou **http://\<span>\</span>localhost:{port}** , onde a *porta* é a porta que o seu servidor web está a ouvir. Deve ver o conteúdo do seu ficheiro *index.html* e o botão **Sign In.**

### <a name="test-with-visual-studio"></a>Teste com Estúdio Visual

Se estiver a utilizar o Visual Studio, selecione a solução do projeto e, em seguida, pressione f5 para executar o seu projeto. O navegador abre-se<span></span>para a localização local http://:{port} e o botão **Sign In** deve ser visível.

## <a name="test-your-application"></a>Teste a sua candidatura

Depois de o navegador carregar o ficheiro *index.html,* selecione 'Iniciar ' **'Sem'selecione Iniciar**o tempo . É-lhe pedido que assine sessão com o ponto final da plataforma de identidade da Microsoft:

![A janela de inscrição da conta JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Fornecer consentimento para acesso à aplicação

A primeira vez que inscreveu a sua candidatura, é-lhe pedido que lhe conceda acesso ao seu perfil e o inscreva:

![A janela "Permissões solicitadas"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Ver resultados da aplicação

Depois de iniciar sessão, as informações do perfil do utilizador são devolvidas na resposta da Microsoft Graph API que é exibida:

![Resultados da chamada da Microsoft Graph API](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre âmbitos e permissões delegadas

O Microsoft Graph API requer que o *utilizador.leia* o âmbito para ler o perfil de um utilizador. Por predefinição, este âmbito é adicionado automaticamente em todas as aplicações registadas no portal de registo. Outras APIs para o Microsoft Graph, bem como APIs personalizadas para o seu servidor back-end, podem requerer âmbitos adicionais. Por exemplo, a Microsoft Graph API requer os *Calendários.Leia* o âmbito para listar os calendários do utilizador.

Para aceder aos calendários do utilizador no contexto de uma aplicação, adicione os *Calendários.Leia* a permissão delegada à informação de registo de aplicações. Em seguida, adicione os *Calendários.Leia* o âmbito à chamada `acquireTokenSilent`.

>[!NOTE]
>O utilizador pode ser solicitado para obter consentimentos adicionais à medida que aumenta o número de âmbitos.

Se uma API de back-end não necessitar de um âmbito (não recomendado), pode utilizar o *clienteId* como âmbito nas chamadas para adquirir fichas.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Ajude-nos a melhorar a plataforma de identidade da Microsoft. Diga-nos o que pensa ao concluir um pequeno inquérito de duas perguntas.

> [!div class="nextstepaction"]
> [Pesquisa da plataforma de identidade da Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
