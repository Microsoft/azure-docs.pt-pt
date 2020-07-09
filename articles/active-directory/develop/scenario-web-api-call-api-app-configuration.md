---
title: Configure uma API web que chama APIs web Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma API web que chama APIs web (configuração de código da aplicação)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 38e319efb100d326d55f6f821e7c903306a7c7d0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80991012"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Uma API web que chama APIs web: configuração de código

Depois de ter registado a sua API web, pode configurar o código da aplicação.

O código que utiliza para configurar a sua API web para que chame APIs web a jusante baseia-se no código que é usado para proteger uma API web. Para obter mais informações, consulte [API web protegida: configuração de aplicações](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="code-subscribed-to-ontokenvalidated"></a>Código subscrito no OnTokenValidated

Além da configuração de código para quaisquer APIs web protegidas, precisa subscrever a validação do sinal do portador que recebe quando a sua API é chamada:

```csharp
/// <summary>
/// Protects the web API with the Microsoft identity platform, or Azure Active Directory (Azure AD) developer platform
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">The service collection to which to add authentication</param>
/// <param name="configuration">Configuration</param>
/// <returns></returns>
public static IServiceCollection AddProtectedApiCallsWebApis(this IServiceCollection services,
                                                             IConfiguration configuration,
                                                             IEnumerable<string> scopes)
{
    services.AddTokenAcquisition();
    services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        // When an access token for our own web API is validated, we add it
        // to the MSAL.NET cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache and handles the incremental consent
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>Em nome do fluxo

O método AddAccountToCacheFromJwt() precisa de:

- Instantiate uma aplicação confidencial do cliente da Microsoft Authentication Library (MSAL).
- Chame o `AcquireTokenOnBehalf` método. Esta chamada troca o token portador que foi adquirido pelo cliente para a API web contra um símbolo ao portador para o mesmo utilizador, mas tem a API a chamar uma API a jusante.

### <a name="instantiate-a-confidential-client-application"></a>Instantiizar uma aplicação confidencial do cliente

Este fluxo está disponível apenas no fluxo de clientes confidenciais, de modo que a API web protegida forneça credenciais de cliente (segredo de cliente ou certificado) à [classe ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) através do `WithClientSecret` ou `WithCertificate` método.

![Lista de métodos de aplicação de IConfidentialClientApplication](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```csharp
IConfidentialClientApplication app;

#if !VariationWithCertificateCredentials
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .Build();
#else
// Building the client credentials from a certificate
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .Build();
#endif
```

Finalmente, em vez de provar a sua identidade através de um segredo de cliente ou de um certificado, os pedidos confidenciais de cliente podem provar a sua identidade utilizando afirmações do cliente.
Para obter mais informações sobre este cenário avançado, consulte [as afirmações confidenciais dos clientes.](msal-net-client-assertions.md)

### <a name="how-to-call-on-behalf-of"></a>Como chamar em nome

Faz a chamada On-Behalf-Of (OBO) ligando para o [método AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) na `IConfidentialClientApplication` interface.

A `UserAssertion` aula é construída a partir do símbolo do portador que é recebido pela API web dos seus próprios clientes. Existem [dois construtores:](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet)
* Um que leva um símbolo de portador da Web Token JSON (JWT)
* Um que toma qualquer tipo de afirmação do utilizador, outro tipo de símbolo de segurança, cujo tipo é então especificado em um parâmetro adicional chamado`assertionType`

![Propriedades e métodos de Utilização do Utilizador](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

Na prática, o fluxo OBO é frequentemente usado para adquirir um símbolo para uma API a jusante e armazená-lo na cache de token do utilizador MSAL.NET. Faça isto para que outras partes da web API possam mais tarde recorrer às [substituições](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) de ``AcquireTokenOnSilent`` chamadas de APIs a jusante. Esta chamada tem o efeito de refrescar as fichas, se necessário.

```csharp
private void AddAccountToCacheFromJwt(IEnumerable<string> scopes, JwtSecurityToken jwtToken, ClaimsPrincipal principal, HttpContext httpContext)
{
    try
    {
        UserAssertion userAssertion;
        IEnumerable<string> requestedScopes;
        if (jwtToken != null)
        {
            userAssertion = new UserAssertion(jwtToken.RawData, "urn:ietf:params:oauth:grant-type:jwt-bearer");
            requestedScopes = scopes ?? jwtToken.Audiences.Select(a => $"{a}/.default");
        }
        else
        {
            throw new ArgumentOutOfRangeException("tokenValidationContext.SecurityToken should be a JWT Token");
        }

        // Create the application
        var application = BuildConfidentialClientApplication(httpContext, principal);

        // .Result to make sure that the cache is filled in before the controller tries to get access tokens
        var result = application.AcquireTokenOnBehalfOf(requestedScopes.Except(scopesRequestedByMsalNet),
                                                        userAssertion)
                                .ExecuteAsync()
                                .GetAwaiter().GetResult();
     }
     catch (MsalException ex)
     {
         Debug.WriteLine(ex.Message);
         throw;
     }
}
```
# <a name="java"></a>[Java](#tab/java)

O fluxo On-behalf-of (OBO) é usado para obter um token para chamar a API web a jusante. Neste fluxo, a sua API web recebe um token ao portador com permissões delegadas pelo utilizador a partir da aplicação do cliente e, em seguida, troca este token para outro token de acesso para ligar para a API web a jusante.

O código abaixo utiliza o quadro de Segurança da primavera `SecurityContextHolder` na API web para obter o token do portador validado. Em seguida, utiliza a biblioteca MSAL Java para obter um símbolo para a API a jusante utilizando a `acquireToken` chamada com `OnBehalfOfParameters` . MSAL caches o token para que as chamadas subsequentes para a API possam usar `acquireTokenSilently` para obter o token em cache.

```Java
@Component
class MsalAuthHelper {

    @Value("${security.oauth2.client.authority}")
    private String authority;

    @Value("${security.oauth2.client.client-id}")
    private String clientId;

    @Value("${security.oauth2.client.client-secret}")
    private String secret;

    @Autowired
    CacheManager cacheManager;

    private String getAuthToken(){
        String res = null;
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        if(authentication != null){
            res = ((OAuth2AuthenticationDetails) authentication.getDetails()).getTokenValue();
        }
        return res;
    }

    String getOboToken(String scope) throws MalformedURLException {
        String authToken = getAuthToken();

        ConfidentialClientApplication application =
                ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(secret))
                        .authority(authority).build();

        String cacheKey = Hashing.sha256()
                .hashString(authToken, StandardCharsets.UTF_8).toString();

        String cachedTokens = cacheManager.getCache("tokens").get(cacheKey, String.class);
        if(cachedTokens != null){
            application.tokenCache().deserialize(cachedTokens);
        }

        IAuthenticationResult auth;
        SilentParameters silentParameters =
                SilentParameters.builder(Collections.singleton(scope))
                        .build();
        auth = application.acquireTokenSilently(silentParameters).join();

        if (auth == null){
            OnBehalfOfParameters parameters =
                    OnBehalfOfParameters.builder(Collections.singleton(scope),
                            new UserAssertion(authToken))
                            .build();

            auth = application.acquireToken(parameters).join();
        }

        cacheManager.getCache("tokens").put(cacheKey, application.tokenCache().serialize());

        return auth.accessToken();
    }
}
```

# <a name="python"></a>[Python](#tab/python)

O fluxo On-behalf-of (OBO) é usado para obter um token para chamar a API web a jusante. Neste fluxo, a sua API web recebe um token ao portador com permissões delegadas pelo utilizador a partir da aplicação do cliente e, em seguida, troca este token para outro token de acesso para ligar para a API web a jusante.

Uma API web Python terá de usar algum middleware para validar o sinal do portador recebido do cliente. A API web pode então obter o token de acesso para a API a jusante usando a biblioteca MSAL Python, chamando o [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) método. Para um exemplo de utilização desta API, consulte o [código de teste para a autenticação microsoft-library-for-python no GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.2.0/tests/test_e2e.py#L429-L472). Veja-se também a discussão da [questão 53](https://github.com/AzureAD/microsoft-authentication-library-for-python/issues/53) no mesmo repositório para uma abordagem que ignore a necessidade de uma aplicação de nível médio.

---

Também pode ver um exemplo da implementação do fluxo OBO em [Node.js e funções Azure](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protocolo

Para obter mais informações sobre o protocolo OBO, consulte a [plataforma de identidade da Microsoft e o fluxo OAuth 2.0 On-Behalf-Of](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Uma API web que chama APIs web: Adquira um símbolo para a app](scenario-web-api-call-api-acquire-token.md)
