---
title: Tutorial - Grant access to an ASP.NET Core web API from a single-page application - Azure Active Directory B2C
description: In this tutorial, learn how to use Active Directory B2C to protect a .NET Core web API and call the API from a single-page Node.js application.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 7dae8bb552fe12d1cdf1f3d8a5fe124491933a87
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420337"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>Tutorial: Grant access to an ASP.NET Core web API from a single-page application using Azure Active Directory B2C

This tutorial shows you how to call an Azure Active Directory B2C (Azure AD B2C)-protected ASP.NET Core web API resource from a single-page application.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configure scopes for a web API
> * Grant permissions to the web API
> * Configure the sample to use the application

## <a name="prerequisites"></a>Pré-requisitos

* Complete the steps and prerequisites in [Tutorial: Enable authentication in a single-page application using Azure Active Directory B2C](active-directory-b2c-tutorials-spa.md).
* Visual Studio 2019 or later, or Visual Studio Code
* .NET Core 2.2 or later
* Node.js

## <a name="add-a-web-api-application"></a>Adicionar uma aplicação de API Web

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Configure scopes

Scopes provide a way to govern access to protected resources. São utilizados pela API Web para implementar o controlo de acesso baseado no âmbito. Por exemplo, alguns utilizadores podem ter o acesso de leitura e de escrita, ao passo que outros podem ter apenas permissões só de leitura. In this tutorial, you define both read and write permissions for the web API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Record the value under **SCOPES** for the `demo.read` scope to use in a later step when you configure the single-page application. The full scope value is similar to `https://contosob2c.onmicrosoft.com/api/demo.read`.

## <a name="grant-permissions"></a>Conceder permissões

To call a protected web API from another application, you need to grant that application permissions to the web API.

In the prerequisite tutorial, you created a web application named *webapp1*. In this tutorial, you configure that application to call the web API you created in a previous section, *webapi1*.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Your single-page web application is registered to call the protected web API. A user authenticates with Azure AD B2C to use the single-page application. The single-page app obtains an authorization grant from Azure AD B2C to access the protected web API.

## <a name="configure-the-sample"></a>Configure the sample

Now that the web API is registered and you have scopes defined, you configure the web API code to use your Azure AD B2C tenant. In this tutorial, you configure a sample .NET Core web application you download from GitHub.

[Download a \*.zip archive](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip) or clone the sample web API project from GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Configurar a API Web

1. Open the <em>B2C-WebApi/**appsettings.json**</em> file in Visual Studio or Visual Studio Code.
1. Modify the `AzureAdB2C` block to reflect your tenant name, the application ID of the web API application, the name of your sign-up/sign-in policy, and the scopes you defined earlier. The block should look similar to the following example (with appropriate `Tenant` and `ClientId` values):

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

To allow your single-page application to call the ASP.NET Core web API, you need to enable [CORS](https://docs.microsoft.com/aspnet/core/security/cors) in the web API.

1. Em *Startup.cs*, adicione o CORS ao método `ConfigureServices()`.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddCors();
    ```

1. Also within the `ConfigureServices()` method, set the `jwtOptions.Authority` value to the following token issuer URI.

    Replace `<your-tenant-name>` with the name of your B2C tenant.

    ```csharp
    jwtOptions.Authority = $"https://<your-tenant-name>.b2clogin.com/{Configuration["AzureAdB2C:Tenant"]}/{Configuration["AzureAdB2C:Policy"]}/v2.0";
    ```

1. In the `Configure()` method, configure CORS.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        app.UseCors(builder =>
            builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

1. (Visual Studio only) Under **Properties** in the Solution Explorer, open the *launchSettings.json* file, then find the `iisExpress` block.
1. (Visual Studio only) Update the `applicationURL` value with the port number you specified when you registered the *webapi1* application in an earlier step. Por exemplo:

    ```json
    "iisExpress": {
      "applicationUrl": "http://localhost:5000/",
      "sslPort": 0
    }
    ```

### <a name="configure-the-single-page-application"></a>Configure the single-page application

The single-page application (SPA) from the [previous tutorial](active-directory-b2c-tutorials-spa.md) in the series uses Azure AD B2C for user sign-up and sign-in, and calls the ASP.NET Core web API protected by the *frabrikamb2c* demo tenant.

In this section, you update the single-page application to call the ASP.NET Core web API protected by *your* Azure AD B2C tenant and which you run on your local machine.

To change the settings in the SPA:

1. Open the *index.html* file in the [active-directory-b2c-javascript-msal-singlepageapp][github-js-spa] project you downloaded or cloned in the previous tutorial.
1. Configure the sample with the URI for the *demo.read* scope you created earlier and the URL of the web API.
    1. In the `appConfig` definition, replace the `b2cScopes` value with the full URI for the scope (the **SCOPE** value you recorded earlier).
    1. Change the `webApi` value to the redirect URI you added when you registered the web API application in an earlier step.

    The `appConfig` definition should look similar to the following code block (with your tenant name in the place of `<your-tenant-name>`):

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var appConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/"
    };
    ```

## <a name="run-the-spa-and-web-api"></a>Run the SPA and web API

Finally, you run both the ASP.NET Core web API and the Node.js single-page application on your local machine. Then, you sign in to the single-page application and press a button to initiate a request to the protected API.

Although both applications run locally in this tutorial, they use Azure AD B2C for secure sign-up/sign-in and to grant access to the protected web API.

### <a name="run-the-aspnet-core-web-api"></a>Run the ASP.NET Core web API

In Visual Studio, press **F5** to build and debug the *B2C-WebAPI.sln* solution. When the project launches, a web page is displayed in your default browser announcing the web API is available for requests.

If you prefer to use the `dotnet` CLI instead of Visual Studio:

1. Open a console window and change to the directory containing the *\*.csproj* file. Por exemplo:

    `cd active-directory-b2c-dotnetcore-webapi/B2C-WebApi`

1. Build and run the web API by executing `dotnet run`.

    When the API is up and running, you should see output similar to the following (for the tutorial, you can safely ignore any `NETSDK1059` warnings):

    ```console
    $ dotnet run
    Hosting environment: Production
    Content root path: /home/user/active-directory-b2c-dotnetcore-webapi/B2C-WebApi
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.
    ```

### <a name="run-the-single-page-app"></a>Executar a aplicação de página única

1. Open a console window and change to the directory containing the Node.js sample. Por exemplo:

    `cd active-directory-b2c-javascript-msal-singlepageapp`

1. Execute os seguintes comandos:

    ```console
    npm install && npm update
    node server.js
    ```

    The console window displays the port number of where the application is hosted.

    ```console
    Listening on port 6420...
    ```

1. Navigate to `http://localhost:6420` in your browser to view the application.
1. Sign in using the email address and password you used in the [previous tutorial](active-directory-b2c-tutorials-spa.md). Upon successful login, you should see the `User 'Your Username' logged-in` message.
1. Select the **Call Web API** button. The SPA obtains an authorization grant from Azure AD B2C, then accesses the protected web API to display the contents of its index page:

    ```Output
    Web APi returned:
    "<html>\r\n<head>\r\n  <title>Azure AD B2C API Sample</title>\r\n ...
    ```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Adicionar uma aplicação de API Web
> * Configure scopes for a web API
> * Grant permissions to the web API
> * Configure the sample to use the application

Now that you've seen an SPA request a resource from a protected web API, gain a deeper understanding of how these application types interact with each other and with Azure AD B2C.

> [!div class="nextstepaction"]
> [Application types that can be used in Active Directory B2C >](active-directory-b2c-apps.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
