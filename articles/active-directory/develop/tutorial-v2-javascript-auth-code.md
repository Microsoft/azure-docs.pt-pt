---
title: Tutorial de aplicação de uma página única JavaScript 2.0 - Plataforma de identidade da Microsoft Azure
description: Como as aplicações javaScript SPA podem usar o fluxo de código auth para chamar uma API que requer acesso a fichas por Azure Ative Directory v2.0 endpoint
services: active-directory
documentationcenter: dev-center-name
author: hahamil
manager: CelesteDG
ROBOTS: NOINDEX
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 04/22/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: ee156c8118a67061d0a000867ee64fe1f3ebd18c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82182140"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa---msaljs-20"></a>Inscreva-se nos utilizadores e ligue para a Microsoft Graph API a partir de uma aplicação de uma página única JavaScript (SPA) - MSAL.js 2.0

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspetos desta funcionalidade podem mudar antes da disponibilidade geral (GA).

Este tutorial utiliza uma versão de MSAL.js que utiliza o Fluxo de Código de Autorização OAuth 2.0 com PKCE. Para ler mais sobre este protocolo, bem como as diferenças entre fluxo implícito e fluxo de código de autorização, consulte a [documentação](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow). Se procura um tutorial que utilize o fluxo implícito, consulte o [tutorial MSAL.js v1](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-spa).

Esta versão do MSAL.js melhora na atual biblioteca msal-core, e utiliza o fluxo de código de autorização no navegador. A maioria das funcionalidades disponíveis na antiga biblioteca estão disponíveis nesta versão, mas existem nuances para o fluxo de autenticação em ambos. Esta versão **NÃO** suporta o fluxo implícito.

Este guia demonstra como uma aplicação de uma página única javaScript (SPA) pode:
- Inscreva-se em contas pessoais, bem como trabalho e contas escolares
- Adquirir um token de acesso
- Ligue para a Microsoft Graph API ou outras APIs que requerem fichas de acesso do ponto final da *plataforma de identidade* da Microsoft

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Como funciona a aplicação de amostragerada por este guia

![Mostra como funciona a aplicação de amostragerada por este tutorial](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

### <a name="more-information"></a>Mais informações

A aplicação de amostra criada por este guia permite que um JavaScript SPA questione o Microsoft Graph API ou um API web que aceita tokens do ponto final da plataforma de identidade da Microsoft. Neste cenário, após a adesão de um utilizador, é solicitado um sinal de acesso e adicionado aos pedidos http através do cabeçalho de autorização. A aquisição e renovação simbólicas são tratadas pela Microsoft Authentication Library (MSAL).

### <a name="libraries"></a>Bibliotecas

Este guia utiliza a seguinte biblioteca:

|Biblioteca|Descrição|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)|Biblioteca de Autenticação da Microsoft para pacote de navegador JavaScript|

## <a name="set-up-your-web-server-or-project"></a>Configurar o seu servidor web ou projeto

Prefere baixar o projeto desta amostra? Para executar o projeto utilizando um servidor web local, como node.js, clone os ficheiros do projeto:

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

