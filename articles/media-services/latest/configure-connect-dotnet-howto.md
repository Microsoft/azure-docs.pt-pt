---
title: Ligar à API de v3 dos serviços de multimédia do Azure - .NET
description: Saiba como ligar a serviços de multimédia v3 API com .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: juliako
ms.openlocfilehash: 8f8a1434af768180e34afcaacd6e92ab402ad8cd
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361238"
---
# <a name="connect-to-media-services-v3-api---net"></a>Ligar à API de v3 dos serviços de suporte de dados - .NET

Este artigo mostra-lhe como ligar ao SDK do .NET dos serviços de multimédia do Azure v3 usando o método de início de sessão principal de serviço.

## <a name="prerequisites"></a>Pré-requisitos

- [Criar uma conta de Media Services](create-account-cli-how-to.md). Lembre-se de que não se esqueça de que o nome do grupo de recursos e o nome da conta dos serviços de multimédia
- Instale uma ferramenta que gostaria de usar para o desenvolvimento do .NET. Os passos neste artigo mostram como usar [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Pode utilizar o Visual Studio Code, consulte [trabalhar com o C# ](https://code.visualstudio.com/docs/languages/csharp). Em alternativa, pode utilizar um editor de código diferentes.

## <a name="create-a-console-application"></a>Criar uma aplicação de consola

1. Inicie o Visual Studio. 
1. Do **arquivo** menu, clique em **New** > **projeto**. 
1. Criar uma **.NET Core** aplicação de consola.

Destina-se a aplicação de exemplo neste tópico, `netcoreapp2.0`. O código utiliza "async principal", que está disponível a partir do C# 7.1. Ver isso [blogue](https://blogs.msdn.microsoft.com/benwilli/2017/12/08/async-main-is-available-but-hidden/) para obter mais detalhes.

## <a name="add-required-nuget-packages"></a>Adicionar pacotes de NuGet necessárias

1. No Visual Studio, selecione **ferramentas** > **Gestor de pacotes NuGet** > **consola do Gestor do NuGet**.
2. Na **Package Manager Console** janela, utilize `Install-Package` comando para adicionar os seguintes pacotes NuGet. Por exemplo, `Install-Package Microsoft.Azure.Management.Media`.

|Pacote|Descrição|
|---|---|
|`Microsoft.Azure.Management.Media`|SDK dos serviços de multimédia do Azure. <br/>Para certificar-se de que está a utilizar o pacote de serviços de multimédia do Azure mais recente, verifique [Microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media).|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|Biblioteca de autenticação da ADAL para o Azure SDK para NET|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|Ler valores de configuração a partir de variáveis de ambiente e de ficheiros locais do JSON|
|`Microsoft.Extensions.Configuration.Json`|Ler valores de configuração a partir de variáveis de ambiente e de ficheiros locais do JSON
|`WindowsAzure.Storage`|SDK de armazenamento|

## <a name="create-and-configure-the-app-settings-file"></a>Criar e configurar o ficheiro de definições de aplicação

### <a name="create-appsettingsjson"></a>Criar appSettings

1. Aceda go **gerais** > **ficheiro de texto**.
1. Nomeie-o "appSettings".
1. Defina a propriedade de "Copiar para diretório de saída" do ficheiro. JSON para "Copiar se for mais recente" (para que o aplicativo é capaz de acessá-lo quando publicado).

### <a name="set-values-in-appsettingsjson"></a>Conjunto de valores no appSettings

Executar o `az ams account sp create` comando, tal como descrito na [aceder a APIs](access-api-cli-how-to.md). O comando devolve json que deve copiar no seu "appSettings".
 
## <a name="add-configuration-file"></a>Adicionar o ficheiro de configuração

Para sua comodidade, adicione um ficheiro de configuração que é responsável pela leitura de valores de "appSettings".

1. Adicione uma nova classe. cs ao seu projeto. Dê-lhe o nome `ConfigWrapper`. 
1. Cole o seguinte código neste ficheiro (Este exemplo assume que tem o espaço de nomes é `ConsoleApp1`).

```csharp
using System;

using Microsoft.Extensions.Configuration;

namespace ConsoleApp1
{
    public class ConfigWrapper
    {
        private readonly IConfiguration _config;

        public ConfigWrapper(IConfiguration config)
        {
            _config = config;
        }

        public string SubscriptionId
        {
            get { return _config["SubscriptionId"]; }
        }

        public string ResourceGroup
        {
            get { return _config["ResourceGroup"]; }
        }

        public string AccountName
        {
            get { return _config["AccountName"]; }
        }

        public string AadTenantId
        {
            get { return _config["AadTenantId"]; }
        }

        public string AadClientId
        {
            get { return _config["AadClientId"]; }
        }

        public string AadSecret
        {
            get { return _config["AadSecret"]; }
        }

        public Uri ArmAadAudience
        {
            get { return new Uri(_config["ArmAadAudience"]); }
        }

        public Uri AadEndpoint
        {
            get { return new Uri(_config["AadEndpoint"]); }
        }

        public Uri ArmEndpoint
        {
            get { return new Uri(_config["ArmEndpoint"]); }
        }

        public string Region
        {
            get { return _config["Region"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>Ligar para o cliente do .NET

Para começar a utilizar as APIs dos Serviços de Multimédia com o .NET, tem de criar um objeto **AzureMediaServicesClient**. Para criar o objeto, tem de fornecer as credenciais necessárias para o cliente se ligar ao Azure com o Azure AD. No código abaixo, a função de GetCredentialsAsync cria o objeto de ServiceClientCredentials com base nas credenciais fornecidas no ficheiro de configuração local.

1. Abra `Program.cs`.
1. Cole o seguinte código:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace ConsoleApp1
{
    class Program
    {
        public static async Task Main(string[] args)
        {
            
            ConfigWrapper config = new ConfigWrapper(new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build());

            try
            {
                IAzureMediaServicesClient client = await CreateMediaServicesClientAsync(config);
                Console.WriteLine("connected");
            }
            catch (Exception exception)
            {
                if (exception.Source.Contains("ActiveDirectory"))
                {
                    Console.Error.WriteLine("TIP: Make sure that you have filled out the appsettings.json file before running this sample.");
                }

                Console.Error.WriteLine($"{exception.Message}");

                ApiErrorException apiException = exception.GetBaseException() as ApiErrorException;
                if (apiException != null)
                {
                    Console.Error.WriteLine(
                        $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
                }
            }

            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }
 
        private static async Task<ServiceClientCredentials> GetCredentialsAsync(ConfigWrapper config)
        {
            // Use ApplicationTokenProvider.LoginSilentWithCertificateAsync or UserTokenProvider.LoginSilentAsync to get a token using service principal with certificate
            //// ClientAssertionCertificate
            //// ApplicationTokenProvider.LoginSilentWithCertificateAsync

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symetric key
            ClientCredential clientCredential = new ClientCredential(config.AadClientId, config.AadSecret);
            return await ApplicationTokenProvider.LoginSilentAsync(config.AadTenantId, clientCredential, ActiveDirectoryServiceSettings.Azure);
        }

        private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
        {
            var credentials = await GetCredentialsAsync(config);

            return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
            {
                SubscriptionId = config.SubscriptionId,
            };
        }

    }
}
```

## <a name="next-steps"></a>Passos Seguintes

- [Tutorial: Carregar, codificar e transmitir vídeos - .NET](stream-files-tutorial-with-api.md) 
- [Tutorial: Stream em direto com o serviços de multimédia v3 - .NET](stream-live-tutorial-with-api.md)
- [Tutorial: Analisar vídeos com os serviços de multimédia v3 - .NET](analyze-videos-tutorial-with-api.md)
- [Criar uma entrada da tarefa a partir de um ficheiro local - .NET](job-input-from-local-file-how-to.md)
- [Criar uma entrada da tarefa a partir de um URL HTTPS - .NET](job-input-from-http-how-to.md)
- [Codificar com uma transformação personalizados - .NET](customize-encoder-presets-how-to.md)
- [Utilizar a encriptação dinâmica de AES-128 e o serviço de entrega de chave - .NET](protect-with-aes128.md)
- [Utilizar DRM dinâmico licença e de encriptação de serviço de entrega - .NET](protect-with-drm.md)
- [Obter uma chave de assinatura da política existente - .NET](get-content-key-policy-dotnet-howto.md)
- [Criar filtros com serviços de multimédia - .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Advanced vídeo sob demanda exemplos de funções do Azure v2 com serviços de multimédia v3](https://aka.ms/ams3functions)

## <a name="see-also"></a>Consulte também

[Referência de .NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
