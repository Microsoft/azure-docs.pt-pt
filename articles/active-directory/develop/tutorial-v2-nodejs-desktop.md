---
title: 'Tutorial: Inscreva-se nos utilizadores e ligue para a Microsoft Graph API numa aplicação de ambiente de trabalho electron | Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste tutorial, você constrói uma aplicação de desktop Electron que pode iniciar súpite nos utilizadores e usar o fluxo de código auth para obter um token de acesso a partir da plataforma de identidade da Microsoft e ligar para a Microsoft Graph API.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: e9e36a3ed2cab05eb63168452bc8472656ab5efb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101644294"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-in-an-electron-desktop-app"></a>Tutorial: Inscreva-se nos utilizadores e ligue para a Microsoft Graph API numa aplicação de ambiente de trabalho electron

Neste tutorial, você constrói uma aplicação de desktop Electron que assina nos utilizadores e chama o Microsoft Graph usando o fluxo de código de autorização com PKCE. A aplicação para desktop que constrói utiliza a [Microsoft Authentication Library (MSAL) para Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Siga os passos neste tutorial para:

> [!div class="checklist"]
> - Registe a inscrição no portal Azure
> - Criar um projeto de aplicativo de desktop eletrões
> - Adicione lógica de autenticação à sua app
> - Adicione um método para chamar uma API web
> - Adicionar detalhes de registo de aplicativos
> - Testar a aplicação

## <a name="prerequisites"></a>Pré-requisitos

- [Node.js](https://nodejs.org/en/download/)
- [Eletrão](https://www.electronjs.org/)
- [Código de Estúdio Visual](https://code.visualstudio.com/download) ou outro editor de código

## <a name="register-the-application"></a>Registar o pedido

Em primeiro lugar, complete os passos no [Registo de uma aplicação com a plataforma de identidade da Microsoft](quickstart-register-app.md) para registar a sua aplicação.

Utilize as seguintes definições para o registo da sua aplicação:

- Nome: `ElectronDesktopApp` (sugerido)
- Tipos de conta **suportados: Contas em qualquer diretório organizacional (qualquer diretório AD Azure - Multitenant) e contas pessoais da Microsoft (por exemplo, Skype, Xbox)**
- Tipo de plataforma: **Aplicações móveis e desktop**
- Redirecionamento URI: `msal://redirect`

## <a name="create-the-project"></a>Criar o projeto

Crie uma pasta para hospedar a sua aplicação, por *exemplo, o ElectronDesktopApp*.

1. Primeiro, mude para o diretório do seu projeto no seu terminal e, em seguida, executar os `npm` seguintes comandos:

    ```console
    npm init -y
    npm install --save @azure/msal-node axios bootstrap dotenv jquery popper.js
    npm install --save-dev babel electron@10.1.6 webpack
    ```

2. Em seguida, crie uma pasta chamada *App*. Dentro desta pasta, crie um ficheiro chamado *index.htmi* que servirá de UI. Adicione o seguinte código:

    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline';" />
        <title>MSAL Node Electron Sample App</title>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="../node_modules/bootstrap/dist/css/bootstrap.min.css">

        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
    </head>

    <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
            <a class="navbar-brand">Microsoft identity platform</a>
            <div class="btn-group ml-auto dropleft">
                <button type="button" id="signIn" class="btn btn-secondary" aria-expanded="false">
                    Sign in
                </button>
                <button type="button" id="signOut" class="btn btn-success" hidden aria-expanded="false">
                    Sign out
                </button>
            </div>
        </nav>
        <br>
        <h5 class="card-header text-center">Electron sample app calling MS Graph API using MSAL Node</h5>
        <br>
        <div class="row" style="margin:auto">
            <div id="cardDiv" class="col-md-3" style="display:none">
                <div class="card text-center">
                    <div class="card-body">
                        <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails
                        </h5>
                        <div id="profileDiv"></div>
                        <br>
                        <br>
                        <button class="btn btn-primary" id="seeProfile">See Profile</button>
                        <br>
                        <br>
                        <button class="btn btn-primary" id="readMail">Read Mails</button>
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

        <script>
            window.jQuery = window.$ = require('jquery');
            require("./renderer.js");
        </script>

        <!-- importing bootstrap.js and supporting js libraries -->
        <script src="../node_modules/jquery/dist/jquery.js"></script>
        <script src="../node_modules/popper.js/dist/umd/popper.js"></script>
        <script src="../node_modules/bootstrap/dist/js/bootstrap.js"></script>
    </body>

    </html>
    ```

3. Em seguida, crie um ficheiro nomeado *main.js* e adicione o seguinte código:

    ```JavaScript
    require('dotenv').config()

    const path = require('path');
    const { app, ipcMain, BrowserWindow } = require('electron');
    const { IPC_MESSAGES } = require('./constants');

    const { callEndpointWithToken } = require('./fetch');
    const AuthProvider = require('./AuthProvider');

    const authProvider = new AuthProvider();
    let mainWindow;

    function createWindow () {
        mainWindow = new BrowserWindow({
            width: 800,
            height: 600,
            webPreferences: {
            nodeIntegration: true
            }
        });

        mainWindow.loadFile(path.join(__dirname, './index.html'));
        };

    app.on('ready', () => {
        createWindow();
    });

    app.on('window-all-closed', () => {
        app.quit();
    });


    // Event handlers
    ipcMain.on(IPC_MESSAGES.LOGIN, async() => {
        const account = await authProvider.login(mainWindow);

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
    });

    ipcMain.on(IPC_MESSAGES.LOGOUT, async() => {
        await authProvider.logout();
        await mainWindow.loadFile(path.join(__dirname, './index.html'));
    });

    ipcMain.on(IPC_MESSAGES.GET_PROFILE, async() => {

        const tokenRequest = {
            scopes: ['User.Read'],
        };

        const token = await authProvider.getToken(mainWindow, tokenRequest);
        const account = authProvider.account

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        const graphResponse = await callEndpointWithToken(`${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`, token);

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
        mainWindow.webContents.send(IPC_MESSAGES.SET_PROFILE, graphResponse);
    });

    ipcMain.on(IPC_MESSAGES.GET_MAIL, async() => {

        const tokenRequest = {
            scopes: ['Mail.Read'],
        };

        const token = await authProvider.getToken(mainWindow, tokenRequest);
        const account = authProvider.account;

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        const graphResponse = await callEndpointWithToken(`${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`, token);

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
        mainWindow.webContents.send(IPC_MESSAGES.SET_MAIL, graphResponse);
    });
    ```

No corte de código acima, inicializamos um objeto da janela principal do Eletrão e criamos alguns manipuladores de eventos para interações com a janela do Eletrão. Também importamos parâmetros de configuração, aula *instantânea authProvider* para manusear a aquisição de srov-in, sé-out e token, e chamamos a API do Gráfico microsoft.

4. Na mesma pasta *(App),* crie outro ficheiro nomeado *renderer.js* e adicione o seguinte código:

    ```JavaScript
    const { ipcRenderer } = require('electron');
    const { IPC_MESSAGES } = require('./constants');

    // UI event handlers
    document.querySelector('#signIn').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.LOGIN);
    });

    document.querySelector('#signOut').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.LOGOUT);
    });

    document.querySelector('#seeProfile').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.GET_PROFILE);
    });

    document.querySelector('#readMail').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.GET_MAIL);
    });

    // Main process message subscribers
    ipcRenderer.on(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, (event, account) => {
        showWelcomeMessage(account);
    });

    ipcRenderer.on(IPC_MESSAGES.SET_PROFILE, (event, graphResponse) => {
        updateUI(graphResponse, `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`);
    });

    ipcRenderer.on(IPC_MESSAGES.SET_MAIL, (event, graphResponse) => {
        updateUI(graphResponse, `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`);
    });

    // DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("signIn");
    const signOutButton = document.getElementById("signOut");
    const cardDiv = document.getElementById("cardDiv");
    const profileDiv = document.getElementById("profileDiv");
    const tabList = document.getElementById("list-tab");
    const tabContent = document.getElementById("nav-tabContent");

    function showWelcomeMessage(account) {
        cardDiv.style.display = "initial";
        welcomeDiv.innerHTML = `Welcome ${account.name}`;
        signInButton.hidden = true;
        signOutButton.hidden = false;
    }

    function clearTabs() {
        tabList.innerHTML = "";
        tabContent.innerHTML = "";
    }

    function updateUI(data, endpoint) {

        console.log(`Graph API responded at: ${new Date().toString()}`);

        if (endpoint === `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`) {
            setProfile(data);
        } else if (endpoint === `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`) {
            setMail(data);
        }
    }

    function setProfile(data) {
        profileDiv.innerHTML = ''

        const title = document.createElement('p');
        const email = document.createElement('p');
        const phone = document.createElement('p');
        const address = document.createElement('p');

        title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
        email.innerHTML = "<strong>Mail: </strong>" + data.mail;
        phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
        address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;

        profileDiv.appendChild(title);
        profileDiv.appendChild(email);
        profileDiv.appendChild(phone);
        profileDiv.appendChild(address);
    }

    function setMail(data) {
        const mailInfo = data;
        if (mailInfo.value.length < 1) {
            alert("Your mailbox is empty!")
        } else {
            clearTabs();
            mailInfo.value.slice(0, 10).forEach((d, i) => {
                    createAndAppendListItem(d, i);
                    createAndAppendContentItem(d, i);
            });
        }
    }

    function createAndAppendListItem(d, i) {
        const listItem = document.createElement("a");
        listItem.setAttribute("class", "list-group-item list-group-item-action")
        listItem.setAttribute("id", "list" + i + "list")
        listItem.setAttribute("data-toggle", "list")
        listItem.setAttribute("href", "#list" + i)
        listItem.setAttribute("role", "tab")
        listItem.setAttribute("aria-controls", i)
        listItem.innerHTML = d.subject;
        tabList.appendChild(listItem);
    }

    function createAndAppendContentItem(d, i) {
        const contentItem = document.createElement("div");
        contentItem.setAttribute("class", "tab-pane fade")
        contentItem.setAttribute("id", "list" + i)
        contentItem.setAttribute("role", "tabpanel")
        contentItem.setAttribute("aria-labelledby", "list" + i + "list")

        if (d.from) {
            contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
            tabContent.appendChild(contentItem);
        }
    }
    ```

5. Finalmente, crie um ficheiro chamado *constants.js* que armazenará as constantes de cordas para descrever os **eventos** da aplicação:

    ```JavaScript
    const IPC_MESSAGES = {
        SHOW_WELCOME_MESSAGE: 'SHOW_WELCOME_MESSAGE',
        LOGIN: 'LOGIN',
        LOGOUT: 'LOGOUT',
        GET_PROFILE: 'GET_PROFILE',
        SET_PROFILE: 'SET_PROFILE',
        GET_MAIL: 'GET_MAIL',
        SET_MAIL: 'SET_MAIL'
    }

    module.exports = {
        IPC_MESSAGES: IPC_MESSAGES,
    }
    ```

Tem agora um GUI simples e interações para a sua aplicação Electron. Após completar o resto do tutorial, o arquivo e a estrutura da pasta do seu projeto devem ser semelhantes aos seguintes:

```
ElectronDesktopApp/
├── App
│   ├── authProvider.js
│   ├── constants.js
│   ├── fetch.js
│   ├── main.js
│   ├── renderer.js
│   ├── index.html
├── package.json
└── .env
```

## <a name="add-authentication-logic-to-your-app"></a>Adicione lógica de autenticação à sua app

Na pasta *App,* crie um ficheiro chamado *AuthProvider.js*. Isto conterá uma classe de fornecedor de autenticação que tratará login, logout, aquisição de fichas, seleção de conta e tarefas de autenticação relacionadas usando o Nó MSAL. Adicione o seguinte código:

```JavaScript
const { PublicClientApplication, LogLevel, CryptoProvider } = require('@azure/msal-node');
const { protocol } = require('electron');
const path = require('path');
const url = require('url');