Para configurar a amostra de código antes de executá-la, salte para o [passo de configuração](#register-your-application).

## <a name="prerequisites"></a>Pré-requisitos

* Para executar este tutorial, precisa de um servidor web local, como [Node.js](https://nodejs.org/en/download/) ou [.NET Core](https://www.microsoft.com/net/core).

* Se estiver a usar o Node.js para executar o projeto, instale um ambiente de desenvolvimento integrado (IDE) como o [Visual Studio Code](https://code.visualstudio.com/download) para editar os ficheiros do projeto.

* As instruções neste tutorial baseiam-se no Node.js.

## <a name="create-your-project"></a>Crie o seu projeto

Certifique-se de que tem [o Node.js](https://nodejs.org/en/download/) instalado e, em seguida, crie uma pasta para hospedar a sua aplicação. Em seguida, [Express](https://expressjs.com/) implemente um pequeno `index.html` servidor web Express para servir o seu ficheiro.

1. Primeiro, navegue para a pasta do projeto no seu terminal e, em seguida, execute os seguintes comandos NPM.
    ```console
    npm init -y
    npm install @azure/msal-Browser
    npm install express
    ```
2. Em seguida, crie um ficheiro .js nomeado *server.js*e, em seguida, adicione o seguinte código:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');
   const argv = require('yargs')
      .usage('Usage: $0 -p [PORT]')
      .alias('p', 'port')
      .describe('port', '(Optional) Port Number - default is 3000')
      .strict()
      .argv;

   const DEFAULT_PORT = 3000;

   //initialize express.
   const app = express();

   // Initialize variables.
   let port = DEFAULT_PORT; // -p {PORT} || 3000;
   if (argv.p) {
      port = argv.p;
   }

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use("/lib", express.static(path.join(__dirname, "../../lib/msal-browser/lib")));

   // Setup app folders
   app.use(express.static('app'));

   // Set up a route for index.html.
   app.get('*', function (req, res) {
      res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log(`Listening on port ${port}...`);
    ```

Agora tem um servidor simples para servir o seu SPA. A estrutura de pasta pretendida no final deste tutorial é a seguinte:

![uma representação de texto da estrutura de pasta SPA pretendida](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>Criar o SPA UI

1. Crie um ficheiro *index.html* para o seu JavaScript SPA na pasta da *aplicação.* Este ficheiro implementa um UI construído com **Bootstrap 4 Framework** e importa ficheiros de script para configuração, autenticação e chamadas API.

   No ficheiro *index.html,* adicione o seguinte código:

   ```html
   <!DOCTYPE html>
   <html lang="en">
      <head>
         <meta charset="UTF-8">
         <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
         <title>Quickstart | MSAL.JS Vanilla JavaScript SPA</title>

         <!-- IE support: add promises polyfill before msal.js  -->
         <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
         <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js"></script>

         <!-- adding Bootstrap 4 for UI components  -->
         <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
         <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
      </head>
      <body>
         <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
            <a class="navbar-brand" href="/">MS Identity Platform</a>
            <div class="btn-group ml-auto dropleft">
               <button type="button" id="SignIn" class="btn btn-secondary" onclick="signIn()">
                  Sign In
               </button>
            </div>
         </nav>
         <br>
         <h5 class="card-header text-center">Vanilla JavaScript SPA calling MS Graph API with MSAL.JS</h5>
         <br>
         <div class="row" style="margin:auto" >
         <div id="card-div" class="col-md-3" style="display:none">
         <div class="card text-center">
            <div class="card-body">
            <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails</h5>
            <div id="profile-div"></div>
            <br>
            <br>
            <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
            <br>
            <br>
            <button class="btn btn-primary" id="readMail" onclick="readMail()">Read Mails</button>
            </div>
         </div>
         </div>
         <br>
         <br>
            <div class="col-md-4">
            <div class="list-group" id="list-tab" role="tablist">
            </div>
            </div>
            <div class="col-md-5">
             <div class="tab-content" id="nav-tabContent">
            </div>
            </div>
         </div>
         <br>
         <br>

         <!-- importing bootstrap.js and supporting js libraries -->
         <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
         <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
         <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>

         <!-- importing app scripts (load order is important) -->
         <script type="text/javascript" src="./authConfig.js"></script>
         <script type="text/javascript" src="./graphConfig.js"></script>
         <script type="text/javascript" src="./ui.js"></script>

         <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
         <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
         <script type="text/javascript" src="./authPopup.js"></script>
         <script type="text/javascript" src="./graph.js"></script>
      </body>
     </html>
     ```

   > [!TIP]
   > Pode substituir a versão de MSAL.js no script anterior pela versão mais recente lançada em [versão MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).


2. Agora, crie um ficheiro .js chamado *ui.js* que aceda e atualize elementos DOM, e adicione o seguinte código:

   ```JavaScript
   // Select DOM elements to work with
   const welcomeDiv = document.getElementById("welcomeMessage");
   const signInButton = document.getElementById("signIn");
   const signOutButton = document.getElementById('signOut');
   const cardDiv = document.getElementById("card-div");
   const mailButton = document.getElementById("readMail");
   const profileButton = document.getElementById("seeProfile");
   const profileDiv = document.getElementById("profile-div");

   function showWelcomeMessage(account) {
     // Reconfiguring DOM elements
     cardDiv.classList.remove('d-none');
     welcomeDiv.innerHTML = `Welcome ${account.name}`;
     signInButton.classList.add('d-none');
     signOutButton.classList.remove('d-none');
   }

   function updateUI(data, endpoint) {
     console.log('Graph API responded at: ' + new Date().toString());

     if (endpoint === graphConfig.graphMeEndpoint) {
       const title = document.createElement('p');
       title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
       const email = document.createElement('p');
       email.innerHTML = "<strong>Mail: </strong>" + data.mail;
       const phone = document.createElement('p');
       phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
       const address = document.createElement('p');
       address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;
       profileDiv.appendChild(title);
       profileDiv.appendChild(email);
       profileDiv.appendChild(phone);
       profileDiv.appendChild(address);

     } else if (endpoint === graphConfig.graphMailEndpoint) {
         if (data.value.length < 1) {
           alert("Your mailbox is empty!")
         } else {
           const tabList = document.getElementById("list-tab");
           tabList.innerHTML = ''; // clear tabList at each readMail call
           const tabContent = document.getElementById("nav-tabContent");

           data.value.map((d, i) => {
             // Keeping it simple
             if (i < 10) {
               const listItem = document.createElement("a");
               listItem.setAttribute("class", "list-group-item list-group-item-action")
               listItem.setAttribute("id", "list" + i + "list")
               listItem.setAttribute("data-toggle", "list")
               listItem.setAttribute("href", "#list" + i)
               listItem.setAttribute("role", "tab")
               listItem.setAttribute("aria-controls", i)
               listItem.innerHTML = d.subject;
               tabList.appendChild(listItem)

               const contentItem = document.createElement("div");
               contentItem.setAttribute("class", "tab-pane fade")
               contentItem.setAttribute("id", "list" + i)
               contentItem.setAttribute("role", "tabpanel")
               contentItem.setAttribute("aria-labelledby", "list" + i + "list")
               contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
               tabContent.appendChild(contentItem);
             }
           });
         }
     }
   }
   ```

## <a name="register-your-application"></a>Registar a sua aplicação

Siga as instruções para [registar uma nova aplicação de página única](https://docs.microsoft.com/zure/active-directory/develop/scenario-spa-app-registration).

#### <a name="set-a-redirect-url-for-nodejs"></a>Delineie um URL de redirecionamento para Node.js

Para node.js, pode definir a porta do servidor web no ficheiro *server.js.* Este tutorial utiliza a porta 3000, mas pode utilizar qualquer outra porta disponível.

Para configurar um URL redireccionador nas informações de registo de aplicação, volte para o painel de registo de **aplicações** e registe um novo **SPA** com qualquer um dos seguintes:

- Definir *`http://localhost:3000/`* como url de **redirecionamento**.
- Se estiver a utilizar uma porta *`http://localhost:<port>/`* TCP personalizada, utilize (onde * \<* a porta>é o número de porta TCP personalizado).

### <a name="configure-your-javascript-spa"></a>Configure o seu JavaScript SPA

Crie um novo ficheiro .js chamado *authConfig.js* para conter os seus parâmetros de configuração para autenticação e adicione o seguinte código:

```javascript
  const msalConfig = {
    auth: {
      clientId: "Enter_the_Application_Id_Here",
      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
      redirectUri: "Enter_the_Redirect_Uri_Here",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

  // Add here scopes for id token to be used at MS Identity Platform endpoints.
  const loginRequest = {
   scopes: ["openid", "profile", "User.Read"]
  };

   // Add here scopes for access token to be used at MS Graph API endpoints.
  const tokenRequest = {
   scopes: ["User.Read", "Mail.Read"]
  };

```

 Em que:
 - Enter_the_Application_Id_Here>é o ID de **Aplicação (cliente)** para o pedido que registou. * \<*
 - Enter_the_Cloud_Instance_Id_Here>é o exemplo da nuvem Azure. * \<* Para a nuvem azure principal *https://login.microsoftonline.com*ou global, basta entrar . Para as nuvens **nacionais** (por exemplo, China), veja [as nuvens nacionais.](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)
 - Enter_the_Tenant_info_here>está definida para uma das seguintes opções: * \<*
   - Se a sua candidatura apoiar *contas neste diretório organizacional,* substitua este valor pelo **nome** de Id do **Arrendatário** (por exemplo, *contoso.microsoft.com).*
   - Se a sua aplicação apoiar *contas em qualquer diretório organizacional,* substitua este valor por **organizações.**
   - Se a sua aplicação suportar *contas em qualquer diretório organizacional e contas pessoais*da Microsoft, substitua este valor por **comum**. Para restringir o suporte apenas às *contas pessoais*da Microsoft, substitua este valor pelos **consumidores.**
- *\Enter_the_Redirect_Uri_Here>* é a porta que*`http://localhost:3000/`* registou no portal


Crie um novo ficheiro `graphConfig.js`.js chamado , que conterá os seus parâmetros de configuração chamando Microsoft Graph API, e adicione o seguinte código:
```javascript
// Add here the endpoints for MS Graph API services you would like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
};
```
- Enter_the_Graph_Endpoint_Here>é o caso da MS Graph API. * \<* Para o ponto final global do Gráfico MS `https://graph.microsoft.com`API, basta substituir esta cadeia por . Para implementações nacionais de nuvem, consulte a [Documentação API do Gráfico](https://docs.microsoft.com/graph/deployments).

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Utilize a Biblioteca de Autenticação da Microsoft (MSAL) para iniciar sessão no utilizador

### <a name="popup"></a>PopUp
Crie um novo ficheiro `authPopup.js`.js chamado , que conterá a sua autenticação e lógica de aquisição simbólica para login popUp, e adicione o seguinte código:

   ```JavaScript
  // Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

function signIn() {
    myMSALObj.loginPopup(loginRequest)
        .then(loginResponse => {
            console.log('id_token acquired at: ' + new Date().toString());

            if (myMSALObj.getAccount()) {
                showWelcomeMessage(myMSALObj.getAccount());
            }
        }).catch(error => {
            console.error(error);
        });
}

function signOut() {
    myMSALObj.logout();
}

function getTokenPopup(request) {
    return myMSALObj.acquireTokenSilent(request)
        .catch(error => {
            console.warn(error);
            console.warn("silent token acquisition fails. acquiring token using popup");

            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenPopup(request)
                .then(tokenResponse => {
                    return tokenResponse;
                }).catch(error => {
                    console.error(error);
                });
        });
}

function seeProfile() {
    if (myMSALObj.getAccount()) {
        getTokenPopup(loginRequest)
            .then(response => {
                callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
                profileButton.classList.add('d-none');
                mailButton.classList.remove('d-none');
            }).catch(error => {
                console.error(error);
            });
    }
}

function readMail() {
    if (myMSALObj.getAccount()) {
        getTokenPopup(tokenRequest)
            .then(response => {
                callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
            }).catch(error => {
                console.error(error);
            });
    }
}
   ```
### <a name="redirect"></a>Redirecionar
Crie um novo ficheiro `authRedirect.js`.js chamado , que conterá a sua autenticação e lógica de aquisição simbólica para redirecionar o login, e adicione o seguinte código:

```javascript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let accessToken;

// Register Callbacks for Redirect flow
myMSALObj.handleRedirectCallback(authRedirectCallBack);

function authRedirectCallBack(error, response) {
    if (error) {
        console.error(error);
    } else {
        if (myMSALObj.getAccount()) {
            console.log('id_token acquired at: ' + new Date().toString());
            showWelcomeMessage(myMSALObj.getAccount());
            getTokenRedirect(loginRequest);
        } else if (response.tokenType === "Bearer") {
            console.log('access_token acquired at: ' + new Date().toString());
        } else {
            console.log("token type is:" + response.tokenType);
        }
    }
}

// Redirect: once login is successful and redirects with tokens, call Graph API
if (myMSALObj.getAccount()) {
    showWelcomeMessage(myMSALObj.getAccount());
}

function signIn() {
    myMSALObj.loginRedirect(loginRequest);
}

function signOut() {
    myMSALObj.logout();
}

// This function can be removed if you do not need to support IE
function getTokenRedirect(request) {
    return myMSALObj.acquireTokenSilent(request)
        .then((response) => {
            console.log(response);
            if (response.accessToken) {
                console.log('access_token acquired at: ' + new Date().toString());
                accessToken = response.accessToken;

                callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
                profileButton.style.display = 'none';
                mailButton.style.display = 'initial';
            }
        })
        .catch(error => {
            console.warn("silent token acquisition fails. acquiring token using redirect");
            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenRedirect(request);
        });
}

function seeProfile() {
    getTokenRedirect(loginRequest);
}

function readMail() {
    getTokenRedirect(tokenRequest);
}
```

### <a name="more-information"></a>Mais informações

Depois de um **Sign In** utilizador selecionar o botão `signIn` Iniciar `loginPopup` sessão pela primeira vez, o método chama-se para iniciar sessão no utilizador. Este método abre uma janela pop-up com o ponto final da *plataforma de identidade da Microsoft* para solicitar e validar as credenciais do utilizador. Após uma entrada bem sucedida, a *Msal.js* inicia o fluxo do código de [autorização](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow).

Neste ponto, um código de autorização protegido por PKCE é enviado para o ponto final protegido pelo CORS e é trocado por fichas. Um símbolo de identificação, ficha de acesso e ficha de atualização são recebidos, processados por *msal.js,* e a informação contida no token é emcached.

O token id contém informações básicas sobre o utilizador, como o seu nome de exibição. Se planeia utilizar quaisquer dados fornecidos por este token, este deve ser validado pelo seu servidor back-end para garantir que o token foi emitido a um utilizador válido para a sua aplicação. O token de atualização tem uma vida útil limitada e expira após 24 horas. O token de atualização pode ser usado para adquirir silenciosamente novos tokens de acesso.

O SPA gerado por `acquireTokenSilent` este guia `acquireTokenPopup` chama e/ou para adquirir um *token* de acesso usado para consultar a Microsoft Graph API para obter informações sobre o perfil do utilizador. Se precisar de uma amostra que valide o token id, consulte a aplicação de amostra de [webpageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) no GitHub. A amostra utiliza uma API web ASP.NET para validação de tokens.

#### <a name="get-a-user-token-interactively"></a>Obter um token de utilizador interativamente

Após o início do início de sessão, os seus utilizadores não devem ser convidados a reautenticar sempre que precisarem de solicitar um sinal de acesso a um recurso. Para evitar tais pedidos de `acquireTokenSilent`reautenticação, utilize . Existem algumas situações, no entanto, em que poderá ser necessário forçar os utilizadores a interagir com o ponto final da plataforma de identidade da Microsoft. Por exemplo:

- Os utilizadores precisam de reintroduzir as suas credenciais porque a palavra-passe expirou.
- A sua aplicação está a solicitar acesso a um recurso e precisa do consentimento do utilizador.
- É necessária autenticação de dois fatores.

A `acquireTokenPopup` chamada abre uma janela `acquireTokenRedirect` pop-up (ou redireciona os utilizadores para o ponto final da plataforma de identidade da Microsoft). Nessa janela, os utilizadores precisam de interagir confirmando as suas credenciais, dando consentimento ao recurso necessário, ou completando a autenticação de dois fatores.

#### <a name="get-a-user-token-silently"></a>Obter um token de utilizador automaticamente

O `acquireTokenSilent` método lida com a aquisição e renovação simbólicas sem qualquer interação do utilizador. Depois `loginPopup` (ou) `loginRedirect`de ser executado `acquireTokenSilent` pela primeira vez, é o método comumente utilizado para obter fichas usadas para aceder a recursos protegidos para chamadas posteriores. (As chamadas para solicitar ou renovar fichas são feitas silenciosamente.) `acquireTokenSilent` pode falhar em alguns casos. Por exemplo, a palavra-passe do utilizador pode ter expirado. A sua aplicação pode lidar com esta exceção de duas formas:

1. Faça uma `acquireTokenPopup` chamada para acionar imediatamente um pedido de sessão de acesso ao utilizador. Este padrão é comumente utilizado em aplicações online onde não existe conteúdo não autenticado na aplicação disponível para o utilizador. A amostra gerada por esta configuração guiada utiliza este padrão.

1. Indicando visualmente ao utilizador que é necessário um sinal de sessão interativo para que o `acquireTokenSilent` utilizador possa selecionar o momento certo para iniciar sessão, ou a aplicação pode voltar a tentar mais tarde. Esta técnica é comumente utilizada quando o utilizador pode utilizar outras funcionalidades da aplicação sem ser interrompida. Por exemplo, pode haver conteúdo não autenticado disponível na aplicação. Nesta situação, o utilizador pode decidir quando pretende iniciar sessão para aceder ao recurso protegido ou atualizar a informação desatualizada.

> [!NOTE]
> Este quickstart `loginPopup` utiliza `acquireTokenPopup` os métodos e métodos por padrão. Se estiver a usar o Internet Explorer como `loginRedirect` `acquireTokenRedirect` navegador, recomenda-se a utilização e métodos, devido a um [problema conhecido](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) relacionado com a forma como o Internet Explorer lida com janelas pop-up. Se quiser ver como obter o mesmo resultado utilizando métodos de redirecionamento, consulte [*authRedirect.js*](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js).

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>Ligue para a Microsoft Graph API usando o símbolo que acabou de adquirir


 Crie um ficheiro .js chamado *graph.js* que fará uma chamada REST para a Microsoft Graph API, e adicione o seguinte código:

   ```javascript

// Helper function to call MS Graph API endpoint
// using authorization bearer token scheme
function callMSGraph(endpoint, token, callback) {
    const headers = new Headers();
    const bearer = `Bearer ${token}`;

    headers.append("Authorization", bearer);

    const options = {
        method: "GET",
        headers: headers
    };

    console.log('request made to Graph API at: ' + new Date().toString());

    fetch(endpoint, options)
        .then(response => response.json())
        .then(response => callback(response, endpoint))
        .catch(error => console.log(error));
}
   ```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Mais informações sobre fazer uma chamada rest contra uma API protegida

Na aplicação da amostra criada `callMSGraph()` por este guia, `GET` o método é utilizado para fazer um pedido http contra um recurso protegido que requer um símbolo. O pedido devolve então o conteúdo ao chamador. Este método adiciona o token adquirido no *cabeçalho*de autorização http . Para a aplicação de amostra criada por este guia, o recurso é o *ponto* final do Microsoft Graph API Me, que exibe as informações de perfil do utilizador.

## <a name="test-your-code"></a>Teste o seu código

1. Para node.js, inicie o servidor web executando os seguintes comandos a partir da pasta de aplicação:

   ```bash
   npm install
   npm start
   ```
1. No seu navegador, entre **http://localhost:3000** ou **http://localhost:{port}**, onde a *porta* é a porta que o seu servidor web está a ouvir. Deve ver o conteúdo do seu ficheiro *index.html* e o botão **Sign In.**

## <a name="test-your-application"></a>Teste a sua candidatura

Depois de o navegador carregar o ficheiro *index.html,* selecione 'Iniciar ' **'Sem'selecione Iniciar**o tempo . É-lhe pedido que assine sessão com o ponto final da plataforma de identidade da Microsoft:

![A janela de inscrição da conta JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Fornecer consentimento para acesso à aplicação

A primeira vez que inscreveu a sua candidatura, é-lhe pedido que lhe conceda acesso ao seu perfil e o inscreva:

![A janela "Permissões solicitadas"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Ver resultados da aplicação

Depois de iniciar sessão, as informações do perfil do utilizador são devolvidas na resposta da Microsoft Graph API que é exibida:

![Resultados da chamada da Microsoft Graph API](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre âmbitos e permissões delegadas

O Microsoft Graph API requer que o *utilizador.leia* o âmbito para ler o perfil de um utilizador. Por predefinição, este âmbito é adicionado automaticamente em todas as aplicações registadas no portal Azure. Outras APIs para o Microsoft Graph, bem como APIs personalizadas para o seu servidor back-end, podem requerer âmbitos adicionais. Por exemplo, a Microsoft Graph API requer o âmbito *Mail.Read* para listar os e-mails do utilizador.

> [!NOTE]
> O utilizador pode ser solicitado para um consentimento adicional à medida que adiciona miná-lo.

Se uma API de back-end não necessitar de um âmbito (não recomendado), pode utilizar o *clienteId* como âmbito nas chamadas para adquirir fichas.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

O [repo MSAL.js GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) contém documentação adicional da biblioteca, uma FAQ, e fornece suporte ao problema.
