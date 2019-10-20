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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f558ecf583c96f36b8bbee19c7c9cbb2ee57aa31
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596732"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Aplicativo Web que faz logon de usuários – configuração de código

Saiba como configurar o código para seu aplicativo Web que faz logon de usuários.

## <a name="libraries-used-to-protect-web-apps"></a>Bibliotecas usadas para proteger aplicativos Web

<!-- This section can be in an include for Web App and Web APIs -->
As bibliotecas usadas para proteger um aplicativo Web (e uma API da Web) são:

| Plataforma | Biblioteca | Descrição |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Extensões do modelo de identidade para .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Usado diretamente por ASP.NET e ASP.NET Core, o Microsoft Identity Extensions for .NET propõe um conjunto de DLLs em execução no .NET Framework e no .NET Core. De um aplicativo Web ASP.NET/ASP.NET Core, você pode controlar a validação de token usando a classe **TokenValidationParameters** (em particular em alguns cenários de ISV) |
| ![Java](media/sample-v2-code/small_logo_java.png) | [msal4j](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | MSAL para Java – atualmente em visualização pública |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | MSAL para Python – atualmente em visualização pública |

Selecione a guia correspondente à plataforma em que você está interessado:

# <a name="aspnet-coretabaspnetcore"></a>[Núcleo do ASP.NET](#tab/aspnetcore)

Os trechos de código neste artigo e os seguintes são extraídos do [tutorial de ASP.NET Core incremental do aplicativo Web, capítulo 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

Talvez você queira consultar este tutorial para obter detalhes completos de implementação.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Trechos de código neste artigo e os seguintes são extraídos do [exemplo de aplicativo Web ASP.net](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)

Talvez você queira consultar este exemplo para obter detalhes completos de implementação.

# <a name="javatabjava"></a>[Java](#tab/java)

Trechos de código neste artigo e os seguintes são extraídos do exemplo de aplicativo Web Java chamando o aplicativo Web [do Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp) msal4j

Talvez você queira consultar este exemplo para obter detalhes completos de implementação.

# <a name="pythontabpython"></a>[Python](#tab/python)

Os trechos de código neste artigo e os seguintes são extraídos do [aplicativo Web Python chamando o Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) MSAL. Exemplo de aplicativo Web Python

Talvez você queira consultar este exemplo para obter detalhes completos de implementação.

---

## <a name="configuration-files"></a>Arquivos de configuração

Os aplicativos Web que conectam usuários com a plataforma de identidade da Microsoft geralmente são configurados por meio de arquivos de configuração. As configurações que você precisa preencher são:

- a nuvem `Instance` se você quiser que seu aplicativo seja executado (por exemplo, em nuvens nacionais)
- o público em `tenantId`
- o `clientId` para seu aplicativo, conforme copiado do portal do Azure.

Às vezes, os aplicativos podem ser parametrizadasdos pelo `authority`, que é a concatenação do `instance` e o `tenantId`

# <a name="aspnet-coretabaspnetcore"></a>[Núcleo do ASP.NET](#tab/aspnetcore)

No ASP.NET Core, essas configurações estão localizadas no arquivo [appSettings. JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) , na seção "AzureAD".

```Json
{
  "AzureAd": {
    // Azure Cloud instance among:
    // "https://login.microsoftonline.com/" for Azure Public cloud.
    // "https://login.microsoftonline.us/" for Azure US government.
    // "https://login.microsoftonline.de/" for Azure AD Germany
    // "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD Audience among:
    // - the tenant Id as a GUID obtained from the azure portal to sign-in users in your organization
    // - "organizations" to sign-in users in any work or school accounts
    // - "common" to sign-in users with any work and school account or Microsoft personal account
    // - "consumers" to sign-in users with Microsoft personal account only
    "TenantId": "[Enter the tenantId here]]",

    // Client Id (Application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

Em ASP.NET Core, há outro arquivo [properties\launchSettings.JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7) que contém a URL (`applicationUrl`) e a porta SSL (`sslPort`) para seu aplicativo e vários perfis.

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

No portal do Azure, os URIs de resposta que você precisa registrar na página de **autenticação** do seu aplicativo precisam corresponder a essas URLs; ou seja, para os dois arquivos de configuração acima, eles seriam `https://localhost:44321/signin-oidc` como applicationUrl é `http://localhost:3110`, mas o `sslPort` é especificado (44321) e o `CallbackPath` é `/signin-oidc` conforme definido no `appsettings.json`.
  
Da mesma forma, o URI de saída seria definido como `https://localhost:44321/signout-callback-oidc`.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

No ASP.NET, o aplicativo é configurado por meio das linhas de arquivo [Web. Config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) 12-15

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, please visit
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

No portal do Azure, os URIs de resposta que você precisa registrar na página de **autenticação** do seu aplicativo precisam corresponder a essas URLs; Isso é `https://localhost:44326/`.

# <a name="javatabjava"></a>[Java](#tab/java)

Em Java, a configuração está localizada no arquivo [Application. Properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) localizado em `src/main/resources`

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraphUsers=http://localhost:8080/msal4jsample/graph/users
```

No portal do Azure, os URIs de resposta que você precisa registrar na página de **autenticação** do seu aplicativo precisam corresponder ao redirectUris definido pelo aplicativo, que é `http://localhost:8080/msal4jsample/secure/aad` e `http://localhost:8080/msal4jsample/graph/users`

# <a name="pythontabpython"></a>[Python](#tab/python)

Aqui está o arquivo de configuração do Python em [app_config. py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py)

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So token cache will be stored in server-side session
```

> [!NOTE]
> Este guia de início rápido propõe armazenar o segredo do cliente no arquivo de configuração para simplificar. Em seu aplicativo de produção, você desejaria usar outras maneiras de armazenar seu segredo, como keyvault ou uma variável de ambiente, conforme descrito na documentação do Flask: https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Código de inicialização

O código de inicialização é diferente dependendo da plataforma. Para ASP.NET Core e ASP.NET, os usuários de entrada são delegados para o middleware OpenIDConnect. Hoje, o modelo ASP.NET/ASP.NET Core gera aplicativos Web para o ponto de extremidade v 1.0 do Azure AD. Portanto, um pouco de configuração é necessário para adaptá-los ao ponto de extremidade da plataforma Microsoft Identity (v 2.0). No caso do Java, ele é tratado pelo Spring com a cooperação do aplicativo.

# <a name="aspnet-coretabaspnetcore"></a>[Núcleo do ASP.NET](#tab/aspnetcore)

Em ASP.NET Core aplicativos Web (e APIs Web), o aplicativo é protegido porque você tem um atributo `[Authorize]` nos controladores ou nas ações do controlador. Esse atributo verifica se o usuário está autenticado. O código que faz a inicialização do aplicativo está localizado no arquivo de `Startup.cs` e, para adicionar autenticação com a plataforma de identidade da Microsoft (anteriormente Azure AD v 2.0), você precisará adicionar o código a seguir. Os comentários no código devem ser auto-explicativos.

  > [!NOTE]
  > Se você iniciar seu projeto com o projeto Web do ASP.NET Core padrão no Visual Studio ou usando `dotnet new mvc` o método `AddAzureAD` estará disponível por padrão porque os pacotes relacionados são carregados automaticamente.
  > No entanto, se você criar um projeto do zero e estiver tentando usar o código abaixo, sugerimos que você adicione o pacote NuGet **"Microsoft. AspNetCore. Authentication. AzureAD. UI"** ao seu projeto para tornar o método `AddAzureAD` disponível.
  
O código a seguir está disponível em [Startup. cs # L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34)

```CSharp
public class Startup
{
 ...

  // This method gets called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign-in users with the Microsoft identity platform
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

O `AddMicrosoftIdentityPlatformAuthentication` é um método de extensão definido em [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23). Fosse

- Adiciona o serviço de autenticação
- configurar opções para ler o arquivo de configuração
- configura as opções do OpenID Connect para que a autoridade usada seja o ponto de extremidade da plataforma Microsoft Identity (anteriormente conhecido como Azure AD v 2.0)
- o emissor do token é validado
- as declarações correspondentes ao nome são mapeadas da declaração "preferred_username" no token de ID 

Além da configuração, você pode especificar, ao chamar `AddMicrosoftIdentityPlatformAuthentication`:

- o nome da seção de configuração (por padrão AzureAD)
- Se você quiser rastrear os eventos de middleware OpenIdConnect, que podem ajudá-lo a solucionar problemas de seu aplicativo Web se a autenticação não funcionar: definir `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` como `true` mostrará como as informações são elaboradas pelo conjunto de middlewares de ASP.NET Core como ele progride da resposta HTTP para a identidade do usuário no `HttpContext.User`.

```CSharp
/// <summary>
/// Add authentication with Microsoft identity platform.
/// This method expects the configuration file will have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenIdConnect events.
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
      // Per the code below, this application signs in users in any Work and School
      // accounts and any Microsoft Personal Accounts.
      // If you want to direct Azure AD to restrict the users that can sign-in, change
      // the tenant value of the appsettings.json file in the following way:
      // - only Work and School accounts => 'organizations'
      // - only Microsoft Personal accounts => 'consumers'
      // - Work and School and Personal accounts => 'common'
      // If you want to restrict the users that can sign-in to only one tenant
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users that can sign-in to several organizations
      // Set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set the nameClaimType to be preferred_username.
      // This change is needed because certain token claims from Azure AD V1 endpoint
      // (on which the original .NET core template is based) are different than Microsoft identity platform endpoint.
      // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
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

A classe `AadIssuerValidator` permite que o emissor do token seja validado em muitos casos (token de v 1.0 ou v 2.0, aplicativo de locatário único ou aplicativo de vários locatários que conecta usuários com suas contas pessoais da Microsoft, na nuvem pública do Azure ou no National nuvens). Ele está disponível em [Microsoft. Identity. Web/Resource/AadIssuerValidator. cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

O código relacionado à autenticação no aplicativo Web/APIs Web do ASP.NET está localizado no arquivo [App_Start/Startup. auth. cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) .

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0
     // The `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="javatabjava"></a>[Java](#tab/java)

O exemplo de Java usa o Spring Framework. O aplicativo é protegido porque você implementa um `Filter`, que intercepta cada resposta HTTP. No início rápido do aplicativo Web Java, esse filtro é `AuthFilter` em `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`. O filtro processa o fluxo de código de autorização do OAuth 2,0 e, portanto,:

- verifica se o usuário está autenticado (`isAuthenticated()` método)
- Se o usuário não for autenticado, ele computa a URL do Azure AD autoriza pontos de extremidade e redireciona o navegador para esse URI
- Quando chega a resposta, contendo o fluxo do código de autenticação, ele deixa msal4j adquirir o token.
- Quando ele finalmente recebe o token do ponto de extremidade do token (no URI de redirecionamento), o usuário é conectado.

Para obter detalhes, consulte o método `doFilter()` no [AuthFilter. java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java)

> [!NOTE]
> O código da `doFilter()` é gravado em uma ordem ligeiramente diferente, mas o fluxo é o descrito.

Consulte a [plataforma de identidade da Microsoft e o fluxo do código de autorização do OAuth 2,0](v2-oauth2-auth-code-flow.md) para obter detalhes sobre o fluxo do código de autorização, disparado por esse método

# <a name="pythontabpython"></a>[Python](#tab/python)

O exemplo de Python usa Flask. A inicialização de Flask e MSAL. O Python é feito no [aplicativo. py # L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28)

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

# <a name="aspnet-coretabaspnetcore"></a>[Núcleo do ASP.NET](#tab/aspnetcore)

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
