---
title: 'Tutorial: Proteja um API web Node.js utilizando Azure AD B2C e conceda acesso a uma aplicação de uma única página (SPA)'
titleSuffix: Azure AD B2C
description: Neste tutorial, aprenda a usar o Ative Directory B2C para proteger uma API web Node.js e chame-a de uma aplicação de uma página única.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 50524159186987b7a30015c878fa3fac949afc79
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875699"
---
# <a name="tutorial-protect-and-grant-access-to-a-nodejs-web-api-from-a-single-page-application-with-azure-ad-b2c"></a>Tutorial: Proteja e conceda acesso a uma API web Node.js a partir de uma aplicação de uma página com Azure AD B2C

Este tutorial mostra-lhe como chamar um Azure Ative Directory B2C (Azure AD B2C) protegido Node.js web API a partir de uma aplicação de uma página única.

Neste tutorial, o segundo de uma série de duas partes:

> [!div class="checklist"]
> * Crie um registo de aplicação web API no seu inquilino Azure AD B2C
> * Configurar os âmbitos para a Web API
> * Conceder permissões à Web API
> * Modifique uma amostra de código API web para trabalhar com o seu inquilino

No [primeiro tutorial](tutorial-single-page-app.md) desta série, descarregou a amostra de código e modificou-a para assinar em utilizadores com um fluxo de utilizador no seu inquilino Azure AD AD B2C.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Complete os passos e pré-requisitos no [Tutorial: Permitir a autenticação numa aplicação de uma página com o Azure AD B2C](tutorial-single-page-app.md)
* [Código de Estúdio Visual](https://code.visualstudio.com/) ou outro editor de código
* [Node.js](https://nodejs.org/en/download/)

## <a name="add-a-web-api-application"></a>Adicionar uma aplicação de API Web

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Configurar os âmbitos

Os âmbitos proporcionam uma forma de reger o acesso a recursos protegidos. São utilizados pela API Web para implementar o controlo de acesso baseado no âmbito. Por exemplo, alguns utilizadores podem ter o acesso de leitura e de escrita, ao passo que outros podem ter apenas permissões só de leitura. Neste tutorial, define tanto a leitura como a escrita de permissões para a Web API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Grave o valor em `demo.read` **Scopes** para que o âmbito seja utilizado num passo posterior quando configurar a aplicação de uma página única. O valor total do `https://contosob2c.onmicrosoft.com/api/demo.read`âmbito é semelhante a .

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API web protegida de outra aplicação, você precisa conceder permissões de aplicação para a Web API.

No tutorial pré-requisito, criou uma aplicação web chamada *webapp1*. Neste tutorial, configura essa aplicação para chamar a Web API que criou numa secção anterior, *webapi1*.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

A sua aplicação web de uma página única foi agora concedida permissões à API web protegida para os âmbitos especificados. Um utilizador autentica com Azure AD B2C para utilizar a aplicação de uma página única. A aplicação de uma página utiliza o fluxo de concessão de autorização para aceder à API web protegida com um token de acesso devolvido pelo Azure AD B2C.

## <a name="configure-the-sample"></a>Configure a amostra

Agora que a Web API está registada e definiu os âmbitos, configure o código Web API para trabalhar com o seu inquilino Azure AD B2C. Neste tutorial, configura uma amostra Node.js web API que você descarrega do GitHub.

[Descarregue um \*arquivo .zip](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) ou clone o projeto API web da amostra a partir do GitHub. Também pode navegar diretamente para o projeto [Azure-Samples/active-directy-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) no GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="configure-the-web-api"></a>Configurar a API Web

1. Abra o ficheiro *config.js* no seu editor de código.
1. Modifique os valores variáveis para refletir os do registo de candidatura que criou anteriormente. Atualize `policyName` também o fluxo de utilizador que criou como parte dos pré-requisitos. Por exemplo, *B2C_1_signupsignin1.*

    ```javascript
    const clientID = "<your-webapi-application-ID>"; // Application (client) ID
    const b2cDomainHost = "<your-tenant-name>.b2clogin.com";
    const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (a GUID)
    const policyName = "B2C_1_signupsignin1";
    ```

#### <a name="enable-cors"></a>Ativar o CORS

Para permitir que a sua aplicação de uma página única ligue para a API web Node.js, precisa de ativar o [CORS](https://expressjs.com/en/resources/middleware/cors.html) na Web API. Numa aplicação de produção deve ter cuidado com o domínio que está a fazer o pedido, mas para este tutorial, permitir pedidos de qualquer domínio.

Para ativar o CORS, utilize os seguintes middleware. Na amostra de código API web Node.js neste tutorial, já foi adicionada ao ficheiro *index.js.*

```javascript
app.use((req, res, next) => {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Authorization, Origin, X-Requested-With, Content-Type, Accept");
    next();
});
```

### <a name="configure-the-single-page-application"></a>Configure a aplicação de uma página única

A aplicação de uma página única (SPA) do [tutorial anterior](tutorial-single-page-app.md) da série utiliza o Azure AD B2C para inscrição e inscrição do utilizador, e por padrão, chama a API web Node.js protegida pelo *fabrikamb2c* demo inquilino.

Nesta secção, atualiza a aplicação web de uma página única para ligar para a API web Node.js protegida pelo *seu* inquilino Azure AD B2C (e que executa na sua máquina local).

Para alterar as definições no SPA:

1. No projeto [active-directy-b2c-javascript-msal-singlepageapp][github-js-spa] que descarregou ou clonou no tutorial anterior, abra o ficheiro *apiConfig.js* dentro da pasta *JavaScriptSPA.*
1. Configure a amostra com o URI para a *demo.read* scope que criou anteriormente e o URL da Web API.
    1. Na `apiConfig` definição, `b2cScopes` substitua o valor por o uri completo para o âmbito *de demo.read* (o valor **scope** que registou anteriormente).
    1. Altere o `webApi` domínio no valor para o URI redirecionamento que adicionou quando registou a aplicação Web API num passo anterior.

    Como a API está `/hello` acessível no ponto final, *deixe/olá* no URI.

    A `apiConfig` definição deve ser semelhante ao seguinte bloco de código, mas com `<your-tenant-name>`o nome do seu inquilino B2C no lugar de:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/hello" // '/hello' should remain in the URI
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Executar o SPA e web API

Está agora pronto para testar o acesso à API da aplicação de uma página única. Execute tanto a API web Node.js como a aplicação de página única JavaScript na sua máquina local. Em seguida, inicie sessão na aplicação de uma página única e selecione o botão **Call API** para iniciar um pedido à API protegida.

Apesar de ambas as aplicações estarem a decorrer localmente quando segue este tutorial, configurou-as para utilizar o Azure AD B2C para iniciar sessão/inscrição segura e para conceder acesso à API da web protegida.

### <a name="run-the-nodejs-web-api"></a>Executar a API web Node.js

1. Abra uma janela de consola e mude para o diretório contendo a amostra API web Node.js. Por exemplo:

    ```console
    cd active-directory-b2c-javascript-nodejs-webapi
    ```

1. Execute os seguintes comandos:

    ```console
    npm install && npm update
    node index.js
    ```

    A janela da consola apresenta o número da porta onde a aplicação está hospedada.

    ```console
    Listening on port 5000...
    ```

### <a name="run-the-single-page-app"></a>Executar a aplicação de uma página única

1. Abra outra janela da consola e mude para o diretório contendo a amostra JavaScript SPA. Por exemplo:

    ```console
    cd active-directory-b2c-javascript-msal-singlepageapp
    ```

1. Execute os seguintes comandos:

    ```console
    npm install && npm update
    npm start
    ```

    A janela da consola apresenta o número de porta onde a aplicação está hospedada.

    ```console
    Listening on port 6420...
    ```

1. Navegue `http://localhost:6420` no seu navegador para ver a aplicação.

    ![App de amostra de aplicação de uma página mostrada no navegador](./media/tutorial-single-page-app-webapi/tutorial-01-sample-app-browser.png)

1. Inscreva-se utilizando o endereço de e-mail e a palavra-passe utilizado no [tutorial anterior](tutorial-single-page-app.md). Após o login bem `User 'Your Username' logged-in` sucedido, deve ver a mensagem.
1. Selecione o botão **Call API.** O SPA obtém uma concessão de autorização do Azure AD B2C, depois acede à API web protegida para exibir o nome do utilizador iniciado:

    ![Aplicação de uma página única no navegador mostrando o resultado JSON do nome de utilizador devolvido pela API](./media/tutorial-single-page-app-webapi/tutorial-02-call-api.png)

## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Criou um registo de aplicação web API no seu inquilino Azure AD B2C
> * Âmbitos configurados para a Web API
> * Permissões concedidas à Web API
> * Modificou uma amostra de código API web para trabalhar com o seu inquilino

Agora que viu um SPA solicitar um recurso de uma API web protegida, obtenha uma compreensão mais profunda de como estes tipos de aplicações interagem entre si e com O Azure AD B2C.

> [!div class="nextstepaction"]
> [Tipos de aplicação que podem ser utilizados no Diretório Ativo B2C >](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
