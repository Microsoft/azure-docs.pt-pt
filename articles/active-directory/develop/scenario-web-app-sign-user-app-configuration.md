---
title: Configure uma aplicação web que assina nos utilizadores - plataforma de identidade da Microsoft Azure
description: Saiba como criar um aplicativo Web que faz logon de usuários (configuração de código)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f15006f9c7f2778c986d1977123e1a396c4317f3
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701608"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>Aplicativo Web que assina usuários: configuração de código

Saiba como configurar o código para seu aplicativo Web que faz logon em usuários.

## <a name="libraries-for-protecting-web-apps"></a>Bibliotecas para proteger aplicativos Web

<!-- This section can be in an include for Web App and Web APIs -->
As bibliotecas que são usadas para proteger um aplicativo Web (e uma API da Web) são:

| Plataforma | Biblioteca | Descrição |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Extensões do Modelo de Identidade para .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Usado diretamente pelo ASP.NET e pelo ASP.NET Core, o Microsoft Identity modelo Extensions for .NET propõe um conjunto de DLLs em execução no .NET Framework e no .NET Core. A partir de uma aplicação web ASP.NET ou ASP.NET Core, pode controlar a validação de fichas utilizando a classe **TokenValidaÇãoParâmetros** (em particular, em alguns cenários de parceiro). |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Suporte para aplicativos Web Java |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | Suporte para aplicativos Web do Python |