/**
 * To demonstrate best security practices, this Electron sample application makes use of
 * a custom file protocol instead of a regular web (https://) redirect URI in order to
 * handle the redirection step of the authorization flow, as suggested in the OAuth2.0 specification for Native Apps.
 */
const CUSTOM_FILE_PROTOCOL_NAME = process.env.REDIRECT_URI.split(':')[0]; // e.g. msal://redirect

/**
 * Configuration object to be passed to MSAL instance on creation.
 * For a full list of MSAL Node configuration parameters, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md
 */
const MSAL_CONFIG = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: `${process.env.AAD_ENDPOINT_HOST}${process.env.TENANT_ID}`,
        redirectUri: process.env.REDIRECT_URI,
    },
    system: {
        loggerOptions: {
            loggerCallback(loglevel, message, containsPii) {
                console.log(message);
            },
            piiLoggingEnabled: false,
            logLevel: LogLevel.Verbose,
        }
    }
};

class AuthProvider {

    clientApplication;
    cryptoProvider;
    authCodeUrlParams;
    authCodeRequest;
    pkceCodes;
    account;

    constructor() {
        /**
         * Initialize a public client application. For more information, visit:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-public-client-application.md
         */
        this.clientApplication = new PublicClientApplication(MSAL_CONFIG);
        this.account = null;

        // Initialize CryptoProvider instance
        this.cryptoProvider = new CryptoProvider();

        this.setRequestObjects();
    }

