---
title: 'Tutorial: Utilizadores de inscrição numa aplicação web Node.js & Express | Rio Azure'
titleSuffix: Microsoft identity platform
description: Neste tutorial, adiciona suporte para utilizadores que se inscrevam numa aplicação web.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 3f1f26acbba0f5830421e760d6a68a11f618fa85
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648995"
---
# <a name="tutorial-sign-in-users-in-a-nodejs--express-web-app"></a>Tutorial: Utilizadores de inscrição numa aplicação web Node.js & Express

Neste tutorial, você constrói uma aplicação web que assina os utilizadores. A aplicação web que constrói utiliza a [Microsoft Authentication Library (MSAL) para nó](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Siga os passos neste tutorial para:

> [!div class="checklist"]
> - Registe a inscrição no portal Azure
> - Criar um projeto de aplicação web Express
> - Instalar os pacotes da biblioteca de autenticação
> - Adicionar detalhes de registo de aplicativos
> - Adicionar código para o início de sessão do utilizador
> - Testar a aplicação

## <a name="prerequisites"></a>Pré-requisitos

- [Node.js](https://nodejs.org/en/download/)
- [Código de Estúdio Visual](https://code.visualstudio.com/download) ou outro editor de código

## <a name="register-the-application"></a>Registar o pedido

Em primeiro lugar, complete os passos no [Registo de uma aplicação com a plataforma de identidade da Microsoft](quickstart-register-app.md) para registar a sua aplicação.

Utilize as seguintes definições para o registo da sua aplicação:

- Nome: `ExpressWebApp` (sugerido)
- Tipos de conta **suportados: Contas em qualquer diretório organizacional (qualquer diretório AD Azure - Multitenant) e contas pessoais da Microsoft (por exemplo, Skype, Xbox)**
- Tipo de plataforma: **Web**
- Redirecionamento URI: `http://localhost:3000/redirect`
- Segredo do cliente: `*********` (registar este valor para uso em um passo posterior - é mostrado apenas uma vez)

## <a name="create-the-project"></a>Criar o projeto

Crie uma pasta para hospedar a sua aplicação, por *exemplo, ExpressWebApp*.

1. Primeiro, mude para o diretório do seu projeto no seu terminal e, em seguida, executar os `npm` seguintes comandos:

```console
    npm init -y
    npm install --save express
```

2. Em seguida, crie um ficheiro nomeado *index.js* e adicione o seguinte código:

```JavaScript
    const express = require("express");
    const msal = require('@azure/msal-node');

    const SERVER_PORT = process.env.PORT || 3000;

    // Create Express App and Routes
    const app = express();

    app.listen(SERVER_PORT, () => console.log(`Msal Node Auth Code Sample app listening on port ${SERVER_PORT}!`))
```

Tem agora um simples servidor web a funcionar na porta 3000. A estrutura de ficheiros e pastas do seu projeto deve ser semelhante ao seguinte:

```
ExpressWebApp/
├── index.js
└── package.json
```

## <a name="install-the-auth-library"></a>Instale a biblioteca de auth

Localize a raiz do seu diretório de projeto num terminal e instale o pacote msal nó através de NPM.

```console
    npm install --save @azure/msal-node
```

## <a name="add-app-registration-details"></a>Adicionar detalhes de registo de aplicativos

No *ficheiroindex.js* que criou anteriormente, adicione o seguinte código:

```JavaScript
    // Before running the sample, you will need to replace the values in the config,
    // including the clientSecret
    const config = {
        auth: {
            clientId: "Enter_the_Application_Id",
            authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Id_here",
            clientSecret: "Enter_the_Client_secret"
        },
        system: {
            loggerOptions: {
                loggerCallback(loglevel, message, containsPii) {
                    console.log(message);
                },
                piiLoggingEnabled: false,
                logLevel: msal.LogLevel.Verbose,
            }
        }
    };
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
- `Enter_the_Client_secret`: Substitua este valor pelo segredo do cliente que criou anteriormente. Para gerar uma nova chave, utilize **Certificados & segredos** nas definições de registo de aplicações no portal Azure.

> [!WARNING]
> Qualquer segredo de texto simples no código fonte representa um risco acrescido de segurança. Este artigo usa um segredo de cliente de texto simples apenas para a simplicidade. Use [credenciais](active-directory-certificate-credentials.md) de certificado em vez de segredos de cliente nas suas aplicações confidenciais de clientes, especialmente as que pretende implementar para a produção.

## <a name="add-code-for-user-login"></a>Adicionar código para o início de sessão do utilizador

No *ficheiroindex.js* que criou anteriormente, adicione o seguinte código:

```JavaScript
    // Create msal application object
    const cca = new msal.ConfidentialClientApplication(config);

    app.get('/', (req, res) => {
        const authCodeUrlParameters = {
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        // get url to sign user in and consent to scopes needed for application
        cca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
            res.redirect(response);
        }).catch((error) => console.log(JSON.stringify(error)));
    });

    app.get('/redirect', (req, res) => {
        const tokenRequest = {
            code: req.query.code,
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        cca.acquireTokenByCode(tokenRequest).then((response) => {
            console.log("\nResponse: \n:", response);
            res.sendStatus(200);
        }).catch((error) => {
            console.log(error);
            res.status(500).send(error);
        });
    });
```

## <a name="test-sign-in"></a>Sinal de teste em

Concluiu a criação da aplicação e está agora pronto para testar a funcionalidade da aplicação.

1. Inicie a aplicação Node.js consola executando o seguinte comando a partir da raiz da sua pasta de projeto:

```console
   node index.js
```

2. Abra uma janela do browser e navegue para `http://localhost:3000`. Você deve ver um ecrã de inscrição:

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/sign-in-screen.png" alt-text="Ecrã de inscrição AD AD Azure":::

3. Assim que introduzir as suas credenciais, deverá ver um ecrã de consentimento a pedir-lhe que aprove as permissões para a aplicação.

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/consent-screen.png" alt-text="Ecrã de consentimento AD Azure exibindo":::

## <a name="how-the-application-works"></a>Como funciona a aplicação

Neste tutorial, inicializou um objeto MSAL Node [ConfidencialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) ao passar-lhe um objeto de configuração *(msalConfig*) que contém parâmetros obtidos a partir do seu registo de aplicações AZure AD no portal Azure. A aplicação web que criou utiliza o fluxo de concessão de [código de autorização OAuth 2.0](./v2-oauth2-auth-code-flow.md) para utilizadores de inscrição e obtenção de ID e tokens de acesso.

## <a name="next-steps"></a>Passos seguintes

Se quiser mergulhar mais profundamente no desenvolvimento de aplicações web Node.js & Express na plataforma de identidade da Microsoft, consulte a nossa série de cenários multi-partes:

> [!div class="nextstepaction"]
> [Cenário: aplicação web que assina nos utilizadores](scenario-web-app-sign-user-overview.md)