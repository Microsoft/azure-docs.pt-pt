---
title: Configure uma aplicação web que chama APIs web - plataforma de identidade microsoft / Azure
description: Saiba como configurar o código de uma aplicação web que chama APIs web
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
ms.openlocfilehash: 82439f8380b0dca676b781e36fff738b5d5bee93
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758185"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Uma aplicação web que chama APIs web: Configuração de código

Como mostra a [aplicação web que assina no](scenario-web-app-sign-user-overview.md) cenário dos utilizadores, a aplicação web utiliza o fluxo de código de autorização [OAuth 2.0](v2-oauth2-auth-code-flow.md) para iniciar sessão do utilizador. Este fluxo tem dois passos:

1. Peça um código de autorização. Esta peça delega um diálogo privado com o utilizador para a plataforma de identidade da Microsoft. Durante esse diálogo, o utilizador assina e consente na utilização de APIs web. Quando o diálogo privado termina com sucesso, a aplicação web recebe um código de autorização no seu URI redirecionado.
1. Solicite um sinal de acesso à API redimindo o código de autorização.

A [aplicação Web que assina em](scenario-web-app-sign-user-overview.md) cenários dos utilizadores cobriu apenas o primeiro passo. Aqui aprende como modificar a sua aplicação web para que não só institua os utilizadores, mas também agora chama APIs web.

## <a name="libraries-that-support-web-app-scenarios"></a>Bibliotecas que suportam cenários de web-app

As seguintes bibliotecas na Microsoft Authentication Library (MSAL) suportam o fluxo de código de autorização para aplicações web:

| Biblioteca MSAL | Descrição |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Suporte para as plataformas .NET Framework e .NET Core. Não suportados estão a Universal Windows Platform (UWP), Xamarin.iOS e Xamarin.Android, porque essas plataformas são usadas para construir aplicações de clientes públicos. Para ASP.NET web apps e APIs web, MSAL.NET é encapsulado numa biblioteca de alto nível chamada Microsoft.Identity.Web|
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL para Python | Suporte para aplicações web Python. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL para Java | Suporte para aplicações web Java. |

Selecione o separador para a plataforma em que está interessado:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Para permitir que a sua aplicação web ligue para APIs protegidos ao utilizar o Microsoft.Identity.Web, basta ligar e especificar um formato de serialização de `AddWebAppCallsProtectedWebApi` cache token (por exemplo, cache token na memória):

```C#
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    // more code here

    services.AddSignIn(Configuration, "AzureAd")
            .AddWebAppCallsProtectedWebApi(Configuration,
                                           initialScopes: new string[] { "user.read" })
            .AddInMemoryTokenCaches();

    // more code here
}
```

