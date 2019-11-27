---
title: Aplicativo Web que conecta usuários (configuração de código)-plataforma de identidade da Microsoft
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16b7032f8568cc944fb95d2e56a84a3d475c3994
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482748"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>Aplicativo Web que assina usuários: configuração de código

Saiba como configurar o código para seu aplicativo Web que faz logon em usuários.

## <a name="libraries-for-protecting-web-apps"></a>Bibliotecas para proteger aplicativos Web

<!-- This section can be in an include for Web App and Web APIs -->
As bibliotecas que são usadas para proteger um aplicativo Web (e uma API da Web) são:

| Plataforma | Biblioteca | Descrição |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Extensões do modelo de identidade para .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Usado diretamente pelo ASP.NET e pelo ASP.NET Core, o Microsoft Identity modelo Extensions for .NET propõe um conjunto de DLLs em execução no .NET Framework e no .NET Core. Em um aplicativo Web ASP.NET ou ASP.NET Core, você pode controlar a validação de token usando a classe **TokenValidationParameters** (em particular, em alguns cenários de parceiros). |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | MSAL (biblioteca de autenticação da Microsoft) para Java. Atualmente em visualização pública. |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | MSAL para Python. Atualmente em visualização pública. |

Selecione a guia que corresponde à plataforma em que você está interessado:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Os trechos de código neste artigo e os seguintes são extraídos do [tutorial de ASP.NET Core incremental do aplicativo Web, capítulo 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

Talvez você queira consultar este tutorial para obter detalhes completos de implementação.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Os trechos de código neste artigo e os seguintes são extraídos do [exemplo de aplicativo web ASP.net](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect).

Talvez você queira consultar este exemplo para obter detalhes completos de implementação.

# <a name="javatabjava"></a>[Java](#tab/java)

Os trechos de código neste artigo e os seguintes são extraídos do exemplo de [aplicativo Web Java chamando o Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp) no MSAL Java.

Talvez você queira consultar este exemplo para obter detalhes completos de implementação.

# <a name="pythontabpython"></a>[Python](#tab/python)

Trechos de código neste artigo e os seguintes são extraídos do exemplo de [aplicativo Web Python chamando o Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) no MSAL Python.

Talvez você queira consultar este exemplo para obter detalhes completos de implementação.

---

## <a name="configuration-files"></a>Arquivos de configuração

Os aplicativos Web que conectam usuários usando a plataforma de identidade da Microsoft geralmente são configurados por meio de arquivos de configuração. As configurações que você precisa preencher são:

- A instância de nuvem (`Instance`) se você quiser que seu aplicativo seja executado em nuvens nacionais, por exemplo
- O público na ID do locatário (`TenantId`)
- A ID do cliente (`ClientId`) para seu aplicativo, conforme copiado do portal do Azure

Às vezes, os aplicativos podem ser parametrizadasdos por `Authority`, que é uma concatenação de `Instance` e `TenantId`.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

No ASP.NET Core, essas configurações estão localizadas no arquivo [appSettings. JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) , na seção "AzureAd".

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

No ASP.NET Core, outro arquivo ([properties\launchSettings.JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)) contém a URL (`applicationUrl`) e a porta SSL (`sslPort`) para seu aplicativo e vários perfis.

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

No portal do Azure, os URIs de resposta que você precisa registrar na página de **autenticação** do seu aplicativo precisam corresponder a essas URLs. Para os dois arquivos de configuração anteriores, eles seriam `https://localhost:44321/signin-oidc`. O motivo é que `applicationUrl` é `http://localhost:3110`, mas `sslPort` é especificado (44321). `CallbackPath` é `/signin-oidc`, conforme definido em `appsettings.json`.

Da mesma forma, o URI de saída seria definido como `https://localhost:44321/signout-callback-oidc`.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

No ASP.NET, o aplicativo é configurado por meio do arquivo [Web. config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) , as linhas 12 a 15.

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

No portal do Azure, os URIs de resposta que você precisa registrar na página de **autenticação** do seu aplicativo precisam corresponder a essas URLs. Ou seja, eles devem ser `https://localhost:44326/`.

# <a name="javatabjava"></a>[Java](#tab/java)

Em Java, a configuração está localizada no arquivo [Application. Properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) localizado em `src/main/resources`.

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

No portal do Azure, os URIs de resposta que você precisa registrar na página de **autenticação** do seu aplicativo precisam corresponder às instâncias de `redirectUri` que o aplicativo define. Ou seja, eles devem ser `http://localhost:8080/msal4jsample/secure/aad` e `http://localhost:8080/msal4jsample/graph/me`.

# <a name="pythontabpython"></a>[Python](#tab/python)

Aqui está o arquivo de configuração do Python em [app_config. py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py):

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> Este guia de início rápido propõe armazenar o segredo do cliente no arquivo de configuração para simplificar. Em seu aplicativo de produção, você desejaria usar outras maneiras de armazenar seu segredo, como um cofre de chaves ou uma variável de ambiente, conforme descrito na [documentação do Flask](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables).
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

Em ASP.NET Core aplicativos Web (e APIs Web), o aplicativo é protegido porque você tem um atributo `[Authorize]` nos controladores ou nas ações do controlador. Esse atributo verifica se o usuário está autenticado. O código que está inicializando o aplicativo está no arquivo Startup.cs. 

Para adicionar autenticação com a plataforma de identidade da Microsoft (anteriormente Azure AD v 2.0), você precisará adicionar o código a seguir. Os comentários no código devem ser auto-explicativos.

> [!NOTE]
> Se você iniciar o projeto com o projeto Web padrão ASP.NET Core no Visual Studio ou usando `dotnet new mvc`, o método `AddAzureAD` estará disponível por padrão. Isso ocorre porque os pacotes relacionados são carregados automaticamente.
>
> Se você criar um projeto do zero e estiver tentando usar o código a seguir, sugerimos que você adicione o pacote NuGet **Microsoft. AspNetCore. Authentication. AzureAD. UI** ao seu projeto para tornar o método `AddAzureAD` disponível.

O código a seguir está disponível em [Startup. cs # L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34).

```CSharp
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

O método de extensão `AddMicrosoftIdentityPlatformAuthentication` é definido em [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23). Fosse

- Adiciona o serviço de autenticação.
- Configura opções para ler o arquivo de configuração.
- Configura as opções do OpenID Connect para que a autoridade usada seja o ponto de extremidade da plataforma Microsoft Identity (anteriormente conhecido como Azure AD v 2.0).
- Valida o emissor do token.
- Garante que as declarações correspondentes ao nome sejam mapeadas a partir da declaração de `preferred_username` no token de ID.

Além da configuração, você pode especificar o nome da seção de configuração ao chamar `AddMicrosoftIdentityPlatformAuthentication`. Por padrão, é `AzureAd`.

O rastreamento de eventos de middleware do OpenId Connect pode ajudá-lo a solucionar problemas de seu aplicativo Web se a autenticação não funcionar. Definir `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` como `true` mostrará como as informações são elaboradas pelo conjunto de middleware ASP.NET Core à medida que ele progride da resposta HTTP para a identidade do usuário no `HttpContext.User`.

```CSharp
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

A classe `AadIssuerValidator` permite que o emissor do token seja validado em muitos casos. Essa classe funciona com um token v 1.0 ou v 2.0, um aplicativo de locatário único ou multilocatário ou um aplicativo que conecta usuários com suas contas pessoais da Microsoft na nuvem pública do Azure ou em nuvens nacionais. Ele está disponível em [Microsoft. Identity. Web/Resource/AadIssuerValidator. cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

O código relacionado à autenticação em um aplicativo Web ASP.NET e APIs Web está localizado no arquivo [App_Start/Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) .

```CSharp
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

O exemplo de Java usa o Spring Framework. O aplicativo é protegido porque você implementa um filtro, que intercepta cada resposta HTTP. No guia de início rápido para aplicativos Web Java, esse filtro é `AuthFilter` em `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`. 

O filtro processa o fluxo do código de autorização OAuth 2,0 e verifica se o usuário é autenticado (`isAuthenticated()` método). Se o usuário não for autenticado, ele computará a URL dos pontos de extremidade de autorização do Azure AD e redirecionará o navegador para esse URI.

Quando a resposta chega, contendo o código de autorização, ela adquire o token usando MSAL Java. Quando ele finalmente recebe o token do ponto de extremidade do token (no URI de redirecionamento), o usuário é conectado.

Para obter detalhes, consulte o método `doFilter()` em [AuthFilter. java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java).

> [!NOTE]
> O código da `doFilter()` é gravado em uma ordem ligeiramente diferente, mas o fluxo é o descrito.

Para obter detalhes sobre o fluxo de código de autorização que esse método dispara, consulte [plataforma de identidade da Microsoft e fluxo de código de autorização do OAuth 2,0](v2-oauth2-auth-code-flow.md).

# <a name="pythontabpython"></a>[Python](#tab/python)

O exemplo de Python usa Flask. A inicialização de Flask e MSAL Python é feita em [app. py # L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

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

## <a name="next-steps"></a>Passos Seguintes

No próximo artigo, você aprenderá a disparar o logon e a saída.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Entrar e sair](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Entrar e sair](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Entrar e sair](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Entrar e sair](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
