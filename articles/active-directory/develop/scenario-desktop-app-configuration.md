---
title: Configurar aplicativos da área de trabalho que chamam APIs da Web-plataforma Microsoft Identity | Azure
description: Saiba como configurar o código de um aplicativo de desktop que chama APIs da Web
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 473f0895ca53fb78b48c828eac807feb25364378
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293135"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>Aplicativo de área de trabalho que chama APIs da Web: configuração de código

Agora que você criou seu aplicativo, você aprenderá a configurar o código com as coordenadas do aplicativo.

## <a name="microsoft-authentication-libraries"></a>Bibliotecas de autenticação da Microsoft

As seguintes MSALs (bibliotecas de autenticação da Microsoft) oferecem suporte a aplicativos de área de trabalho.

  Biblioteca de Autenticação da Microsoft | Descrição
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Dá suporte à criação de um aplicativo de área de trabalho em várias plataformas, como Linux, Windows e macOS.
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Dá suporte à criação de um aplicativo de área de trabalho em várias plataformas.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Dá suporte à criação de um aplicativo de área de trabalho em várias plataformas.
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | Dá suporte a aplicativos de área de trabalho que são executados somente no macOS.

## <a name="public-client-application"></a>Aplicativo cliente público

De um ponto de vista de código, os aplicativos de área de trabalho são aplicativos cliente públicos. A configuração será um pouco diferente se você usar a autenticação interativa ou não.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Você precisará criar e manipular MSAL.NET `IPublicClientApplication`.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Exclusivamente por código

O código a seguir cria uma instância de um aplicativo cliente público e entra em usuários na nuvem pública Microsoft Azure com uma conta corporativa ou de estudante ou uma conta Microsoft pessoal.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Se você pretende usar a autenticação interativa ou o fluxo de código do dispositivo, como visto anteriormente, use o modificador de `.WithRedirectUri`.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>Utilizar os ficheiros de configuração

O código a seguir instancia um aplicativo cliente público de um objeto de configuração, que pode ser preenchido de forma programática ou lido de um arquivo de configuração.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Configuração mais elaborada

Você pode elaborar a criação de aplicativos adicionando vários modificadores. Por exemplo, se você quiser que seu aplicativo seja um aplicativo multilocatário em uma nuvem nacional, como o governo dos EUA mostrado aqui, você poderia escrever:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET também contém um modificador para Serviços de Federação do Active Directory (AD FS) 2019:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Por fim, se você quiser adquirir tokens para um locatário Azure Active Directory (Azure AD) B2C, especifique seu locatário, conforme mostrado no seguinte trecho de código:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Saber mais

Para saber mais sobre como configurar um aplicativo de área de trabalho MSAL.NET:

- Para obter uma lista de todos os modificadores disponíveis em `PublicClientApplicationBuilder`, consulte a documentação de referência [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).
- Para obter uma descrição de todas as opções expostas em `PublicClientApplicationOptions`, consulte [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) na documentação de referência.

### <a name="complete-example-with-configuration-options"></a>Exemplo completo com opções de configuração

Imagine um aplicativo de console do .NET Core que tenha o seguinte arquivo de configuração de `appsettings.json`:

```JSon
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

Você tem pouco código para ler nesse arquivo usando o. Estrutura de configuração fornecida pela rede:

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

Agora, para criar seu aplicativo, escreva o seguinte código:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

Antes da chamada para o método `.Build()`, você pode substituir sua configuração por chamadas para métodos `.WithXXX`, como visto anteriormente.

# <a name="javatabjava"></a>[Java](#tab/java)

Aqui está a classe usada em exemplos de desenvolvimento do MSAL Java para configurar os exemplos: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
PublicClientApplication app = PublicClientApplication.builder(TestData.PUBLIC_CLIENT_ID)
        .authority(TestData.AUTHORITY_COMMON)
        .build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

O código a seguir cria uma instância de um aplicativo cliente público e entra em usuários na nuvem pública Microsoft Azure com uma conta corporativa ou de estudante ou uma conta Microsoft pessoal.

### <a name="quick-configuration"></a>Configuração rápida

Objective-C:

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Configuração mais elaborada

Você pode elaborar a criação de aplicativos adicionando vários modificadores. Por exemplo, se você quiser que seu aplicativo seja um aplicativo multilocatário em uma nuvem nacional, como o governo dos EUA mostrado aqui, você poderia escrever:

Objective-C:

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

Swift

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```
---

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adquirir um token para um aplicativo de área de trabalho](scenario-desktop-acquire-token.md)
