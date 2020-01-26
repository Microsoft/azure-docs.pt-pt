---
title: Configure uma aplicação web que chama APIs web - plataforma de identidade microsoft / Azure
description: Saiba como configurar o código de uma aplicação web que chama APIs web
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
ms.openlocfilehash: 374b215a737efbe3d421b6dc49af01303ec54473
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759165"
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
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Suporte para as plataformas .NET Framework e .NET Core. Não suportados estão a Universal Windows Platform (UWP), Xamarin.iOS e Xamarin.Android, porque essas plataformas são usadas para construir aplicações de clientes públicos. |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL para Python | Suporte para aplicações web Python. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL para Java | Suporte para aplicações web Java. |

Selecione o separador para a plataforma em que está interessado:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Uma vez que o acesso ao utilizador é delegado ao middleware de ligação Open ID (OIDC), deve interagir com o processo OIDC. A forma como interages depende da estrutura que usas.

Para ASP.NET Core, você subscreverá eventos oIDC de middleware:

- Deixará ASP.NET Core solicitar um código de autorização através do middleware Open ID Connect. ASP.NET ou ASP.NET Core permitirá ao utilizador iniciar sessão e consentir.
- Subscreverá a aplicação web para receber o código de autorização. Esta subscrição é C# feita utilizando um delegado.
- Quando o código de autorização for recebido, utilizará bibliotecas MSAL para o resgatar. As fichas de acesso resultantes e as fichas de atualização são armazenadas na cache simbólica. A cache pode ser utilizada noutras partes da aplicação, como os controladores, para adquirir outras fichas silenciosamente.

Exemplos de código neste artigo e no seguinte são extraídos do tutorial incremental da [aplicação web ASP.NET Core, capítulo 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph). Talvez queira consultar esse tutorial para obter detalhes completos de implementação.

