---
title: Tutorial de aplicativo de uma página única JavaScript - fluxo de código auth / Azure
titleSuffix: Microsoft identity platform
description: Como as aplicações javaScript SPA podem usar o fluxo de código auth para chamar uma API que requer acesso a fichas por Azure Ative Directory v2.0 endpoint
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 05/19/2020
ms.author: hahamil
ms.custom: aaddev
ms.openlocfilehash: e02f6946ff6f520fec63ead7e14e94f33182357f
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682310"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-app-spa-using-auth-code-flow"></a>Tutorial: Inscreva-se nos utilizadores e ligue para a Microsoft Graph API a partir de uma aplicação de uma página única JavaScript (SPA) utilizando fluxo de código auth

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são disponibilizadas a si na condição de concordar com os [termos suplementares de utilização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspetos desta funcionalidade podem mudar antes da disponibilidade geral (GA).

Este tutorial mostra-lhe como criar uma aplicação de uma página única JavaScript (SPA) que utiliza a Biblioteca de Autenticação da Microsoft (MSAL) para JavaScript v2.0 para:

> [!div class="checklist"]
> * Execute o fluxo de código de autorização OAuth 2.0 com PKCE
> * Inscreva-se em contas pessoais da Microsoft, bem como contas de trabalho e escolas
> * Adquirir um token de acesso
> * Ligue para o Microsoft Graph ou para a sua própria API que requer fichas de acesso obtidas a partir do ponto final da plataforma de identidade da Microsoft

MSAL.js 2.0 melhora em MSAL.js 1.0 suportando o fluxo de código de autorização no navegador em vez do fluxo implícito de subvenção. MSAL.js 2.0 **NÃO** suporta o fluxo implícito.

## <a name="how-the-tutorial-app-works"></a>Como funciona a aplicação tutorial

:::image type="content" source="media/tutorial-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="Diagrama mostrando o fluxo de código de autorização em uma aplicação de uma página única":::

A aplicação que cria neste tutorial permite que um JavaScript SPA questione o Microsoft Graph API, adquirindo fichas de segurança do ponto final da plataforma de identidade da Microsoft. Neste cenário, após a adesão de um utilizador, é solicitado um sinal de acesso e adicionado aos pedidos http no cabeçalho de autorização. A aquisição e renovação de token sujeira são tratadas pela Microsoft Authentication Library para JavaScript (MSAL.js).

Este tutorial utiliza a seguinte biblioteca:

| | |
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)|Biblioteca de Autenticação microsoft para pacote de navegador JavaScript v2.0|
| | |

## <a name="get-the-completed-code-sample"></a>Obtenha a amostra de código completa

