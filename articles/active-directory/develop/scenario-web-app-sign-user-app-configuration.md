---
title: Configure uma aplicação web que assina nos utilizadores - plataforma de identidade da Microsoft Azure
description: Saiba como construir uma aplicação web que assina nos utilizadores (configuração de código)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 3cb51a57baa87849e97f7b05762dc4d6eba787a6
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537116"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>Aplicação web que assina nos utilizadores: Configuração de código

Saiba como configurar o código para a sua aplicação web que assina nos utilizadores.

## <a name="libraries-for-protecting-web-apps"></a>Bibliotecas para proteger aplicações web

<!-- This section can be in an include for web app and web APIs -->
As bibliotecas que são usadas para proteger uma aplicação web (e uma API web) são:

| Plataforma | Biblioteca | Descrição |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Extensões do Modelo de Identidade para .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Utilizados diretamente por ASP.NET e ASP.NET Core, as extensões do modelo de identidade da Microsoft para .NET propõem um conjunto de DLLs em execução tanto no .NET Framework como no .NET Core. A partir de uma aplicação web ASP.NET ou ASP.NET Core, pode controlar a validação de fichas utilizando a classe **TokenValidaÇãoParâmetros** (em particular, em alguns cenários de parceiro). |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Suporte para aplicações web Java |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | Suporte para aplicações web Python |

