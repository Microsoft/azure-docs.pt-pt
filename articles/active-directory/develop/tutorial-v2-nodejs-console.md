---
title: 'Tutorial: Ligue para o Microsoft Graph numa aplicação de consola Node.js | Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste tutorial, você constrói uma aplicação para consolas para chamar o Microsoft Graph para uma aplicação de consola Node.js.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 33d3712e25a06419e0ccc5914cdddfae7d85a371
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101645794"
---
# <a name="tutorial-call-the-microsoft-graph-api-in-a-nodejs-console-app"></a>Tutorial: Ligue para a Microsoft Graph API numa aplicação de consola Node.js

Neste tutorial, você constrói uma aplicação de consola que chama API de Gráfico microsoft usando a sua própria identidade. A aplicação de consola que constrói utiliza a [Microsoft Authentication Library (MSAL) para Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Siga os passos neste tutorial para:

> [!div class="checklist"]
> - Registe a inscrição no portal Azure
> - Criar um projeto de aplicativo de consola Node.js
> - Adicione lógica de autenticação à sua app
> - Adicionar detalhes de registo de aplicativos
> - Adicione um método para chamar uma API web
> - Testar a aplicação

## <a name="prerequisites"></a>Pré-requisitos

- [Node.js](https://nodejs.org/en/download/)
- [Código de Estúdio Visual](https://code.visualstudio.com/download) ou outro editor de código

## <a name="register-the-application"></a>Registar o pedido

Em primeiro lugar, complete os passos no [Registo de uma aplicação com a plataforma de identidade da Microsoft](quickstart-register-app.md) para registar a sua aplicação.

Utilize as seguintes definições para o registo da sua aplicação:

- Nome: `NodeConsoleApp` (sugerido)
- Tipos de conta **suportados: Contas neste diretório organizacional apenas**
- Permissões API: **Microsoft APIs**  >  **Microsoft Graph**  >  **Application Permisses** > `User.Read.All`
- Segredo do cliente: `*********` (registar este valor para uso em um passo posterior - é mostrado apenas uma vez)

## <a name="create-the-project"></a>Criar o projeto

Crie uma pasta para hospedar a sua aplicação, por exemplo *NodeConsoleApp*.

1. Primeiro, mude para o diretório do seu projeto no seu terminal e, em seguida, executar os seguintes comandos NPM:

```console
    npm init -y
    npm install --save dotenv yargs axios @azure/msal-node
```

2. Em seguida, crie uma pasta chamada *bin*. Em seguida, dentro desta pasta, crie um ficheiro nomeado *index.js* e adicione o seguinte código:

```JavaScript
#!/usr/bin/env node

// read in env settings
require('dotenv').config();

const yargs = require('yargs');

const fetch = require('./fetch');
const auth = require('./auth');

const options = yargs
    .usage('Usage: --op <operation_name>')
    .option('op', { alias: 'operation', describe: 'operation name', type: 'string', demandOption: true })
    .argv;

async function main() {
    console.log(`You have selected: ${options.op}`);

    switch (yargs.argv['op']) {
        case 'getUsers':

            try {
                // here we get an access token
                const authResponse = await auth.getToken(auth.tokenRequest);

                // call the web API with the access token
                const users = await fetch.callApi(auth.apiConfig.uri, authResponse.accessToken);

                // display result
                console.log(users);
            } catch (error) {
                console.log(error);
            }

            break;
        default:
            console.log('Select a Graph operation first');
            break;
    }
};

main();
```

Este ficheiro refere dois outros módulos de nó: *auth.js* que contém uma implementação do Nó MSAL para a aquisição de tokens de acesso, e *fetch.js* que contém um método para fazer um pedido HTTP à Microsoft Graph API com um token de acesso. Após completar o resto do tutorial, o arquivo e a estrutura da pasta do seu projeto devem ser semelhantes aos seguintes:

```
NodeConsoleApp/
├── bin
│   ├── auth.js
│   ├── fetch.js
│   ├── index.js
├── package.json
└── .env
```

## <a name="add-authentication-logic"></a>Adicionar lógica de autenticação

Dentro da pasta *de caixotes,* crie outro ficheiro nomeado *auth.js* e adicione o seguinte código para adquirir um token de acesso para apresentar ao ligar para a API do Gráfico microsoft.

```JavaScript
const msal = require('@azure/msal-node');

/**
 * Configuration object to be passed to MSAL instance on creation.
 * For a full list of MSAL Node configuration parameters, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md
 */
const msalConfig = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: process.env.AAD_ENDPOINT + process.env.TENANT_ID,
        clientSecret: process.env.CLIENT_SECRET,
    }
};

/**
 * With client credentials flows permissions need to be granted in the portal by a tenant administrator.
 * The scope is always in the format '<resource>/.default'. For more, visit:
 * https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow
 */
const tokenRequest = {
    scopes: [process.env.GRAPH_ENDPOINT + '.default'],
};

const apiConfig = {
    uri: process.env.GRAPH_ENDPOINT + 'v1.0/users',
};

/**
 * Initialize a confidential client application. For more info, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md
 */
const cca = new msal.ConfidentialClientApplication(msalConfig);

/**
 * Acquires token with client credentials.
 * @param {object} tokenRequest
 */
async function getToken(tokenRequest) {
    return await cca.acquireTokenByClientCredential(tokenRequest);
}

module.exports = {
    apiConfig: apiConfig,
    tokenRequest: tokenRequest,
    getToken: getToken
};
```

No corte de código acima, criamos primeiro um objeto de configuração *(msalConfig*) e passamos-o para rubricar uma MsAL [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md). Em seguida, criamos um método para adquirir fichas através **de credenciais** de cliente e finalmente expor este módulo a ser acedido por *main.js*. Os parâmetros de configuração deste módulo são extraídos de um ficheiro de ambiente, que iremos criar no passo seguinte.

## <a name="add-app-registration-details"></a>Adicionar detalhes de registo de aplicativos

Crie um ficheiro ambiental para armazenar os detalhes do registo da aplicação que serão usados na aquisição de fichas. Para tal, crie um ficheiro chamado *.env* dentro da pasta raiz da amostra *(NodeConsoleApp*), e adicione o seguinte código:

```
# Credentials
TENANT_ID=Enter_the_Tenant_Id_Here
CLIENT_ID=Enter_the_Application_Id_Here
CLIENT_SECRET=Enter_the_Client_Secret_Here

# Endpoints
AAD_ENDPOINT=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT=Enter_the_Graph_Endpoint_Here
```

Preencha estes detalhes com os valores que obtém no portal de registo de aplicações Azure:

- `Enter_the_Tenant_Id_here` deve ser um dos seguintes:
  - Se a sua candidatura suporta *contas neste diretório organizacional,* substitua este valor pelo **ID** do Inquilino ou **nome de Inquilino.** Por exemplo, `contoso.microsoft.com`.
  - Se a sua candidatura suportar *contas em qualquer diretório organizacional,* substitua este valor por `organizations` .
  - Se a sua aplicação suportar *contas em qualquer diretório organizacional e contas pessoais da Microsoft,* substitua este valor por `common` .
  - Para restringir apenas o suporte às *contas pessoais da Microsoft,* substitua este valor por `consumers` .
- `Enter_the_Application_Id_Here`: **Identificação do Formulário de Inscrição (cliente)** do requerimento que registou.
- `Enter_the_Cloud_Instance_Id_Here`: A caixa de nuvem Azure em que a sua aplicação está registada.
  - Para a nuvem azure principal (ou *global),* insira `https://login.microsoftonline.com` .
  - Para nuvens **nacionais** (por exemplo, China), você pode encontrar valores apropriados nas [nuvens nacionais.](authentication-national-cloud.md)
- `Enter_the_Graph_Endpoint_Here` é o caso da API do Gráfico microsoft com que a aplicação deve comunicar.
  - Para o ponto final **global** da Microsoft Graph API, substitua ambas as instâncias desta cadeia por `https://graph.microsoft.com` .
  - Para pontos finais em implementações **nacionais** em nuvem, consulte [as implementações](/graph/deployments) nacionais em nuvem na documentação do Microsoft Graph.

## <a name="add-a-method-to-call-a-web-api"></a>Adicione um método para chamar uma API web

Dentro da pasta *do caixote do lixo,* crie outro ficheiro nomeado *fetch.js* e adicione o seguinte código para escoar chamadas REST para a API do Gráfico microsoft:

```javascript
const axios = require('axios');

/**
 * Calls the endpoint with authorization bearer token.
 * @param {string} endpoint
 * @param {string} accessToken
 */
async function callApi(endpoint, accessToken) {

    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('request made to web API at: ' + new Date().toString());

    try {
        const response = await axios.default.get(endpoint, options);
        return response.data;
    } catch (error) {
        console.log(error)
        return error;
    }
};

module.exports = {
    callApi: callApi
};
```

Aqui, o `callApi` método é usado para fazer um pedido HTTP contra um recurso protegido que requer um `GET` token de acesso. Em seguida, o pedido devolve o conteúdo ao autor da chamada. Este método adiciona o símbolo adquirido no *cabeçalho de autorização HTTP*. O recurso protegido aqui é o [ponto final dos utilizadores](/graph/api/user-list) da Microsoft Graph API que exibe os utilizadores no inquilino onde esta aplicação está registada.

## <a name="test-the-app"></a>Testar a aplicação

Concluiu a criação da aplicação e está agora pronto para testar a funcionalidade da aplicação.

Inicie a aplicação Node.js consola executando o seguinte comando a partir da raiz da sua pasta de projeto:

```console
node . --op getUsers
```

Isto deve resultar em alguma resposta JSON da Microsoft Graph API e deve ver uma série de objetos de utilizador na consola:

```console
You have selected: getUsers
request made to web API at: Fri Jan 22 2021 09:31:52 GMT-0800 (Pacific Standard Time)
{
    '@odata.context': 'https://graph.microsoft.com/v1.0/$metadata#users',
    value: [
        {
            displayName: 'Adele Vance'
            givenName: 'Adele',
            jobTitle: 'Retail Manager',
            mail: 'AdeleV@msaltestingjs.onmicrosoft.com',
            mobilePhone: null,
            officeLocation: '18/2111',
            preferredLanguage: 'en-US',
            surname: 'Vance',
            userPrincipalName: 'AdeleV@msaltestingjs.onmicrosoft.com',
            id: 'a6a218a5-f5ae-462a-acd3-581af4bcca00'
        }
    ]
}
```
:::image type="content" source="media/tutorial-v2-nodejs-console/screenshot.png" alt-text="Interface de linha de comando que apresenta resposta de gráfico":::

## <a name="how-the-application-works"></a>Como funciona a aplicação

Esta aplicação utiliza [o subsídio de credenciais de clienteS OAuth 2.0](./v2-oauth2-client-creds-grant-flow.md). Este tipo de concessão é comumente utilizado para interações de servidor para servidor que têm de ser executadas em segundo plano, sem interação imediata com um utilizador. As credenciais concedem fluxo permite que um serviço web (cliente confidencial) utilize as suas próprias credenciais, em vez de se fazer passar por utilizador, para autenticar quando liga para outro serviço web. O tipo de aplicações suportadas com este modelo de autenticação são normalmente **daemons** ou **contas de serviço.**

O âmbito de pedido de um fluxo de credencial do cliente é o nome do recurso seguido por `/.default` . Esta notação diz ao Azure Ative Directory (Azure AD) para utilizar as permissões de nível de aplicação declaradas estáticas durante o registo da aplicação. Além disso, estas permissões da API devem ser concedidas por um **administrador de inquilinos.**

## <a name="next-steps"></a>Passos seguintes

Se quiser mergulhar mais profundamente no desenvolvimento de Node.js aplicações de consola na plataforma de identidade da Microsoft, consulte a nossa série de cenários multi-partes:

> [!div class="nextstepaction"]
> [Cenário: Aplicação Daemon](scenario-daemon-overview.md)