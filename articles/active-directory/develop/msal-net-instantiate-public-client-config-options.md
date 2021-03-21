---
title: Instantiate uma aplicação de cliente público (MSAL.NET) | Rio Azure
titleSuffix: Microsoft identity platform
description: Aprenda a instantaneaizar uma aplicação de cliente público com opções de configuração utilizando a Biblioteca de Autenticação do Microsoft para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 3e2ffebf0b414d4b59178fe04fb109530365786b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98064713"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Instantiizar uma aplicação de cliente público com opções de configuração usando MSAL.NET

Este artigo descreve como instantaneamente uma [aplicação de cliente público](msal-client-applications.md) utilizando a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET).  A aplicação é instantânea com opções de configuração definidas num ficheiro de definições.

Antes de rubricar uma aplicação, primeiro precisa [de a registar](quickstart-register-app.md) para que a sua aplicação possa ser integrada na plataforma de identidade da Microsoft. Após o registo, poderá necessitar das seguintes informações (que podem ser encontradas no portal Azure):

- O ID do cliente (uma corda que representa um GUID)
- O URL do fornecedor de identidade (nomeado o caso) e o público de inscrição para a sua aplicação. Estes dois parâmetros são coletivamente conhecidos como a autoridade.
- O ID do inquilino se estiver a escrever uma linha de aplicação de negócio apenas para a sua organização (também nomeado pedido de inquilino único).
- Para aplicações web, e às vezes para aplicações de clientes públicos (em particular quando a sua aplicação precisa de usar um corretor), também terá definido o redirectUri onde o fornecedor de identidade irá contactar de volta a sua aplicação com os tokens de segurança.


Uma aplicação de consola .NET Core pode ter as *seguintesappsettings.jsno* ficheiro de configuração:

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

O seguinte código lê este ficheiro utilizando a estrutura de configuração .NET:

```csharp
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
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

O seguinte código cria a sua aplicação, utilizando a configuração a partir do ficheiro de definições:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