Selecione o separador que corresponde à plataforma em que está interessado:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Os fragmentos de código neste artigo e no seguinte são extraídos do tutorial incremental da [aplicação web ASP.NET Core, capítulo 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

Talvez queira consultar este tutorial para obter detalhes completos de implementação.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Os fragmentos de código neste artigo e no seguinte são extraídos da [amostra da aplicação web ASP.NET.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)

Talvez queira consultar esta amostra para obter detalhes completos de implementação.

# <a name="java"></a>[Java](#tab/java)

Os fragmentos de código neste artigo e no seguinte são extraídos da [aplicação web Java, chamando](https://github.com/Azure-Samples/ms-identity-java-webapp) a amostra de gráfico da Microsoft em MSAL Java.

Talvez queira consultar esta amostra para obter detalhes completos de implementação.

# <a name="python"></a>[Python](#tab/python)

Os fragmentos de código neste artigo e no seguinte são extraídos da [aplicação web Python, chamando](https://github.com/Azure-Samples/ms-identity-python-webapp) a amostra de gráfico da Microsoft em MSAL Python.

Talvez queira consultar esta amostra para obter detalhes completos de implementação.

---

## <a name="configuration-files"></a>Ficheiros de configuração

As aplicações web que assinam os utilizadores utilizando a plataforma de identidade da Microsoft são normalmente configuradas através de ficheiros de configuração. As definições que precisa preencher são:

- A instância`Instance`da nuvem () se você quiser que a sua app corra em nuvens nacionais, por exemplo
- O público na identificação do inquilino (`TenantId`)
- O ID`ClientId`do cliente ( ) para a sua aplicação, como copiado do portal Azure

Por vezes, as aplicações `Authority`podem ser parametrizadas por , que é uma concatenação de `Instance` e `TenantId`.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

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
    "TenantId": "[Enter the tenantId here]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

Em ASP.NET Core, um outro ficheiro ([properties\launchSettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)) contém o URL`applicationUrl``sslPort`( ) e a porta TLS/SSL ( ) para a sua aplicação e vários perfis.

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

No portal Azure, a resposta URIs que precisa registar na página **de Autenticação** para a sua aplicação precisa de corresponder a estes URLs. Para os dois ficheiros de `https://localhost:44321/signin-oidc`configuração anteriores, seriam . A razão `applicationUrl` `http://localhost:3110`é `sslPort` que é, mas é especificado (44321). `CallbackPath`é, `/signin-oidc`tal `appsettings.json`como definido em .

Da mesma forma, o URI de inscrição seria definido para `https://localhost:44321/signout-callback-oidc`.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

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

No portal Azure, a resposta URIs que precisa registar na página **de Autenticação** para a sua aplicação precisa de corresponder a estes URLs. Ou seja, deviam `https://localhost:44326/`estar.

# <a name="java"></a>[Java](#tab/java)

Em Java, a configuração está localizada no `src/main/resources`ficheiro [application.properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) localizado em .

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

No portal Azure, a resposta URIs que precisa de registar na página `redirectUri` **de Autenticação** para a sua aplicação precisa de corresponder às instâncias que a aplicação define. Ou seja, deviam `http://localhost:8080/msal4jsample/secure/aad` `http://localhost:8080/msal4jsample/graph/me`ser e.

# <a name="python"></a>[Python](#tab/python)

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
> Este quickstart propõe-se armazenar o segredo do cliente no ficheiro de configuração para a simplicidade. Na sua aplicação de produção, gostaria de usar outras formas de armazenar o seu segredo, como um cofre chave ou uma variável ambiental, como descrito na [documentação do Flask.](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables)
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Código de inicialização

O código de inicialização é diferente dependendo da plataforma. Para ASP.NET Core e ASP.NET, a assinatura nos utilizadores é delegada no middleware OpenID Connect. O modelo ASP.NET ou ASP.NET Core gera aplicações web para o Azure Ative Directory (Azure AD) v1.0 endpoint. É necessária alguma configuração para adaptá-las ao ponto final da plataforma de identidade da Microsoft (v2.0). No caso de Java, é tratado pela primavera com a cooperação do pedido.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Em ASP.NET aplicações web Core (e APIs web), `[Authorize]` a aplicação está protegida porque tem um atributo nos controladores ou nas ações do controlador. Este atributo verifica se o utilizador é autenticado. O código que está a inicializar a aplicação está no ficheiro Startup.cs.

Para adicionar a autenticação à plataforma de identidade da Microsoft (anteriormente Azure AD v2.0), terá de adicionar o seguinte código. Os comentários no código devem ser autoexplicativos.

> [!NOTE]
> Se iniciar o seu projeto com o projeto web `dotnet new mvc`ASP.NET Core `AddAzureAD` dentro do estúdio Visual ou utilizando , o método está disponível por defeito. Isso é porque os pacotes relacionados são automaticamente carregados.
>
> Se construir um projeto de raiz e estiver a tentar utilizar o seguinte código, sugerimos que adicione o pacote NuGet **Microsoft.AspNetCore.Authentication.AzureAD.UI** ao seu projeto para disponibilizar o `AddAzureAD` método.

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

O `AddMicrosoftIdentityPlatformAuthentication` método de extensão é definido em [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23). É:

- Adiciona o serviço de autenticação.
- Configura as opções para ler o ficheiro de configuração.
- Configura as opções OpenID Connect de modo a que a autoridade usada seja a plataforma de identidade da Microsoft (anteriormente Azure AD v2.0).
- Valida o emitente do símbolo.
- Garante que as reclamações correspondentes ao `preferred_username` nome são mapeadas a partir da reclamação no token ID.

Além da configuração, pode especificar o nome `AddMicrosoftIdentityPlatformAuthentication`da secção de configuração ao ligar . Por defeito, `AzureAd`é.

Rastrear eventos de middleware OpenId Connect pode ajudá-lo a resolver problemas na sua aplicação web se a autenticação não funcionar. A `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` `true` definição irá mostrar-lhe como a informação é elaborada pelo conjunto de ASP.NET Core middleware à medida que progride da resposta HTTP à identidade do utilizador em `HttpContext.User`.

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

A `AadIssuerValidator` classe permite que o emitente do símbolo seja validado em muitos casos. Esta classe trabalha com um token v1.0 ou v2.0, uma aplicação de inquilino único ou multiarrendata, ou uma aplicação que assina nos utilizadores com as suas contas pessoais da Microsoft na nuvem pública azure ou nas nuvens nacionais. Está disponível em [Microsoft.Identity.Web/Resource/AadIssuerValidator.cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

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

# <a name="java"></a>[Java](#tab/java)

A amostra de Java usa a estrutura da primavera. A aplicação está protegida porque implementa um filtro, que interceta cada resposta HTTP. No arranque rápido das aplicações web `AuthFilter` `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`java, este filtro está dentro .

O filtro processa o fluxo de código de autorização OAuth 2.0 e verifica se o utilizador é autenticado (método).`isAuthenticated()` Se o utilizador não for autenticado, calcula o URL dos pontos finais de autorização da AD Azure e redireciona o navegador para este URI.

Quando a resposta chega, contendo o código de autorização, adquire o símbolo utilizando a MSAL Java. Quando finalmente recebe o símbolo do ponto final simbólico (no URI redirecionado), o utilizador é assinado.

Para mais detalhes, consulte o `doFilter()` método em [AuthFilter.java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java).

> [!NOTE]
> O código `doFilter()` do é escrito numa ordem ligeiramente diferente, mas o fluxo é o descrito.

Para mais detalhes sobre o fluxo de código de autorização que este método desencadeia, consulte a plataforma de identidade da Microsoft e o fluxo de código de [autorização OAuth 2.0](v2-oauth2-auth-code-flow.md).

# <a name="python"></a>[Python](#tab/python)

A amostra python usa o Frasco. A inicialização do Flask e do MSAL Python é feita em [app.py#L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

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

No próximo artigo, aprenderá a desencadear o sessão e a inscrição.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Iniciar e terminar sessão](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Iniciar e terminar sessão](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Iniciar e terminar sessão](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Iniciar e terminar sessão](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
