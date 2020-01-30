---
title: 'Tutorial: Conceder acesso a um ASP.NET Core web API a partir de uma aplicação de uma única página'
titleSuffix: Azure AD B2C
description: Neste tutorial, aprenda a usar o Ative Directory B2C para proteger uma API web .NET Core e ligue para a API a partir de uma aplicação nóde.js de página única.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 80b2165b0ec652358a3eb8ac9d55b64f525e4690
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76850021"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Tutorial: Conceder acesso a um ASP.NET Core web API a partir de uma aplicação de uma página única usando o Diretório Ativo Azure B2C

Este tutorial mostra-lhe como chamar um Azure Ative Directory B2C (Azure AD B2C) protegido ASP.NET recurso Core web API a partir de uma aplicação de uma página única.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar os âmbitos para uma API web
> * Conceder permissões à Web API
> * Configurar o exemplo para usar o aplicativo

## <a name="prerequisites"></a>Pré-requisitos

* Complete os passos e pré-requisitos no Tutorial: Ativar a [autenticação numa aplicação de uma página única utilizando o Diretório Ativo Azure B2C](tutorial-single-page-app.md).
* Estúdio Visual 2019 ou mais tarde, ou Código do Estúdio Visual
* .NET Core 2.2 ou mais tarde
* Node.js

## <a name="add-a-web-api-application"></a>Adicionar uma aplicação de API Web

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Configurar os âmbitos

Os âmbitos fornecem uma forma de governar o acesso aos recursos protegidos. São utilizados pela API Web para implementar o controlo de acesso baseado no âmbito. Por exemplo, alguns utilizadores podem ter o acesso de leitura e de escrita, ao passo que outros podem ter apenas permissões só de leitura. Neste tutorial, define tanto a leitura como a escrita de permissões para a Web API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Registe o valor em **scopes** para que o âmbito `demo.read` possa ser utilizado num passo posterior quando configurar a aplicação de uma página única. O valor total do âmbito é semelhante ao `https://contosob2c.onmicrosoft.com/api/demo.read`.

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API web protegida de outra aplicação, você precisa conceder permissões de aplicação para a Web API.

No tutorial pré-requisito, criou uma aplicação web chamada *webapp1*. Neste tutorial, configura essa aplicação para chamar a Web API que criou numa secção anterior, *webapi1*.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

A sua aplicação web de uma página está registada para chamar a API da web protegida. Um utilizador autentica com Azure AD B2C para utilizar a aplicação de uma página única. A aplicação de uma página única obtém uma autorização do Azure AD B2C para aceder à API web protegida.

## <a name="configure-the-sample"></a>Configurar o exemplo

Agora que a Web API está registada e tem âmbitos definidos, configura o código Web API para utilizar o seu inquilino Azure AD B2C. Neste tutorial, configura uma aplicação web .NET Core que descarrega a partir do GitHub.

[Descarregue um arquivo \*.zip](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) ou clone o projeto API web da amostra a partir do GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Configurar a API Web

1. Abra o ficheiro <em>B2C-WebApi/appsettings.json</em>  no Visual Studio ou Visual Studio Code.
1. Modifique o bloco `AzureAdB2C` para refletir o nome do seu inquilino, o ID de aplicação da aplicação Web API, o nome da sua política de inscrição/inscrição e os âmbitos que definiu anteriormente. O bloco deve parecer semelhante ao seguinte exemplo (com `Tenant` e valores adequados `ClientId`):

    ```json
    "AzureAdB2C": {
      "Tenant": "<your-tenant-name>.onmicrosoft.com",
      "ClientId": "<webapi-application-ID>",
      "Policy": "B2C_1_signupsignin1",

      "ScopeRead": "demo.read",
      "ScopeWrite": "demo.write"
    },
    ```

#### <a name="enable-cors"></a>Ativar o CORS

