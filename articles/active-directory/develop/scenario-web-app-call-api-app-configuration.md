---
title: Configure uma aplicação web que chama APIs web | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como configurar o código de uma aplicação web que chama APIs web
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: aa377547f7f4961e199ec8d62bf0f1435296f983
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104669309"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Uma aplicação web que chama APIs web: configuração de código

Como mostrado na aplicação Web que assina no cenário [dos utilizadores,](scenario-web-app-sign-user-overview.md) a aplicação web utiliza o [fluxo de código de autorização OAuth 2.0](v2-oauth2-auth-code-flow.md) para iniciar a inscrição do utilizador. Este fluxo tem dois passos:

1. Solicite um código de autorização. Esta parte delega um diálogo privado com o utilizador na plataforma de identidade da Microsoft. Durante esse diálogo, o utilizador assina e consente na utilização de APIs web. Quando o diálogo privado termina com sucesso, a aplicação web recebe um código de autorização no seu URI de redirecionamento.
1. Solicite um sinal de acesso à API resgatando o código de autorização.

A [aplicação Web que assina nos](scenario-web-app-sign-user-overview.md) cenários dos utilizadores abrangeu apenas o primeiro passo. Aqui aprende a modificar a sua aplicação web para que não só assine os utilizadores, mas também agora chame APIs web.

## <a name="microsoft-libraries-supporting-web-apps"></a>Bibliotecas da Microsoft que suportam aplicações web

As seguintes bibliotecas da Microsoft suportam aplicações web:

[!INCLUDE [active-directory-develop-libraries-webapp](../../../includes/active-directory-develop-libraries-webapp.md)]

Selecione o separador para a plataforma em que está interessado:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="client-secrets-or-client-certificates"></a>Segredos do cliente ou certificados de cliente

Dado que a sua aplicação web agora chama uma API web a jusante, forneça um certificado secreto ou cliente do cliente no *appsettings.jsem* arquivo. Também pode adicionar uma secção que especifica:

- O URL da API da web a jusante
- Os âmbitos necessários para chamar a API

No exemplo seguinte, a `GraphBeta` secção especifica estas definições.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-app-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 },
 "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
    }
}
```

Em vez de um segredo de cliente, pode fornecer um certificado de cliente. O seguinte código snippet mostra usando um certificado armazenado em Azure Key Vault.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-app-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
   ]
  },
  "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
  }
}
```

*Microsoft.Identity.Web* fornece várias formas de descrever certificados, tanto por configuração como por código. Para mais detalhes, consulte [Microsoft.Identity.Web - Utilizando certificados](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) no GitHub.

## <a name="startupcs"></a>Startup.cs

A sua aplicação web terá de adquirir um símbolo para a API a jusante. Especifica-o adicionando a `.EnableTokenAcquisitionToCallDownstreamApi()` linha depois `.AddMicrosoftIdentityWebApi(Configuration)` . Esta linha expõe o `ITokenAcquisition` serviço que pode utilizar no seu controlador e ações de página. No entanto, como verá nas duas opções seguintes, pode ser feito de forma mais simples. Você também terá de escolher uma implementação de cache simbólica, por `.AddInMemoryTokenCaches()` exemplo, no *Startup.cs*:

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, Configuration.GetSection("AzureAd"))
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

Os âmbitos passados `EnableTokenAcquisitionToCallDownstreamApi` são opcionais e permitem que a sua aplicação web solicite os âmbitos e o consentimento do utilizador para esses âmbitos quando estes iniciarem sessão. Se não especificar os âmbitos, o *Microsoft.Identity.Web* permitirá uma experiência de consentimento incremental.

Se você não quiser adquirir o token por si mesmo, *o Microsoft.Identity.Web* fornece dois mecanismos para chamar uma API web a partir de uma aplicação web. A opção que escolher depende se pretende ligar para o Microsoft Graph ou para outra API.

### <a name="option-1-call-microsoft-graph"></a>Opção 1: Ligue para o Gráfico da Microsoft

Se pretender ligar para o Microsoft Graph, *o Microsoft.Identity.Web* permite-lhe utilizar diretamente o `GraphServiceClient` (exposto pelo Microsoft Graph SDK) nas suas ações API. Para expor o Gráfico do Microsoft:

