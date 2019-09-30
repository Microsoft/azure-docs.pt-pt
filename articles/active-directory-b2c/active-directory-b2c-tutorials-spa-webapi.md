---
title: Tutorial-conceder acesso a uma API Web do ASP.NET Core de um aplicativo de página única-Azure Active Directory B2C
description: Saiba como usar Active Directory B2C para proteger uma API Web do .NET Core e chamar a API de um aplicativo node. js de página única.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: f940893a5328db65549b40269578399655f8539e
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679251"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Tutorial: Conceder acesso a uma API Web do ASP.NET Core de um aplicativo de página única usando Azure Active Directory B2C

Este tutorial mostra como chamar um recurso de API Web ASP.NET Core protegido por Azure AD B2C (Azure Active Directory B2C) de um aplicativo de página única.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar escopos para uma API Web
> * Conceder permissões para a API da Web
> * Configurar o exemplo para usar o aplicativo

## <a name="prerequisites"></a>Pré-requisitos

* Conclua as etapas e os pré-requisitos [no tutorial: Habilite a autenticação em um aplicativo de página única](active-directory-b2c-tutorials-spa.md)usando Azure Active Directory B2C.
* Visual Studio 2019 ou posterior, ou Visual Studio Code
* .NET Core 2,2 ou posterior
* Node.js

## <a name="add-a-web-api-application"></a>Adicionar uma aplicação de API Web

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Configurar escopos

Os escopos fornecem uma maneira de controlar o acesso a recursos protegidos. São utilizados pela API Web para implementar o controlo de acesso baseado no âmbito. Por exemplo, alguns utilizadores podem ter o acesso de leitura e de escrita, ao passo que outros podem ter apenas permissões só de leitura. Neste tutorial, você define as permissões de leitura e gravação para a API da Web.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Registre o **valor de escopo completo** do `demo.read` escopo a ser usado em uma etapa posterior ao configurar o aplicativo de página única. O valor de escopo completo é semelhante `https://yourtenant.onmicrosoft.com/api/demo.read`a.

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API Web protegida de outro aplicativo, você precisa conceder permissões de aplicativo para a API da Web.

No tutorial de pré-requisito, você criou um aplicativo Web chamado *webapp1*. Neste tutorial, você configura esse aplicativo para chamar a API da Web criada em uma seção anterior, *webapi1*.

1. Navegue até seu locatário B2C no portal do Azure
1. Selecione **aplicativos**e, em seguida, selecione *webapp1*.
1. Selecione **acesso à API**e, em seguida, selecione **Adicionar**.
1. Na lista suspensa **selecionar API** , selecione *webapi1*.
1. Na lista suspensa **selecionar escopos** , selecione os escopos que você definiu anteriormente. Por exemplo, *demo. Read* e *demo. Write*.
1. Selecione **OK**.

Seu aplicativo Web de página única é registrado para chamar a API Web protegida. Um usuário é autenticado com Azure AD B2C para usar o aplicativo de página única. O aplicativo de página única Obtém uma concessão de autorização de Azure AD B2C para acessar a API Web protegida.

## <a name="configure-the-sample"></a>Configurar o exemplo

Agora que a API Web está registrada e você tem escopos definidos, configure o código da API Web para usar seu locatário Azure AD B2C. Neste tutorial, você configura um aplicativo Web .NET Core de exemplo que você baixa do GitHub.

[Baixe um \*arquivo. zip](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) ou clone o projeto de API Web de exemplo do github.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Configurar a API Web

