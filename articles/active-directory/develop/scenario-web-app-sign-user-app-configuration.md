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
ms.openlocfilehash: 1a746b0f6d85e3f012cdd2e78fff8cd10a586950
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086759"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Aplicativo Web que faz logon de usuários – configuração de código

Saiba como configurar o código para seu aplicativo Web que faz logon de usuários.

## <a name="libraries-used-to-protect-web-apps"></a>Bibliotecas usadas para proteger aplicativos Web

<!-- This section can be in an include for Web App and Web APIs -->
As bibliotecas usadas para proteger um aplicativo Web (e uma API da Web) são:

| Plataforma | Biblioteca | Descrição |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Extensões do modelo de identidade para .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Usado diretamente por ASP.NET e ASP.NET Core, o Microsoft Identity Extensions for .NET propõe um conjunto de DLLs em execução no .NET Framework e no .NET Core. De um aplicativo Web ASP.NET/ASP.NET Core, você pode controlar a validação de token usando a classe **TokenValidationParameters** (em particular em alguns cenários de ISV) |
| ![Java](media/sample-v2-code/logo_java.png) | [msal4j](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | MSAL para Java – atualmente em visualização pública |
| ![Python](media/sample-v2-code/logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | MSAL para Python – atualmente em visualização pública |

Os trechos de código neste artigo e os seguintes são extraídos de:

- o [tutorial incremental do aplicativo Web ASP.NET Core, capítulo 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).
- o [aplicativo Web ASP.net de exemplo](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)
- o [aplicativo Web Java chamando o Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp) msal4j aplicativo Web de exemplo
- o [aplicativo Web Python que chama o Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) MSAL. Exemplo de aplicativo Web Python

Talvez você queira consultar esses tutoriais e exemplos de detalhes de implementação completos.

## <a name="configuration-files"></a>Arquivos de configuração

Os aplicativos Web que conectam usuários com a plataforma de identidade da Microsoft geralmente são configurados por meio de arquivos de configuração. As configurações que você precisa preencher são:

- a nuvem `Instance` se você quiser que seu aplicativo seja executado (por exemplo, em nuvens nacionais)
- o público em`tenantId`
- o `clientId` para seu aplicativo, conforme copiado do portal do Azure.

Às vezes, os aplicativos podem ser parametrizadasdos pelo `authority`, que é a concatenação `instance` do e do`tenantId`

# <a name="aspnet-coretabaspnetcore"></a>[Núcleo do ASP.NET](#tab/aspnetcore)

No ASP.NET Core, essas configurações estão localizadas no `appsettings.json` arquivo, na seção "AzureAD".

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

Em ASP.NET Core, há outro arquivo (`properties\launchSettings.json`) que contém a URL (`applicationUrl`) e a porta SSL (`sslPort`) para seu aplicativo e vários perfis.

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

No portal do Azure, os URIs de resposta que você precisa registrar na página de **autenticação** do seu aplicativo precisam corresponder a essas URLs; ou seja, para os dois arquivos de configuração acima, eles seriam `https://localhost:44321/signin-oidc` como o applicationUrl é `http://localhost:3110` , mas `sslPort` o é especificado (44321) e o `CallbackPath` é `/signin-oidc` definido no `appsettings.json`.
  
Da mesma forma, o URI de saída seria definido como `https://localhost:44321/signout-callback-oidc`.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

No ASP.net, o aplicativo é configurado por meio `Web.Config` do arquivo

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

No portal do Azure, os URIs de resposta que você precisa registrar na página de **autenticação** do seu aplicativo precisam corresponder a essas URLs; ou seja `https://localhost:44326/`.

# <a name="javatabjava"></a>[Java](#tab/java)

Em Java, a configuração está localizada no `application.properties` arquivo localizado em`src/main/resources`

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraphUsers=http://localhost:8080/msal4jsample/graph/users
```

No portal do Azure, os URIs de resposta que você precisa registrar na página de **autenticação** do seu aplicativo precisam corresponder ao redirectUris definido pelo aplicativo, ou seja `http://localhost:8080/msal4jsample/secure/aad` ,`http://localhost:8080/msal4jsample/graph/users`

# <a name="pythontabpython"></a>[Python](#tab/python)

Aqui está o arquivo de configuração do Python em [app_config. py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/web_app_sample/app_config.py)

```Python
AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
CLIENT_ID = "Enter_the_Application_Id_here"
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
SCOPE = ["https://graph.microsoft.com/User.Read"]
REDIRECT_URI = "http://localhost:5000/getAToken"
```

---

## <a name="initialization-code"></a>Código de inicialização

O código de inicialização é diferente dependendo da plataforma. Para ASP.NET Core e ASP.NET, os usuários de entrada são delegados para o middleware OpenIDConnect. Hoje, o modelo ASP.NET/ASP.NET Core gera aplicativos Web para o ponto de extremidade v 1.0 do Azure AD. Portanto, um pouco de configuração é necessário para adaptá-los ao ponto de extremidade da plataforma Microsoft Identity (v 2.0). No caso do Java, ele é tratado pelo Spring com a cooperação do aplicativo.

# <a name="aspnet-coretabaspnetcore"></a>[Núcleo do ASP.NET](#tab/aspnetcore)

Em ASP.NET Core aplicativos Web (e APIs Web), o aplicativo é protegido porque você tem um `[Authorize]` atributo nos controladores ou nas ações do controlador. Esse atributo verifica se o usuário está autenticado. O código que faz a inicialização do aplicativo está localizado `Startup.cs` no arquivo e, para adicionar autenticação com a plataforma de identidade da Microsoft (anteriormente Azure ad v 2.0), você precisará adicionar o código a seguir. Os comentários no código devem ser auto-explicativos.

  > [!NOTE]
  > Se você iniciar o projeto com `dotnet new mvc` o projeto Web padrão ASP.NET Core no Visual Studio ou se o método `AddAzureAD` estiver disponível por padrão, pois os pacotes relacionados serão carregados automaticamente.
  > No entanto, se você criar um projeto do zero e estiver tentando usar o código abaixo, sugerimos que você adicione o pacote NuGet **"Microsoft. AspNetCore. Authentication. AzureAD. UI"** ao seu projeto `AddAzureAD` para tornar o método disponível.
  
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
- Se você quiser rastrear os eventos de middleware OpenIdConnect, que podem ajudá-lo a solucionar problemas de seu aplicativo Web se a autenticação `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` não `true` funcionar: a configuração para mostrará como as informações são elaboradas pelo conjunto de ASP.NET Core o middleware conforme progride da resposta HTTP para a identidade do usuário no `HttpContext.User`.

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

A `AadIssuerValidator` classe permite que o emissor do token seja validado em muitos casos (o token v 1.0 ou v 2.0, um aplicativo de locatário único ou multilocatário ou aplicativo que conecta usuários com suas contas pessoais da Microsoft, na nuvem pública do Azure ou no National nuvens). Ele está disponível em [Microsoft. Identity. Web/Resource/AadIssuerValidator. cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

O código relacionado à autenticação no aplicativo Web/APIs Web do ASP.net está localizado no `App_Start/Startup.Auth.cs` arquivo.

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

O exemplo de Java usa o Spring Framework. O aplicativo é protegido porque você implementa um `Filter`, que obtém cada resposta http. No guia de início rápido do aplicativo Web Java `AuthFilter` , `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`está em. O filtro processa o fluxo de código de autorização do OAuth 2,0 e, portanto,:

- verifica se o usuário é autenticado (`isAuthenticated()` método)
- Se o usuário não for autenticado, ele computa a URL do Azure AD autoriza pontos de extremidade e redireciona o navegador para esse URI
- Quando chega a resposta, contendo o fluxo do código de autenticação, ele deixa msal4j adquirir o token.
- Quando ele finalmente recebe o token do ponto de extremidade do token (no URI de redirecionamento), o usuário é conectado.

Para obter detalhes, `doFilter()` consulte o método em [AuthFilter. java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java)

> [!NOTE]
> O código do `doFilter()` é escrito em uma ordem ligeiramente diferente, mas o fluxo é o descrito.

Consulte a [plataforma de identidade da Microsoft e o fluxo do código de autorização OAuth 2,0](v2-oauth2-auth-code-flow.md) para obter detalhes sobre o fluxo de código de autorização disparado por esse método

# <a name="pythontabpython"></a>[Python](#tab/python)

O exemplo de Python usa Flask. A inicialização de Flask e MSAL. O Python é feito no [aplicativo. py # L1-L17](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e1199b4c3cdcb637cf0d8306832efbd85492e123/app.py#L1-L17)

```Python
import uuid
import flask
import requests
from flask import Flask, render_template, session, request
from flask_session import Session
import msal
import app_config

sess = Session()
app = Flask(__name__)
app.config.from_object('config.Config')
sess.init_app(app)
cache = msal.SerializableTokenCache()
application = msal.ConfidentialClientApplication(
    app_config.CLIENT_ID, authority=app_config.AUTHORITY,
    client_credential=app_config.CLIENT_SECRET,
    token_cache=cache)
```

Isso é MSAL. Python que cuidará de permitir que o usuário entre. Consulte [app. py # L74-84](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e1199b4c3cdcb637cf0d8306832efbd85492e123/app.py#L74-84)

```Python
@app.route('/authenticate')
def authenticate():
    # Call to the authorize endpoint
    auth_state = str(uuid.uuid4())
    session[(request.cookies.get("session")+'state')] = auth_state
    authorization_url = application.get_authorization_request_url(app_config.SCOPE, state=auth_state,
                                                                  redirect_uri=app_config.REDIRECT_URI)
    resp = flask.Response(status=307)
    resp.headers['location'] = authorization_url
    return resp
```

---

## <a name="next-steps"></a>Passos Seguintes

No próximo artigo, você aprenderá a disparar o logon e a saída.

> [!div class="nextstepaction"]
> [Entrar e sair](scenario-web-app-sign-user-sign-in.md)
