---
title: 'Tutorial: Criar uma aplicação de página única JavaScript que utiliza fluxo de código auth | Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste tutorial, cria-se um JavaScript SPA que pode iniciar sessão nos utilizadores e utilizar o fluxo de código auth para obter um token de acesso a partir da plataforma de identidade da Microsoft e ligar para a Microsoft Graph API.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 1ec046ca6b42a5ca8f33b0347c562c85abd42684
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98756176"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-app-spa-using-auth-code-flow"></a>Tutorial: Inscreva-se nos utilizadores e ligue para a API do Gráfico microsoft a partir de uma aplicação de página única JavaScript (SPA) usando fluxo de código auth

Neste tutorial, você constrói uma aplicação de página única JavaScript (SPA) que assina nos utilizadores e chama o Microsoft Graph usando o fluxo de código de autorização com PKCE. O SPA que constrói utiliza a Biblioteca de Autenticação da Microsoft (MSAL) para JavaScript v2.0.

Neste tutorial:
> [!div class="checklist"]
> * Executar o fluxo de código de autorização OAuth 2.0 com PKCE
> * Assine nas contas pessoais da Microsoft, bem como contas de trabalho e escola
> * Adquirir um token de acesso
> * Ligue para o Microsoft Graph ou para a sua própria API que requer acesso a tokens obtidos a partir da plataforma de identidade da Microsoft

