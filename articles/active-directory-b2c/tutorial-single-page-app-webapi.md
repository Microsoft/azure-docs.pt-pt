---
title: 'Tutorial: Proteja uma API web Node.js utilizando o Azure AD B2C e conceda acesso a uma aplicação de uma página (SPA)'
titleSuffix: Azure AD B2C
description: Neste tutorial, aprenda a usar o Ative Directory B2C para proteger uma API web Node.js e chamá-lo de uma aplicação de uma página.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, devx-track-js
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 737810a7d07d0d97b2e42acffa17fdd32986c48b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93421095"
---
# <a name="tutorial-protect-and-grant-access-to-a-nodejs-web-api-from-a-single-page-application-with-azure-ad-b2c"></a>Tutorial: Proteja e conceda acesso a uma API web Node.js a partir de uma aplicação de uma página com Azure AD B2C

Este tutorial mostra-lhe como chamar um Azure Ative Directory B2C (Azure AD B2C)protegido Node.js API web a partir de uma aplicação de uma página.

Neste tutorial, o segundo de uma série em duas partes:

> [!div class="checklist"]
> * Crie um registo de aplicação web API no seu inquilino Azure AD B2C
> * Configurar âmbitos para a API web
> * Conceder permissões à Web API
> * Modifique uma amostra de código API web para trabalhar com o seu inquilino