    /**
     * Initialize request objects used by this AuthModule.
     */
    setRequestObjects() {
        const requestScopes =  ['openid', 'profile', 'User.Read'];
        const redirectUri = process.env.REDIRECT_URI;

        this.authCodeUrlParams = {
            scopes: requestScopes,
            redirectUri: redirectUri
        };

        this.authCodeRequest = {
            scopes: requestScopes,
            redirectUri: redirectUri,
            code: null
        }

        this.pkceCodes = {
            challengeMethod: "S256", // Use SHA256 Algorithm
            verifier: "", // Generate a code verifier for the Auth Code Request first
            challenge: "" // Generate a code challenge from the previously generated code verifier
        };
    }

    async login(authWindow) {
        const authResult = await this.getTokenInteractive(authWindow, this.authCodeUrlParams);
        return this.handleResponse(authResult);
    }

    async logout() {
        if (this.account) {
            await this.clientApplication.getTokenCache().removeAccount(this.account);
            this.account = null;
        }
    }

    async getToken(authWindow, tokenRequest) {
        let authResponse;

        authResponse = await this.getTokenInteractive(authWindow, tokenRequest);

        return authResponse.accessToken || null;
    }

    // This method contains an implementation of access token acquisition in authorization code flow
    async getTokenInteractive(authWindow, tokenRequest) {

        /**
         * Proof Key for Code Exchange (PKCE) Setup
         *
         * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod parameters
         * in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
         * second leg (acquireTokenByCode() API).
         *
         * MSAL Node provides PKCE Generation tools through the CryptoProvider class, which exposes
         * the generatePkceCodes() asynchronous API. As illustrated in the example below, the verifier
         * and challenge values should be generated previous to the authorization flow initiation.
         *
         * For details on PKCE code generation logic, consult the
         * PKCE specification https://tools.ietf.org/html/rfc7636#section-4
         */

        const {verifier, challenge} = await this.cryptoProvider.generatePkceCodes();

        this.pkceCodes.verifier = verifier;
        this.pkceCodes.challenge = challenge;

        const authCodeUrlParams = {
            ...this.authCodeUrlParams,
            scopes: tokenRequest.scopes,
            codeChallenge: this.pkceCodes.challenge, // PKCE Code Challenge
            codeChallengeMethod: this.pkceCodes.challengeMethod // PKCE Code Challenge Method
        };

        const authCodeUrl = await this.clientApplication.getAuthCodeUrl(authCodeUrlParams);

        protocol.registerFileProtocol(CUSTOM_FILE_PROTOCOL_NAME, (req, callback) => {
            const requestUrl = url.parse(req.url, true);
            callback(path.normalize(`${__dirname}/${requestUrl.path}`));
        });

        const authCode = await this.listenForAuthCode(authCodeUrl, authWindow);

        const authResponse = await this.clientApplication.acquireTokenByCode({
            ...this.authCodeRequest,
            scopes: tokenRequest.scopes,
            code: authCode,
            codeVerifier: this.pkceCodes.verifier // PKCE Code Verifier
        });

        return authResponse;
    }