Selecione a guia que corresponde à plataforma em que você está interessado:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Os fragmentos de código neste artigo e no seguinte são extraídos do tutorial incremental da [aplicação web ASP.NET Core, capítulo 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

Talvez você queira consultar este tutorial para obter detalhes completos de implementação.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Os fragmentos de código neste artigo e no seguinte são extraídos da [amostra da aplicação web ASP.NET.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)

Talvez você queira consultar este exemplo para obter detalhes completos de implementação.

# <a name="javatabjava"></a>[Java](#tab/java)

Os fragmentos de código neste artigo e no seguinte são extraídos da [aplicação web Java, chamando](https://github.com/Azure-Samples/ms-identity-java-webapp) a amostra de gráfico da Microsoft em MSAL Java.

Talvez você queira consultar este exemplo para obter detalhes completos de implementação.

# <a name="pythontabpython"></a>[Python](#tab/python)

Os fragmentos de código neste artigo e no seguinte são extraídos da [aplicação web Python, chamando](https://github.com/Azure-Samples/ms-identity-python-webapp) a amostra de gráfico da Microsoft em MSAL Python.

Talvez você queira consultar este exemplo para obter detalhes completos de implementação.

---

## <a name="configuration-files"></a>Arquivos de configuração

Os aplicativos Web que conectam usuários usando a plataforma de identidade da Microsoft geralmente são configurados por meio de arquivos de configuração. As configurações que você precisa preencher são:

- A instância de nuvem (`Instance`) se você quiser que a sua app seja executada em nuvens nacionais, por exemplo
- O público na identificação do inquilino (`TenantId`)
- O ID do cliente (`ClientId`) para a sua aplicação, como copiado do portal Azure

Por vezes, as aplicações podem ser parametrizadas por `Authority`, que é uma concatenação de `Instance` e `TenantId`.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Em ASP.NET Core, estas definições estão localizadas no ficheiro [appsettings.json,](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) na secção "AzureAd".

```Json
{
  "AzureAd": {
    // Azure cloud instance among:
    // - "https://login.microsoftonline.com/" for Azure public cloud
    // - "https://login.microsoftonline.us/" for Azure US government
    // - "https://login.microsoftonline.de/" for Azure AD Germany
    // - "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD audience among:
    // - "TenantId" as a GUID obtained from the Azure portal to sign in users in your organization
    // - "organizations" to sign in users in any work or school account
    // - "common" to sign in users with any work or school account or Microsoft personal account
    // - "consumers" to sign in users with a Microsoft personal account only
    "TenantId": "[Enter the tenantId here]]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

Em ASP.NET Core, um outro ficheiro ([properties\launchSettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)) contém o URL (`applicationUrl`) e a porta SSL (`sslPort`) para a sua aplicação e vários perfis.

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

No portal Azure, a resposta URIs que precisa registar na página **de Autenticação** para a sua aplicação precisa de corresponder a estes URLs. Para os dois ficheiros de configuração anteriores, seriam `https://localhost:44321/signin-oidc`. A razão é que `applicationUrl` é `http://localhost:3110`, mas `sslPort` é especificado (44321). `CallbackPath` é `/signin-oidc`, tal como definido em `appsettings.json`.

Da mesma forma, o URI de inscrição seria definido para `https://localhost:44321/signout-callback-oidc`.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Em ASP.NET, a aplicação é configurada através do ficheiro [Web.config,](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) linhas 12 a 15.

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

No portal Azure, a resposta URIs que precisa registar na página **de Autenticação** para a sua aplicação precisa de corresponder a estes URLs. Ou seja, deviam ser `https://localhost:44326/`.

# <a name="javatabjava"></a>[Java](#tab/java)

Em Java, a configuração está localizada no ficheiro [application.properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) localizado sob `src/main/resources`.

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

No portal Azure, a resposta URIs que precisa de registar na página **de Autenticação** para a sua aplicação precisa de corresponder à `redirectUri` instâncias que a aplicação define. Ou seja, deviam ser `http://localhost:8080/msal4jsample/secure/aad` e `http://localhost:8080/msal4jsample/graph/me`.

# <a name="pythontabpython"></a>[Python](#tab/python)

Aqui está o ficheiro de configuração Python em [app_config.py:](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py)

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> Este guia de início rápido propõe armazenar o segredo do cliente no arquivo de configuração para simplificar. Na sua aplicação de produção, gostaria de usar outras formas de armazenar o seu segredo, como um cofre chave ou uma variável ambiental, como descrito na [documentação do Flask.](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables)
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Código de inicialização

O código de inicialização é diferente dependendo da plataforma. Para ASP.NET Core e ASP.NET, os usuários de entrada são delegados para o middleware OpenID Connect. O modelo ASP.NET ou ASP.NET Core gera aplicativos Web para o ponto de extremidade do Azure Active Directory (Azure AD) v 1.0. Algumas configurações são necessárias para adaptá-las ao ponto de extremidade da plataforma Microsoft Identity (v 2.0). No caso do Java, ele é tratado pelo Spring com a cooperação do aplicativo.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Em ASP.NET aplicações web Core (e APIs web), a aplicação está protegida porque tem um atributo `[Authorize]` nos controladores ou nas ações do controlador. Esse atributo verifica se o usuário está autenticado. O código que está inicializando o aplicativo está no arquivo Startup.cs.

Para adicionar autenticação com a plataforma de identidade da Microsoft (anteriormente Azure AD v 2.0), você precisará adicionar o código a seguir. Os comentários no código devem ser auto-explicativos.

> [!NOTE]
> Se iniciar o seu projeto com o projeto web padrão ASP.NET Core dentro do estúdio Visual ou utilizando `dotnet new mvc`, o método `AddAzureAD` está disponível por padrão. Isso ocorre porque os pacotes relacionados são carregados automaticamente.
>
> Se construir um projeto de raiz e estiver a tentar utilizar o seguinte código, sugerimos que adicione o pacote NuGet **Microsoft.AspNetCore.Authentication.AzureAD.UI** ao seu projeto para disponibilizar o método `AddAzureAD`.

O seguinte código está disponível a partir de [Startup.cs#L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34).

```csharp
public class Startup
{
 ...

  // This method is called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign in users with the Microsoft identity platform
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration);

      services.AddMvc(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
            })
        .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
    }
```

O método de extensão `AddMicrosoftIdentityPlatformAuthentication` é definido em [Microsoft.Identity.Web/WebAppServiceCollectionsExtensions.cs#L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23). Fosse

- Adiciona o serviço de autenticação.
- Configura opções para ler o arquivo de configuração.
- Configura as opções do OpenID Connect para que a autoridade usada seja o ponto de extremidade da plataforma Microsoft Identity (anteriormente conhecido como Azure AD v 2.0).
- Valida o emissor do token.
- Garante que as reclamações correspondentes ao nome são mapeadas a partir da `preferred_username` reclamação no token id.

Além da configuração, pode especificar o nome da secção de configuração ao ligar `AddMicrosoftIdentityPlatformAuthentication`. Por defeito, é `AzureAd`.

O rastreamento de eventos de middleware do OpenId Connect pode ajudá-lo a solucionar problemas de seu aplicativo Web se a autenticação não funcionar. Definir `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` para `true` mostrar-lhe-á como a informação é elaborada pelo conjunto de ASP.NET Core middleware à medida que progride da resposta HTTP à identidade do utilizador em `HttpContext.User`.

```csharp
/// <summary>
/// Add authentication with the Microsoft identity platform.
/// This method expects the configuration file to have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenID Connect events.
/// </param>
/// <returns></returns>
public static IServiceCollection AddMicrosoftIdentityPlatformAuthentication(
  this IServiceCollection services,
  IConfiguration configuration,
  string configSectionName = "AzureAd",
  bool subscribeToOpenIdConnectMiddlewareDiagnosticsEvents = false)
{
  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
      .AddAzureAD(options => configuration.Bind(configSectionName, options));
  services.Configure<AzureADOptions>(options => configuration.Bind(configSectionName, options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
      // Per the following code, this application signs in users in any work or school
      // accounts and any Microsoft personal accounts.
      // If you want to direct Azure AD to restrict the users who can sign in, change
      // the tenant value of the appsettings.json file in the following way:
      // - Only work or school accounts => 'organizations'
      // - Only Microsoft personal accounts => 'consumers'
      // - Work or school and personal accounts => 'common'
      // If you want to restrict the users who can sign in to only one tenant,
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization.
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users who can sign in to several organizations,
      // set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to the options.TokenValidationParameters.ValidIssuers collection.
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set nameClaimType to be preferred_username.
      // This change is needed because certain token claims from the Azure AD v1 endpoint
      // (on which the original .NET Core template is based) are different from the Microsoft identity platform endpoint.
      // For more details, see [ID tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).
      options.TokenValidationParameters.NameClaimType = "preferred_username";

      // ...

      if (subscribeToOpenIdConnectMiddlewareDiagnosticsEvents)
      {
          OpenIdConnectMiddlewareDiagnostics.Subscribe(options.Events);
      }
  });
  return services;
}
  ...
```

A classe `AadIssuerValidator` permite que o emitente do símbolo seja validado em muitos casos. Essa classe funciona com um token v 1.0 ou v 2.0, um aplicativo de locatário único ou multilocatário ou um aplicativo que conecta usuários com suas contas pessoais da Microsoft na nuvem pública do Azure ou em nuvens nacionais. Está disponível em [Microsoft.Identity.Web/Resource/AadIssuerValidator.cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

O código relacionado com a autenticação numa aplicação web ASP.NET e apis web está localizado no ficheiro [App_Start/Startup.Auth.cs.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61)

```csharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0.
     // `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/.
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="javatabjava"></a>[Java](#tab/java)

O exemplo de Java usa o Spring Framework. O aplicativo é protegido porque você implementa um filtro, que intercepta cada resposta HTTP. No arranque rápido das aplicações web java, este filtro é `AuthFilter` em `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`.

O filtro processa o fluxo de código de autorização OAuth 2.0 e verifica se o utilizador é autenticado (método`isAuthenticated()`). Se o usuário não for autenticado, ele computará a URL dos pontos de extremidade de autorização do Azure AD e redirecionará o navegador para esse URI.

Quando a resposta chega, contendo o código de autorização, ela adquire o token usando MSAL Java. Quando ele finalmente recebe o token do ponto de extremidade do token (no URI de redirecionamento), o usuário é conectado.

Para mais detalhes, consulte o método `doFilter()` em [AuthFilter.java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java).

> [!NOTE]
> O código da `doFilter()` está escrito numa ordem ligeiramente diferente, mas o fluxo é o descrito.

Para mais detalhes sobre o fluxo de código de autorização que este método desencadeia, consulte a plataforma de identidade da Microsoft e o fluxo de código de [autorização OAuth 2.0](v2-oauth2-auth-code-flow.md).

# <a name="pythontabpython"></a>[Python](#tab/python)

O exemplo de Python usa Flask. A inicialização do Flask e do MSAL Python é feita em [app.py#L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>Passos seguintes

No próximo artigo, você aprenderá a disparar o logon e a saída.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Iniciar e terminar sessão](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Iniciar e terminar sessão](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Iniciar e terminar sessão](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Iniciar e terminar sessão](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
