---
title: Configure uma aplicação web que assina nos utilizadores - plataforma de identidade microsoft / Rio Azure
description: Saiba como construir uma aplicação web que assina nos utilizadores (configuração de código)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: dad7b0563fd1ca0dbf60403bc6172e7616e278b2
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443658"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>Aplicação web que assina nos utilizadores: Configuração de código

Saiba como configurar o código da sua aplicação web que assina nos utilizadores.

## <a name="libraries-for-protecting-web-apps"></a>Bibliotecas para proteger aplicações web

<!-- This section can be in an include for web app and web APIs -->
As bibliotecas que são usadas para proteger uma aplicação web (e uma API web) são:

| Plataforma | Biblioteca | Description |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_NET.png) | [Extensões de modelo de identidade para .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Utilizados diretamente por ASP.NET e ASP.NET Core, as extensões do modelo de identidade da Microsoft para .NET propõe um conjunto de DLLs em execução tanto no .NET Framework como no .NET Core. A partir de uma aplicação web core ASP.NET ou ASP.NET, pode controlar a validação de tokens utilizando a classe **TokenValidationParameters** (em particular, em alguns cenários de parceiros). Na prática, a complexidade é encapsulada na biblioteca [Microsoft.Identity.Web](https://aka.ms/ms-identity-web) |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Suporte para aplicações web java |
| ![Python](media/sample-v2-code/small_logo_python.png) | [Pitão MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | Suporte para aplicações web Python |

Selecione o separador que corresponde à plataforma em que está interessado:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Os códigos snippets neste artigo e os seguintes são extraídos do tutorial incremental da [aplicação web core ASP.NET, capítulo 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg).

Talvez queira consultar este tutorial para obter todos os detalhes da implementação.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Os códigos snippets neste artigo e os seguintes são extraídos da [amostra de aplicações web ASP.NET.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)

É melhor consultar esta amostra para obter todos os detalhes da implementação.

# <a name="java"></a>[Java](#tab/java)

Os snippets de código neste artigo e os seguintes são extraídos da [aplicação web java chamando](https://github.com/Azure-Samples/ms-identity-java-webapp) a amostra de gráfico da Microsoft em MSAL Java.

É melhor consultar esta amostra para obter todos os detalhes da implementação.

# <a name="python"></a>[Python](#tab/python)

Os snippets de código neste artigo e os seguintes são extraídos da [aplicação web Python chamando](https://github.com/Azure-Samples/ms-identity-python-webapp) a amostra de gráfico da Microsoft em MSAL Python.

É melhor consultar esta amostra para obter todos os detalhes da implementação.

---

## <a name="configuration-files"></a>Ficheiros de configuração

As aplicações web que assinam nos utilizadores utilizando a plataforma de identidade da Microsoft são configuradas através de ficheiros de configuração. As definições que precisa de preencher são:

- O exemplo da nuvem ( `Instance` ) se você quiser que a sua app seja executada em nuvens nacionais, por exemplo
- O público no ID do inquilino ( `TenantId` )
- O ID do cliente `ClientId` para a sua aplicação, copiado do portal Azure

Por vezes, as aplicações podem ser parametrizadas `Authority` por, que é uma concatenação de `Instance` e `TenantId` .

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Em ASP.NET Core, estas definições estão localizadas no [appsettings.jsem](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) ficheiro, na secção "AzureAd".

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
    "SignedOutCallbackPath": "/signout-oidc"
  }
}
```

Em ASP.NET Core, um outro ficheiro ([properties\launchSettings.jsem)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)contém o URL ( `applicationUrl` ) e a porta TLS/SSL para a sua `sslPort` aplicação e vários perfis.

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

No portal Azure, os URIs de resposta que precisa de registar na página **autenticação** para a sua aplicação precisam de corresponder a estes URLs. Para os dois ficheiros de configuração anteriores, seriam `https://localhost:44321/signin-oidc` . A razão é que `applicationUrl` é , mas é especificado `http://localhost:3110` `sslPort` (44321). `CallbackPath` é, `/signin-oidc` tal como definido em `appsettings.json` .

Da mesma forma, o URI de assinatura seria definido para `https://localhost:44321/signout-oidc` .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Em ASP.NET, a aplicação é configurada através do ficheiro [Web.config, ](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) linhas 12 a 15.

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

No portal Azure, os URIs de resposta que precisa de registar na página **autenticação** para a sua aplicação precisam de corresponder a estes URLs. Ou seja, deviam `https://localhost:44326/` estar.

# <a name="java"></a>[Java](#tab/java)

Em Java, a configuração está localizada no ficheiro [application.properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) localizado sob `src/main/resources` .

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

No portal Azure, os URIs de resposta que precisa de registar na página **autenticação** para a sua aplicação precisam de corresponder às `redirectUri` instâncias que a aplicação define. Ou seja, deviam estar `http://localhost:8080/msal4jsample/secure/aad` `http://localhost:8080/msal4jsample/graph/me` e.

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
> Este quickstart propõe-se armazenar o segredo do cliente no ficheiro de configuração para a simplicidade. Na sua aplicação de produção, gostaria de usar outras formas de armazenar o seu segredo, como um cofre-chave ou uma variável ambiental, conforme descrito na [documentação do Flask.](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables)
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Código de inicialização

O código de inicialização é diferente dependendo da plataforma. Para ASP.NET Core e ASP.NET, a assinatura nos utilizadores é delegada no middleware OpenID Connect. O modelo ASP.NET ou ASP.NET Core gera aplicações web para o Azure Ative Directory (Azure AD) v1.0. Alguma configuração é necessária para adaptá-las ao ponto final da plataforma de identidade da Microsoft (v2.0). No caso de Java, é tratado pela primavera com a cooperação da aplicação.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Em ASP.NET aplicações web Core (e APIs web), a aplicação está protegida porque tem um `[Authorize]` atributo nos controladores ou nas ações do controlador. Este atributo verifica se o utilizador é autenticado. O código que está a inicializar a aplicação está no *ficheiro Startup.cs.*

Para adicionar autenticação com a plataforma de identidade da Microsoft (anteriormente Azure AD v2.0), terá de adicionar o seguinte código. Os comentários no código devem ser autoexplicativos.

> [!NOTE]
> Se pretender começar diretamente com os novos modelos core ASP.NET para plataforma de identidade da Microsoft, que alavancam o Microsoft.Identity.Web, pode descarregar um pacote nuGet de pré-visualização contendo modelos de projeto para .NET Core 3.1 e .NET 5.0. Em seguida, uma vez instalado, pode instantaneamente instantaneamente ASP.NET aplicações web Core (MVC ou Blazor). Consulte [os modelos de projeto de aplicações web Microsoft.Identity.Web](https://aka.ms/ms-id-web/webapp-project-templates) para obter detalhes. Esta é a abordagem mais simples, pois fará todos os passos abaixo para si.
>
> Se preferir iniciar o seu projeto com o projeto web core ASP.NET atual dentro do Visual Studio ou utilizando `dotnet new mvc --auth SingleAuth` `dotnet new webapp --auth SingleAuth` ou, verá código como o seguinte:
>
>```c#
>  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
>          .AddAzureAD(options => Configuration.Bind("AzureAd", options));
> ```
>
> Este código utiliza o pacote **legado Microsoft.AspNetCore.Authentication.AzureAD.UI** NuGet que é utilizado para criar uma aplicação AD v1.0 Azure. Este artigo explica como criar uma aplicação de plataforma de identidade microsoft (Azure AD v2.0) que substitui esse código.
>

1. Adicione os pacotes [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) e [Microsoft.Identity.Web.UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) NuGet ao seu projeto. Remova o pacote Microsoft.AspNetCore.Authentication.AzureAD.UI NuGet se estiver presente.

2. Atualize o código `ConfigureServices` de modo a utilizar os `AddMicrosoftIdentityWebAppAuthentication` métodos e `AddMicrosoftIdentityUI` métodos.

   ```c#
   public class Startup
   {
    ...
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
     services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAd");

     services.AddRazorPages().AddMvcOptions(options =>
     {
      var policy = new AuthorizationPolicyBuilder()
                    .RequireAuthenticatedUser()
                    .Build();
      options.Filters.Add(new AuthorizeFilter(policy));
     }).AddMicrosoftIdentityUI();
    ```

3. No `Configure` método em *Startup.cs,* permitir a autenticação com uma chamada para `app.UseAuthentication();`

   ```c#
   // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
    // more code here
    app.UseAuthentication();
    app.UseAuthorization();
    // more code here
   }
   ```

No código acima:
- O `AddMicrosoftIdentityWebAppAuthentication` método de extensão é definido em **Microsoft.Identity.Web**. É:
  - Adiciona o serviço de autenticação.
  - Configura opções para ler o ficheiro de configuração (aqui a partir da secção "AzureAD")
  - Configura as opções OpenID Connect de modo a que a autoridade seja o ponto final da plataforma de identidade da Microsoft.
  - Valida o emitente do símbolo.
  - Garante que as reclamações correspondentes ao nome são mapeadas a partir da `preferred_username` reclamação no token de identificação.

- Além do objeto de configuração, pode especificar o nome da secção de configuração ao ligar `AddMicrosoftIdentityWebAppAuthentication` . Por defeito, `AzureAd` é.

- `AddMicrosoftIdentityWebAppAuthentication` tem outros parâmetros para cenários avançados. Por exemplo, rastrear eventos de middleware OpenID Connect pode ajudá-lo a resolver problemas na sua aplicação web se a autenticação não funcionar. Definir o parâmetro opcional `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` irá `true` mostrar-lhe como as informações são processadas pelo conjunto de ASP.NET core middleware à medida que progride a partir da resposta HTTP à identidade do utilizador em `HttpContext.User` .

- O `AddMicrosoftIdentityUI` método de extensão é definido em **Microsoft.Identity.Web.UI**. Fornece um controlador predefinido para manusear a sposição e a sposição.

Pode encontrar mais detalhes sobre como o Microsoft.Identity.Web lhe permite criar aplicações web em <https://aka.ms/ms-id-web/webapp>

> [!WARNING]
> Atualmente, o Microsoft.Identity.Web não suporta o cenário de **Contas individuais** de Utilizador (armazenar contas de utilizador na aplicação) quando se utiliza Azure AD como fornecedor de login externo. Para mais detalhes, consulte: [AzureAD/microsoft-identity-web#133](https://github.com/AzureAD/microsoft-identity-web/issues/133)

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

A amostra de Java usa a estrutura da primavera. A aplicação está protegida porque implementa um filtro, que interceta cada resposta HTTP. No arranque rápido das aplicações web java, este filtro está `AuthFilter` em `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java` .

O filtro processa o fluxo de código de autorização OAuth 2.0 e verifica se o utilizador é autenticado `isAuthenticated()` (método). Se o utilizador não for autenticado, calcula o URL dos pontos finais de autorização AZure AD e redireciona o navegador para este URI.

Quando a resposta chega, contendo o código de autorização, adquire o token utilizando o MSAL Java. Quando finalmente recebe o token do ponto final do token (no URI de redirecionamento), o utilizador é assinado.

Para mais detalhes, consulte o `doFilter()` método em [AuthFilter.java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java).

> [!NOTE]
> O código do `doFilter()` código está escrito numa ordem ligeiramente diferente, mas o fluxo é o descrito.

Para obter mais informações sobre o fluxo de código de autorização que este método desencadeia, consulte a [plataforma de identidade da Microsoft e o fluxo de código de autorização OAuth 2.0](v2-oauth2-auth-code-flow.md).

# <a name="python"></a>[Python](#tab/python)

A amostra python usa o Frasco. A inicialização de Flask e MSAL Python é feita em [app.py#L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

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

No próximo artigo, aprenderá a desencadear o sº de sção e a assinatura.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Passe para o próximo artigo neste cenário, [inscreva-se e assine](./scenario-web-app-sign-user-sign-in.md?tabs=aspnetcore).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Passe para o próximo artigo neste cenário, [inscreva-se e assine](./scenario-web-app-sign-user-sign-in.md?tabs=aspnet).

# <a name="java"></a>[Java](#tab/java)

Passe para o próximo artigo neste cenário, [inscreva-se e assine](./scenario-web-app-sign-user-sign-in.md?tabs=java).

# <a name="python"></a>[Python](#tab/python)

Passe para o próximo artigo neste cenário, [inscreva-se e assine](./scenario-web-app-sign-user-sign-in.md?tabs=python).

---