1. Adicione o pacote [Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) NuGet ao seu projeto.
1. Adicione `.AddMicrosoftGraph()` depois no ficheiro .cs `.EnableTokenAcquisitionToCallDownstreamApi()` *Arranque.* `.AddMicrosoftGraph()` tem vários sobreposições. Utilizando o sobreposição que toma uma secção de configuração como parâmetro, o código torna-se:

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, Configuration.GetSection("AzureAd"))
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
                  .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>Opção 2: Ligue para uma API web a jusante que não seja o Microsoft Graph

Para chamar uma API web diferente do Microsoft Graph, *o Microsoft.Identity.Web* fornece `.AddDownstreamWebApi()` , que solicita tokens e chama a API web a jusante.

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, "AzureAd")
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
                  .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

### <a name="summary"></a>Resumo

Tal como acontece com as APIs web, pode escolher várias implementações de cache simbólicas. Para mais detalhes, consulte [Microsoft.Identity.Web - Serialization cache token](https://aka.ms/ms-id-web/token-cache-serialization) no GitHub.

A imagem a seguir mostra as várias possibilidades do *Microsoft.Identity.Web* e o seu impacto no ficheiro *.cs Startup:*

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="Diagrama de bloco mostrando opções de configuração de serviço no ponto de arranque C S para chamar uma API web e especificar uma implementação de cache de símbolo":::

> [!NOTE]
> Para compreender plenamente os exemplos de código aqui, conheça [os fundamentos ASP.NET Core](/aspnet/core/fundamentals), e em particular com a injeção de [dependência](/aspnet/core/fundamentals/dependency-injection) e [opções.](/aspnet/core/fundamentals/configuration/options)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Como o pedido de sômis do utilizador é delegado ao middleware OpenID Connect (OIDC), deve interagir com o processo OIDC. A forma como interage depende da estrutura que usa.

Para ASP.NET, você vai subscrever eventos OIDC de middleware:

- Você vai deixar ASP.NET Core solicitar um código de autorização através do middleware Open ID Connect. ASP.NET ou ASP.NET O Núcleo permitirá que o utilizador faça o seu s consentimento.
- Você vai subscrever a aplicação web para receber o código de autorização. Esta subscrição é feita utilizando um delegado C#.
- Quando o código de autorização for recebido, usará as bibliotecas MSAL para o resgatar. Os tokens de acesso resultantes e fichas de atualização são armazenados na cache simbólica. A cache pode ser usada noutras partes da aplicação, como controladores, para adquirir outros tokens silenciosamente.

Exemplos de código neste artigo e o seguinte são extraídos da [amostra de aplicações web ASP.NET.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) Talvez queira consultar essa amostra para obter todos os detalhes da implementação.

# <a name="java"></a>[Java](#tab/java)

Exemplos de código neste artigo e o seguinte são extraídos da [aplicação web Java que chama Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp), uma amostra de aplicação web que usa MSAL para Java.
A amostra permite atualmente que a MSAL para a Java produza o URL do código de autorização e manuseie a navegação até ao ponto final de autorização para a plataforma de identidade da Microsoft. Também é possível usar a segurança Sprint para iniciar a inscrição do utilizador. É melhor consultar a amostra para obter todos os detalhes da implementação.

# <a name="python"></a>[Python](#tab/python)

Exemplos de código neste artigo e o seguinte são extraídos da [aplicação web Python chamada Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp), uma amostra de aplicação web que utiliza o MSAL. A Python.
A amostra permite atualmente a MSAL. Python produz o URL de código de autorização e trata da navegação até ao ponto final de autorização para a plataforma de identidade da Microsoft. É melhor consultar a amostra para obter todos os detalhes da implementação.

---

## <a name="code-that-redeems-the-authorization-code"></a>Código que resgata o código de autorização

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft.Identity.Web simplifica o seu código definindo as definições corretas do OpenID Connect, subscrevendo o caso recebido do código e redentora do código. Não é necessário um código adicional para resgatar o código de autorização. Consulte [o código fonte Microsoft.Identity.Web](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L140) para obter mais detalhes sobre como isto funciona.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET lida com as coisas de forma semelhante à ASP.NET Core, exceto que a configuração do OpenID Connect e a subscrição do `OnAuthorizationCodeReceived` evento acontecem no ficheiro .cs [App_Start\Startup.Auth.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) Os conceitos também são semelhantes aos de ASP.NET Core, exceto que em ASP.NET deve especificar o `RedirectUri` [ inWeb.config#L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Esta configuração é um pouco menos robusta do que a do ASP.NET Core, porque terá de alterá-la quando implementar a sua aplicação.

Aqui está o código para Startup.Auth.cs:

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the query string (&code=<code>).
    app.UseOAuth2CodeRedeemer(
        new OAuth2CodeRedeemerOptions
        {
            ClientId = AuthenticationConfig.ClientId,
            ClientSecret = AuthenticationConfig.ClientSecret,
            RedirectUri = AuthenticationConfig.RedirectUri
        }
      );

  app.UseOpenIdConnectAuthentication(
      new OpenIdConnectAuthenticationOptions
      {
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0.
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // A basic set of permissions for user sign-in and profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application, you would use IssuerValidator for additional checks, such as making sure the user's organization has signed up for your app.
            //     IssuerValidator = (issuer, token, tvp) =>
            //     {
            //        //if(MyCustomTenantValidation(issuer))
            //        return issuer;
            //        //else
            //        //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
            //    },
            //NameClaimType = "name",
        },
        Notifications = new OpenIdConnectAuthenticationNotifications()
        {
            AuthorizationCodeReceived = OnAuthorizationCodeReceived,
            AuthenticationFailed = OnAuthenticationFailed,
        }
      });
  }

  private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
  {
      // Upon successful sign-in, get the access token and cache it by using MSAL.
      IConfidentialClientApplication clientApp = MsalAppBuilder.BuildConfidentialClientApplication(new ClaimsPrincipal(context.AuthenticationTicket.Identity));
      AuthenticationResult result = await clientApp.AcquireTokenByAuthorizationCode(new[] { "Mail.Read" }, context.Code).ExecuteAsync();
  }

  private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
  {
      notification.HandleResponse();
      notification.Response.Redirect("/Error?message=" + notification.Exception.Message);
      return Task.FromResult(0);
  }
}
```

# <a name="java"></a>[Java](#tab/java)

Consulte [a aplicação Web que assina nos utilizadores: Configuração de código](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) para entender como a amostra de Java obtém o código de autorização. Depois de a aplicação receber o código, o [AuthFilter.java#L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Delegados ao `AuthHelper.processAuthenticationCodeRedirect` método em [AuthHelper.java#L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
1. `getAuthResultByAuthCode`Chamadas.

```Java
class AuthHelper {
  // Code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // Code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // Code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// Code omitted
  }
}
```

O `getAuthResultByAuthCode` método é definido em [AuthHelper.java#L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Cria um MSAL `ConfidentialClientApplication` , e depois liga com o código de `acquireToken()` `AuthorizationCodeParameters` autorização.

```Java
   private IAuthenticationResult getAuthResultByAuthCode(
            HttpServletRequest httpServletRequest,
            AuthorizationCode authorizationCode,
            String currentUri) throws Throwable {

        IAuthenticationResult result;
        ConfidentialClientApplication app;
        try {
            app = createClientApplication();

            String authCode = authorizationCode.getValue();
            AuthorizationCodeParameters parameters = AuthorizationCodeParameters.builder(
                    authCode,
                    new URI(currentUri)).
                    build();

            Future<IAuthenticationResult> future = app.acquireToken(parameters);

            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        }

        if (result == null) {
            throw new ServiceUnavailableException("authentication result was null");
        }

        SessionManagementHelper.storeTokenCacheInSession(httpServletRequest, app.tokenCache().serialize());

        return result;
    }

    private ConfidentialClientApplication createClientApplication() throws MalformedURLException {
        return ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(clientSecret)).
                authority(authority).
                build();
    }