> [!NOTE]
> Para compreender completamente os exemplos de código aqui, é preciso estar familiarizado com [os fundamentos do núcleo ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals), e em particular com a injeção de [dependência](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) e [opções.](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Uma vez que o acesso ao utilizador é delegado ao middleware de ligação Open ID (OIDC), deve interagir com o processo OIDC. A forma como interages depende da estrutura que usas.

Para ASP.NET, subscreve eventos da Middleware OIDC:

- Deixará ASP.NET Core solicitar um código de autorização através do middleware Open ID Connect. ASP.NET ou ASP.NET Core permitirá ao utilizador iniciar sessão e consentir.
- Subscreverá a aplicação web para receber o código de autorização. Esta subscrição é C# feita utilizando um delegado.
- Quando o código de autorização for recebido, utilizará bibliotecas MSAL para o resgatar. As fichas de acesso resultantes e as fichas de atualização são armazenadas na cache simbólica. A cache pode ser utilizada noutras partes da aplicação, como os controladores, para adquirir outras fichas silenciosamente.

Exemplos de código neste artigo e no seguinte são extraídos da [amostra de aplicação Web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Talvez queira consultar essa amostra para obter detalhes completos de implementação.

# <a name="javatabjava"></a>[Java](#tab/java)

Exemplos de código neste artigo e no seguinte são extraídos da [aplicação web Java que chama microsoft graph](https://github.com/Azure-Samples/ms-identity-java-webapp), uma amostra de aplicação web que usa MSAL para Java.
A amostra permite atualmente que a MSAL para a Java produza o URL do código de autorização e manuseie a navegação até ao ponto final de autorização da plataforma de identidade da Microsoft. Também é possível usar a segurança sprint para iniciar sessão. Talvez queira consultar a amostra para obter detalhes completos de implementação.

# <a name="pythontabpython"></a>[Python](#tab/python)

Exemplos de código neste artigo e no seguinte são extraídos da [aplicação web Python chamada Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp), uma amostra de aplicação web que utiliza mSAL. A Python.
A amostra permite atualmente a MSAL. Python produz o URL de código de autorização e manuseia a navegação até ao ponto final de autorização para a plataforma de identidade da Microsoft. Talvez queira consultar a amostra para obter detalhes completos de implementação.

---

## <a name="code-that-redeems-the-authorization-code"></a>Código que resgata o código de autorização

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

No ASP.NET Core, no ficheiro `Startup.cs`, subscreve o evento `OnAuthorizationCodeReceived` OpenID Connect. Deste evento, ligue para o método `AcquireTokenFromAuthorizationCode` MSAL.NET. Este método armazena as seguintes fichas na cache simbólica:

- O *sinal de acesso* para o `scopes`solicitado.
- Um *símbolo refrescante.* Este token será usado para refrescar o token de acesso quando estiver perto de expirar, ou para obter outro símbolo em nome do mesmo utilizador, mas para um recurso diferente.

O [tutor da aplicação ASP.NET Core Web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) fornece-lhe código reutilizável para as suas aplicações web.

Segue-se o código de [Startup.cs#L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42). Apresenta chamadas para:

- O método `AddMicrosoftIdentityPlatformAuthentication`, que adiciona autenticação à aplicação web.
- O método `AddMsal`, que adiciona a capacidade de chamar APIs web.
- O método `AddInMemoryTokenCaches`, que consiste em escolher uma implementação de cache de token.

```csharp
public class Startup
{
  // Code not shown here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token-cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not shown here
}
```

`Constants.ScopeUserRead` é definido em [Constants.cs#L5:](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5)

```csharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

Já estudou o conteúdo de `AddMicrosoftIdentityPlatformAuthentication` na [aplicação Web que assina nos utilizadores - configuração de código](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code).

### <a name="the-addmsal-method"></a>O método AddMsal

O código para `AddMsal` está localizado em [Microsoft.Identity.Web/WebAppServiceCollections.cs#L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159).

```csharp

/// <summary>
/// Extensions for IServiceCollection for startup initialization
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the web app or web API.
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection.
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign in by using their personal Microsoft accounts.
          // (It's required by MSAL.NET and automatically provided when users sign in by using work or school accounts.)
          options.Scope.Add("offline_access");
          if (initialScopes != null)
          {
              foreach (string scope in initialScopes)
              {
                  if (!options.Scope.Contains(scope))
                  {
                      options.Scope.Add(scope);
                  }
              }
          }

          // Handle the auth redemption by MSAL.NET so that a token is available in the token cache,
          // where it will be usable from controllers later (by means of the TokenAcquisition service).
          var handler = options.Events.OnAuthorizationCodeReceived;
          options.Events.OnAuthorizationCodeReceived = async context =>
          {
              var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
              await tokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync(context, options.Scope).ConfigureAwait(false);
              await handler(context).ConfigureAwait(false);
          };
      });
      return services;
  }
}
```

O método `AddMsal` garante que:

- A aplicação web ASP.NET Core solicita um símbolo de identificação para o utilizador e um código de autenticação (`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`).
- O âmbito `offline_access` é adicionado. Este âmbito obtém o consentimento do utilizador para que a aplicação obtenha um token de atualização.
- A aplicação subscreve o evento `OnAuthorizationCodeReceived` OIDC e resgata a chamada utilizando MSAL.NET, que é encapsulada aqui num componente reutilizável que implementa `ITokenAcquisition`.

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>O método TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync

O método `TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` está localizado em [Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145). Garante que:

- ASP.NET não tenta resgatar o código de autenticação em paralelo com MSAL.NET (`context.HandleCodeRedemption();`).
- As reclamações no token ID estão disponíveis para que a MSAL calcule uma chave de cache simbólica para a conta do utilizador.
- Se necessário, é criado um exemplo da aplicação MSAL.NET.
- O código é redimido pela aplicação MSAL.NET.
- O novo símbolo de identificação é partilhado com ASP.NET Core durante a chamada para `context.HandleCodeRedemption(null, result.IdToken);`. O sinal de acesso não é partilhado com ASP.NET Core. Permanece na cache token MSAL.NET associada ao utilizador, onde está pronto para ser utilizado em controladores core ASP.NET.

Aqui está o código relevante para `TokenAcquisition`:

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  string[] scopesRequestedByMsalNet = new string[]{ "openid", "profile", "offline_access" };

  // Code omitted here for clarity


  public async Task AddAccountToCacheFromAuthorizationCodeAsync(AuthorizationCodeReceivedContext context, IEnumerable<string> scopes)
  {
   // Code omitted here for clarity

    try
    {
      // Because AcquireTokenByAuthorizationCodeAsync is asynchronous, we tell ASP.NET core that we're handing the code
      // even if it's not done yet, so that it doesn't concurrently call the token endpoint. Otherwise, there will be a
      // race condition that causes an Azure AD error message ("code already redeemed").
      context.HandleCodeRedemption();

      // The cache needs the claims from the ID token.
      // If they're not yet in the HttpContext.User's claims, add them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Don't share the access token with ASP.NET Core. If we share it, ASP.NET will cache it and won't send the OAuth 2.0 request if
      // a further call to AcquireTokenByAuthorizationCodeAsync is required later for incremental consent (getting a code requesting more scopes).
      // Do share the ID token, however.
      var result = await application
          .AcquireTokenByAuthorizationCode(scopes.Except(_scopesRequestedByMsalNet), context.ProtocolMessage.Code)
          .ExecuteAsync()
          .ConfigureAwait(false);

      context.HandleCodeRedemption(null, result.IdToken);
  }
  catch (MsalException ex)
  {
      Debug.WriteLine(ex.Message);
      throw;
  }
 }
```

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>O método TokenAcquisition.BuildConfidentialClientApplication

Em ASP.NET Core, a construção da aplicação confidencial do cliente utiliza informações que estão no `HttpContext`. O `HttpContext` associado ao pedido é acedido através da utilização da propriedade `CurrentHttpContext`. `HttpContext` tem informações sobre o URL para a aplicação web e sobre o utilizador inscrito (num `ClaimsPrincipal`). 

O método `BuildConfidentialClientApplication` também utiliza a configuração ASP.NET Core. A configuração tem uma secção "AzureAD", e também está ligada a ambos os seguintes elementos:

- A estrutura de dados `_applicationOptions` do tipo [ConfidentialClientApplicationOptions.](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet)
- A `azureAdOptions` instância de opções do tipo [AzureAdOptions,](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs)definida em ASP.NET Core `Authentication.AzureAD.UI`.

Finalmente, a aplicação precisa de manter caches simbólicos. Saberá mais sobre isso na próxima secção.

O código para o método `GetOrBuildConfidentialClientApplication()` está em [Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). Utiliza membros que foram injetados por injeção de dependência (passado no construtor de `TokenAcquisition` em [Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59)).

Aqui está o código para `GetOrBuildConfidentialClientApplication`:

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor.
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL confidential client application, if needed.
  /// </summary>
  private IConfidentialClientApplication GetOrBuildConfidentialClientApplication()
  {
    if (application == null)
    {
        application = BuildConfidentialClientApplication();
    }
    return application;
  }

  /// <summary>
  /// Creates an MSAL Confidential client application
  /// </summary>
  /// <param name="claimsPrincipal"></param>
  /// <returns></returns>
  private IConfidentialClientApplication BuildConfidentialClientApplication()
  {
    var request = CurrentHttpContext.Request;
    var azureAdOptions = _azureAdOptions;
    var applicationOptions = _applicationOptions;
    string currentUri = UriHelper.BuildAbsolute(
        request.Scheme,
        request.Host,
        request.PathBase,
        azureAdOptions.CallbackPath ?? string.Empty);

    string authority = $"{applicationOptions.Instance}{applicationOptions.TenantId}/";

    var app = ConfidentialClientApplicationBuilder
        .CreateWithApplicationOptions(applicationOptions)
        .WithRedirectUri(currentUri)
        .WithAuthority(authority)
        .Build();

    // Initialize token cache providers
    _appTokenCacheProvider?.InitializeAsync(app.AppTokenCache);
    _userTokenCacheProvider?.InitializeAsync(app.UserTokenCache);

    return app;
  }

```

### <a name="summary"></a>Resumo

`AcquireTokenByAuthorizationCode` é realmente o método que resgata o código de autorização que ASP.NET solicita, e que recebe as fichas que são adicionadas à cache MSAL.NET token do utilizador. A partir da cache, as fichas são então usadas no ASP.NET comandos Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET lida com as coisas de forma semelhante à ASP.NET Core, exceto que a configuração do OpenID Connect e a subscrição do evento `OnAuthorizationCodeReceived` acontecem no ficheiro [App_Start\Startup.Auth.cs.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) Os conceitos também são semelhantes aos do núcleo ASP.NET, exceto que em ASP.NET deve especificar o `RedirectUri` em [Web.config#L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Esta configuração é um pouco menos robusta do que a do ASP.NET Core, porque terá de alterá-la quando implementar a sua aplicação.

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

# <a name="javatabjava"></a>[Java](#tab/java)

Consulte [a aplicação Web que assina nos utilizadores: Configuração](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) de código para entender como a amostra java obtém o código de autorização. Depois de a aplicação receber o código, o [AuthFilter.java#L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Delegados ao método `AuthHelper.processAuthenticationCodeRedirect` em [AuthHelper.java#L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
1. Chama `getAuthResultByAuthCode`.

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

O método `getAuthResultByAuthCode` é definido em [AuthHelper.java#L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Cria um `ConfidentialClientApplication`MSAL, e depois chama `acquireToken()` com `AuthorizationCodeParameters` criado a partir do código de autorização.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

O fluxo de código de autorização é solicitado como mostrado na [aplicação Web que assina nos utilizadores: Configuração de código](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). O código é então recebido na função `authorized`, que o Flask faz a partir do URL `/getAToken`. Consulte [app.py#L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) para obter todo o contexto deste código:

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

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

O tutorial central ASP.NET usa a injeção de dependência para que você decida a implementação do cache simbólico no ficheiro Startup.cs para a sua aplicação. Microsoft.Identity.Web vem com serializadores de cache de token pré-construídos descritos em serialização de [cache token](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization). Uma possibilidade interessante é escolher ASP.NET [caches de memória distribuídos](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache)core:

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// Then, choose your implementation.

// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache()

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

Para mais detalhes sobre os fornecedores de cache token, consulte também os [tutoriais de aplicações ASP.NET Core Web  Fase](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) de caches simbólicos do tutorial.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

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

# <a name="javatabjava"></a>[Java](#tab/java)

A MSAL Java fornece métodos para serializar e desserializar a cache simbólica. A amostra java trata da serialização da sessão, como mostra o método `getAuthResultBySilentFlow` em [AuthHelper.java#L99-L122:](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122)

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

Os pormenores da classe `SessionManagementHelper` são fornecidos na [amostra MSAL para Java.](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)

# <a name="pythontabpython"></a>[Python](#tab/python)

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