No [primeiro tutorial](tutorial-single-page-app.md) desta série, descarregou a amostra de código e modificou-a para assinar nos utilizadores com um fluxo de utilizador no seu inquilino Azure AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Complete os passos e pré-requisitos em [Tutorial: Ative a autenticação numa aplicação de uma página com Azure AD B2C](tutorial-single-page-app.md)
* [Código de Estúdio Visual](https://code.visualstudio.com/) ou outro editor de código
* [Node.js](https://nodejs.org/en/download/)

## <a name="add-a-web-api-application"></a>Adicionar uma aplicação de API Web

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Configure âmbitos

Os âmbitos proporcionam uma forma de reger o acesso a recursos protegidos. São utilizados pela API Web para implementar o controlo de acesso baseado no âmbito. Por exemplo, alguns utilizadores podem ter o acesso de leitura e de escrita, ao passo que outros podem ter apenas permissões só de leitura. Neste tutorial, você define tanto ler como escrever permissões para a API web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Grave o valor em **Âmbitos** para o `demo.read` âmbito de utilização num passo posterior quando configurar a aplicação de uma página. O valor total do âmbito é semelhante a `https://contosob2c.onmicrosoft.com/api/demo.read` .

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API web protegida de outra aplicação, você precisa conceder essa permissão de aplicação para a API web.

No tutorial pré-requisito, criou uma aplicação de uma página chamada *spaapp1*. Neste tutorial, configurar a aplicação para ligar para a API web que criou numa secção anterior, *spaapp1*.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

A sua aplicação web de uma página única foi agora concedida permissões à API web protegida para os âmbitos especificados. Um utilizador autentica-se com Azure AD B2C para utilizar a aplicação de uma página única. A aplicação de uma página única obtém um token de acesso do Azure AD B2C para aceder à API web protegida.

## <a name="configure-the-sample"></a>Configure a amostra

Agora que a API web está registada e definiu os âmbitos, configuure o código API web para trabalhar com o seu inquilino Azure AD B2C. Neste tutorial, você configura uma amostra Node.js web API que você descarrega a partir de GitHub.

[Faça o download de um \* arquivo .zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) ou clone o projeto API da web de amostras do GitHub. Também pode navegar diretamente para o projeto [Azure-Samples/active-directy-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) no GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="configure-the-web-api"></a>Configurar a API Web

1. Abra a *config.jsno* arquivo do seu editor de código.
1. Modifique os valores variáveis para refletir os do registo de candidatura que criou anteriormente. Atualize também o `policyName` fluxo de utilizador que criou como parte dos pré-requisitos. Por exemplo, *B2C_1_signupsignin1.*
    
    ```json
    "credentials": {
        "tenantName": "<your-tenant-name>",
        "clientID": "<your-webapi-application-ID>"
    },
    "policies": {
        "policyName": "B2C_1_signupsignin1"
    },
    "resource": {
        "scope": ["demo.read"] 
    },
    ```

#### <a name="enable-cors"></a>Ativar o CORS

Para permitir que a sua aplicação de uma página única ligue para a API web Node.js, precisa de ativar o [CORS](https://expressjs.com/en/resources/middleware/cors.html) na API web. Numa aplicação de produção deve ter cuidado com o domínio que está a fazer o pedido, mas para este tutorial, permita pedidos de qualquer domínio.

Para ativar o CORS, utilize o seguinte middleware. Na Node.js amostra de código da API web neste tutorial, já foi adicionada ao ficheiro *index.js.*

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

### <a name="configure-the-single-page-application"></a>Configure a aplicação de uma página única

A aplicação de uma página única (SPA) do [tutorial anterior](tutorial-single-page-app.md) da série utiliza Azure AD B2C para inscrição e inscrição do utilizador, e por padrão, chama a API web Node.js protegida pelo inquilino de demonstração *fabrikamb2c.*

Nesta secção, você atualizou a aplicação web de uma página para ligar para a API web Node.js protegida pelo *seu* inquilino Azure AD B2C (e que você executou na sua máquina local).

Para alterar as definições no SPA:

1. No projeto [active-directy-b2c-javascript-msal-singlepageapp][github-js-spa] que descarregou ou clonou no tutorial anterior, abra o ficheiro *apiConfig.js* dentro da pasta *JavaScriptSPA.*
1. Configure a amostra com o URI para o âmbito *de demonstração.read* que criou anteriormente e o URL da API web.
    1. Na `apiConfig` definição, substitua o `b2cScopes` valor pelo URI completo pelo âmbito *de demo.read* (o valor **de Âmbito** que registou anteriormente).
    1. Altere o domínio do `webApi` valor para o URI de redirecionamento que adicionou quando registou a aplicação API web num passo anterior.

    Como a API está acessível no `/hello` ponto final, deixe */olá* no URI.

    A `apiConfig` definição deve ser semelhante ao seguinte bloco de código, mas com o nome do seu inquilino B2C no lugar `<your-tenant-name>` de:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/hello" // '/hello' should remain in the URI
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Executar o SPA e web API

Está agora pronto para testar o acesso da aplicação de uma página à API. Execute tanto a API web Node.js como a aplicação de uma página única javaScript na sua máquina local. Em seguida, inicie sedumento na aplicação de uma página e selecione o botão **Call API** para iniciar um pedido à API protegida.

Embora ambas as aplicações estejam a funcionar localmente quando segue este tutorial, você as configura para usar Azure AD B2C para inscrição/inscrição segura e para conceder acesso à API web protegida.

### <a name="run-the-nodejs-web-api"></a>Executar a API web Node.js

1. Abra uma janela da consola e mude para o diretório que contém a amostra de API web Node.js. Por exemplo:

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. Execute os seguintes comandos:

    ```console
    npm install && npm update
    node index.js
    ```

    A janela da consola apresenta o número de porta onde a aplicação está hospedada.

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>Executar a aplicação de uma página única

1. Abra outra janela da consola e mude para o diretório que contém a amostra JAVAScript SPA. Por exemplo:

    ```console
    cd ms-identity-b2c-javascript-spa
    ```

1. Execute os seguintes comandos:

    ```console
    npm install && npm update
    npm start
    ```

    A janela da consola mostra o número da porta do local onde a aplicação está hospedada.

    ```console
    Listening on port 6420...
    ```

1. Navegue `http://localhost:6420` no seu navegador para ver a aplicação.

    ![App de amostra de aplicação de uma página mostrada no navegador](./media/tutorial-single-page-app-webapi/tutorial-01-sample-app-browser.png)

1. Faça o sjoso usando o endereço de e-mail e a palavra-passe que usou no [tutorial anterior.](tutorial-single-page-app.md) Após o login bem sucedido, deverá ver a `User 'Your Username' logged-in` mensagem.
1. Selecione o botão **Call API.** O SPA obtém uma concessão de autorização da Azure AD B2C e, em seguida, acede à API web protegida para mostrar o nome do utilizador iniciado:

    ![Aplicação de uma página única no navegador mostrando o resultado do nome de utilizador JSON devolvido pela API](./media/tutorial-single-page-app-webapi/tutorial-02-call-api.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Criei um registo de aplicação web API no seu inquilino Azure AD B2C
> * Âmbitos configurados para a API web
> * Permissões concedidas à API web
> * Modificado uma amostra de código API web para trabalhar com o seu inquilino

Agora que viu um SPA solicitar um recurso de uma API web protegida, obtenha uma compreensão mais profunda de como estes tipos de aplicação interagem entre si e com Azure AD B2C.

> [!div class="nextstepaction"]
> [Tipos de aplicação que podem ser usados em Diretório Ativo B2C >](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
