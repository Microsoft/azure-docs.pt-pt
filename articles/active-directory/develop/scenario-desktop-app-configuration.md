---
title: Aplicativo de Desktop que chamadas de web APIs (a configuração do código) – plataforma de identidade da Microsoft
description: Saiba como criar uma aplicação de ambiente de trabalho que chamadas de web APIs (configuração de código da aplicação)
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
ms.date: 05/o7/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c52f6fc66187d961dc93089a9f81f6de4d67fe41
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075944"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>Aplicativo de Desktop que chamadas de web APIs - configuração de código

Agora que criou seu aplicativo, aprenderá como configurar o código com coordenadas do aplicativo.

## <a name="msal-libraries"></a>Bibliotecas MSAL

A biblioteca MSAL apenas suporte aos aplicativos de área de trabalho hoje é MSAL.NET

## <a name="public-client-application"></a>Aplicação cliente público

A partir de um código ponto de vista, aplicativos de desktop são aplicativos cliente público e é por isso que vai criar e manipular MSAL.NET `IPublicClientApplication`. Novamente as coisas será um pouco diferente se utilizar a autenticação interativa ou não.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Exclusivamente por código

O código a seguir cria uma instância de um aplicativo cliente pública, os utilizadores iniciar sessão na cloud pública do Microsoft Azure, com o seu trabalho e contas escolares ou contas pessoais da Microsoft.

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Se pretende usar a autenticação interativa, como visto acima, o que pretende utilizar o `.WithRedirectUri` modificador:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithRedirectUri(PublicClientApplicationBuilder.DefaultInteractiveDesktopRedirectUri)
         .Build();
```

### <a name="using-configuration-files"></a>Usando arquivos de configuração

O código a seguir cria uma instância de uma aplicação de cliente pública de um objeto de configuração, o que poderia ser preenchido-no através de programação ou ler a partir de um ficheiro de configuração

```CSharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="more-elaborated-configuration"></a>Mais fosse elaborado de acordo de configuração

Pode falar mais adicionando um número de modificadores de criação. Por exemplo, se pretender que o aplicativo seja um aplicativo de vários inquilino numa cloud nacional (aqui administração pública dos EUA), poderia escrever:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET também contém um modificador de ADFS 2019:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Por fim, se quiser adquira tokens por um inquilino do Azure AD B2C, pode especificar o seu inquilino, conforme mostrado no seguinte fragmento de código:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Saiba mais

Para obter mais informações sobre como configurar uma aplicação de ambiente de trabalho MSAL.NET:

- Para obter a lista de todos os modificadores disponíveis no `PublicClientApplicationBuilder`, consulte a documentação de referência [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.appconfig.publicclientapplicationbuilder?view=azure-dotnet-preview#methods)
- Para a descrição de todas as opções expostos no `PublicClientApplicationOptions` veja [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.appconfig.publicclientapplicationoptions?view=azure-dotnet-preview), na documentação de referência

## <a name="complete-example-with-configuration-options"></a>Exemplo completo com opções de configuração

Imagine uma aplicação de consola .NET Core que tem o seguinte `appsettings.json` ficheiro de configuração:

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

Tem pouco de código para ler este ficheiro com o .NET fornecido framework de configuração;

```CSharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
 /// in Microsoft Azure public clouds or national / sovereign clouds
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a json file
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

  // Read the auth and graph endpoint config
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

Agora, para criar a sua aplicação, apenas terá de escrever o código a seguir:

```CSharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

e antes da chamada para o `.Build()` método, pode substituir a configuração com chamadas para `.WithXXX` métodos, como visto anteriormente.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Obter um token para uma aplicação de ambiente de trabalho](scenario-desktop-acquire-token.md)
