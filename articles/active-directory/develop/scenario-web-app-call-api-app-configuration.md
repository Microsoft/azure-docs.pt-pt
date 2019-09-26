---
title: Aplicativo Web que chama APIs da Web (configuração de código) – plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo Web que chama APIs da Web (configuração de código do aplicativo)
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1131cba204b7b7af33cc0441ee455b6e333aba20
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310086"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Aplicativo Web que chama as APIs da Web-configuração de código

Como visto no [cenário de usuários de entrada do aplicativo Web](scenario-web-app-sign-user-overview.md), o aplicativo Web usa o fluxo de [código de autorização](v2-oauth2-auth-code-flow.md) do OAuth 2.0 para conectar o usuário. Esse fluxo está em duas partes:

1. Solicite um código de autorização. Esta parte Delega à plataforma Microsoft Identity uma caixa de diálogo particular com o usuário. O usuário entra e consente com o uso de APIs da Web. Quando essa caixa de diálogo particular termina com êxito, o aplicativo recebe um código de autorização em seu URI de redirecionamento.
1. Solicite um token de acesso para a API, resgatando o código de autorização.

O [cenário de usuários de entrada do aplicativo Web](scenario-web-app-sign-user-overview.md) estava apenas fazendo o primeiro trecho. Saiba mais aqui como modificar sua API Web que faz logon de usuários, para que agora ele chame APIS da Web.

## <a name="libraries-supporting-web-app-scenarios"></a>Bibliotecas que dão suporte a cenários de aplicativos Web

As bibliotecas que dão suporte ao fluxo de código de autorização para aplicativos Web são:

