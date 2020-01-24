---
title: Configure aplicação web que chama APIs web - plataforma de identidade microsoft / Azure
description: Saiba como configurar o código de uma aplicação Web que chama APIs web
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
ms.openlocfilehash: 7bf5256390961f4d469aa70be4b0b0c1069db4cf
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701693"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Aplicação web que chama APIs web - configuração de código

Como se pode ver no cenário de [sinais de entrada da aplicação web](scenario-web-app-sign-user-overview.md), a aplicação web utiliza o fluxo de código de [autorização](v2-oauth2-auth-code-flow.md) OAuth2.0 para assinar no utilizador. Este fluxo é em duas partes:

1. Peça um código de autorização. Esta peça delega na plataforma de identidade da Microsoft um diálogo privado com o utilizador. O utilizador insere-se e consente na utilização de APIs web. Quando este diálogo privado termina com sucesso, a aplicação recebe um código de autorização no seu URI redirecionado.
1. Solicite um sinal de acesso à API redimindo o código de autorização.

[O cenário de sinais de aplicação web](scenario-web-app-sign-user-overview.md) dos utilizadores estava apenas a fazer a primeira perna. Saiba aqui, como modificar a sua API web que inscreve utilizadores, de modo que agora chama apis web.

## <a name="libraries-supporting-web-app-scenarios"></a>Bibliotecas que suportam cenários de Web App

As bibliotecas que suportam o fluxo de código de autorização para aplicações web são:

| Biblioteca MSAL | Descrição |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | As plataformas suportadas são .NET Framework e .NET Core plataformas (não UWP, Xamarin.iOS e Xamarin.Android, uma vez que essas plataformas são usadas para construir aplicações de clientes públicos) |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Suporte para aplicativos Web do Python |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Suporte para aplicativos Web Java |

Selecione o separador correspondente à plataforma em que está interessado:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Dado que deixar entrar um utilizador é delegado ao middleware de ligação Open ID (OIDC), pretende ligar-se no processo OIDC. A forma de o fazer é diferente dependendo do quadro que utiliza.
No caso de ASP.NET Core, você subscreverá eventos oIDC de middleware. O princípio é que:

- Deixará ASP.NET núcleo solicitar um código de autorização, através do open ID connect middleware. Ao fazê-lo ASP.NET/ASP.NET núcleo permitirá ao utilizador iniciar sessão e consentir,
- Subscreverá a receção do código de autorização pela aplicação web. Esta subscrição é C# feita através de um delegado.
- Quando o código auth for recebido, você usará bibliotecas MSAL para resgatar o código e as fichas de acesso resultantes e tokens de atualização são armazenados na cache token. A partir daí, a cache pode ser utilizada noutras partes da aplicação, por exemplo nos controladores, para adquirir outras fichas silenciosamente.

Os fragmentos de código neste artigo e no seguinte são extraídos do tutorial incremental da [aplicação ASP.NET Core Web, capítulo 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph). Talvez você queira consultar este tutorial para obter detalhes completos de implementação.