    // Listen for authorization code response from Azure AD
    async listenForAuthCode(navigateUrl, authWindow) {

        authWindow.loadURL(navigateUrl);

        return new Promise((resolve, reject) => {
            authWindow.webContents.on('will-redirect', (event, responseUrl) => {
                try {
                    const parsedUrl = new URL(responseUrl);
                    const authCode = parsedUrl.searchParams.get('code');
                    resolve(authCode);
                } catch (err) {
                    reject(err);
                }
            });
        });
    }

    /**
     * Handles the response from a popup or redirect. If response is null, will check if we have any accounts and attempt to sign in.
     * @param response
     */
    async handleResponse(response) {
        if (response !== null) {
            this.account = response.account;
        } else {
            this.account = await this.getAccount();
        }

        return this.account;
    }

    /**
     * Calls getAllAccounts and determines the correct account to sign into, currently defaults to first account found in cache.
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    async getAccount() {
        const cache = this.clientApplication.getTokenCache();
        const currentAccounts = await cache.getAllAccounts();

        if (currentAccounts === null) {
            console.log('No accounts detected');
            return null;
        }

        if (currentAccounts.length > 1) {
            // Add choose account code here
            console.log('Multiple accounts detected, need to add choose account code.');
            return currentAccounts[0];
        } else if (currentAccounts.length === 1) {
            return currentAccounts[0];
        } else {
            return null;
        }
    }
}

module.exports = AuthProvider;
```

No corte de código acima, inicializamos o nó MSAL `PublicClientApplication` passando um objeto de configuração `msalConfig` (). Em `login` seguida, expusemos, `logout` e `getToken` métodos a serem chamados pelo módulo principal *(main.js).* Dentro `login` `getToken` e, adquirimos ID e tokens de acesso, respectivamente, solicitando primeiro um código de autorização e, em seguida, trocando-o com um token usando a API pública MSAL `acquireTokenByCode` Node.

## <a name="add-a-method-to-call-a-web-api"></a>Adicione um método para chamar uma API web

Criar outro ficheiro chamado *fetch.js*. Este ficheiro conterá um cliente Axios HTTP para escoar chamadas REST para a API do Gráfico microsoft.

```JavaScript
const axios = require('axios');

/**
 * Makes an Authorization 'Bearer' request with the given accessToken to the given endpoint.
 * @param endpoint
 * @param accessToken
 */
async function callEndpointWithToken(endpoint, accessToken) {
    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('Request made at: ' + new Date().toString());

    const response = await axios.default.get(endpoint, options);

    return response.data;
}

module.exports = {
    callEndpointWithToken: callEndpointWithToken,
};
```

## <a name="add-app-registration-details"></a>Adicionar detalhes de registo de aplicativos

Por fim, crie um ficheiro ambiental para armazenar os detalhes do registo da aplicação que serão utilizados na aquisição de fichas. Para tal, crie um ficheiro chamado *.env* dentro da pasta raiz da amostra *(ElectronDesktopApp),* e adicione o seguinte código:

```
# Credentials
CLIENT_ID=Enter_the_Application_Id_Here
TENANT_ID=Enter_the_Tenant_Id_Here

# Configuration
REDIRECT_URI=msal://redirect

# Endpoints
AAD_ENDPOINT_HOST=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT_HOST=Enter_the_Graph_Endpoint_Here

# RESOURCES
GRAPH_ME_ENDPOINT=v1.0/me
GRAPH_MAIL_ENDPOINT=v1.0/me/messages

# SCOPES
GRAPH_SCOPES=User.Read Mail.Read
```

Preencha estes detalhes com os valores que obtém no portal de registo de aplicações Azure:

- `Enter_the_Tenant_Id_here` deve ser um dos seguintes:
  - Se a sua candidatura suporta *contas neste diretório organizacional,* substitua este valor pelo **ID** do Inquilino ou **nome de Inquilino.** Por exemplo, `contoso.microsoft.com`.
  - Se a sua candidatura suportar *contas em qualquer diretório organizacional,* substitua este valor por `organizations` .
  - Se a sua aplicação suportar *contas em qualquer diretório organizacional e contas pessoais da Microsoft,* substitua este valor por `common` .
  - Para restringir apenas o suporte às *contas pessoais da Microsoft,* substitua este valor por `consumers` .
- `Enter_the_Application_Id_Here`: **Identificação do Formulário de Inscrição (cliente)** do requerimento que registou.
- `Enter_the_Cloud_Instance_Id_Here`: A caixa de nuvem Azure em que a sua aplicação está registada.
  - Para a nuvem azure principal (ou *global),* insira `https://login.microsoftonline.com/` .
  - Para nuvens **nacionais** (por exemplo, China), você pode encontrar valores apropriados nas [nuvens nacionais.](authentication-national-cloud.md)
- `Enter_the_Graph_Endpoint_Here` é o caso da API do Gráfico microsoft com que a aplicação deve comunicar.
  - Para o ponto final **global** da Microsoft Graph API, substitua ambas as instâncias desta cadeia por `https://graph.microsoft.com/` .
  - Para pontos finais em implementações **nacionais** em nuvem, consulte [as implementações](/graph/deployments) nacionais em nuvem na documentação do Microsoft Graph.

## <a name="test-the-app"></a>Testar a aplicação

Concluiu a criação da aplicação e está agora pronto para lançar a aplicação de desktop da Electron e testar a funcionalidade da aplicação.

1. Inicie a aplicação executando o seguinte comando a partir da raiz da sua pasta de projeto:

```console
electron App/main.js
```

2. Na janela principal da aplicação, deverá ver o conteúdo do seu *ficheiroindex.html* e o botão **Iniciar.**

## <a name="test-sign-in-and-sign-out"></a>Sinal de teste e assinar

Depois da *index.htmcargas* de ficheiros l, selecione **Iniciar S-No**. É-lhe pedido que assine com a plataforma de identidade da Microsoft:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-01-signin-prompt.png" alt-text="sinal de insusição":::

Se consentir com as permissões solicitadas, as aplicações web exibem o seu nome de utilizador, o que significa um login bem sucedido:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-03-after-signin.png" alt-text="bem-sucedido de in":::

## <a name="test-web-api-call"></a>Teste chamada de API web

Depois de iniciar sessão, selecione **Ver Perfil** para visualizar as informações do perfil do utilizador devolvidas na resposta da chamada para a API do Gráfico da Microsoft:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-04-profile.png" alt-text="informações de perfil do Microsoft Graph":::

Selecione **Ler E-mails** para ver as mensagens na conta do utilizador. Será apresentado com um ecrã de consentimento:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-05-consent-mail.png" alt-text="ecrã de consentimento para read.mail permissão":::

Após o consentimento, irá visualizar as mensagens devolvidas na resposta da chamada para a API do Gráfico da Microsoft:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-06-mails.png" alt-text="informações de correio do Microsoft Graph":::

## <a name="how-the-application-works"></a>Como funciona a aplicação

Quando um utilizador seleciona o botão **'Iniciar'** pela primeira vez, obtenha o `getTokenInteractive` método de *AuthProvider.js* é chamado. Este método redireciona o utilizador para iniciar sômposições com o ponto final da *plataforma de identidade* da Microsoft e validar as credenciais do utilizador e, em seguida, obtém um código de **autorização**. Este código é então trocado por um token de acesso utilizando `acquireTokenByCode` a API pública do Nó MSAL.

Neste momento, um código de autorização protegido pelo PKCE é enviado para o ponto final de token protegido pelo CORS e é trocado por fichas. Um token de ID, token de acesso e token de atualização são recebidos pela sua aplicação e processados pelo MSAL Node, e as informações contidas nos tokens estão em cache.

O token de ID contém informações básicas sobre o utilizador, como o seu nome de exibição. O token de acesso tem uma vida útil limitada e expira após 24 horas. Se planeia utilizar estes tokens para aceder a recursos protegidos, o seu servidor back-end *deve* validá-lo para garantir que o token foi emitido a um utilizador válido para a sua aplicação.

A aplicação de ambiente de trabalho que criou neste tutorial faz uma chamada REST para a Microsoft Graph API usando um token de acesso como símbolo de portador no cabeçalho de pedido[(RFC 6750).](https://tools.ietf.org/html/rfc6750)

A API do Microsoft Graph requer que o âmbito de leitura do *utilizador.leia* para ler o perfil de um utilizador. Por predefinição, este âmbito é automaticamente adicionado em todas as aplicações registadas no portal Azure. Outros APIs para o Microsoft Graph, bem como APIs personalizados para o seu servidor back-end, podem necessitar de âmbitos adicionais. Por exemplo, a API do Gráfico microsoft requer o âmbito *Mail.Read* para listar o e-mail do utilizador.

À medida que adiciona âmbitos, os seus utilizadores poderão ser solicitados a fornecer consentimento adicional para os âmbitos adicionados.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passos seguintes

Se quiser mergulhar mais profundamente no desenvolvimento de aplicações para desktop Node.js e Eletrões na plataforma de identidade da Microsoft, consulte a nossa série de cenários multi-partes:

> [!div class="nextstepaction"]
> [Cenário: Aplicativo de desktop que chama APIs web](scenario-desktop-overview.md)