Para permitir que a sua aplicação de uma página única ligue para a ASP.NET Core web API, precisa de ativar o [CORS](https://docs.microsoft.com/aspnet/core/security/cors) na Web API.

1. Em *Startup.cs*, adicione o CORS ao método `ConfigureServices()`.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddCors();
    ```

1. Também dentro do método `ConfigureServices()`, definir o valor `jwtOptions.Authority` para o seguinte emitente simbólico URI.

    Substitua `<your-tenant-name>` pelo nome do seu inquilino B2C.

    ```csharp
    jwtOptions.Authority = $"https://<your-tenant-name>.b2clogin.com/{Configuration["AzureAdB2C:Tenant"]}/{Configuration["AzureAdB2C:Policy"]}/v2.0";
    ```

1. No método `Configure()`, configure o CORS.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        app.UseCors(builder =>
            builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

1. (Apenas estúdio visual) No âmbito **do Properties** in the Solution Explorer, abra o ficheiro *launchSettings.json* e, em seguida, encontre o bloco `iisExpress`.
1. (Apenas estúdio visual) Atualize o valor `applicationURL` com o número de porta especificado quando registou a aplicação *webapi1* num passo anterior. Por exemplo:

    ```json
    "iisExpress": {
      "applicationUrl": "http://localhost:5000/",
      "sslPort": 0
    }
    ```

### <a name="configure-the-single-page-application"></a>Configure a aplicação de uma página única

A aplicação de uma página única (SPA) do [tutorial anterior](tutorial-single-page-app.md) da série utiliza o Azure AD B2C para inscrição e inscrição no utilizador, e chama a ASP.NET Core web API protegida pelo inquilino de demonstração *frabrikamb2c.*

Nesta secção, atualiza a aplicação de uma página única para chamar a ASP.NET Core web API protegida pelo *seu* inquilino Azure AD AD B2C e que executa na sua máquina local.

Para alterar as definições no SPA:

1. Abra o ficheiro *index.html* no projeto [active-directy-b2c-javascript-msal-singlepageapp][github-js-spa] que descarregou ou clonou no tutorial anterior.
1. Configure a amostra com o URI para a *demo.read* scope que criou anteriormente e o URL da Web API.
    1. Na definição `appConfig`, substitua o valor `b2cScopes` pelo URI completo para o âmbito (o valor **SCOPE** que registou anteriormente).
    1. Altere o valor `webApi` para o URI redirecionado que adicionou quando registou a aplicação Web API num passo anterior.

    A definição `appConfig` deve ser semelhante ao seguinte bloco de código (com o nome do seu inquilino no lugar de `<your-tenant-name>`):

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var appConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/"
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Executar o SPA e web API

Finalmente, executa tanto a ASP.NET Web API e a aplicação de página única no Nó.js na sua máquina local. Em seguida, insere-se na aplicação de uma página única e prima um botão para iniciar um pedido à API protegida.

Embora ambas as aplicações sejam executadas localmente neste tutorial, utilizam o Azure AD B2C para iniciar sessão/inscrição segura e para dar acesso à API web protegida.

### <a name="run-the-aspnet-core-web-api"></a>Executar a ASP.NET Core web API

No Estúdio Visual, prima **F5** para construir e depurar a solução *B2C-WebAPI.sLN.* Quando o projeto é lançado, uma página web é exibida no seu navegador predefinido anunciando que a API web está disponível para pedidos.

Se preferir usar o `dotnet` CLI em vez de Visual Studio:

1. Abra uma janela da consola e mude para o diretório que contém o ficheiro *\*.csproj.* Por exemplo:

    `cd active-directory-b2c-dotnetcore-webapi/B2C-WebApi`

1. Construir e executar a Web API executando `dotnet run`.

    Quando a API estiver em funcionamento, deve ver saída semelhante à seguinte (para o tutorial, pode ignorar com segurança qualquer `NETSDK1059` avisos):

    ```console
    $ dotnet run
    Hosting environment: Production
    Content root path: /home/user/active-directory-b2c-dotnetcore-webapi/B2C-WebApi
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.
    ```

### <a name="run-the-single-page-app"></a>Executar a aplicação de página única

1. Abra uma janela da consola e mude para o diretório que contém a amostra Nó.js. Por exemplo:

    `cd active-directory-b2c-javascript-msal-singlepageapp`

1. Execute os seguintes comandos:

    ```console
    npm install && npm update
    node server.js
    ```

    A janela da consola apresenta o número de porta onde a aplicação está hospedada.

    ```console
    Listening on port 6420...
    ```

1. Navegue para `http://localhost:6420` no seu navegador para ver a aplicação.
1. Inscreva-se utilizando o endereço de e-mail e a palavra-passe utilizado no [tutorial anterior](tutorial-single-page-app.md). Após o login bem sucedido, deve ver a mensagem `User 'Your Username' logged-in`.
1. Selecione o botão **Call Web API.** O SPA obtém uma concessão de autorização do Azure AD B2C, e depois acede à API web protegida para exibir o conteúdo da sua página de índice:

    ```Output
    Web APi returned:
    "<html>\r\n<head>\r\n  <title>Azure AD B2C API Sample</title>\r\n ...
    ```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar os âmbitos para uma API web
> * Conceder permissões à Web API
> * Configurar o exemplo para usar o aplicativo

Agora que viu um SPA solicitar um recurso de uma API web protegida, obtenha uma compreensão mais profunda de como estes tipos de aplicações interagem entre si e com O Azure AD B2C.

> [!div class="nextstepaction"]
> [Tipos de aplicação que podem ser utilizados no Diretório Ativo B2C >](application-types.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