```

# <a name="python"></a>[Python](#tab/python)

O fluxo de código de autorização é solicitado como mostrado na [aplicação Web que assina nos utilizadores: Configuração de código](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). O código é então recebido na `authorized` função, que o Frasco encaminha a partir do `/getAToken` URL. Consulte [o aplicativo.py#L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) para o contexto completo deste código:

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD.
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here.
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

Em vez de um segredo de cliente, a aplicação confidencial do cliente também pode provar a sua identidade usando um certificado de cliente, ou uma afirmação do cliente.
O uso de afirmações de clientes é um cenário avançado, detalhado nas [afirmações do Cliente.](msal-net-client-assertions.md)

## <a name="token-cache"></a>Cache token

> [!IMPORTANT]
> A implementação de token-cache para aplicações web ou APIs web é diferente da implementação para aplicações de desktop, que é frequentemente [baseada em ficheiros](scenario-desktop-acquire-token.md#file-based-token-cache).
> Por razões de segurança e desempenho, é importante garantir que para aplicações web e APIs web existe uma cache simbólica por conta de utilizador. Tem de serializar a cache simbólica para cada conta.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

O ASP.NET tutorial principal usa a injeção de dependência para permitir que você decida a implementação da cache simbólica no ficheiro .cs Arranque para a sua aplicação. Microsoft.Identity.Web vem com serializers de cache de fichas pré-construídos descritos na [serialização da cache Token](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application). Uma possibilidade interessante é escolher [caches](/aspnet/core/performance/caching/distributed#distributed-memory-cache)de memória distribuídos ASP.NET Core:

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi(
                initialScopes: new string[] { "user.read" })
            .AddDistributedTokenCaches();

// Then, choose your implementation.
// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache();

// Or a Redis cache:
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache:
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Para obter mais informações sobre os fornecedores de cache de fichas simbólicas, consulte também o artigo [de serialização](https://aka.ms/ms-id-web/token-cache-serialization) da cache token da Microsoft.Identity.Web, bem como os [tutoriais de aplicações core web ASP.NET | Fase de caches token](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) do tutorial de aplicações web.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A implementação de token-cache para aplicações web ou APIs web é diferente da implementação para aplicações de desktop, que é frequentemente [baseada em ficheiros](scenario-desktop-acquire-token.md#file-based-token-cache).

A implementação da aplicação web pode usar a sessão de ASP.NET ou a memória do servidor. Por exemplo, veja como a implementação da cache é viciada após a criação da aplicação MSAL.NET no [MsalAppBuilder.cs#L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51):

```csharp
public static class MsalAppBuilder
{
 // Omitted code
    public static IConfidentialClientApplication BuildConfidentialClientApplication(ClaimsPrincipal currentUser)
    {
      IConfidentialClientApplication clientapp = ConfidentialClientApplicationBuilder.Create(AuthenticationConfig.ClientId)
            .WithClientSecret(AuthenticationConfig.ClientSecret)
            .WithRedirectUri(AuthenticationConfig.RedirectUri)
            .WithAuthority(new Uri(AuthenticationConfig.Authority))
            .Build();

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation.
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="java"></a>[Java](#tab/java)

MSAL Java fornece métodos para serializar e deserizar a cache simbólica. A amostra de Java lida com a serialização da sessão, como mostra o `getAuthResultBySilentFlow` método em [AuthHelper.java#L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122):

```Java
IAuthenticationResult getAuthResultBySilentFlow(HttpServletRequest httpRequest, HttpServletResponse httpResponse)
      throws Throwable {

  IAuthenticationResult result =  SessionManagementHelper.getAuthSessionObject(httpRequest);

  IConfidentialClientApplication app = createClientApplication();

  Object tokenCache = httpRequest.getSession().getAttribute("token_cache");
  if (tokenCache != null) {
      app.tokenCache().deserialize(tokenCache.toString());
  }

  SilentParameters parameters = SilentParameters.builder(
          Collections.singleton("User.Read"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  // Update session with latest token cache.
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

O detalhe da `SessionManagementHelper` aula é fornecido na amostra [MSAL para Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java).

# <a name="python"></a>[Python](#tab/python)

Na amostra Python, uma cache por conta é assegurada recriando um pedido de cliente confidencial para cada pedido e, em seguida, serializando-o na cache da sessão flask:

```python
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)

# Code omitted here for simplicity

def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)
```

---

## <a name="next-steps"></a>Passos seguintes

Neste ponto, quando o utilizador assina, um token é armazenado na cache simbólica. Vamos ver como é usado noutras partes da aplicação web.

[Remover as contas da cache na assinatura global](scenario-web-app-call-api-sign-in.md)
