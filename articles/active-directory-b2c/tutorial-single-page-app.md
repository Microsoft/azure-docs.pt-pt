---
title: 'Tutorial: Ativar a autenticação numa aplicação de uma página'
titleSuffix: Azure AD B2C
description: Neste tutorial, aprenda a usar o Azure Ative Directory B2C para fornecer login do utilizador para uma aplicação de página única baseada em JavaScript (SPA).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, seo-javascript-september2019, devx-track-js
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6a9f3b864bd8aba2140c7d32d4b5474ff7b95f88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96171233"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-with-azure-ad-b2c"></a>Tutorial: Ativar a autenticação numa aplicação de uma página com Azure AD B2C

Este tutorial mostra-lhe como utilizar o Azure Ative Directory B2C (Azure AD B2C) para se inscrever e inscrever-se nos utilizadores numa aplicação de uma página única (SPA) utilizando:
* [Fluxo de código de autorização OAuth 2.0](./authorization-code-flow.md) (utilizando [MSAL.js 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser))
* [OAuth 2.0 fluxo de subvenção implícita](./implicit-flow-single-page-application.md) (utilizando [MSAL.js 1.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core))

Neste tutorial, o primeiro de uma série em duas partes:

> [!div class="checklist"]
> * Adicione um URL de resposta a um pedido registado no seu inquilino Azure AD B2C
> * Faça o download de uma amostra de código do GitHub
> * Modifique o código do pedido de amostra para trabalhar com o seu inquilino
> * Inscreva-se usando o fluxo do utilizador de inscrição/inscrição

O [próximo tutorial](tutorial-single-page-app-webapi.md) da série permite a parte web da API da amostra de código.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Você precisa dos seguintes recursos Azure AD B2C em vigor antes de continuar com os passos neste tutorial:

* [Inquilino Azure AD B2C](tutorial-create-tenant.md)
* [Inscrição registada](tutorial-register-spa.md) no seu inquilino
* [Fluxos de utilizador criados](tutorial-create-user-flows.md) no seu inquilino

Além disso, precisa do seguinte no seu ambiente de desenvolvimento local:

