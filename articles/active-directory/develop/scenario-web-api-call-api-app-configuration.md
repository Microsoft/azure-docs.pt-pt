---
title: API web a jusante esse chamadas APIs (configuração de código da aplicação) - a plataforma de identidade do Microsoft Web
description: Saiba como criar uma web API que chamadas de web APIs (configuração de código da aplicação)
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
ms.openlocfilehash: f62cf65e275d8a9b909bf60103ccbd84e91e4574
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65785059"
---
# <a name="web-api-that-calls-web-apis---code-configuration"></a>Web API que chamadas de web APIs - configuração de código

Depois de registar a API web, pode configurar o código da aplicação.

O código para configurar a API web para que ele chame APIs de downstream web baseia-se sobre o código usado para proteger uma API web. Para mais informações, veja [API da web - configuração de aplicação protegida](scenario-protected-web-api-app-configuration.md).

## <a name="code-subscribed-to-ontokenvalidated"></a>Código subscrito para OnTokenValidated

Sobre a configuração de código para todas as APIs de web protegida, terá de subscrever a validação do token de portador que é recebida quando denomina-se a sua API:

```CSharp
/// <summary>
/// Protects the web API with Microsoft Identity Platform v2.0 (AAD v2.0)
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">Service collection to which to add authentication</param>
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
        // to MSAL.NET's cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache, and also handles the incremental consent 
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>Fluxo em-nome-de

O método AddAccountToCacheFromJwt() precisa:

- Criar uma instância de uma aplicação de cliente confidencial de MSAL.
- Chamar `AcquireTokenOnBehalf` para trocar o token de portador foi adquirido pelo cliente para a web API, em relação a um token de portador para o mesmo utilizador, mas para a nossa API para chamar uma API de downstream.

### <a name="instantiate-a-confidential-client-application"></a>Criar uma instância de um aplicativo de cliente confidencial

Este fluxo só está disponível no fluxo de cliente confidencial, para a API web protegida fornece as credenciais de cliente (segredo do cliente ou certificado) para o [ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) via o `WithClientSecret` ou `WithCertificate`métodos, respectivamente.

![image](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```CSharp
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

### <a name="how-to-call-on-behalf-of"></a>Como chamar em-nome-de

A chamada de (OBO) em-nome-de é feita chamando o [AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) método no `IConfidentialClientApplication` interface.

O `ClientAssertion` foi desenvolvido desde o token de portador recebido pela API web de seus próprios clientes. Existem [dois construtores](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet), que recebe um portador do JWT token e outro que aceita qualquer tipo de asserção de utilizador (outro tipo de token de segurança, o tipo, em seguida, é especificado um parâmetro adicional com o nome `assertionType`).

![image](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

Na prática, o fluxo OBO, muitas vezes, é utilizado para adquirir um token para uma API de downstream e armazená-lo na cache de token de utilizador MSAL.NET, para que outras partes da web API mais tarde podem chamar no [substitui](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) de ``AcquireTokenOnSilent`` para chamar as APIs de downstream. Isso tem o efeito de atualizar os tokens, se necessário.

```CSharp
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

  // .Result to make sure that the cache is filled-in before the controller tries to get access tokens
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

## <a name="protocol"></a>Protocol

Para obter mais informações sobre o protocolo em-nome-de, consulte [plataforma de identidade da Microsoft e o fluxo do OAuth 2.0 On-Behalf-Of](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Obter um token para a aplicação](scenario-web-api-call-api-acquire-token.md)