MSAL.js 2.0 melhora em MSAL.js 1.0, suportando o fluxo de código de autorização no navegador em vez do fluxo de concessão implícito. MSAL.js 2.0 **NÃO** suporta o fluxo implícito.

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/download/) para gerir um webserver local
* [Código de Estúdio Visual](https://code.visualstudio.com/download) ou outro editor de código

## <a name="how-the-tutorial-app-works"></a>Como funciona a aplicação tutorial

:::image type="content" source="media/tutorial-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="Diagrama mostrando o fluxo de código de autorização numa aplicação de uma página":::

A aplicação que cria neste tutorial permite a um JavaScript SPA consultar a API do Microsoft Graph, adquirindo fichas de segurança a partir da plataforma de identidade da Microsoft. Neste cenário, após a indicação de um utilizador, é solicitado um token de acesso e adicionado aos pedidos HTTP no cabeçalho de autorização. A aquisição e renovação de token são tratadas pela Microsoft Authentication Library for JavaScript (MSAL.js).

Este tutorial utiliza a seguinte biblioteca:

[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) a Biblioteca de Autenticação do Microsoft para o pacote de navegador JavaScript v2.0

## <a name="get-the-completed-code-sample"></a>Obtenha a amostra de código completa

Prefere baixar o projeto de amostra concluído deste tutorial? Para executar o projeto utilizando um servidor web local, como Node.js, clone o repositório [ms-identidade-javascript-v2:](https://github.com/Azure-Samples/ms-identity-javascript-v2)

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

Em seguida, para configurar a amostra de código antes de executá-la, salte para o [passo de configuração](#register-your-application).

Para continuar com o tutorial e construir a aplicação por si mesmo, passe para a secção seguinte, [Pré-requisitos.](#prerequisites)

## <a name="create-your-project"></a>Criar o seu projeto

Uma vez instalada [Node.js,](https://nodejs.org/en/download/) crie uma pasta para hospedar a sua aplicação, por *exemplo, msal-spa-tutorial*.

Em seguida, implemente um pequeno servidor web [Express](https://expressjs.com/) para servir o seu *ficheiroindex.html.*

1. Primeiro, mude para o diretório do seu projeto no seu terminal e, em seguida, executar os `npm` seguintes comandos:
    ```console
    npm init -y
    npm install @azure/msal-browser
    npm install express
    npm install morgan
    npm install yargs
    ```
2. Em seguida, crie um ficheiro nomeado *server.js* e adicione o seguinte código:

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

Você agora tem um pequeno webserver para servir o seu SPA. Após completar o resto do tutorial, o arquivo e a estrutura da pasta do seu projeto devem ser semelhantes aos seguintes:

```
msal-spa-tutorial/
├── app
│   ├── authConfig.js
│   ├── authPopup.js
│   ├── authRedirect.js
│   ├── graphConfig.js
│   ├── graph.js
│   ├── index.html
│   └── ui.js
└── server.js
```

## <a name="create-the-spa-ui"></a>Criar o SPA UI

1. Crie uma pasta de *aplicativos* no seu diretório de projetos e nele crie um ficheiro *index.html* para o seu JavaScript SPA. Este ficheiro implementa um UI construído com o **Quadro Bootstrap 4** e importa ficheiros de script para configuração, autenticação e chamadas API.

    No *ficheiroindex.html,* adicione o seguinte código:

    ```html
    <!DOCTYPE html>
    <html lang="en">
      <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <title>Tutorial | MSAL.js JavaScript SPA</title>

        <!-- IE support: add promises polyfill before msal.js  -->
        <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
        <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.4/js/msal-browser.js" integrity="sha384-7sxY2tN3GMVE5jXH2RL9AdbO6s46vUh9lUid4yNCHJMUzDoj+0N4ve6rLOmR88yN" crossorigin="anonymous"></script>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
      </head>
      <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
          <a class="navbar-brand" href="/">Microsoft identity platform</a>
          <div class="btn-group ml-auto dropleft">
              <button type="button" id="SignIn" class="btn btn-secondary" onclick="signIn()">
                Sign In
              </button>
          </div>
        </nav>
        <br>
        <h5 class="card-header text-center">JavaScript SPA calling Microsoft Graph API with MSAL.js</h5>
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
            <button class="btn btn-primary" id="readMail" onclick="readMail()">Read Mail</button>
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

2. Em seguida, também na pasta de *aplicações,* crie um ficheiro nomeado *ui.js* e adicione o seguinte código. Este ficheiro irá aceder e atualizar elementos DOM.

    ```JavaScript
    // Select DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("SignIn");
    const cardDiv = document.getElementById("card-div");
    const mailButton = document.getElementById("readMail");
    const profileButton = document.getElementById("seeProfile");
    const profileDiv = document.getElementById("profile-div");

    function showWelcomeMessage(account) {
        // Reconfiguring DOM elements
        cardDiv.style.display = 'initial';
        welcomeDiv.innerHTML = `Welcome ${account.username}`;
        signInButton.setAttribute("onclick", "signOut();");
        signInButton.setAttribute('class', "btn btn-success")
        signInButton.innerHTML = "Sign Out";
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

## <a name="register-your-application"></a>Registar a aplicação

Siga os passos na [aplicação de página única: Registo de aplicação](scenario-spa-app-registration.md) para criar um registo de aplicações para o seu SPA.

No [URI de redirecionamento: MSAL.js 2.0 com passo de fluxo de código auth,](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow) insira, `http://localhost:3000` o local padrão onde a aplicação deste tutorial é executado.

Se quiser utilizar uma porta diferente, `http://localhost:<port>` entre, onde `<port>` está o número de porta TCP preferido. Se especificar um número de porta `3000` diferente, também atualize *server.js* com o número de porta preferido.

### <a name="configure-your-javascript-spa"></a>Configure o seu JavaScript SPA

Crie um ficheiro nomeado *authConfig.js* na pasta da *aplicação* para conter os parâmetros de configuração para autenticação e, em seguida, adicione o seguinte código:

```javascript
const msalConfig = {
  auth: {
    clientId: "Enter_the_Application_Id_Here",
    authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
    redirectUri: "Enter_the_Redirect_Uri_Here",
  },
  cache: {
    cacheLocation: "sessionStorage", // This configures where your cache will be stored
    storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
  }
};

// Add scopes here for ID token to be used at Microsoft identity platform endpoints.
const loginRequest = {
 scopes: ["openid", "profile", "User.Read"]
};

// Add scopes here for access token to be used at Microsoft Graph API endpoints.
const tokenRequest = {
 scopes: ["User.Read", "Mail.Read"]
};
```

Modifique os valores na `msalConfig` secção descrita aqui:

- `Enter_the_Application_Id_Here`: **Identificação do Formulário de Inscrição (cliente)** do requerimento que registou.
- `Enter_the_Cloud_Instance_Id_Here`: A caixa de nuvem Azure em que a sua aplicação está registada.
  - Para a nuvem azure principal (ou *global),* insira `https://login.microsoftonline.com` .
  - Para nuvens **nacionais** (por exemplo, China), você pode encontrar valores apropriados nas [nuvens nacionais.](authentication-national-cloud.md)
- `Enter_the_Tenant_info_here` deve ser um dos seguintes:
  - Se a sua candidatura suporta *contas neste diretório organizacional,* substitua este valor pelo **ID** do Inquilino ou **nome de Inquilino.** Por exemplo, `contoso.microsoft.com`.
  - Se a sua candidatura suportar *contas em qualquer diretório organizacional,* substitua este valor por `organizations` .
  - Se a sua aplicação suportar *contas em qualquer diretório organizacional e contas pessoais da Microsoft,* substitua este valor por `common` .
  - Para restringir apenas o suporte às *contas pessoais da Microsoft,* substitua este valor por `consumers` .
- `Enter_the_Redirect_Uri_Here` é `http://localhost:3000`.

O `authority` valor do seu *authConfig.js* deve ser semelhante ao seguinte se estiver a usar a nuvem Azure global:

```javascript
authority: "https://login.microsoftonline.com/common",
```

Ainda na pasta de *aplicações,* crie um ficheiro chamado *graphConfig.js*. Adicione o seguinte código para fornecer à sua aplicação os parâmetros de configuração para chamar a API do Gráfico microsoft:

```javascript
// Add the endpoints here for Microsoft Graph API services you'd like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
};
```

Modifique os valores na `graphConfig` secção descrita aqui:

- `Enter_the_Graph_Endpoint_Here` é o caso da API do Gráfico microsoft com que a aplicação deve comunicar.
  - Para o ponto final **global** da Microsoft Graph API, substitua ambas as instâncias desta cadeia por `https://graph.microsoft.com` .
  - Para pontos finais em implementações **nacionais** em nuvem, consulte [as implementações](/graph/deployments) nacionais em nuvem na documentação do Microsoft Graph.

Os `graphMeEndpoint` `graphMailEndpoint` valores e valores no seu *graphConfig.js* devem ser semelhantes aos seguintes se estiver a utilizar o ponto final global:

```javascript
graphMeEndpoint: "https://graph.microsoft.com/v1.0/me",
graphMailEndpoint: "https://graph.microsoft.com/v1.0/me/messages"
```

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-user"></a>Utilize a Biblioteca de Autenticação microsoft (MSAL) para iniciar sação no utilizador

### <a name="pop-up"></a>Pop-up

Na pasta de *aplicações,* crie um ficheiro nomeado *authPopup.js* e adicione o seguinte código de autenticação e de aquisição simbólico para o pop-up de login:

```JavaScript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let username = "";

function loadPage() {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    const currentAccounts = myMSALObj.getAllAccounts();
    if (currentAccounts === null) {
        return;
    } else if (currentAccounts.length > 1) {
        // Add choose account code here
        console.warn("Multiple accounts detected.");
    } else if (currentAccounts.length === 1) {
        username = currentAccounts[0].username;
        showWelcomeMessage(currentAccounts[0]);
    }
}

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        loadPage();
    }
}

function signIn() {
    myMSALObj.loginPopup(loginRequest).then(handleResponse).catch(error => {
        console.error(error);
    });
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenPopup(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
        console.warn("silent token acquisition fails. acquiring token using redirect");
        if (error instanceof msal.InteractionRequiredAuthError) {
            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenPopup(request).then(tokenResponse => {
                console.log(tokenResponse);

                return tokenResponse;
            }).catch(error => {
                console.error(error);
            });
        } else {
            console.warn(error);
        }
    });
}

function seeProfile() {
    getTokenPopup(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenPopup(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}

loadPage();
```

### <a name="redirect"></a>Redirecionar

Crie um ficheiro nomeado *authRedirect.js* na pasta da *aplicação* e adicione o seguinte código de autenticação e de aquisição simbólico para redirecionamento de login:

```javascript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let accessToken;
let username = "";

// Redirect: once login is successful and redirects with tokens, call Graph API
myMSALObj.handleRedirectPromise().then(handleResponse).catch(err => {
    console.error(err);
});

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        /**
         * See here for more info on account retrieval:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
         */
        const currentAccounts = myMSALObj.getAllAccounts();
        if (currentAccounts === null) {
            return;
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
            console.warn("Multiple accounts detected.");
        } else if (currentAccounts.length === 1) {
            username = currentAccounts[0].username;
            showWelcomeMessage(currentAccounts[0]);
        }
    }
}

function signIn() {
    myMSALObj.loginRedirect(loginRequest);
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenRedirect(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
            console.warn("silent token acquisition fails. acquiring token using redirect");
            if (error instanceof msal.InteractionRequiredAuthError) {
                // fallback to interaction when silent call fails
                return myMSALObj.acquireTokenRedirect(request);
            } else {
                console.warn(error);
            }
        });
}

function seeProfile() {
    getTokenRedirect(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenRedirect(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}
```

### <a name="how-the-code-works"></a>Como funciona o código

Quando um utilizador seleciona o botão **'Iniciar'** pela primeira vez, o `signIn` método chama para assinar no `loginPopup` utilizador. O `loginPopup` método abre uma janela pop-up com o ponto final da plataforma de identidade da *Microsoft* para solicitar e validar as credenciais do utilizador. Após uma entrada bem sucedida, *msal.js* inicia o fluxo de código de [autorização](v2-oauth2-auth-code-flow.md).

Neste momento, um código de autorização protegido pelo PKCE é enviado para o ponto final de token protegido pelo CORS e é trocado por fichas. Um token de ID, token de acesso e token de atualização são recebidos pela sua aplicação e processados por *msal.js*, e as informações contidas nos tokens estão em cache.

O token de ID contém informações básicas sobre o utilizador, como o seu nome de exibição. Se planeia utilizar quaisquer dados fornecidos pelo token de ID, o seu servidor back-end *deve* validá-lo para garantir que o token foi emitido a um utilizador válido para a sua aplicação.

O token de acesso tem uma vida útil limitada e expira após 24 horas. O token refresh pode ser usado para adquirir silenciosamente novos tokens de acesso.

O SPA que criou nestas chamadas tutoriais `acquireTokenSilent` e/ou `acquireTokenPopup` para adquirir um *token* de acesso usado para consultar a API do Microsoft Graph para obter informações sobre o perfil do utilizador. Se precisar de uma amostra que valide o token de ID, consulte a aplicação de amostra [activa-directório-javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) no GitHub. A amostra utiliza uma API web ASP.NET para validação de fichas.

#### <a name="get-a-user-token-interactively"></a>Obter um token de utilizador interativamente

Após o seu primeiro sôs-in, a sua aplicação não deve pedir aos utilizadores que reauttenenássem sempre que precisam de aceder a um recurso protegido (isto é, para solicitar um token). Para evitar tais pedidos de reautoria, `acquireTokenSilent` ligue. Existem algumas situações, no entanto, em que poderá ser necessário forçar os utilizadores a interagirem com a plataforma de identidade da Microsoft. Por exemplo:

- Os utilizadores precisam de reintrodutar as suas credenciais porque a palavra-passe expirou.
- A sua aplicação está a solicitar o acesso a um recurso e precisa do consentimento do utilizador.
- É necessária autenticação de dois fatores.

A chamada `acquireTokenPopup` abre uma janela pop-up (ou `acquireTokenRedirect` redireciona os utilizadores para a plataforma de identidade da Microsoft). Nessa janela, os utilizadores precisam interagir confirmando as suas credenciais, dando consentimento ao recurso necessário ou completando a autenticação de dois fatores.

#### <a name="get-a-user-token-silently"></a>Obter um token de utilizador automaticamente

O `acquireTokenSilent` método lida com a aquisição e renovação de símbolos sem qualquer interação do utilizador. Depois `loginPopup` de (ou `loginRedirect` ) ser executado pela primeira vez, é `acquireTokenSilent` o método comumente usado para obter fichas usadas para aceder a recursos protegidos para chamadas subsequentes. (As chamadas para solicitar ou renovar fichas são feitas silenciosamente.) `acquireTokenSilent` pode falhar em alguns casos. Por exemplo, a palavra-passe do utilizador pode ter expirado. A sua aplicação pode lidar com esta exceção de duas formas:

1. Faça uma chamada imediatamente `acquireTokenPopup` para acionar um pedido de inscrição do utilizador. Este padrão é comumente utilizado em aplicações online onde não existe conteúdo não autenticado na aplicação disponível para o utilizador. A amostra gerada por esta configuração guiada utiliza este padrão.
1. Indicar visualmente ao utilizador que é necessário um início de sção interativo para que o utilizador possa selecionar o momento certo para iniciar sedutar, ou a aplicação pode voltar a tentar `acquireTokenSilent` mais tarde. Esta técnica é comumente utilizada quando o utilizador pode utilizar outra funcionalidade da aplicação sem ser interrompida. Por exemplo, pode haver conteúdo não autenticado disponível na aplicação. Nesta situação, o utilizador pode decidir quando pretender iniciar seducação para aceder ao recurso protegido ou para atualizar as informações desatualizadas.

> [!NOTE]
> Este tutorial utiliza os `loginPopup` métodos e `acquireTokenPopup` métodos por defeito. Se estiver a utilizar o Internet Explorer, recomendamos que utilize os `loginRedirect` métodos e `acquireTokenRedirect` métodos devido a um [problema conhecido](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) com o Internet Explorer e janelas pop-up. Para um exemplo de obtenção do mesmo resultado utilizando métodos de redirecionamento, consulte [*authRedirect.js*](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js) no GitHub.

## <a name="call-the-microsoft-graph-api"></a>Ligue para a Microsoft Graph API

Crie um ficheiro nomeado *graph.js* na pasta da *aplicação* e adicione o seguinte código para fazer chamadas REST para a API do Gráfico microsoft:

```javascript
// Helper function to call Microsoft Graph API endpoint
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

Na aplicação de amostra criada neste tutorial, o `callMSGraph()` método é utilizado para fazer um pedido HTTP contra um recurso protegido que requer um `GET` símbolo. Em seguida, o pedido devolve o conteúdo ao autor da chamada. Este método adiciona o símbolo adquirido no *cabeçalho de autorização HTTP*. Na aplicação de amostra criada neste tutorial, o recurso protegido é o ponto final do Microsoft Graph API *me* que exibe as informações de perfil do utilizador inscrito.

## <a name="test-your-application"></a>Teste a sua aplicação

Concluiu a criação da aplicação e está agora pronto para lançar o Node.js servidor web e testar a funcionalidade da aplicação.

1. Inicie o Node.js servidor web executando o seguinte comando a partir da raiz da sua pasta de projeto:

   ```console
   npm start
   ```
1. No seu navegador, navegue para `http://localhost:3000` `http://localhost:<port>` ou, onde `<port>` está a porta que o seu servidor web está a ouvir. Deverá ver o conteúdo do seuindex.htmficheiro *L* e o botão **Iniciar.**

### <a name="sign-in-to-the-application"></a>Iniciar sessão na aplicação

Depois de o navegador carregar o ficheiro *index.html,* selecione **Iniciar Sôs.** É-lhe pedido que assine com a plataforma de identidade da Microsoft:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-01-signin-dialog.png" alt-text="Web browser exibindo diálogo de insusimento":::

### <a name="provide-consent-for-application-access"></a>Fornecer consentimento para acesso à aplicação

A primeira vez que iniciar sessão na sua candidatura, é-lhe pedido que lhe conceda acesso ao seu perfil e o inscreva:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-02-consent-dialog.png" alt-text="Diálogo de conteúdo exibido no navegador web":::

Se consentir com as permissões solicitadas, as aplicações web exibem o seu nome de utilizador, o que significa um login bem sucedido:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-03-signed-in.png" alt-text="Resultados de um sucesso de sind in no navegador web":::

### <a name="call-the-graph-api"></a>Ligue para a API do Gráfico

Depois de iniciar sessão, selecione **Ver Perfil** para visualizar as informações do perfil do utilizador devolvidas na resposta da chamada para a API do Gráfico da Microsoft:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-04-see-profile.png" alt-text="Informações de perfil do Microsoft Graph exibidas no navegador":::

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre âmbitos e permissões delegadas

A API do Microsoft Graph requer que o âmbito de leitura do *utilizador.leia* para ler o perfil de um utilizador. Por predefinição, este âmbito é automaticamente adicionado em todas as aplicações registadas no portal Azure. Outros APIs para o Microsoft Graph, bem como APIs personalizados para o seu servidor back-end, podem necessitar de âmbitos adicionais. Por exemplo, a API do Gráfico microsoft requer o âmbito *Mail.Read* para listar o e-mail do utilizador.

À medida que adiciona âmbitos, os seus utilizadores poderão ser solicitados a fornecer consentimento adicional para os âmbitos adicionados.

Se uma API de back-end não necessitar de um âmbito, o que não é recomendado, pode usar `clientId` como âmbito nas chamadas para adquirir fichas.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

Se quiser mergulhar mais profundamente no desenvolvimento de aplicações de página única javaScript na plataforma de identidade da Microsoft, consulte a nossa série de cenários multi-partes:

> [!div class="nextstepaction"]
> [Cenário: Aplicação de página única](scenario-spa-overview.md)