* [Código de Estúdio Visual](https://code.visualstudio.com/) ou outro editor de código
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Atualizar a aplicação

No [segundo tutorial](./tutorial-register-spa.md) que completou como parte dos pré-requisitos, registou uma aplicação de uma página em Azure AD B2C. Para permitir a comunicação com a amostra de código neste tutorial, adicione um URL de resposta (também chamado de URI redirecionado) ao registo de pedidos.

Para atualizar uma aplicação no seu inquilino Azure AD B2C, pode utilizar a nossa nova experiência de registos de **Aplicações unificadas** ou a nossa experiência de **Aplicações (Legacy).** [Saiba mais sobre a nova experiência.](./app-registrations-training-guide.md)

#### <a name="app-registrations-auth-code-flow"></a>[Registos de aplicativos (fluxo de código auth)](#tab/app-reg-auth/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **as inscrições da App**, selecione o **separador de aplicações Próprias** e, em seguida, selecione a aplicação *spaapp1.*
1. No **Aplicativo de página única**, selecione o link Add **URI** e, em seguida, introduza `http://localhost:6420` .
1. Selecione **Guardar**.
1. Selecione **Descrição geral**.
1. Grave o **ID da Aplicação (cliente)** para utilização num passo posterior quando atualizar o código na aplicação web de uma página única.

#### <a name="app-registrations-implicit-flow"></a>[Registos de aplicativos (fluxo implícito)](#tab/app-reg-implicit/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Selecione **as inscrições da App**, selecione o **separador de aplicações Próprias** e, em seguida, selecione a aplicação *spaapp1.*
1. No **Aplicativo de página única**, selecione o link Add **URI** e, em seguida, introduza `http://localhost:6420` .
1. Em **Implicit Grant**, selecione as caixas de verificação para **Tokens de acesso** e **tokens de ID** se ainda não estiver selecionado e, em seguida, selecione **Save**.
1. Selecione **Descrição geral**.
1. Grave o **ID da Aplicação (cliente)** para utilização num passo posterior quando atualizar o código na aplicação web de uma página única.

#### <a name="applications-legacy"></a>[Candidaturas (Legado)](#tab/applications-legacy/)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Certifique-se de que está a utilizar o diretório que contém o seu inquilino Azure AD B2C selecionando o filtro **de subscrição Diretório +** no menu superior e escolhendo o diretório que contém o seu inquilino.
1. Selecione **Todos os serviços** no canto superior esquerdo do portal Azure e, em seguida, procure e selecione **Azure AD B2C**.
1. Selecione **Aplicações (Legado)** e, em seguida, selecione a aplicação *spaapp1.*
1. Em **URL de resposta**, adicione `http://localhost:6420` .
1. Selecione **Guardar**.
1. Na página de propriedades, grave o **ID da aplicação.** Utilize o ID da aplicação num passo posterior quando atualiza o código na aplicação web de uma página única.

* * *

## <a name="get-the-sample-code"></a>Obter o código de exemplo

Neste tutorial, você configura uma amostra de código que você descarrega do GitHub para trabalhar com o seu inquilino B2C. A amostra demonstra como uma aplicação de uma página pode usar Azure AD B2C para inscrição e inscrição do utilizador, e para chamar uma API web protegida (você ativa a API web no próximo tutorial da série).

* MSAL.js amostra de fluxo de código de autorização de 2.x:

    [Faça o download de um ficheiro zip](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/archive/main.zip) ou clone a amostra do GitHub:

    ```
    git clone https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa.git
    ```
* MSAL.js amostra implícita de fluxo de 1.x:

    [Faça o download de um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) ou clone a amostra do GitHub:

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="update-the-sample"></a>Atualizar a amostra

Agora que obteve a amostra, atualize o código com o nome do seu inquilino Azure AD B2C e o ID da aplicação que gravou num passo anterior.

#### <a name="auth-code-flow-sample"></a>[Amostra de fluxo de código auth](#tab/config-auth/)

1. Abra o ficheiro *authConfig.js* dentro da pasta *App.*
1. No `msalConfig` objeto, encontre a atribuição `clientId` e substitua-a pelo **ID de Aplicação (cliente)** que gravou num passo anterior.
1. Abra o ficheiro `policies.js`.
1. Encontre as entradas e `names` substitua a sua atribuição pelo nome dos fluxos de utilizador que criou num passo anterior, por exemplo `B2C_1_signupsignin1` .
1. Encontre as entradas por baixo `authorities` e substitua-as conforme apropriado pelos nomes dos fluxos de utilizador que criou num passo anterior, por exemplo `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>` .
1. Encontre a atribuição `authorityDomain` e substitua-a por `<your-tenant-name>.b2clogin.com` .
1. Abra o ficheiro `apiConfig.js`.
1. Encontre a atribuição `b2cScopes` e substitua o URL pelo URL de âmbito que criou para a API web, por `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/helloapi/demo.read"]` exemplo.
1. Encontre a atribuição `webApi` e substitua o URL atual pelo URL onde implementou a sua API web no passo 4, por exemplo `webApi: http://localhost:5000/hello` .

#### <a name="implicit-flow-sample"></a>[Amostra de fluxo implícita](#tab/config-implicit/)

1. Abra o ficheiro *authConfig.js* dentro da pasta *JavaScriptSPA.*
1. No `msalConfig` objeto, encontre a atribuição `clientId` e substitua-a pelo **ID de Aplicação (cliente)** que gravou num passo anterior.
1. Abra o ficheiro `policies.js`.
1. Encontre as entradas e `names` substitua a sua atribuição pelo nome dos fluxos de utilizador que criou num passo anterior, por exemplo `B2C_1_signupsignin1` .
1. Encontre as entradas por baixo `authorities` e substitua-as conforme apropriado pelos nomes dos fluxos de utilizador que criou num passo anterior, por exemplo `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>` .
1. Abra o ficheiro `apiConfig.js`.
1. Encontre a atribuição `b2cScopes` e substitua o URL pelo URL de âmbito que criou para a API web, por `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/helloapi/demo.read"]` exemplo.
1. Encontre a atribuição `webApi` e substitua o URL atual pelo URL onde implementou a sua API web no passo 4, por exemplo `webApi: http://localhost:5000/hello` .

* * *

O seu código resultante deve ser semelhante ao seguinte:

#### <a name="auth-code-flow-sample"></a>[Amostra de fluxo de código auth](#tab/review-auth/)

*authConfig.js:*

```javascript
const msalConfig = {
  auth: {
    clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    knownAuthorities: [b2cPolicies.authorityDomain],
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
};
```

*policies.js:*

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
    authorityDomain: "fabrikamb2c.b2clogin.com"
}
```

*apiConfig.js:*

```javascript
const apiConfig = {
  b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
  webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
};
```

#### <a name="implicit-flow-sample"></a>[Amostra de fluxo implícita](#tab/review-implicit/)

*authConfig.js:*

```javascript
const msalConfig = {
  auth: {
    clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    validateAuthority: false
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
};
```

*policies.js:*

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
}
```

*apiConfig.js:*

```javascript
const apiConfig = {
  b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
  webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
};
```

* * *


## <a name="run-the-sample"></a>Executar o exemplo

1. Abra uma janela da consola e navegue para o diretório que contém a amostra. 

    - Para MSAL.js amostra de fluxo de código de autorização de 2,x:

        ```console
        cd ms-identity-b2c-javascript-spa
        ```
    - Para MSAL.js amostra implícita de fluxo de 1,x: 

        ```console
        cd active-directory-b2c-javascript-msal-singlepageapp
        ```

1. Execute os seguintes comandos:

    ```console
    npm install && npm update
    npm start
    ```

    A janela da consola exibe o número de porta do servidor Node.js em execução local:

    ```console
    Listening on port 6420...
    ```
1. Navegue para `http://localhost:6420` ver a aplicação web em execução na sua máquina local.

    :::image type="content" source="media/tutorial-single-page-app/web-app-spa-01-not-logged-in.png" alt-text="Navegador web mostrando aplicação de página única executando localmente":::

### <a name="sign-up-using-an-email-address"></a>Inscrever-se com um endereço de e-mail

Esta aplicação de amostra suporta a inscrição, o início e o reset da palavra-passe. Neste tutorial, inscreva-se através de um endereço de e-mail.

1. Selecione **Iniciar sôs-in** para iniciar o fluxo de utilizador *B2C_1_signupsignin1* especificado num passo anterior.
1. Azure AD B2C apresenta uma página de inscrição que inclui um link de inscrição. Uma vez que ainda não tem uma conta, selecione o link **'Iniciar s-se).**
1. O fluxo de trabalho de inscrição apresenta uma página para recolher e verificar a identidade do utilizador usando um endereço de e-mail. O fluxo de trabalho de inscrição também recolhe a palavra-passe do utilizador e os atributos solicitados definidos no fluxo do utilizador.

    Utilize um endereço de e-mail válido e valide com o código de verificação. Defina uma palavra-passe. Introduza os valores para os atributos solicitados.

    :::image type="content" source="media/tutorial-single-page-app/user-flow-sign-up-workflow-01.png" alt-text="Inscreva-se na página exibida pelo fluxo de utilizador Azure AD B2C":::

1. Selecione **Criar** para criar uma conta local no diretório Azure AD B2C.

Quando seleciona **Criar,** a aplicação mostra o nome do utilizador assinado.

:::image type="content" source="media/tutorial-single-page-app/web-app-spa-02-logged-in.png" alt-text="Navegador web mostrando aplicação de página única com registrado no utilizador":::

Se quiser testar o s-in, selecione o botão **Iniciar sê-lo,** em seguida, selecione **Iniciar sôs e** iniciar sôs com o endereço de e-mail e a palavra-passe que inseriu quando se inscreveu.

### <a name="what-about-calling-the-api"></a>Que tal ligar para a API?

Se selecionar o botão **Call API** após a sessão de sessão, é-lhe apresentado a página de fluxo de utilizador de inscrição/inscrição em vez dos resultados da chamada API. Isto é esperado porque ainda não configuraste a parte da API da aplicação para comunicar com uma aplicação web API registada no *seu* inquilino Azure AD B2C.

Neste momento, a aplicação ainda está a tentar comunicar com a API registada no inquilino de demonstração (fabrikamb2c.onmicrosoft.com), e como não está autenticado com esse inquilino, é apresentada a página de inscrição/inscrição.

Passe para o próximo tutorial da série para ativar a API protegida (ver a secção [etapas seguintes).](#next-steps)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configura uma aplicação de uma página para trabalhar com um fluxo de utilizador no seu inquilino Azure AD B2C para fornecer inscrição e inscrição na capacidade. Completou estes passos:

> [!div class="checklist"]
> * Adicionei um URL de resposta a um pedido registado no seu inquilino Azure AD B2C
> * Descarregou uma amostra de código do GitHub
> * Modificou o código do pedido de amostra para trabalhar com o seu inquilino
> * Inscreva-se usando o fluxo de utilizador de inscrição/inscrição

Passe agora para o próximo tutorial da série para conceder acesso a uma API web protegida a partir do SPA:

> [!div class="nextstepaction"]
> [Tutorial: Proteja e conceda acesso à API web a partir de uma aplicação de uma página >](tutorial-single-page-app-webapi.md)