---
title: Configure aplicações de ambiente de trabalho que liguem para APIs web | Rio Azure
titleSuffix: Microsoft identity platform
description: Saiba como configurar o código de uma aplicação de desktop que chama APIs web
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 372efa217e400ffd4bcecd286f3c3dc7b81f64d6
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103007974"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>Aplicativo de desktop que chama APIs web: configuração de código

Agora que criou a sua aplicação, aprenderá a configurar o código com as coordenadas da aplicação.

## <a name="microsoft-libraries-supporting-desktop-apps"></a>Bibliotecas da Microsoft que suportam aplicações de desktop

As seguintes bibliotecas da Microsoft suportam aplicações de ambiente de trabalho:

[!INCLUDE [active-directory-develop-libraries-desktop](../../../includes/active-directory-develop-libraries-desktop.md)]

## <a name="public-client-application"></a>Aplicação cliente pública

Do ponto de vista do código, as aplicações para desktop são aplicações de clientes públicos. A configuração será um pouco diferente com base na utilização ou não de autenticação interativa.

# <a name="net"></a>[.NET](#tab/dotnet)

Terá de construir e manipular `IPublicClientApplication` MSAL.NET.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Exclusivamente por código

O código que se segue instantaneamente uma aplicação de cliente público e assina em utilizadores na nuvem pública do Microsoft Azure com uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Se pretender utilizar a autenticação interativa ou o fluxo de código do dispositivo, como visto anteriormente, utilize o `.WithRedirectUri` modificador.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>Utilizar os ficheiros de configuração

O código que se segue instantaneamente uma aplicação do cliente público a partir de um objeto de configuração, que pode ser preenchido programáticamente ou lido a partir de um ficheiro de configuração.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Configuração mais elaborada

Pode elaborar o edifício de aplicações adicionando uma série de modificadores. Por exemplo, se quiser que a sua aplicação seja uma aplicação multitenante numa nuvem nacional, como o Governo dos EUA mostrado aqui, pode escrever:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET também contém um modificador para serviços da Federação de Diretórios Ativos 2019:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Finalmente, se pretender adquirir fichas para um inquilino B2C (Azure AD) B2C, especifique o seu inquilino como indicado no seguinte corte de código:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Saber mais

Para saber mais sobre como configurar uma aplicação de ambiente de trabalho MSAL.NET:

- Para obter uma lista de todos os modificadores `PublicClientApplicationBuilder` disponíveis, consulte a documentação de referência [PublicClientApplicationBuilder](/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).
- Para uma descrição de todas as opções expostas em `PublicClientApplicationOptions` , consulte [PublicClientApplicationOptions](/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) na documentação de referência.

### <a name="complete-example-with-configuration-options"></a>Exemplo completo com opções de configuração

Imagine uma aplicação de consola .NET Core que tenha o seguinte `appsettings.json` ficheiro de configuração:

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

Tem pouco código para ler neste ficheiro utilizando o . Quadro de configuração fornecido pela NET:

```csharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application runs
 /// in Microsoft Azure public clouds or national or sovereign clouds)
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a JSON file
 /// </summary>
 /// <param name="path">Path to the configuration json file</param>
 /// <returns>SampleConfiguration as read from the json file</returns>
 public static SampleConfiguration ReadFromJsonFile(string path)
 {
  // .NET configuration
  IConfigurationRoot Configuration;
  var builder = new ConfigurationBuilder()
                    .SetBasePath(Directory.GetCurrentDirectory())
                    .AddJsonFile(path);
  Configuration = builder.Build();

  // Read the auth and graph endpoint configuration
  SampleConfiguration config = new SampleConfiguration()
  {
   PublicClientApplicationOptions = new PublicClientApplicationOptions()
  };
  Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

Agora, para criar a sua aplicação, escreva o seguinte código:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

Antes da chamada para o `.Build()` método, pode anular a sua configuração com chamadas a `.WithXXX` métodos, como visto anteriormente.

# <a name="java"></a>[Java](#tab/java)

Aqui está a classe usada em amostras de desenvolvimento da MSAL Java para configurar as amostras: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
        .authority(AUTHORITY)
        .build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macos"></a>[MacOS](#tab/macOS)

O código que se segue instantaneamente uma aplicação de cliente público e assina em utilizadores na nuvem pública do Microsoft Azure com uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.

### <a name="quick-configuration"></a>Configuração rápida

Objetivo C:

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Rápido:
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Configuração mais elaborada

Pode elaborar o edifício de aplicações adicionando uma série de modificadores. Por exemplo, se quiser que a sua aplicação seja uma aplicação multitenante numa nuvem nacional, como o Governo dos EUA mostrado aqui, pode escrever:

Objetivo C:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

Rápido:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```
---

## <a name="next-steps"></a>Passos seguintes

Passe para o próximo artigo neste cenário, [adquira um símbolo para a aplicação de ambiente de trabalho](scenario-desktop-acquire-token.md).