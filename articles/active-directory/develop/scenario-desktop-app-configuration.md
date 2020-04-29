---
title: Configure aplicativos de desktop que chamam APIs web - plataforma de identidade microsoft / Azure
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
ms.custom: aaddev
ms.openlocfilehash: e0b43f7563c9dfac6374590f6b081197536fe31e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869002"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>Aplicação de desktop que chama APIs web: Configuração de código

Agora que criou a sua aplicação, aprenderá a configurar o código com as coordenadas da aplicação.

## <a name="microsoft-authentication-libraries"></a>Bibliotecas de Autenticação da Microsoft

As seguintes Bibliotecas de Autenticação da Microsoft (MSALs) suportam aplicações de ambiente de trabalho.

  Biblioteca de Autenticação da Microsoft | Descrição
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Suporta a construção de uma aplicação de desktop em várias plataformas, como Linux, Windows e macOS.
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Suporta a construção de uma aplicação de desktop em várias plataformas.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Suporta a construção de uma aplicação de desktop em várias plataformas.
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | Suporta aplicações de ambiente de trabalho que funcionam apenas com macOS.

## <a name="public-client-application"></a>Aplicação de cliente público

Do ponto de vista do código, as aplicações de desktop são aplicações de clientes públicos. A configuração será um pouco diferente com base na utilização ou não da autenticação interativa.

# <a name="net"></a>[.NET](#tab/dotnet)

Tens de construir e manipular `IPublicClientApplication`MSAL.NET.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Exclusivamente por código

O código seguinte instantaneamente uma aplicação de cliente público e sinais nos utilizadores na nuvem pública do Microsoft Azure com uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Se pretender utilizar a autenticação interativa ou o fluxo de `.WithRedirectUri` código do dispositivo, como se viu anteriormente, utilize o modificador.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>Utilizar os ficheiros de configuração

O código seguinte instantaneamente uma aplicação do cliente público a partir de um objeto de configuração, que poderia ser preenchido programáticamente ou lido a partir de um ficheiro de configuração.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Configuração mais elaborada

Pode elaborar o edifício da aplicação adicionando uma série de modificadores. Por exemplo, se quiser que a sua candidatura seja uma aplicação multiarrendatária numa nuvem nacional, como o Governo dos EUA mostrado aqui, pode escrever:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET também contém um modificador para serviços da Federação de Diretório Ativo 2019:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Finalmente, se quiser adquirir fichas para um inquilino B2C do Azure Ative Directory (Azure AD), especifique o seu inquilino como mostrado no seguinte código:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Saiba mais

Para saber mais sobre como configurar uma aplicação MSAL.NET ambiente de trabalho:

- Para obter uma lista de `PublicClientApplicationBuilder`todos os modificadores disponíveis, consulte a documentação de referência [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).
- Para uma descrição de `PublicClientApplicationOptions`todas as opções expostas, consulte [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) na documentação de referência.

### <a name="complete-example-with-configuration-options"></a>Exemplo completo com opções de configuração

Imagine uma aplicação de consola `appsettings.json` .NET Core que tem o seguinte ficheiro de configuração:

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

Tem pouco código para ler neste ficheiro utilizando o . Quadro de configuração fornecido pelo NET:

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

Antes da chamada `.Build()` para o método, pode anular `.WithXXX` a sua configuração com chamadas para métodos, como visto anteriormente.

# <a name="java"></a>[Java](#tab/java)

Aqui está a classe usada nas amostras de desenvolvimento do MSAL Java para configurar as amostras: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

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

O código seguinte instantaneamente uma aplicação de cliente público e sinais nos utilizadores na nuvem pública do Microsoft Azure com uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.

### <a name="quick-configuration"></a>Configuração rápida

Objetivo C:

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift:
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Configuração mais elaborada

Pode elaborar o edifício da aplicação adicionando uma série de modificadores. Por exemplo, se quiser que a sua candidatura seja uma aplicação multiarrendatária numa nuvem nacional, como o Governo dos EUA mostrado aqui, pode escrever:

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

Swift:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```
---

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adquira um símbolo para uma aplicação de desktop](scenario-desktop-acquire-token.md)
