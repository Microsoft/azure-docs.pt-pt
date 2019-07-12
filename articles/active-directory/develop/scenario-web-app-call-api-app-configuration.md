---
title: Aplicação Web que chamadas de web APIs (a configuração do código) – plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação Web que chamadas de web APIs (configuração de código da aplicação)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c78a951258e3c279f96f44ceac469e4c38cf22c
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785565"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Aplicação Web que chamadas de web APIs - configuração de código

Como visto na [cenário de utilizadores inicia sessão de aplicação Web](scenario-web-app-sign-user-overview.md), uma vez que iniciar sessão de utilizador é delegada para o abrir ID connect (OIDC) middleware, quer em termos de hook de segurança no processo de OIDC. A forma de fazer isso é diferente consoante a arquitetura que utilizou (aqui ASP.NET e ASP.NET Core), mas no final, irá subscrever a eventos de OIDC de middleware. O princípio é que:

- Vai permitir que ASP.NET ou ASP.NET core pedir um código de autorização. Ao fazê-lo neste ASP.NET/ASP.NET core permitirá ao utilizador iniciar sessão e dar consentimento,
- Irá subscrever a receção do código de autorização pela aplicação Web.
- Quando o código de autenticação é recebido, irá utilizar bibliotecas MSAL para resgatar o código e os tokens de acesso resultante e atualizar o arquivo de tokens na cache de token. A partir daí, o cache pode ser utilizado em outras partes do aplicativo para adquirir outros tokens silenciosamente.

## <a name="libraries-supporting-web-app-scenarios"></a>Bibliotecas de suporte a cenários de aplicação Web

As bibliotecas que suportam o fluxo de código de autorização para aplicações Web são:

| Biblioteca MSAL | Descrição |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | As plataformas suportadas são plataformas .NET Framework e .NET Core (não UWP, xamarin. IOS e xamarin. Android como essas plataformas são utilizados para criar aplicativos cliente público) |
| ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Desenvolvimento em curso - em pré-visualização pública |
| ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Desenvolvimento em curso - em pré-visualização pública |

## <a name="aspnet-core-configuration"></a>Configuração do ASP.NET Core

No ASP.NET Core, as coisas acontecem no `Startup.cs` ficheiro. Desejará subscrever o `OnAuthorizationCodeReceived` abrir ID evento connect e desse evento, chame MSAL. Método do NET `AcquireTokenFromAuthorizationCode` que tem o efeito de armazenamento no cache de tokens, o token de acesso para os âmbitos de pedido e um token de atualização que será utilizado para atualizar o token de acesso quando está próximo de expiração ou para obter um token em nome do mesmo utilizador , mas para um recurso diferente.

Os comentários no código abaixo irão ajudá-lo a compreender alguns aspetos complicados de combinando MSAL.NET e ASP.NET Core. Todos os detalhes são fornecidos no [capítulo tutorial, incremental de Web do ASP.NET Core aplicação 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)

```CSharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Response type. We ask ASP.NET to request an Auth Code, and an IDToken
   options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

   // This "offline_access" scope is needed to get a refresh token when users sign in with
   // their Microsoft personal accounts
   // (it's required by MSAL.NET and automatically provided by Azure AD when users
   // sign in with work or school accounts, but not with their Microsoft personal accounts)
   options.Scope.Add(OidcConstants.ScopeOfflineAccess);
   options.Scope.Add("user.read"); // for instance

   // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
   // where it will be usable from Controllers later (through the TokenAcquisition service)
   var handler = options.Events.OnAuthorizationCodeReceived;
   options.Events.OnAuthorizationCodeReceived = async context =>
   {
    // As AcquireTokenByAuthorizationCode is asynchronous we want to tell ASP.NET core
    // that we are handing the code even if it's not done yet, so that it does 
    // not concurrently call the Token endpoint.
    context.HandleCodeRedemption();

    // Call MSAL.NET AcquireTokenByAuthorizationCode
    var application = BuildConfidentialClientApplication(context.HttpContext,
                                                         context.Principal);
    var result = await application.AcquireTokenByAuthorizationCode(scopes.Except(scopesRequestedByMsalNet), context.ProtocolMessage.Code)
                                  .ExecuteAsync();

    // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it
    // and will not send the OAuth 2.0 request in case a further call to
    // AcquireTokenByAuthorizationCodeAsync in the future for incremental consent 
    // (getting a code requesting more scopes)
    // Share the ID Token so that the identity of the user is known in the application (in 
    // HttpContext.User)
    context.HandleCodeRedemption(null, result.IdToken);

    // Call the previous handler if any
    await handler(context);
   };
```

No ASP.NET Core, a criação da aplicação de cliente confidencial utiliza informações que estão no HttpContext. Este HttpContext sabe sobre o URL para a aplicação Web e o utilizador com sessão iniciada (num `ClaimsPrincipal`). Ele também usa a configuração do ASP.NET Core, que tem uma seção de "AzureAD" e que está vinculado ao `_applicationOptions` estrutura de dados. Por fim, o aplicativo precisa manter as caches de token.