> [!NOTE]
> Para compreender completamente os códigos abaixo, você precisa estar familiarizado com [ASP.NET fundamentos do Núcleo,](https://docs.microsoft.com/aspnet/core/fundamentals)e em particular a injeção e [opções](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options) de dependência e [opções](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Dado que deixar entrar um utilizador é delegado ao middleware de ligação Open ID (OIDC), pretende ligar-se no processo OIDC. A forma de o fazer é diferente dependendo do quadro que utiliza.
No caso de ASP.NET, subscreverá os eventos da Middleware OIDC. O princípio é que:

- Deixará ASP.NET núcleo solicitar um código de autorização, através do open ID connect middleware. Ao fazê-lo ASP.NET/ASP.NET núcleo permitirá ao utilizador iniciar sessão e consentir,
- Subscreverá a receção do código de autorização pela aplicação web. Este é C# um delegado.
- Quando o código auth for recebido, você usará bibliotecas MSAL para resgatar o código. As fichas de acesso resultantes e as fichas de atualização são, então, armazenadas na cache simbólica. A partir daí, a cache pode ser utilizada noutras partes da aplicação, por exemplo nos controladores, para adquirir outras fichas silenciosamente.

Os fragmentos de código neste artigo e no seguinte são extraídos da amostra da [aplicação Web ASP.NET](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Talvez você queira consultar este exemplo para obter detalhes completos de implementação.

# <a name="javatabjava"></a>[Java](#tab/java)

Os códigos deste artigo e os seguintes são extraídos da [aplicação web Java, chamando](https://github.com/Azure-Samples/ms-identity-java-webapp) a amostra da aplicação web MSAL Java do microsoft graph.
A amostra permite que a MSAL Java produza o URL do código de autorização e manuseie a navegação para a plataforma de identidade da Microsoft autoriza o ponto final. Também é possível usar a segurança sprint para assinar no utilizador. Talvez você queira consultar este exemplo para obter detalhes completos de implementação.

# <a name="pythontabpython"></a>[Python](#tab/python)

Os fragmentos de código neste artigo e no seguinte são extraídos da [aplicação web Python chamada Microsoft graph](https://github.com/Azure-Samples/ms-identity-python-webapp) MSAL. Amostra de aplicativo web Python.
A amostra permite atualmente a MSAL. Python produz o URL do código de autorização e trata a navegação para a plataforma de identidade da Microsoft autoriza ponto final. Talvez você queira consultar este exemplo para obter detalhes completos de implementação.

---

## <a name="code-that-redeems-the-authorization-code"></a>Código que resgata o código de autorização

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

Em ASP.NET Core, o princípio é que no ficheiro `Startup.cs`. Você vai querer subscrever o `OnAuthorizationCodeReceived` evento de ligação de ID aberto, e deste evento, ligue para mSAL. O método da NET `AcquireTokenFromAuthorizationCode`, que tem o efeito de armazenar na cache simbólica, o símbolo de acesso para o `scopes`solicitado , e um token refrescante que será usado para refrescar o token de acesso quando estiver perto de expirar, ou para obter um símbolo em nome do mesmo utilizador, mas para um recurso diferente.

Na prática, o [tutoriatutorial da aplicação ASP.NET Core Web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) tenta fornecer-lhe um código reutilizável para as suas aplicações web.

Aqui está o código [Startup.cs#L40-L42,](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42) com a chamada para o método `AddMicrosoftIdentityPlatformAuthentication` que adiciona autenticação à aplicação web, e `AddMsal` que adiciona a capacidade de chamar APIs web. O apelo para `AddInMemoryTokenCaches` é sobre escolher uma implementação de cache simbólica entre as que são possíveis:

```csharp
public class Startup
{
  // Code not show here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not show here
}
```

`Constants.ScopeUserRead` é definido em [Constants.cs#L5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5)

```csharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

Já estudou o conteúdo de `AddMicrosoftIdentityPlatformAuthentication` na [aplicação Web que inscreve minhá-lo - configuração de código](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code)

### <a name="the-addmsal-method"></a>O método AddMsal

O código para `AddMsal` está localizado em [Microsoft.Identity.Web/WebAppServiceCollections.cs#L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159).

```csharp

/// <summary>
/// Extensions for IServiceCollection for startup initialization.
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the Web App or Web API
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign-in with their Microsoft personal accounts
          // (it's required by MSAL.NET and automatically provided when users sign-in with work or school accounts)
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

          // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
          // where it will be usable from Controllers later (through the TokenAcquisition service)
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

- a aplicação ASP.NET Core Web solicita um IDToken para o utilizador e um código de autenticação (`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`)
- o âmbito `offline_access` é adicionado. É necessário que o utilizador consinta em deixar a aplicação obter um token refrescante.
- a aplicação subscreve o evento oIDC `OnAuthorizationCodeReceived`, e resgata a chamada utilizando MSAL.NET, que está aqui encapsulada num componente reutilizável que implementa `ITokenAcquisition`.

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>O método TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync

O método `TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` está localizado em [Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145). Garante que:

- ASP.NET não tenta resgatar o código de autenticação em paralelo com MSAL.NET (`context.HandleCodeRedemption();`)
- As reclamações no IDToken estão disponíveis para que a MSAL calcule uma chave de cache simbólica para a conta do utilizador
- a aplicação MSAL.NET é instantaneamente instantânea se necessário
- o código é redimido pela aplicação MSAL.NET
- O novo símbolo de identificação é partilhado com ASP.NET Core (durante a chamada para `context.HandleCodeRedemption(null, result.IdToken);`). O sinal de acesso não é partilhado com ASP.NET Core. Permanece na cache token MSAL.NET associada ao utilizador, onde está pronto para ser utilizado em controladores core ASP.NET.

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
      // As AcquireTokenByAuthorizationCodeAsync is asynchronous we want to tell ASP.NET core that we are handing the code
      // even if it's not done yet, so that it does not concurrently call the Token endpoint. (otherwise there will be a
      // race condition ending-up in an error from Azure AD telling "code already redeemed")
      context.HandleCodeRedemption();

      // The cache will need the claims from the ID token.
      // If they are not yet in the HttpContext.User's claims, so adding them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it and will not send the OAuth 2.0 request in
      // case a further call to AcquireTokenByAuthorizationCodeAsync in the future is required for incremental consent (getting a code requesting more scopes)
      // Share the ID Token though
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

Em ASP.NET Core, a construção da aplicação confidencial do cliente utiliza informações que se encontra no HttpContext. Acedido através da propriedade `CurrentHttpContext`, o HttpContext, associado ao pedido, sabe sobre o URL para a Web App, e o utilizador inscrito (em `ClaimsPrincipal`). O `BuildConfidentialClientApplication` também utiliza a configuração ASP.NET Core, que tem uma secção "AzureAD", e que está ligada tanto a:

- a estrutura de dados `_applicationOptions` do tipo [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet)
- a `azureAdOptions` instância de opções do tipo [AzureAdDefine-se](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs) em ASP.NET Core `Authentication.AzureAD.UI`. Finalmente, a aplicação precisa de manter caches simbólicos. Saberá mais sobre isso na próxima secção.

O código para o método `GetOrBuildConfidentialClientApplication()` está em [Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). Utiliza membros que foram injetados por injeção de dependência (passado no construtor de TokenAcquisition em [Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59))

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL Confidential client application if needed
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

Resumindo, `AcquireTokenByAuthorizationCode` realmente resgata o código de autorização solicitado pela ASP.NET e recebe as fichas que são adicionadas à cache simbólica do utilizador MSAL.NET. A partir daí, são usados, nos controladores ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

A forma como ASP.NET lida com as coisas é semelhante à ASP.NET Core, exceto que a configuração do OpenIdConnect e a subscrição do evento `OnAuthorizationCodeReceived` acontece no ficheiro [App_Start\Startup.Auth.cs.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) Você encontrará conceitos semelhantes ao ASP.NET Core, exceto que em ASP.NET você precisará especificar o RedirectUri no [Web.config#L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Esta configuração é um pouco menos robusta do que a que é feita no ASP.NET Core, pois terá de alterá-la quando implementar a sua aplicação.

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the querystring (&code=<code>).
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
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // a basic set of permissions for user sign in & profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application you would use IssuerValidator for additional checks, like making sure the user's organization has signed up for your app.
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
      // Upon successful sign in, get the access token & cache it using MSAL
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

Veja na [aplicação Web que insine os utilizadores - configuração](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) de código para entender como a amostra java obtém o código de autorização. Uma vez recebido pela aplicação, o [AuthFilter.java#L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56) delega ao método `AuthHelper.processAuthenticationCodeRedirect` em [AuthHelper.java#L67-L97,](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97)e depois chama `getAuthResultByAuthCode`:

```Java
class AuthHelper {
  // code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// code omitted
  }
}
```

O método `getAuthResultByAuthCode` é definido em [AuthHelper.java#L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Cria uma `ConfidentialClientApplication` MSAL e chama `acquireToken()` com `AuthorizationCodeParameters` criado a partir do código de autorização.

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

Uma vez solicitado o fluxo de código de autorização como visto na [aplicação Web que insine os utilizadores - configuração](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code)de código - o código de autorização é recebido na função `authorized` que o Flask faz a partir do URL /getAToken. Ver [app.py#L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44)

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here
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
Usar afirmações de clientes é um cenário avançado, detalhado nas [afirmações](msal-net-client-assertions.md) do Cliente

## <a name="token-cache"></a>Cache de token

> [!IMPORTANT]
> Nas aplicações web (ou APIs web, na verdade), a implementação de cache token é diferente das implementações de cache de aplicações do Desktop (que muitas vezes são [baseadas em ficheiros).](scenario-desktop-acquire-token.md#file-based-token-cache)
> É importante, por razões de segurança e desempenho, garantir que, para aplicações web e APIs web, deve haver uma cache simbólica por utilizador (por conta). Tens de serializar o cache simbólico para cada conta.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

O tutorial central ASP.NET usa a injeção de dependência para que você decida a implementação do cache simbólico no ficheiro Startup.cs para a sua aplicação. Microsoft.Identity.Web vem com uma série de serializadores de cache de token pré-construídos descritos na serialização de [cache token](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization). Uma possibilidade interessante é escolher ASP.NET [caches de memória distribuídos](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache)core:

```csharp
// or use a distributed Token Cache by adding
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// and then choose your implementation

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache()

// Or a Redis cache
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Para mais detalhes sobre os fornecedores de cache token, consulte também os [tutoriais de aplicações ASP.NET Core Web  ](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)Fase de caches simbólicos do tutorial

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Nas aplicações web (ou APIs web, na verdade), a implementação de cache simbólica é diferente das implementações de cache de aplicações do Desktop (que muitas vezes são [baseadas em ficheiros](scenario-desktop-acquire-token.md#file-based-token-cache). Pode utilizar a sessão ASP.NET ou a memória do servidor. Veja, por exemplo, como a implementação do cache é anexada após a criação da aplicação MSAL.NET em [MsalAppBuilder.cs#L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51)

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

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="javatabjava"></a>[Java](#tab/java)

A MSAL Java fornece métodos para serializar e desserializar a cache simbólica. A amostra java trata da serialização da sessão, como ilustrado no método `getAuthResultBySilentFlow` em [AuthHelper.java#L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122)

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

  //update session with latest token cache
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

O detalhe da classe `SessionManagementHelper` é fornecido em[](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)

# <a name="pythontabpython"></a>[Python](#tab/python)

Na amostra Python, em cache por conta é assegurado recriando um pedido de cliente confidencial para cada pedido e serializando-o na cache da sessão do Flask:

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

Neste ponto, quando o utilizador assina uma ficha é guardado na cache token. Vamos ver como é usado noutras partes da aplicação web.

> [!div class="nextstepaction"]
> [Inscreva-se na Web App](scenario-web-app-call-api-sign-in.md)