Se estiver interessado em entender mais sobre a cache simbólica, consulte opções de serialização de [cache token](#token-cache)

> [!NOTE]
> Para compreender completamente os exemplos de código aqui, é preciso estar familiarizado com [os fundamentos do núcleo ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals), e em particular com a injeção de [dependência](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) e [opções.](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Uma vez que o acesso ao utilizador é delegado ao middleware de ligação Open ID (OIDC), deve interagir com o processo OIDC. A forma como interages depende da estrutura que usas.

Para ASP.NET, subscreve eventos da Middleware OIDC:

- Deixará ASP.NET Core solicitar um código de autorização através do middleware Open ID Connect. ASP.NET ou ASP.NET Core permitirá ao utilizador iniciar sessão e consentir.
- Subscreverá a aplicação web para receber o código de autorização. Esta subscrição é feita utilizando um delegado C#.
- Quando o código de autorização for recebido, utilizará bibliotecas MSAL para o resgatar. As fichas de acesso resultantes e as fichas de atualização são armazenadas na cache simbólica. A cache pode ser utilizada noutras partes da aplicação, como os controladores, para adquirir outras fichas silenciosamente.

Exemplos de código neste artigo e no seguinte são extraídos da [amostra de aplicação Web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Talvez queira consultar essa amostra para obter detalhes completos de implementação.

# <a name="java"></a>[Java](#tab/java)

Exemplos de código neste artigo e no seguinte são extraídos da [aplicação web Java que chama microsoft graph](https://github.com/Azure-Samples/ms-identity-java-webapp), uma amostra de aplicação web que usa MSAL para Java.
A amostra permite atualmente que a MSAL para a Java produza o URL do código de autorização e manuseie a navegação até ao ponto final de autorização da plataforma de identidade da Microsoft. Também é possível usar a segurança sprint para iniciar sessão. Talvez queira consultar a amostra para obter detalhes completos de implementação.

# <a name="python"></a>[Python](#tab/python)

Exemplos de código neste artigo e no seguinte são extraídos da [aplicação web Python chamada Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp), uma amostra de aplicação web que utiliza mSAL. A Python.
A amostra permite atualmente a MSAL. Python produz o URL de código de autorização e manuseia a navegação até ao ponto final de autorização para a plataforma de identidade da Microsoft. Talvez queira consultar a amostra para obter detalhes completos de implementação.

---

## <a name="code-that-redeems-the-authorization-code"></a>Código que resgata o código de autorização

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft.Identity.Web simplifica o seu código definindo as definições corretas do OpenID Connect, subscrevendo o evento recebido pelo código e redimindo o código. Não é necessário um código adicional para resgatar o código de autorização.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET lida com as coisas de forma semelhante à ASP.NET Core, exceto que a configuração do OpenID Connect e a subscrição do `OnAuthorizationCodeReceived` evento acontecem no ficheiro [App_Start\Startup.Auth.cs.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) Os conceitos também são semelhantes aos do ASP.NET Core, exceto que em ASP.NET deve especificar o `RedirectUri` [web.config#L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Esta configuração é um pouco menos robusta do que a do ASP.NET Core, porque terá de alterá-la quando implementar a sua aplicação.

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

Consulte [a aplicação Web que assina nos utilizadores: Configuração](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) de código para entender como a amostra java obtém o código de autorização. Depois de a aplicação receber o código, o [AuthFilter.java#L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Delegados ao `AuthHelper.processAuthenticationCodeRedirect` método em [AuthHelper.java#L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
1. `getAuthResultByAuthCode`Ligações.

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

O `getAuthResultByAuthCode` método é definido em [AuthHelper.java#L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Cria um MSAL, `ConfidentialClientApplication` e depois liga com criado a partir do código de `acquireToken()` `AuthorizationCodeParameters` autorização.

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

O fluxo de código de autorização é solicitado como mostrado na [aplicação Web que assina nos utilizadores: Configuração de código](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). O código é então recebido na `authorized` função, que o Flask faz a partir do `/getAToken` URL. Consulte [app.py#L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) para obter todo o contexto deste código:

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
O uso de afirmações de clientes é um cenário avançado, detalhado nas afirmações do [Cliente.](msal-net-client-assertions.md)

## <a name="token-cache"></a>Cache de token

> [!IMPORTANT]
> A implementação de token-cache para aplicações web ou APIs web é diferente da implementação para aplicações de desktop, que muitas vezes é [baseada em ficheiros](scenario-desktop-acquire-token.md#file-based-token-cache).
> Por razões de segurança e desempenho, é importante garantir que para aplicações web e APIs web existe uma cache simbólica por conta de utilizador. Tem de serializar a cache simbólica para cada conta.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

O tutorial central ASP.NET usa a injeção de dependência para que você decida a implementação do cache simbólico no ficheiro Startup.cs para a sua aplicação. Microsoft.Identity.Web vem com serializadores de cache de token pré-construídos descritos em serialização de [cache token](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application). Uma possibilidade interessante é escolher ASP.NET [caches de memória distribuídos](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache)core:

```csharp
// Use a distributed token cache by adding:
    services.AddSignIn(Configuration, "AzureAd")
            .AddWebAppCallsProtectedWebApi(Configuration,
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

Para mais detalhes sobre os fornecedores de cache token, consulte também os [tutoriais de aplicações ASP.NET Core Web [ Fase](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) de caches simbólicos do tutorial.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A implementação de token-cache para aplicações web ou APIs web é diferente da implementação para aplicações de desktop, que muitas vezes é [baseada em ficheiros](scenario-desktop-acquire-token.md#file-based-token-cache).

A implementação da aplicação web pode utilizar a sessão ASP.NET ou a memória do servidor. Por exemplo, veja como a implementação do cache é anexada após a criação da aplicação MSAL.NET em [MsalAppBuilder.cs#L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51):

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

A MSAL Java fornece métodos para serializar e desserializar a cache simbólica. A amostra java trata da serialização da sessão, como mostra o `getAuthResultBySilentFlow` método em [AuthHelper.java#L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122):

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

Os detalhes da `SessionManagementHelper` aula são fornecidos na amostra [MSAL para Java.](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)

# <a name="python"></a>[Python](#tab/python)

Na amostra Python, uma cache por conta é assegurada recriando um pedido de cliente confidencial para cada pedido e, em seguida, serializando-o na cache da sessão do Flask:

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

Neste ponto, quando o utilizador entra, uma ficha é armazenada na cache token. Vamos ver como é usado em outras partes da aplicação web.

> [!div class="nextstepaction"]
> [Uma aplicação web que chama APIs web: Remova as contas da cache no sign-out global](scenario-web-app-call-api-sign-in.md)
