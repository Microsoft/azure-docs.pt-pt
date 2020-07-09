---
title: Configurar aplicações de API protegidas na Web / Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como construir uma API web protegida e configurar o código da sua aplicação.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 073eca94ad93c69811b02abe2c8649940a394e8e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80882476"
---
# <a name="protected-web-api-code-configuration"></a>API web protegida: configuração de código

Para configurar o código para a sua API web protegida, precisa entender:

- O que define as APIs como protegidas.
- Como configurar um símbolo portador.
- Como validar o símbolo.

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>O que define ASP.NET e ASP.NET AP's de núcleo como protegidos?

Tal como as aplicações web, os APIs web ASP.NET e ASP.NET estão protegidos porque as suas ações de controlador estão prefixadas com o atributo **[Authorize].** As ações do controlador só podem ser chamadas se a API for chamada com uma identidade autorizada.

Considere as perguntas seguintes:

- Apenas uma aplicação pode chamar uma API web. Como é que a API sabe a identidade da app que lhe chama?
- Se a aplicação ligar para a API em nome de um utilizador, qual é a identidade do utilizador?

## <a name="bearer-token"></a>Ficha de portador

O token portador que é definido no cabeçalho quando a aplicação é chamada contém informações sobre a identidade da app. Também contém informações sobre o utilizador, a menos que a aplicação web aceite chamadas de serviço a serviço a partir de uma aplicação daemon.

Aqui está um exemplo de código C# que mostra um cliente a ligar para a API depois de adquirir um token com a Microsoft Authentication Library para .NET (MSAL.NET):

```csharp
var scopes = new[] {$"api://.../access_as_user"};
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> Uma aplicação de cliente solicita o token ao ponto final da plataforma de identidade da Microsoft *para a API web*. A API web é a única aplicação que deve verificar o token e ver as alegações que contém. As aplicações de clientes nunca devem tentar inspecionar as reclamações em fichas.
>
> No futuro, a API web pode exigir que o símbolo seja encriptado. Este requisito impediria o acesso a aplicações de clientes que possam ver fichas de acesso.

## <a name="jwtbearer-configuration"></a>Configuração JwtBearer

Esta secção descreve como configurar um símbolo portador.

### <a name="config-file"></a>Arquivo Config

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="code-initialization"></a>Inicialização de código

Quando uma aplicação é chamada a uma ação do controlador que detém um atributo **[Authorize],** ASP.NET e ASP.NET Core extrai o token de acesso do símbolo do porta-voz da Autorização. O token de acesso é então reencaminhado para o middleware JwtBearer, que chama extensões de Modelo de Identidade da Microsoft para .NET.

Em ASP.NET Core, este middleware é inicializado no ficheiro Startup.cs.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

O middleware é adicionado à API web por esta instrução:

```csharp
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
         .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

 Atualmente, os modelos core ASP.NET criam APIs web Azure Ative (Azure AD) que assinam em utilizadores dentro da sua organização ou em qualquer organização. Não assinam utilizadores com contas pessoais. Mas pode alterar os modelos para utilizar o ponto final da plataforma de identidade da Microsoft adicionando este código a Startup.cs:

```csharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform web API.
    options.Authority += "/v2.0";

    // The web API accepts as audiences both the Client ID (options.Audience) and api://{ClientID}.
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line-of-business apps),
    // we inject our own multitenant validation logic (which even accepts both v1 and v2 tokens).
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;;
});
```

O corte de código anterior é extraído do tutorial incremental da API web ASP.NET em [Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). O método **AddProtectedWebApi,** que faz mais do que os programas de snippet, é chamado de Startup.cs.

## <a name="token-validation"></a>Validação token

No snippet anterior, o middleware JwtBearer, tal como o middleware OpenID Connect em aplicações web, valida o token com base no valor de `TokenValidationParameters` . O símbolo é desencriptado conforme necessário, as alegações são extraídas, e a assinatura é verificada. O middleware valida então o token verificando estes dados:

- Público: O token é direcionado para a API web.
- Sub: Foi emitido para uma aplicação que é permitida a chamada a API web.
- Emitente: Foi emitido por um serviço de símbolo de segurança de confiança (STS).
- Expiração: A sua vida útil está ao alcance.
- Assinatura: Não foi adulterado.

Também pode haver validações especiais. Por exemplo, é possível validar que as chaves de assinatura, quando incorporadas num token, são confiáveis e que o símbolo não está a ser reproduzido. Finalmente, alguns protocolos requerem validações específicas.

### <a name="validators"></a>Validadores

Os passos de validação são capturados em validadores, que são fornecidos pelas [Extensões do Microsoft IdentityModel para](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) biblioteca de código aberto .NET. Os validadores são definidos no ficheiro de origem da biblioteca [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

Esta tabela descreve os validadores:

| Validador | Descrição |
|---------|---------|
| **ValidarAudiência** | Garante que o token é para a aplicação que valida o token para si. |
| **ValidarIssuer** | Garante que o símbolo foi emitido por uma STS de confiança, o que significa que é de alguém em quem confias. |
| **ValidarIssuerSigningKey** | Garante que a aplicação que valida o token confia na chave que foi usada para assinar o token. Há um caso especial onde a chave está embutida no símbolo. Mas este caso não costuma surgir. |
| **ValidarLifetime** | Garante que o token é ainda ou já válido. O validador verifica se o tempo de vida útil do token está no intervalo especificado pelo **não antes** e expira os **sinistros.** |
| **Validar Assinatura** | Garante que o símbolo não foi adulterado. |
| **ValidaçãoTokenReplay** | Garante que o símbolo não é reproduzido. Há um caso especial para alguns protocolos de uso único. |

Os validadores estão associados com propriedades da classe **TokenValidationParameters.** As propriedades são inicializadas a partir da configuração ASP.NET e ASP.NET Core.

Na maioria dos casos, não é preciso alterar os parâmetros. Aplicativos que não são inquilinos solteiros são exceções. Estas aplicações web aceitam utilizadores de qualquer organização ou de contas pessoais da Microsoft. Os emitentes neste caso devem ser validados.

## <a name="token-validation-in-azure-functions"></a>Validação simbólica em Funções Azure

Também pode validar fichas de acesso recebidas em Funções Azure. Pode encontrar exemplos dessa validação no [Microsoft .NET,](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions) [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)e [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions).

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Verifique os âmbitos e as funções de aplicação no seu código](scenario-protected-web-api-verification-scope-app-roles.md)