| Biblioteca MSAL | Descrição |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | As plataformas com suporte são .NET Framework e plataformas .NET Core (não UWP, Xamarin. iOS e Xamarin. Android, pois essas plataformas são usadas para criar aplicativos cliente públicos) |
| ![MSAL.Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Desenvolvimento em andamento-em visualização pública |
| ![MSAL.Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Desenvolvimento em andamento-em visualização pública |

Selecione a guia correspondente à plataforma em que você está interessado:

# <a name="aspnet-coretabaspnetcore"></a>[Núcleo do ASP.NET](#tab/aspnetcore)

Considerando que permitir que uma entrada de usuário seja delegada ao middleware do Open ID Connect (OIDC), você deseja conectar-se no processo OIDC. A maneira de fazer isso é diferente dependendo da estrutura usada.
No caso de ASP.NET Core, você assinará os eventos de middleware OIDC. O princípio é que:

- Você permitirá que o ASP.NET Core solicite um código de autorização, por meio do middleware Open ID Connect. Ao fazer isso, o ASP.NET/ASP.NET Core permitirá que o usuário entre e consentisse,
- Você assinará a recepção do código de autorização pelo aplicativo Web. Essa assinatura é feita por meio C# de um delegado.
- Quando o código de autenticação for recebido, você usará bibliotecas MSAL para resgatar o código e os tokens de acesso resultantes e os tokens de atualização serão armazenados no cache de token. A partir daí, o cache pode ser usado em outras partes do aplicativo, por exemplo, em controladores, para adquirir outros tokens silenciosamente.

Os trechos de código neste artigo e os seguintes são extraídos do [tutorial de ASP.NET Core incremental do aplicativo Web, capítulo 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph). Talvez você queira consultar este tutorial para obter detalhes completos de implementação.

> [!NOTE]
> Para entender totalmente os trechos de código a seguir, você precisa estar familiarizado com os [conceitos básicos de ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals)e, em particular, [Opções](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options) e [injeção de dependência](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Considerando que permitir que uma entrada de usuário seja delegada ao middleware do Open ID Connect (OIDC), você deseja conectar-se no processo OIDC. A maneira de fazer isso é diferente dependendo da estrutura usada.
No caso do ASP.NET, você assinará os eventos do middleware OIDC. O princípio é que:

- Você permitirá que o ASP.NET Core solicite um código de autorização, por meio do middleware Open ID Connect. Ao fazer isso, o ASP.NET/ASP.NET Core permitirá que o usuário entre e consentisse,
- Você assinará a recepção do código de autorização pelo aplicativo Web. Este é um C# delegado.
- Quando o código de autenticação for recebido, você usará bibliotecas MSAL para resgatar o código. Os tokens de acesso e tokens de atualização resultantes são, então, armazenados no cache de token. A partir daí, o cache pode ser usado em outras partes do aplicativo, por exemplo, em controladores, para adquirir outros tokens silenciosamente.

Os trechos de código neste artigo e os seguintes são extraídos do [exemplo de aplicativo Web ASP.net](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect). Talvez você queira consultar este exemplo para obter detalhes completos de implementação.

# <a name="javatabjava"></a>[Java](#tab/java)

Os trechos de código neste artigo e os seguintes são extraídos do exemplo [aplicativo Web Java chamando o aplicativo Web do Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp) msal4j.
O exemplo atualmente permite que o msal4j produza a URL do código de autorização e manipule a navegação para o ponto de extremidade autorizado da plataforma de identidade da Microsoft. Também é possível usar a segurança do Sprint para conectar o usuário. Talvez você queira consultar este exemplo para obter detalhes completos de implementação.

# <a name="pythontabpython"></a>[Python](#tab/python)

Os trechos de código neste artigo e os seguintes são extraídos do [aplicativo Web Python chamando o Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp) MSAL. Exemplo de aplicativo Web Python.
O exemplo atualmente permite MSAL. O Python produz a URL do código de autorização e manipula a navegação para o ponto de extremidade autorizado da plataforma de identidade da Microsoft. Talvez você queira consultar este exemplo para obter detalhes completos de implementação.

---

## <a name="code-that-redeems-the-authorization-code"></a>Código que resgata o código de autorização

# <a name="aspnet-coretabaspnetcore"></a>[Núcleo do ASP.NET](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

Em ASP.NET Core, o princípio é que no `Startup.cs` arquivo. Você desejará assinar o `OnAuthorizationCodeReceived` evento Open ID Connect e, a partir desse evento, chame MSAL. O método `AcquireTokenFromAuthorizationCode`da rede, que tem o efeito de armazenar no cache do token, o token de acesso para `scopes`o solicitado e um token de atualização que será usado para atualizar o token de acesso quando ele estiver próximo à expiração ou obter um token em nome do mesmo usuário , mas para um recurso diferente.

Na prática, o [tutorial ASP.NET Core aplicativo Web](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) tenta fornecer código reutilizável para seus aplicativos Web.

Aqui está o código [Startup. cs # L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42) , que apresenta a chamada para `AddMicrosoftIdentityPlatformAuthentication` o método que adiciona a autenticação ao aplicativo Web e `AddMsal` que adiciona a capacidade de chamar APIs da Web. A chamada a `AddInMemoryTokenCaches` é sobre a escolha de uma implementação de cache de token entre as que são possíveis:

```CSharp
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

`Constants.ScopeUserRead`é definido em [Constants. cs # L5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5)

```CSharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

Você já estudou o conteúdo de `AddMicrosoftIdentityPlatformAuthentication` no [aplicativo Web que entra com usuários que se](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code) conectam à configuração de código

### <a name="the-addmsal-method"></a>O método AddMsal

O código para `AddMsal` está localizado em [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159).

```CSharp

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

O `AddMsal` método garante que:

- o aplicativo Web ASP.NET Core solicita um token para o usuário e um código de autenticação (`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`)
- o `offline_access` escopo é adicionado. Ele é necessário para que o usuário consentisse que o aplicativo obtenha um token de atualização.
- o aplicativo assina o evento OIDC `OnAuthorizationCodeReceived` e resgata a chamada usando MSAL.net, que está aqui encapsulado em um componente reutilizável que está implementando. `ITokenAcquisition`

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>O método TokenAcquisition. AddAccountToCacheFromAuthorizationCodeAsync

O `TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` método está localizado em [Microsoft. Identity. Web/TokenAcquisition. cs # L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145). Ele garante que:

- ASP.NET não tenta resgatar o código de autenticação em paralelo ao MSAL.NET (`context.HandleCodeRedemption();`)
- As declarações no token estão disponíveis para MSAL para computar uma chave de cache de token para a conta do usuário
- se necessário, o aplicativo MSAL.NET é instanciado
- o código é resgatado pelo aplicativo MSAL.NET
- O novo token de ID é compartilhado com ASP.NET Core (durante a chamada `context.HandleCodeRedemption(null, result.IdToken);`para). O token de acesso não é compartilhado com ASP.NET Core. Ele permanece no cache de token MSAL.NET associado ao usuário, onde está pronto para ser usado em controladores de ASP.NET Core.

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

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>O método TokenAcquisition. BuildConfidentialClientApplication

No ASP.NET Core, a criação do aplicativo cliente confidencial usa informações contidas no HttpContext. Acessado `CurrentHttpContext` por meio da propriedade, o HttpContext, associado à solicitação, conhece a URL do aplicativo Web e o usuário conectado (em um `ClaimsPrincipal`). O `BuildConfidentialClientApplication` também usa a configuração de ASP.NET Core, que tem uma seção "AzureAD" e que está associada a:

- a `_applicationOptions` estrutura de dados do tipo [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet)
- a `azureAdOptions` instância do tipo [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs) definida em ASP.NET Core `Authentication.AzureAD.UI`. Por fim, o aplicativo precisa manter os caches de token. Você aprenderá mais sobre isso na próxima seção.

O código para o `GetOrBuildConfidentialClientApplication()` método está em [Microsoft. Identity. Web/TokenAcquisition. cs # L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). Ele usa Membros que foram injetados por injeção de dependência (passado no construtor de TokenAcquisition em [Microsoft. Identity. Web/TokenAcquisition. cs # L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59))

```CSharp
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

Para somar, `AcquireTokenByAuthorizationCode` realmente reconsidera o código de autorização solicitado por ASP.net e obtém os tokens que são adicionados ao cache de tokens de usuário MSAL.net. A partir daí, eles são usados, nos controladores de ASP.NET Core.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

A maneira como o ASP.net lida com coisas é semelhante ao ASP.NET Core, exceto pelo fato de que a configuração de `OnAuthorizationCodeReceived` OpenIdConnect e a assinatura para o evento ocorre no arquivo [App_Start\Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) . Você encontrará conceitos semelhantes em ASP.NET Core, exceto que, em ASP.NET, precisará especificar o RedirectUri no [Web. config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Essa configuração é um pouco menos robusta do que a feita no ASP.NET Core, pois você precisará alterá-la quando implantar seu aplicativo.

```CSharp
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

Consulte no [aplicativo Web que entra em usuários-configuração de código](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) para entender como o exemplo de Java Obtém o código de autorização. Uma vez recebido pelo aplicativo, o [AuthFilter. java # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56) delega para o `AuthHelper.processAuthenticationCodeRedirect` método em [AuthHelper. java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97)e, em seguida `getAuthResultByAuthCode`, chama:

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

O `getAuthResultByAuthCode` método é definido em [AuthHelper. java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Ele cria um MSAL `ConfidentialClientApplication` e chama `acquireToken()` com `AuthorizationCodeParameters` criado a partir do código de autorização.

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

Depois que o fluxo do código de autorização é solicitado como visto no [aplicativo Web que entra em usuários-configuração de código](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code), o código de autorização `authorized` é recebido na função que Flask rotas da URL/getAToken. Consulte [app. py # L30-l44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44)

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

Em vez de um segredo do cliente, o aplicativo cliente confidencial também pode provar sua identidade usando um certificado de cliente ou uma asserção de cliente.
Usar declarações de cliente é um cenário avançado, detalhado em [asserções de cliente](msal-net-client-assertions.md)

## <a name="token-cache"></a>Cache de token

> [!IMPORTANT]
> Em aplicativos Web (ou APIs Web como uma questão de fato), a implementação de cache de token é diferente das implementações de cache de token de aplicativos de área de trabalho (que geralmente são [baseadas em arquivo](scenario-desktop-acquire-token.md#file-based-token-cache)).
> É importante, por motivos de segurança e desempenho, garantir que, para aplicativos Web e APIs Web, deve haver um cache de token por usuário (por conta). Você precisa serializar o cache de token para cada conta.

# <a name="aspnet-coretabaspnetcore"></a>[Núcleo do ASP.NET](#tab/aspnetcore)

O tutorial do ASP.NET Core usa injeção de dependência para permitir que você decida a implementação do cache de token no arquivo Startup.cs para seu aplicativo. Microsoft. Identity. Web vem com vários serializadores de cache de token pré-criados descritos em serialização de [cache de token](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization). Uma possibilidade interessante é escolher ASP.NET Core [caches de memória distribuída](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache):

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

Para obter detalhes sobre os provedores de cache de token, consulte também os [tutoriais do aplicativo Web ASP.NET Core | Fase de caches de token](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) do tutorial

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Em aplicativos Web (ou APIs Web como uma questão de fato), a implementação de cache de token é diferente das implementações de cache de token de aplicativos de área de trabalho (que geralmente são [baseadas em arquivo](scenario-desktop-acquire-token.md#file-based-token-cache). Ele pode usar a sessão ASP.NET ou a memória do servidor. Consulte por exemplo, como a implementação do cache é vinculada após a criação do aplicativo MSAL.NET em [MsalAppBuilder. cs # L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51)

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

msal4j fornece métodos para serializar e desserializar o cache de token. O exemplo de Java manipula a serialização da sessão, conforme ilustrado no `getAuthResultBySilentFlow` método em [AuthHelper. java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122)

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
          Collections.singleton("User.ReadBasic.All"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  //update session with latest token cache
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

O detalhe da `SessionManagementHelper` classe é fornecido em[](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)

# <a name="pythontabpython"></a>[Python](#tab/python)

No exemplo do Python, no cache por conta, é garantido recriando um aplicativo cliente confidencial para cada solicitação e serializando-o no cache da sessão Flask:

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

Neste ponto, quando o usuário entra em um token, ele é armazenado no cache de token. Vamos ver como ele é usado em outras partes do aplicativo Web.

> [!div class="nextstepaction"]
> [Entrar no aplicativo Web](scenario-web-app-call-api-sign-in.md)
