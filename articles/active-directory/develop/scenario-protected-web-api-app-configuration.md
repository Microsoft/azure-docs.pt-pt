---
title: Configure aplicações web API protegidas [ Azure
titleSuffix: Microsoft identity platform
description: Aprenda a construir uma API web protegida e configure o código da sua aplicação.
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882476"
---
# <a name="protected-web-api-code-configuration"></a>API da web protegida: Configuração de código

Para configurar o código para a sua API web protegida, precisa entender:

- O que define as APIs como protegidas.
- Como configurar um símbolo do portador.
- Como validar o símbolo.

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>O que define ASP.NET e ASP.NET APIs do Núcleo como protegidos?

Tal como as aplicações web, as APIs web ASP.NET e ASP.NET Core estão protegidas porque as suas ações de controlador são pré-fixadas com o atributo **[Autorizar].** As ações do controlador só podem ser chamadas se a API for chamada com uma identidade autorizada.

Considere as perguntas seguintes:

- Apenas uma aplicação pode chamar uma API web. Como é que a API sabe a identidade da app que a chama?
- Se a aplicação chamar a API em nome de um utilizador, qual é a identidade do utilizador?

## <a name="bearer-token"></a>Símbolo do portador

O token portador que está definido no cabeçalho quando a aplicação é chamada contém informações sobre a identidade da aplicação. Também detém informações sobre o utilizador, a menos que a aplicação web aceite chamadas de serviço a serviço a partir de uma aplicação daemon.

Aqui está um exemplo de código C# que mostra um cliente a ligar para a API depois de adquirir um símbolo com a Microsoft Authentication Library para .NET (MSAL.NET):

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
> Uma aplicação de cliente solicita ao portador o ponto final da plataforma de identidade da Microsoft *para a Web API*. A API web é a única aplicação que deve verificar o símbolo e ver as alegações que contém. As aplicações de clientes nunca devem tentar inspecionar as reclamações em fichas.
>
> No futuro, a API web pode exigir que o token seja encriptado. Este requisito impediria o acesso de aplicações de clientes que possam ver fichas de acesso.

## <a name="jwtbearer-configuration"></a>Configuração JwtBearer

Esta secção descreve como configurar um símbolo do portador.

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

### <a name="code-initialization"></a>Inicialização do código

Quando uma aplicação é chamada a uma ação de controlador que detém um atributo **[Autorizar],** ASP.NET e ASP.NET Core extraem o sinal de acesso do símbolo do portador do cabeçalho de Autorização. O token de acesso é então encaminhado para o middleware JwtBearer, que chama as extensões do Microsoft IdentityModel para .NET.

Em ASP.NET Core, este middleware é inicializado no ficheiro Startup.cs.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

O middleware é adicionado à Web API por esta instrução:

```csharp
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
         .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

 Atualmente, os modelos ASP.NET Core criam APIs web Azure Ative Directory (Azure AD) que assinam utilizadores dentro da sua organização ou de qualquer organização. Não assinam utilizadores com contas pessoais. Mas pode alterar os modelos para utilizar o ponto final da plataforma de identidade da Microsoft adicionando este código à Startup.cs:

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

O snippet de código anterior é extraído do tutorial incremental web API ASP.NET em [Microsoft.Identity.Web/WebApiServiceCollections.cs#L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). O método **AddProtectedWebApi,** que faz mais do que os programas de corte, é chamado de Startup.cs.

## <a name="token-validation"></a>Validação de fichas

No snippet anterior, o middleware JwtBearer, como o OpenID Connect middleware em aplicações `TokenValidationParameters`web, valida o token com base no valor de . O símbolo é desencriptado conforme necessário, as alegações são extraídas, e a assinatura é verificada. O middleware valida então o símbolo verificando estes dados:

- Audiência: O símbolo é direcionado para a Web API.
- Sub: Foi emitido para uma aplicação que é permitida a chamada a API web.
- Emitente: Foi emitido por um serviço de fichas de segurança confiável (STS).
- Expiração: A sua vida útil está ao alcance.
- Não foi adulterado.

Também pode haver validações especiais. Por exemplo, é possível validar que as chaves de assinatura, quando incorporadas num símbolo, são confiáveis e que o símbolo não está a ser repetido. Finalmente, alguns protocolos requerem validações específicas.

### <a name="validators"></a>Validadores

As etapas de validação são capturadas em validadores, que são fornecidos pelas extensões do Microsoft IdentityModel para a biblioteca de código aberto [.NET.](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) Os validadores são definidos no ficheiro fonte da biblioteca [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

Esta tabela descreve os validadores:

| Validador | Descrição |
|---------|---------|
| **ValidaoAudience** | Garante que o símbolo é para a aplicação que valida o símbolo para si. |
| **Validador** | Garante que o símbolo foi emitido por um STS de confiança, o que significa que é de alguém em quem confias. |
| **ValidaçãoDeaSigningKey** | Garante que a aplicação que valida o símbolo confia na chave que foi usada para assinar o símbolo. Há um caso especial onde a chave está incorporada no símbolo. Mas este caso não costuma surgir. |
| **Validação Vitalícia** | Garante que o símbolo ainda é válido ou já é válido. O validador verifica se o tempo de vida útil do token está no intervalo especificado pelo **notantes** e **expira** os pedidos. |
| **ValidaçãoAssinatura** | Garante que o símbolo não foi adulterado. |
| **ValidatokenReplay** | Garante que o símbolo não é repetido. Há um caso especial para alguns protocolos de uso único. |

Os validadores estão associados a propriedades da classe **TokenValidaçãoParameters.** As propriedades são inicializadas a partir da configuração ASP.NET e ASP.NET Core.

Na maioria dos casos, não é preciso mudar os parâmetros. Aplicativos que não são inquilinos únicos são exceções. Estas aplicações web aceitam utilizadores de qualquer organização ou de contas pessoais da Microsoft. Neste caso, os emitentes têm de ser validados.

## <a name="token-validation-in-azure-functions"></a>Validação simbólica em Funções Azure

Também pode validar fichas de acesso de entrada em Funções Azure. Pode encontrar exemplos de tal validação na [Microsoft .NET,](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions) [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)e [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Verifique os âmbitos e as funções da aplicação no seu código](scenario-protected-web-api-verification-scope-app-roles.md)
