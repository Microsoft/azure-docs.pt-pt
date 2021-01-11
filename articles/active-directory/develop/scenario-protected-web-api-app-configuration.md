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
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f1277972480f504d9d2df67930d9385cbe8c06b4
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063200"
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

Aqui está um exemplo de código C# que mostra um cliente a ligar para a API depois de adquirir um token com a Microsoft Authentication Library for .NET (MSAL.NET):

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

#### <a name="case-where-you-used-a-custom-app-id-uri-for-your-web-api"></a>Caso em que usou um ID URI de aplicações personalizado para a sua API web

Se aceitou a Aplicação ID URI proposta pelo portal de registo da aplicação, não precisa de especificar o público (ver [ID URI de aplicação e âmbitos).](scenario-protected-web-api-app-registration.md#application-id-uri-and-scopes) Caso contrário, deverá adicionar uma `Audience` propriedade cujo valor é o App ID URI para a sua API web.

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common",
    "Audience": "custom App ID URI for your web API"
  },
  // more lines
}
```

### <a name="code-initialization"></a>Inicialização de código

Quando uma aplicação é chamada a uma ação do controlador que detém um atributo **[Authorize],** ASP.NET e ASP.NET Core extrai o token de acesso do símbolo do porta-voz da Autorização. O token de acesso é então reencaminhado para o middleware JwtBearer, que chama extensões de Modelo de Identidade da Microsoft para .NET.

#### <a name="microsoftidentityweb"></a>Microsoft.Identity.Web

A Microsoft recomenda que utilize o pacote [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) NuGet ao desenvolver uma API web com ASP.NET Core.

_Microsoft.Identity.Web_ fornece a cola entre ASP.NET Core, o middleware de autenticação e a Biblioteca de Autenticação do [Microsoft (MSAL)](msal-overview.md) para .NET. Permite uma experiência de desenvolvimento mais clara e robusta e aproveita o poder da plataforma de identidade da Microsoft e do Azure AD B2C.

#### <a name="using-microsoftidentityweb-templates"></a>Utilizando modelos Microsoft.Identity.Web

Pode criar uma API web de raiz utilizando modelos de projeto Microsoft.Identity.Web. Para mais detalhes consulte [Microsoft.Identity.Web - Modelo de projeto web API](https://aka.ms/ms-id-web/webapi-project-templates).

#### <a name="starting-from-an-existing-aspnet-core-31-application"></a>A partir de uma aplicação core 3.1 ASP.NET existente

Hoje, ASP.NET Core 3.1 utiliza a biblioteca Microsoft.AspNetCore.AzureAD.UI. O middleware é inicializado no ficheiro Startup.cs.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

O middleware é adicionado à API web por esta instrução:

```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
  services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
          .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
}
```

 Atualmente, os modelos core ASP.NET criam APIs web Azure Ative (Azure AD) que assinam em utilizadores dentro da sua organização ou em qualquer organização. Não assinam utilizadores com contas pessoais. No entanto, pode alterar os modelos para utilizar o ponto final da plataforma de identidade da Microsoft utilizando o [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) substituindo o código em *Startup.cs*:

```csharp
using Microsoft.Identity.Web;
```

```csharp
public void ConfigureServices(IServiceCollection services)
{
 // Adds Microsoft Identity platform (AAD v2.0) support to protect this API
 services.AddMicrosoftIdentityWebApiAuthentication(Configuration, "AzureAd");

 services.AddControllers();
}
```

também pode escrever o seguinte (que é equivalente)

```csharp
public void ConfigureServices(IServiceCollection services)
{
 // Adds Microsoft Identity platform (AAD v2.0) support to protect this API
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
             .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");

services.AddControllers();
}
```

> [!NOTE]
> Se utilizar o Microsoft.Identity.Web e não colocar o `Audience` *appsettings.jsligado,* utiliza-se o seguinte:
> -  `$"{ClientId}"` se tiver definido a [versão aceite para o token](scenario-protected-web-api-app-registration.md#accepted-token-version) de acesso ou para `2` APIs web Azure AD B2C.
> - `$"api://{ClientId}` em todos os outros casos (para [fichas de acesso](access-tokens.md)v1.0).
> Para mais informações, consulte o [código fonte](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/Resource/RegisterValidAudience.cs#L70-L83)microsoft.Identity.Web .

O corte de código anterior é extraído do tutorial incremental da [API web ASP.NET.](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/63087e83326e6a332d05fee6e1586b66d840b08f/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Startup.cs#L23-L28) O detalhe da **AddMicrosoftIdentityWebApiAuthentication** está disponível em [Microsoft.Identity.Web](microsoft-identity-web.md). Este método chama [AddMicrosoftIdentityWebAPI,](https://docs.microsoft.com/dotnet/api/microsoft.identity.web.microsoftidentitywebapiauthenticationbuilderextensions.addmicrosoftidentitywebapi?view=azure-dotnet-preview&preserve-view=true)que por si só instrui o middleware sobre como validar o token.

## <a name="token-validation"></a>Validação token

No snippet anterior, o middleware JwtBearer, tal como o middleware OpenID Connect em aplicações web, valida o token com base no valor de `TokenValidationParameters` . O símbolo é desencriptado conforme necessário, as alegações são extraídas, e a assinatura é verificada. O middleware valida então o token verificando estes dados:

- Público: O token é direcionado para a API web.
- Sub: Foi emitido para uma aplicação que é permitida a chamada a API web.
- Emitente: Foi emitido por um serviço de símbolo de segurança de confiança (STS).
- Expiração: A sua vida útil está ao alcance.
- Assinatura: Não foi adulterado.

Também pode haver validações especiais. Por exemplo, é possível validar que as chaves de assinatura, quando incorporadas num token, são confiáveis e que o símbolo não está a ser reproduzido. Finalmente, alguns protocolos requerem validações específicas.

### <a name="validators"></a>Validadores

Os passos de validação são capturados em validadores, que são fornecidos pelas [Extensões do Microsoft IdentityModel para](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) biblioteca de código aberto .NET. Os validadores são definidos no ficheiro de origem da biblioteca [Microsoft.IdentityModel.Tokens/Validadores.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

Esta tabela descreve os validadores:

| Validador | Descrição |
|---------|---------|
| **ValidarAudiência** | Garante que o token é para a aplicação que valida o token para si. |
| **ValidarIssuer** | Garante que o símbolo foi emitido por uma STS de confiança, o que significa que é de alguém em quem confias. |
| **ValidarIssuerSigningKey** | Garante que a aplicação que valida o token confia na chave que foi usada para assinar o token. Há um caso especial onde a chave está embutida no símbolo. Mas este caso não costuma surgir. |
| **ValidarLifetime** | Garante que o token é ainda ou já válido. O validador verifica se o tempo de vida útil do token está no intervalo especificado pelo **não antes** e expira os **sinistros.** |
| **Validar Assinatura** | Garante que o símbolo não foi adulterado. |
| **ValidaçãoTokenReplay** | Garante que o símbolo não é reproduzido. Há um caso especial para alguns protocolos de uso único. |

#### <a name="customizing-token-validation"></a>Validação de fichas de personalização

Os validadores estão associados com propriedades da classe **TokenValidationParameters.** As propriedades são inicializadas a partir da configuração ASP.NET e ASP.NET Core.

Na maioria dos casos, não é preciso alterar os parâmetros. Aplicativos que não são inquilinos solteiros são exceções. Estas aplicações web aceitam utilizadores de qualquer organização ou de contas pessoais da Microsoft. Os emitentes neste caso devem ser validados. Microsoft.Identity.Web também cuida da validação do emitente. Para mais detalhes consulte Microsoft.Identity.Web [AadIssuerValidator](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs).

Em ASP.NET Core, se pretender personalizar os parâmetros de validação do token, utilize o seguinte corte no seu *Startup.cs:*

```c#
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApi(Configuration);
services.Configure<JwtBearerOptions>(JwtBearerDefaults.AuthenticationScheme, options =>
{
  var existingOnTokenValidatedHandler = options.Events.OnTokenValidated;
  options.Events.OnTokenValidated = async context =>
  {
       await existingOnTokenValidatedHandler(context);
      // Your code to add extra configuration that will be executed after the current event implementation.
      options.TokenValidationParameters.ValidIssuers = new[] { /* list of valid issuers */ };
      options.TokenValidationParameters.ValidAudiences = new[] { /* list of valid audiences */};
  }
});
```

Para ASP.NET MVC, a seguinte amostra de código mostra como fazer a validação de fichas personalizadas:

https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation

## <a name="token-validation-in-azure-functions"></a>Validação simbólica em Funções Azure

Também pode validar fichas de acesso recebidas em Funções Azure. Pode encontrar exemplos dessa validação nas seguintes amostras de código no GitHub:

- .NET: [Azure-Samples/ms-identity-dotnet-webapi-azurefunctions](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)
- Node.js: [Azure-Samples/ms-identidade-nodejs-webapi-azurefunctions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)
- Python: [Azure-Samples/ms-identity-python-webapi-azurefunctions)](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)

## <a name="next-steps"></a>Próximos passos

Passe para o próximo artigo neste cenário, [Verifique os âmbitos e as funções de aplicação no seu código.](scenario-protected-web-api-verification-scope-app-roles.md)