Prefere baixar o projeto de amostra completa deste tutorial? Para executar o projeto utilizando um servidor web local, como node.js, clone o repositório [ms-identity-javascript-v2:](https://github.com/Azure-Samples/ms-identity-javascript-v2)

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

Em seguida, para configurar a amostra de código antes de executá-la, salte para o [passo de configuração](#register-your-application).

Para continuar com o tutorial e construir a aplicação por si mesmo, passe para a secção seguinte, [Pré-requisitos.](#prerequisites)

## <a name="prerequisites"></a>Pré-requisitos

* [Node.js](https://nodejs.org/en/download/) para executar um webserver local
* [Código de Estúdio Visual](https://code.visualstudio.com/download) ou outro editor de código

## <a name="create-your-project"></a>Crie o seu projeto

Assim que tiver o [Node.js](https://nodejs.org/en/download/) instalado, crie uma pasta para hospedar a sua aplicação, por exemplo *msal-spa-tutorial*.

Em seguida, implemente um pequeno servidor web [Express](https://expressjs.com/) para servir o seu ficheiro *index.html.*

1. Primeiro, mude para o seu diretório de projeto no seu terminal e, em seguida, executar os `npm` seguintes comandos:
    ```console
    npm init -y
    npm install @azure/msal-browser
    npm install express
    npm install morgan
    npm install yargs
    ```
2. Em seguida, crie ficheiros *nomeados server.js* e adicione o seguinte código:

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

Agora tem um pequeno webserver para servir o seu SPA. Após completar o resto do tutorial, o arquivo e a estrutura da pasta do seu projeto devem ser semelhantes aos seguintes:

```
msal-spa-tutorial/
├── app
│   ├── authConfig.js
│   ├── authPopup.js
│   ├── authRedirect.js
│   ├── graphConfig.js
│   ├── graph.js
│   ├── index.html
│   └── ui.js
└── server.js
```

## <a name="create-the-spa-ui"></a>Criar o SPA UI

1. Crie uma pasta de *aplicativos* no seu diretório de projeto, e nele crie um ficheiro *index.html* para o seu JavaScript SPA. Este ficheiro implementa um UI construído com o **Quadro Bootstrap 4** e importa ficheiros de script para configuração, autenticação e chamadas API.

    No ficheiro *index.html,* adicione o seguinte código:

    ```html
    <!DOCTYPE html>
    <html lang="en">
      <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <title>Tutorial | MSAL.js JavaScript SPA</title>

        <!-- IE support: add promises polyfill before msal.js  -->
        <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
        <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js"></script>

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

2. Em seguida, também na pasta da *aplicação,* crie um ficheiro chamado *ui.js* e adicione o seguinte código. Este ficheiro acederá e atualizará os elementos DOM.

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
        cardDiv.classList.remove('d-none');
        welcomeDiv.innerHTML = `Welcome ${account.name}`;

        // Reconfiguring DOM elements
        cardDiv.style.display = 'initial';
        welcomeDiv.innerHTML = `Welcome ${account.name}`;
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

## <a name="register-your-application"></a>Registar a sua aplicação

Siga os passos na [aplicação de uma página única: Registo de aplicativos](scenario-spa-app-registration.md) para criar um registo de aplicação para o seu SPA.

No [Redirect URI: MSAL.js 2.0 com](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow) passo de fluxo de código auth, `http://localhost:3000` introduza , o local padrão onde a aplicação deste tutorial é executado.

Se quiser utilizar uma porta diferente, `http://localhost:<port>` entre, onde `<port>` está o seu número de porta TCP preferido. Se especificar um número de porta diferente `3000` de , também atualize o *servidor.js* com o seu número de porta preferido.

### <a name="configure-your-javascript-spa"></a>Configure o seu JavaScript SPA

Crie um ficheiro chamado *authConfig.js* na pasta da *aplicação* para conter os parâmetros de configuração para autenticação e, em seguida, adicione o seguinte código:

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

Modificar os valores na `msalConfig` secção aqui descrita:

- `Enter_the_Application_Id_Here`: A identificação do **pedido (cliente)** do pedido que registou.
- `Enter_the_Cloud_Instance_Id_Here`: A instância de nuvem Azure em que a sua aplicação está registada.
  - Para a nuvem azure principal (ou *global),* entre `https://login.microsoftonline.com` .
  - Para as nuvens **nacionais** (por exemplo, China), você pode encontrar valores apropriados nas [nuvens nacionais.](authentication-national-cloud.md)
- `Enter_the_Tenant_info_here`Deve ser um dos seguintes:
  - Se a sua candidatura apoiar *contas neste diretório organizacional,* substitua este valor pelo nome de Id do **Arrendatário** ou **inquilino**. Por exemplo, `contoso.microsoft.com`.
  - Se a sua aplicação apoiar *contas em qualquer diretório organizacional,* substitua este valor por `organizations` .
  - Se a sua aplicação suportar *contas em qualquer diretório organizacional e contas pessoais*da Microsoft, substitua este valor por `common` .
  - Para restringir o suporte apenas a *contas pessoais*da Microsoft, substitua este valor por `consumers` .
- `Enter_the_Redirect_Uri_Here` é `http://localhost:3000`.

O `authority` valor no seu *authConfig.js* deve ser semelhante ao seguinte se estiver a usar a nuvem azure global:

```javascript
authority: "https://login.microsoftonline.com/common",
```

Ainda na pasta da *aplicação,* crie um ficheiro chamado *graphConfig.js*. Adicione o seguinte código para fornecer à sua aplicação os parâmetros de configuração para chamar a API do Microsoft Graph:

```javascript
// Add the endpoints here for Microsoft Graph API services you'd like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
};
```

Modificar os valores na `graphConfig` secção aqui descrita:

- `Enter_the_Graph_Endpoint_Here`é a instância da Microsoft Graph API com a que a aplicação deve comunicar.
  - Para o ponto final **global** da Microsoft Graph API, substitua ambas as instâncias desta cadeia por `https://graph.microsoft.com` .
  - Para pontos finais em implementações **nacionais** em nuvem, consulte [as implementações nacionais](https://docs.microsoft.com/graph/deployments) em nuvem na documentação do Microsoft Graph.

Os `graphMeEndpoint` `graphMailEndpoint` valores e valores no seu *gráficoConfig.js* devem ser semelhantes aos seguintes se estiver a usar o ponto final global:

```javascript
graphMeEndpoint: "https://graph.microsoft.com/v1.0/me",
graphMailEndpoint: "https://graph.microsoft.com/v1.0/me/messages"
```

## <a name="use-microsoft-authentication-library-msal-to-sign-in-user"></a>Utilize a Microsoft Authentication Library (MSAL) para iniciar sessão no utilizador

### <a name="pop-up"></a>Pop-up

Na pasta da *aplicação,* crie um ficheiro chamado *authPopup.js* e adicione o seguinte código de autenticação e aquisição de token para o pop-up de login:

```JavaScript
// Create the main myMSALObj instance
// configuration parameters are located in authConfig.js
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

Crie um ficheiro chamado *authRedirect.js* na pasta da *aplicação* e adicione o seguinte código de autenticação e aquisição de fichas para redirecionar o login:

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

### <a name="how-the-code-works"></a>Como funciona o código

Quando um utilizador seleciona o botão **Iniciar** sessão pela primeira vez, o `signIn` método `loginPopup` chama-se para iniciar sessão no utilizador. O `loginPopup` método abre uma janela pop-up com o ponto final da plataforma de identidade da *Microsoft* para solicitar e validar as credenciais do utilizador. Após uma entrada bem sucedida, a *Msal.js* inicia o fluxo do código de [autorização](v2-oauth2-auth-code-flow.md).

Neste ponto, um código de autorização protegido por PKCE é enviado para o ponto final protegido pelo CORS e é trocado por fichas. Um token de identificação, ficha de acesso e ficha de atualização são recebidos pela sua aplicação e processados por *msal.js,* e a informação contida nos tokens está em cache.

O token id contém informações básicas sobre o utilizador, como o seu nome de exibição. Se planeia utilizar quaisquer dados fornecidos pelo token ID, o seu servidor back-end *deve* validá-lo para garantir que o token foi emitido a um utilizador válido para a sua aplicação. O token de atualização tem uma vida útil limitada e expira após 24 horas. O token de atualização pode ser usado para adquirir silenciosamente novos tokens de acesso.

O SPA que criou neste tutorial de chamadas `acquireTokenSilent` e/ou `acquireTokenPopup` para adquirir um *token* de acesso usado para consultar a Microsoft Graph API para obter informações sobre o perfil do utilizador. Se precisar de uma amostra que valide o token id, consulte a aplicação de amostra de [webpageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) no GitHub. A amostra utiliza uma API web ASP.NET para validação de tokens.

#### <a name="get-a-user-token-interactively"></a>Obter um token de utilizador interativamente

Após o início do início de sessão, a sua aplicação não deve pedir aos utilizadores que se reatentem sempre que precisam de aceder a um recurso protegido (isto é, para solicitar um sinal). Para evitar tais pedidos de reautenticação, ligue `acquireTokenSilent` para . Existem algumas situações, no entanto, em que poderá ser necessário forçar os utilizadores a interagir com o ponto final da plataforma de identidade da Microsoft. Por exemplo:

- Os utilizadores precisam de reintroduzir as suas credenciais porque a palavra-passe expirou.
- A sua aplicação está a solicitar acesso a um recurso e precisa do consentimento do utilizador.
- É necessária autenticação de dois fatores.

A chamada `acquireTokenPopup` abre uma janela pop-up (ou `acquireTokenRedirect` redireciona os utilizadores para o ponto final da plataforma de identidade da Microsoft). Nessa janela, os utilizadores precisam de interagir confirmando as suas credenciais, dando consentimento ao recurso necessário, ou completando a autenticação de dois fatores.

#### <a name="get-a-user-token-silently"></a>Obter um token de utilizador automaticamente

O `acquireTokenSilent` método lida com a aquisição e renovação simbólicas sem qualquer interação do utilizador. Depois `loginPopup` `loginRedirect` (ou) de ser executado pela primeira vez, é o método comumente utilizado para obter `acquireTokenSilent` fichas usadas para aceder a recursos protegidos para chamadas posteriores. (As chamadas para solicitar ou renovar fichas são feitas silenciosamente.) `acquireTokenSilent`pode falhar em alguns casos. Por exemplo, a palavra-passe do utilizador pode ter expirado. A sua aplicação pode lidar com esta exceção de duas formas:

1. Faça uma chamada para `acquireTokenPopup` acionar imediatamente um pedido de sessão de acesso ao utilizador. Este padrão é comumente utilizado em aplicações online onde não existe conteúdo não autenticado na aplicação disponível para o utilizador. A amostra gerada por esta configuração guiada utiliza este padrão.
1. Indicando visualmente ao utilizador que é necessário um sinal de sessão interativo para que o utilizador possa selecionar o momento certo para iniciar sessão, ou a aplicação pode voltar a tentar `acquireTokenSilent` mais tarde. Esta técnica é comumente utilizada quando o utilizador pode utilizar outras funcionalidades da aplicação sem ser interrompida. Por exemplo, pode haver conteúdo não autenticado disponível na aplicação. Nesta situação, o utilizador pode decidir quando pretende iniciar sessão para aceder ao recurso protegido ou atualizar a informação desatualizada.

> [!NOTE]
> Este tutorial utiliza os `loginPopup` métodos e `acquireTokenPopup` métodos por padrão. Se estiver a utilizar o Internet Explorer, recomendamos que utilize os `loginRedirect` métodos e `acquireTokenRedirect` métodos devido a um [problema conhecido](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) com o Internet Explorer e janelas pop-up. Para obter o mesmo resultado utilizando métodos de redirecionamento, consulte [*authRedirect.js*](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js) no GitHub.

## <a name="call-the-microsoft-graph-api"></a>Ligue para a Microsoft Graph API

Crie ficheiros chamados *graph.js* na pasta da *aplicação* e adicione o seguinte código para fazer chamadas REST para a Microsoft Graph API:

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

Na aplicação da amostra criada neste tutorial, o `callMSGraph()` método é usado para fazer um pedido http contra um recurso protegido que requer um `GET` símbolo. O pedido devolve então o conteúdo ao chamador. Este método adiciona o token adquirido no *cabeçalho*de autorização http . Na aplicação de amostra criada neste tutorial, o recurso protegido é o ponto final do Microsoft Graph API *me* que exibe as informações de perfil do utilizador inscritos.

## <a name="test-your-application"></a>Teste a sua candidatura

Completou a criação da aplicação e está agora pronto para lançar o servidor web Node.js e testar a funcionalidade da aplicação.

1. Inicie o servidor web Node.js executando o seguinte comando a partir da raiz da sua pasta de projeto:

   ```console
   npm start
   ```
1. No seu navegador, navegue para ou , onde está a porta que o seu servidor web está a `http://localhost:3000` `http://localhost:<port>` `<port>` ouvir. Deve ver o conteúdo do seu ficheiro *index.html* e o botão **Sign In.**

### <a name="sign-in-to-the-application"></a>Iniciar sessão na aplicação

Depois de o navegador carregar o ficheiro *index.html,* selecione 'Iniciar ' **'Sem'selecione Iniciar**o tempo . É-lhe pedido que assine sessão com o ponto final da plataforma de identidade da Microsoft:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-01-signin-dialog.png" alt-text="Diálogo de visualização de início de navegador web":::

### <a name="provide-consent-for-application-access"></a>Fornecer consentimento para acesso à aplicação

A primeira vez que inscreveu a sua candidatura, é-lhe pedido que lhe conceda acesso ao seu perfil e o inscreva:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-02-consent-dialog.png" alt-text="Diálogo de conteúdo exibido no navegador web":::

Se consentir com as permissões solicitadas, as aplicações web exibem o seu nome de utilizador, significando um login bem sucedido:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-03-signed-in.png" alt-text="Resultados de um sessão de acesso bem-sucedido no navegador web":::

### <a name="call-the-graph-api"></a>Ligue para a API de gráfico

Depois de iniciar sessão, selecione **Ver Perfil** para ver as informações de perfil do utilizador devolvidas na resposta da chamada para a Microsoft Graph API:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-04-see-profile.png" alt-text="Informações de perfil do Microsoft Graph exibidas no navegador":::

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Mais informações sobre âmbitos e permissões delegadas

O Microsoft Graph API requer que o *utilizador.leia* o âmbito para ler o perfil de um utilizador. Por predefinição, este âmbito é adicionado automaticamente em todas as aplicações registadas no portal Azure. Outras APIs para o Microsoft Graph, bem como APIs personalizadas para o seu servidor back-end, podem requerer âmbitos adicionais. Por exemplo, a Microsoft Graph API requer o âmbito *Mail.Read* para listar o e-mail do utilizador.

À medida que adiciona misejáça, os seus utilizadores podem ser solicitados a fornecer consentimento adicional para os âmbitos adicionais.

Se uma API de back-end não necessitar de um âmbito, o que não é recomendado, pode usar `clientId` como âmbito nas chamadas para adquirir fichas.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximos passos

Neste tutorial, criou uma aplicação de uma página única JavaScript (SPA) que utiliza a Biblioteca de Autenticação da Microsoft (MSAL) para JavaScript v2.0 para:

> [!div class="checklist"]
> * Execute o fluxo de código de autorização OAuth 2.0 com PKCE
> * Inscreva-se em contas pessoais da Microsoft, bem como contas de trabalho e escolas
> * Adquirir um token de acesso
> * Ligue para o Microsoft Graph ou para a sua própria API que requer fichas de acesso obtidas a partir do ponto final da plataforma de identidade da Microsoft

Para saber mais sobre o fluxo de código de autorização, incluindo as diferenças entre os fluxos de código implícito e auth, consulte a plataforma de identidade da Microsoft e o fluxo de código de [autorização OAuth 2.0](v2-oauth2-auth-code-flow.md).

Se quiser mergulhar mais profundamente no desenvolvimento de aplicações de uma página única do JavaScript na plataforma de identidade da Microsoft, o cenário em várias partes: série de aplicações de uma página única pode [ajudá-lo](scenario-spa-overview.md) a começar.
