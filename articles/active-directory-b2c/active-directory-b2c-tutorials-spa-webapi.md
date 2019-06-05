---
title: Tutorial - conceder acesso a uma API web ASP.NET Core através de um aplicativo de página única - Azure Active Directory B2C | Documentos da Microsoft
description: Tutorial sobre como utilizar o Active Directory B2C para proteger uma API Web .NET Core e chamá-la a partir de uma aplicação de página única.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 609e7bc4104a445c5d77109c9470fb487b2f1336
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507738"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Tutorial: Conceder acesso a uma API web ASP.NET Core a partir de uma aplicação de página única com o Azure Active Directory B2C

Este tutorial mostra-lhe como chamar um Azure Active Directory (Azure AD) B2C ASP.NET Core protegida web recurso de API a partir de uma aplicação de página única.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar âmbitos para uma API web
> * Conceder permissões para a API web
> * Configurar o exemplo a utilizar a aplicação

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Conclua os passos e a pré-requisitos no [Tutorial: Ativar a autenticação de aplicação de página única com contas através do Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md).

## <a name="add-a-web-api-application"></a>Adicionar uma aplicação de API Web

Recursos da API Web têm de ser registados no seu inquilino antes de poderem aceitar e responder a pedidos de recursos protegidos por aplicações cliente que apresentam um token de acesso.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolher **todos os serviços** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **do Azure AD B2C**.
4. Selecione **aplicativos**e, em seguida, selecione **Add**.
5. Introduza um nome para a aplicação. Por exemplo, *webapi1*.
6. Para **incluir a aplicação web / web API** e **permitir fluxo implícito**, selecione **Sim**.
7. Para **URL de resposta**, introduza um ponto de extremidade em que o Azure AD B2C deverá devolver quaisquer tokens que a aplicação solicite. Neste tutorial, o exemplo é executada localmente e escuta na `https://localhost:5000`.
8. Para **URI de ID de aplicação**, introduza o identificador utilizado para a API web. O URI identificador completo, incluindo o domínio, é gerado por si. Por exemplo, `https://contosotenant.onmicrosoft.com/api`.
9. Clique em **Criar**.
10. Na página de propriedades, registe o ID da aplicação que irá utilizar quando configurar a aplicação web.

## <a name="configure-scopes"></a>Configurar âmbitos

Âmbitos proporcionam uma forma de reger o acesso a recursos protegidos. São utilizados pela API Web para implementar o controlo de acesso baseado no âmbito. Por exemplo, alguns utilizadores podem ter o acesso de leitura e de escrita, ao passo que outros podem ter apenas permissões só de leitura. Neste tutorial, vai definir as permissões de leitura para a API Web.

1. Selecione **aplicativos**e, em seguida, selecione *webapi1*.
2. Selecione **âmbitos publicados**.
3. Para **âmbito**, introduza `Hello.Read`e para a descrição, insira `Read access to hello`.
4. Para **âmbito**, introduza `Hello.Write`e para a descrição, insira `Write access to hello`.
5. Clique em **Guardar**.

Os âmbitos publicados podem ser utilizados para conceder permissão a uma aplicação cliente à API Web.

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API web protegida a partir de uma aplicação, tem de conceder as permissões de aplicação para a API. O tutorial de pré-requisitos, criou uma aplicação web no Azure AD B2C com o nome *webapp1*. Utilizar esta aplicação para chamar a API web.

1. Selecione **aplicativos**e, em seguida, selecione *webapp1*.
2. Selecione **acesso à API**e, em seguida, selecione **Add**.
3. Na **selecionar API** menu pendente, selecione *webapi1*.
4. Na **selecionar âmbitos** menu pendente, selecione a **Hello.Read** e **Hello.Write** âmbitos que definiu anteriormente.
5. Clique em **OK**.

**A minha aplicação de página única de exemplo** está registada para chamar a **Hello Core API** protegida. Um utilizador é autenticado com o Azure AD B2C para utilizar a aplicação de página única. A aplicação de página única obtém uma concessão de autorização do Azure AD B2C para aceder à API web protegida.

## <a name="configure-the-sample"></a>Configurar o exemplo

