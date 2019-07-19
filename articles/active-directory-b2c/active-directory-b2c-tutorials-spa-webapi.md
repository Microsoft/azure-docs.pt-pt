---
title: Tutorial-conceder acesso a uma API Web do ASP.NET Core de um aplicativo de página única-Azure Active Directory B2C | Microsoft Docs
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
ms.openlocfilehash: cbea29320c896637190766d1b2b60c09f7db5163
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347162"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Tutorial: Conceder acesso a uma API Web do ASP.NET Core de um aplicativo de página única usando Azure Active Directory B2C

Este tutorial mostra como chamar um recurso de API Web do Azure Active Directory (Azure AD) B2C protegido ASP.NET Core de um aplicativo de página única.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar escopos para uma API Web
> * Conceder permissões para a API da Web
> * Configurar o exemplo para usar o aplicativo

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas e os pré-requisitos [no tutorial: Habilite a autenticação de aplicativo de página única com](active-directory-b2c-tutorials-spa.md)contas usando Azure Active Directory B2C.

## <a name="add-a-web-api-application"></a>Adicionar uma aplicação de API Web

Os recursos da API Web precisam ser registrados em seu locatário antes que possam aceitar e responder a solicitações de recursos protegidos por aplicativos cliente que apresentam um token de acesso.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está a utilizar o diretório que contém o seu inquilino do Azure AD B2C, clicando no **filtro de diretório e subscrição** no menu superior e escolher o diretório que contém o seu inquilino.
3. Escolha **todos os serviços** no canto superior esquerdo da portal do Azure e, em seguida, procure e selecione **Azure ad B2C**.
4. Selecione **aplicativos**e, em seguida, selecione **Adicionar**.
5. Insira um nome para o aplicativo. Por exemplo, *webapi1*.
6. Para **incluir aplicativo Web/API Web** e **permitir fluxo implícito**, selecione **Sim**.
7. Para **URL de resposta**, insira um ponto de extremidade onde Azure ad B2C deve retornar quaisquer tokens que seu aplicativo solicitar. Neste tutorial, o exemplo é executado localmente e escuta em `https://localhost:5000`.
8. Para **URI da ID do aplicativo**, insira o identificador usado para sua API Web. O URI identificador completo, incluindo o domínio, é gerado por si. Por exemplo, `https://contosotenant.onmicrosoft.com/api`.
9. Clique em **Criar**.
10. Na página Propriedades, registre a ID do aplicativo que você usará ao configurar o aplicativo Web.

## <a name="configure-scopes"></a>Configurar escopos

Os escopos fornecem uma maneira de controlar o acesso a recursos protegidos. São utilizados pela API Web para implementar o controlo de acesso baseado no âmbito. Por exemplo, alguns utilizadores podem ter o acesso de leitura e de escrita, ao passo que outros podem ter apenas permissões só de leitura. Neste tutorial, vai definir as permissões de leitura para a API Web.

1. Selecione **aplicativos**e, em seguida, selecione *webapi1*.
2. Selecione **escopos publicados**.
3. Para **escopo**, insira `Hello.Read`e, para descrição, digite `Read access to hello`.
4. Para **escopo**, insira `Hello.Write`e, para descrição, digite `Write access to hello`.
5. Clique em **Guardar**.

Os âmbitos publicados podem ser utilizados para conceder permissão a uma aplicação cliente à API Web.

## <a name="grant-permissions"></a>Conceder permissões

Para chamar uma API Web protegida de um aplicativo, você precisa conceder permissões de aplicativo para a API. No tutorial de pré-requisito, você criou um aplicativo Web em Azure AD B2C chamado *webapp1*. Você usa esse aplicativo para chamar a API da Web.

1. Selecione **aplicativos**e, em seguida, selecione *webapp1*.
2. Selecione **acesso à API**e, em seguida, selecione **Adicionar**.
3. Na lista suspensa **selecionar API** , selecione *webapi1*.
4. Na lista suspensa **selecionar** escopos, selecione os escopos **Hello. Read** e **Hello. Write** que você definiu anteriormente.
5. Clique em **OK**.

**A minha aplicação de página única de exemplo** está registada para chamar a **Hello Core API** protegida. Um usuário é autenticado com Azure AD B2C para usar o aplicativo de página única. O aplicativo de página única Obtém uma concessão de autorização de Azure AD B2C para acessar a API Web protegida.

## <a name="configure-the-sample"></a>Configurar o exemplo

Agora que a API Web está registrada e você tem escopos definidos, configure o código da API Web para usar seu locatário Azure AD B2C. Neste tutorial, você configura um aplicativo Web .NET Core de exemplo que pode ser baixado do GitHub. [Transfira um ficheiro zip](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) ou clone a aplicação Web de exemplo a partir do GitHub.

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

Para permitir que seu aplicativo de página única chame a API Web do ASP.NET Core, você precisa habilitar o [CORS](https://docs.microsoft.com/aspnet/core/security/cors).

1. Em **Startup.cs**, adicione o CORS ao método `ConfigureServices()`.

    ```csharp
    public void ConfigureServices(IServiceCollection services) {
      services.AddCors();
    ```

2. Em **Startup.cs**, configure o CORS no método `Configure()`.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory) {
      app.UseCors(builder =>
        builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

3. Abra o ficheiro **launchSettings.json** em **Properties** (Propriedades), localize a definição **iisSettings** *applicationURL* e defina o número da porta para o número registado para o URL de Resposta da API `http://localhost:5000`.

### <a name="configure-the-single-page-application"></a>Configurar o aplicativo de página única

O aplicativo de página única usa Azure AD B2C para inscrição de usuário, entrada e chama a API Web ASP.NET Core protegida. Você atualiza o aplicativo de página única para chamar a API Web do .NET Core.

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

## <a name="run-the-spa-application-and-web-api"></a>Executar o aplicativo SPA e a API Web

Você precisa executar o aplicativo de página única do node. js e a API Web do .NET Core.

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

    A janela do console exibe o número da porta onde o aplicativo está hospedado.
    
    ```
    Listening on port 6420...
    ```

4. Use um navegador para navegar até o endereço `http://localhost:6420` para exibir o aplicativo.
5. Inicie sessão com o endereço de e-mail e a palavra-passe utilizados em [Autenticar utilizadores com o Azure Active Directory B2C numa aplicação de página única (JavaScript)](active-directory-b2c-tutorials-spa.md).
6. Clique em **chamar API**.

Depois de se inscrever ou iniciar sessão com uma conta de utilizador, o exemplo chama a API Web protegida e devolve um resultado.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configurar escopos para uma API Web
> * Conceder permissões para a API da Web
> * Configurar o exemplo para usar o aplicativo

> [!div class="nextstepaction"]
> [Tutorial: Adicionar provedores de identidade a seus aplicativos no Azure Active Directory B2C](tutorial-add-identity-providers.md)