1. Abra o arquivo *B2C-WebApi/**appSettings. JSON*** no Visual Studio ou Visual Studio Code.
1. Modifique o `AzureAdB2C` bloco para refletir o nome do locatário, a ID do aplicativo da API Web, o nome da sua política de inscrição/entrada e os escopos definidos anteriormente. O bloco deve ser semelhante ao exemplo a seguir (com os `Tenant` valores `ClientId` apropriados e):

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

Para permitir que o aplicativo de página única chame a API Web do ASP.NET Core, você precisa habilitar o [CORS](https://docs.microsoft.com/aspnet/core/security/cors) na API Web.

1. Em *Startup.cs*, adicione o CORS ao método `ConfigureServices()`.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddCors();
    ```

1. Também dentro do `ConfigureServices()` método, defina o `jwtOptions.Authority` valor para o URI do emissor do token a seguir.

    Substitua `<your-tenant-name>` pelo nome do seu locatário B2C.

    ```csharp
    jwtOptions.Authority = $"https://<your-tenant-name>.b2clogin.com/{Configuration["AzureAdB2C:Tenant"]}/{Configuration["AzureAdB2C:Policy"]}/v2.0";
    ```

1. `Configure()` No método, configure CORS.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        app.UseCors(builder =>
            builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

1. (Somente Visual Studio) Em **Propriedades** no Gerenciador de soluções, abra o arquivo *launchSettings. JSON* e localize o `iisExpress` bloco.
1. (Somente Visual Studio) Atualize o `applicationURL` valor com o número da porta especificado quando você registrou o aplicativo *webapi1* em uma etapa anterior. Por exemplo:

    ```json
    "iisExpress": {
      "applicationUrl": "http://localhost:5000/",
      "sslPort": 0
    }
    ```

### <a name="configure-the-single-page-application"></a>Configurar o aplicativo de página única

O aplicativo de página única (SPA) do [tutorial anterior](active-directory-b2c-tutorials-spa.md) na série usa Azure ad B2C para inscrição e entrada do usuário e chama a API web do ASP.NET Core protegida pelo locatário de demonstração do *frabrikamb2c* .

Nesta seção, você atualiza o aplicativo de página única para chamar a API Web do ASP.NET Core protegida pelo *seu locatário do* Azure ad B2C e que você executa em seu computador local.

Para alterar as configurações no SPA:

1. Abra o arquivo *index. html* no projeto [Active-Directory-B2C-JavaScript-MSAL-singlepageapp][github-js-spa] que você baixou ou clonou no tutorial anterior.
1. Configure o exemplo com o URI para a *demonstração.* escopo de leitura que você criou anteriormente e a URL da API Web.
    1. Na definição, substitua o `b2cScopes` valor pelo URI completo do escopo (o **valor de escopo completo** que você registrou anteriormente). `appConfig`
    1. Altere o `webApi` valor para o `applicationURL` valor especificado na seção anterior.

    A `appConfig` definição deve ser semelhante ao seguinte bloco de código (com o nome do locatário no lugar de `<your-tenant-name>`):

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var appConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/"
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Executar a API da Web e SPA

Finalmente, você executa a API Web do ASP.NET Core e o aplicativo de página única do node. js no computador local. Em seguida, você entra no aplicativo de página única e pressiona um botão para iniciar uma solicitação para a API protegida.

Embora os dois aplicativos sejam executados localmente neste tutorial, eles usam Azure AD B2C para inscrição/entrada segura e para conceder acesso à API Web protegida.

### <a name="run-the-aspnet-core-web-api"></a>Executar a API Web do ASP.NET Core

No Visual Studio, pressione **F5** para compilar e depurar a solução *B2C-webAPI. sln* . Quando o projeto é iniciado, uma página da Web é exibida no navegador padrão anunciando que a API Web está disponível para solicitações.

Se você preferir usar a `dotnet` CLI em vez do Visual Studio:

1. Abra uma janela de console e altere para o diretório que contém o  *\*arquivo. csproj* . Por exemplo:

    `cd active-directory-b2c-dotnetcore-webapi/B2C-WebApi`

1. Compile e execute a API Web executando `dotnet run`.

    Quando a API estiver em execução, você deverá ver uma saída semelhante à seguinte (para o tutorial, você pode ignorar com segurança todos os `NETSDK1059` avisos):

    ```console
    $ dotnet run
    Hosting environment: Production
    Content root path: /home/user/active-directory-b2c-dotnetcore-webapi/B2C-WebApi
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.
    ```

### <a name="run-the-single-page-app"></a>Executar a aplicação de página única

1. Abra uma janela de console e altere para o diretório que contém o exemplo de Node. js. Por exemplo:

    `cd active-directory-b2c-javascript-msal-singlepageapp`

1. Execute os seguintes comandos:

    ```console
    npm install && npm update
    node server.js
    ```

    A janela do console exibe o número da porta onde o aplicativo está hospedado.

    ```console
    Listening on port 6420...
    ```

1. Navegue até `http://localhost:6420` no navegador para exibir o aplicativo.
1. Entre usando o endereço de email e a senha usados no [tutorial anterior](active-directory-b2c-tutorials-spa.md). Após o logon bem-sucedido, você deverá ver `User 'Your Username' logged-in` a mensagem.
1. Selecione o botão **chamar API da Web** . O SPA Obtém uma concessão de autorização de Azure AD B2C e, em seguida, acessa a API Web protegida para exibir o conteúdo de sua página de índice:

    ```Output
    Web APi returned:
    "<html>\r\n<head>\r\n  <title>Azure AD B2C API Sample</title>\r\n ...
    ```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar escopos para uma API Web
> * Conceder permissões para a API da Web
> * Configurar o exemplo para usar o aplicativo

Agora que você já viu uma solicitação SPA de um recurso de uma API Web protegida, tenha uma compreensão mais profunda de como esses tipos de aplicativos interagem entre si e com Azure AD B2C.

> [!div class="nextstepaction"]
> [Tipos de aplicativos que podem ser usados em Active Directory B2C >](active-directory-b2c-apps.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