Agora que a API web está registada e já estão definidos âmbitos, configura a web, código de API para utilizar o inquilino do Azure AD B2C. Neste tutorial, irá configurar uma aplicação web do .NET Core de exemplo que pode transferir a partir do GitHub. [Transfira um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) ou clone a aplicação Web de exemplo a partir do GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Configurar a API Web

1. Abra a solução **B2C-WebAPI.sln** no Visual Studio.
2. Abra o ficheiro **appsettings.json**. Atualize os valores seguintes para configurar a API Web para utilizar o seu inquilino:

    ```javascript
    "AzureAdB2C": 
      {
        "Tenant": "<your tenant name>.onmicrosoft.com", 
        "ClientId": "<application-ID>",
        "Policy": "B2C_1_signupsignin1>",
        "ScopeRead": "Hello.Read"  
      },
    ```

#### <a name="enable-cors"></a>Ativar o CORS

Permitir que o aplicativo de página única chamar a API web ASP.NET Core, tem de ativar [CORS](https://docs.microsoft.com/aspnet/core/security/cors).

1. Em **Startup.cs**, adicione o CORS ao método `ConfigureServices()`.

    ```C#
    public void ConfigureServices(IServiceCollection services) {
      services.AddCors();
    ```

2. Em **Startup.cs**, configure o CORS no método `Configure()`.

    ```C#
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory) {
      app.UseCors(builder =>
        builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

3. Abra o ficheiro **launchSettings.json** em **Properties** (Propriedades), localize a definição **iisSettings** *applicationURL* e defina o número da porta para o número registado para o URL de Resposta da API `http://localhost:5000`.

### <a name="configure-the-single-page-application"></a>Configurar a aplicação de página única

A aplicação de página única utiliza o Azure AD B2C para inscrição e início de sessão do utilizador e chama a API de web ASP.NET Core protegida. Atualizar a aplicação de página única para chamar a API web .NET Core.

Para alterar as definições da aplicação:

1. Abra o ficheiro `index.html`.
2. Configure o exemplo com as informações de registo de inquilino do Azure AD B2C. No seguinte código, adicione o nome do seu inquilino a **b2cScopes** e altere o valor **webApi** para o valor *applicationURL* que apontou anteriormente:

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<application-ID>',
        authority: "https://<your-tenant-name>.b2clogin.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://<Your tenant name>.onmicrosoft.com/api/Hello.Read"],
        webApi: 'http://localhost:5000/api/values',
    };
    ```

## <a name="run-the-spa-application-and-web-api"></a>Executar a API de web e de aplicação SPA

Terá de executar a aplicação de página única node. js e a API web .NET Core.

### <a name="run-the-aspnet-core-web-api"></a>Executar a API Web ASP.NET Core 

Prima **F5** para depurar a solução **B2C-WebAPI.sln** no Visual Studio.

Quando o projeto é iniciado, é apresentada uma página Web no browser predefinido a anunciar que a API Web está disponível para pedidos.

### <a name="run-the-single-page-app"></a>Executar a aplicação de página única

1. Inicie uma linha de comandos Node.js.
2. Mude para o diretório que contém o exemplo de Node.js. Por exemplo, `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. Execute os seguintes comandos:
    ```
    npm install && npm update
    node server.js
    ```

    A janela da consola mostra o número de porta de onde o aplicativo é hospedado.
    
    ```
    Listening on port 6420...
    ```

4. Utilize um browser para navegar para o endereço `http://localhost:6420` para ver a aplicação.
5. Inicie sessão com o endereço de e-mail e a palavra-passe utilizados em [Autenticar utilizadores com o Azure Active Directory B2C numa aplicação de página única (JavaScript)](active-directory-b2c-tutorials-spa.md).
6. Clique em **chamar a API**.

Depois de se inscrever ou iniciar sessão com uma conta de utilizador, o exemplo chama a API Web protegida e devolve um resultado.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar âmbitos para uma API web
> * Conceder permissões para a API web
> * Configurar o exemplo a utilizar a aplicação

> [!div class="nextstepaction"]
> [Tutorial: Adicionar fornecedores de identidade às suas aplicações no Azure Active Directory B2C](tutorial-add-identity-providers.md)