```CSharp
/// <summary>
/// Creates an MSAL Confidential client application
/// </summary>
/// <param name="httpContext">HttpContext associated with the OIDC response</param>
/// <param name="claimsPrincipal">Identity for the signed-in user</param>
/// <returns></returns>
private IConfidentialClientApplication BuildConfidentialClientApplication(HttpContext httpContext, ClaimsPrincipal claimsPrincipal)
{
 var request = httpContext.Request;

 // Find the URI of the application)
 string currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, azureAdOptions.CallbackPath ?? string.Empty);

 // Updates the authority from the instance (including national clouds) and the tenant
 string authority = $"{azureAdOptions.Instance}{azureAdOptions.TenantId}/";

 // Instantiates the application based on the application options (including the client secret)
 var app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
               .WithRedirectUri(currentUri)
               .WithAuthority(authority)
               .Build();

 // Initialize token cache providers. In the case of Web applications, there must be one
 // token cache per user (here the key of the token cache is in the claimsPrincipal which
 // contains the identity of the signed-in user)
 if (this.UserTokenCacheProvider != null)
 {
  this.UserTokenCacheProvider.Initialize(app.UserTokenCache, httpContext, claimsPrincipal);
 }
 if (this.AppTokenCacheProvider != null)
 {
  this.AppTokenCacheProvider.Initialize(app.AppTokenCache, httpContext);
 }
 return app;
}
```

`AcquireTokenByAuthorizationCode` realmente redeems o código de autorização pedido pelo ASP.NET e obtém os tokens que são adicionados à cache de token de utilizador MSAL.NET. A partir daí, eles são, em seguida, utilizada, os controladores do ASP.NET Core.

## <a name="aspnet-configuration"></a>Configuração do ASP.NET

A forma como o ASP.NET manipula coisas é semelhante, exceto pelo fato da configuração de OpenIdConnect e a subscrição para o `OnAuthorizationCodeReceived` evento que acontece no `App_Start\Startup.Auth.cs` ficheiro. Encontrará conceitos similares, exceto pelo fato de aqui terá de especificar o RedirectUri no ficheiro de configuração, o que é um pouco menos eficiente:

```CSharp
private void ConfigureAuth(IAppBuilder app)
{
 app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

 app.UseCookieAuthentication(new CookieAuthenticationOptions { });

 app.UseOpenIdConnectAuthentication(
 new OpenIdConnectAuthenticationOptions
 {
  Authority = Globals.Authority,
  ClientId = Globals.ClientId,
  RedirectUri = Globals.RedirectUri,
  PostLogoutRedirectUri = Globals.RedirectUri,
  Scope = Globals.BasicSignInScopes, // a basic set of permissions for user sign in & profile access
  TokenValidationParameters = new TokenValidationParameters
  {
   NameClaimType = "name",
  },
  Notifications = new OpenIdConnectAuthenticationNotifications()
  {
   AuthorizationCodeReceived = OnAuthorizationCodeReceived,
  }
 });
}
```

```CSharp
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
{
 // Upon successful sign in, get & cache a token using MSAL
 string userId = context.AuthenticationTicket.Identity.FindFirst(ClaimTypes.NameIdentifier).Value;
 IConfidentialClientApplication clientapp;
 clientApp = ConfidentialClientApplicationBuilder.Create(Globals.ClientId)
                  .WithClientSecret(Globals.ClientSecret)
                  .WithRedirectUri(Globals.RedirectUri)
                  .WithAuthority(new Uri(Globals.Authority))
                  .Build();

  EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);

 AuthenticationResult result = await clientapp.AcquireTokenByAuthorizationCode(scopes, context.Code)
                                              .ExecuteAsync();
}
```

### <a name="msalnet-token-cache-for-a-aspnet-core-web-app"></a>Cache de Token de MSAL.NET para uma aplicação Web ASP.NET (núcleos)

Em aplicações web (ou web APIs como na verdade), a implementação de cache de token é diferente do que as implementações de cache de tokens de aplicativos de Desktop (que são frequentemente [baseado em ficheiros](scenario-desktop-acquire-token.md#file-based-token-cache). Ele pode usar a sessão de ASP.NET/ASP.NET núcleos, ou uma cache de Redis, ou uma base de dados ou até mesmo armazenamento de Blobs do Azure. No código do trecho de código acima isso é o objeto do `EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);` chamada de método, que vincula um serviço de cache. Os detalhes sobre o que acontece aqui está além do escopo deste guia de cenário, mas são fornecidas ligações abaixo.

> [!IMPORTANT]
> Uma coisa muito importante a observar é que para aplicações web e web APIs, deve haver uma cache de tokens por utilizador (por conta). Precisa serializar o cache de token para cada conta.

Exemplos de como utilizar caches de token para aplicações Web e web APIs estão disponíveis no [tutorial de aplicação ASP.NET Core Web](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) na fase [Cache de tokens de 2 de 2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). Para as implementações têm uma olhada na seguinte pasta [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) no [microsoft-authentication-extensões-para-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) biblioteca (no [ Microsoft.Identity.Client.Extensions.Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) pasta.

## <a name="next-steps"></a>Passos Seguintes

Neste momento, quando o utilizador inicia sessão um token é armazenado na cache de token. Vamos ver como ela, em seguida, é usada em outras partes da aplicação Web.

> [!div class="nextstepaction"]
> [Inicie sessão na aplicação Web](scenario-web-app-call-api-sign-in.md)
