---
title: Instantiate uma aplicação de cliente público (MSAL.NET) / Azure
titleSuffix: Microsoft identity platform
description: Saiba como instantaneamente uma aplicação de cliente público com opções de configuração utilizando a Biblioteca de Autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1dd06e139f931bbf8554f05f05c5d9b9ccf200e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083592"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Instantiate uma aplicação de cliente público com opções de configuração usando MSAL.NET

Este artigo descreve como instantaneamente uma [aplicação de cliente público](msal-client-applications.md) usando a Microsoft Authentication Library para .NET (MSAL.NET).  A aplicação é instantânea com opções de configuração definidas num ficheiro de definições.

Antes de inicializar uma aplicação, primeiro é necessário [registá-la](quickstart-register-app.md) para que a sua aplicação possa ser integrada na plataforma de identidade da Microsoft. Após o registo, poderá necessitar das seguintes informações (que podem ser encontradas no portal Azure):

- O ID do cliente (uma corda que representa um GUID)
- O URL do fornecedor de identidade (nome ou a instância) e o público de inscrição para a sua aplicação. Estes dois parâmetros são coletivamente conhecidos como autoridade.
- O ID do inquilino se estiver a escrever uma linha de aplicação de negócios exclusivamente para a sua organização (também nomeada candidatura de inquilino único).
- Para aplicações web, e por vezes para aplicações de clientes públicos (em particular quando a sua aplicação precisa de usar um corretor), também terá definido o redirectUri onde o fornecedor de identidade irá contactar de volta a sua aplicação com os tokens de segurança.


Uma aplicação de consola .NET Core poderia ter o seguinte ficheiro de *configuração appsettings.json:*

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

