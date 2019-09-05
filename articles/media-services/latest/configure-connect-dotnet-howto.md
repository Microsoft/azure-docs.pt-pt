---
title: Conectar-se à API dos serviços de mídia do Azure v3-.NET
description: Saiba como se conectar à API dos serviços de mídia v3 com .NET.
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
ms.openlocfilehash: 3ddf5a1ab37ac0af25379394b4513627139fcbd5
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307942"
---
# <a name="connect-to-media-services-v3-api---net"></a>Conectar-se à API dos serviços de mídia v3-.NET

Este artigo mostra como se conectar ao SDK do .NET dos serviços de mídia do Azure v3 usando o método de logon da entidade de serviço.

## <a name="prerequisites"></a>Pré-requisitos

- [Criar uma conta de Media Services](create-account-cli-how-to.md). Lembre-se de lembrar o nome do grupo de recursos e o nome da conta dos serviços de mídia
- Instale uma ferramenta que você gostaria de usar para o desenvolvimento do .NET. As etapas neste artigo mostram como usar o [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Você pode usar Visual Studio Code, consulte [trabalhando com C# ](https://code.visualstudio.com/docs/languages/csharp). Ou, você pode usar um editor de código diferente.

> [!IMPORTANT]
> Examine as [convenções de nomenclatura](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-console-application"></a>Criar uma aplicação de consola

1. Inicie o Visual Studio. 
1. No menu **arquivo** , clique em **novo** > **projeto**. 
1. Crie um aplicativo de console do **.NET Core** .

O aplicativo de exemplo neste tópico, targets `netcoreapp2.0`. O código usa ' async Main ', que está disponível a partir C# de 7,1. Consulte este [blog](https://blogs.msdn.microsoft.com/benwilli/2017/12/08/async-main-is-available-but-hidden/) para obter mais detalhes.

## <a name="add-required-nuget-packages"></a>Adicionar pacotes NuGet necessários

1. No Visual Studio, selecione **ferramentas** > **Gerenciador** > de pacotes NuGet**console do Gerenciador do NuGet**.
2. Na janela do **console do Gerenciador** de pacotes `Install-Package` , use o comando para adicionar os seguintes pacotes NuGet. Por exemplo, `Install-Package Microsoft.Azure.Management.Media`.

|Pacote|Descrição|
|---|---|
|`Microsoft.Azure.Management.Media`|SDK dos serviços de mídia do Azure. <br/>Para verificar se você está usando o pacote mais recente dos serviços de mídia do Azure, verifique [Microsoft. Azure. Management. Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media).|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|Biblioteca de autenticação ADAL para SDK do Azure para NET|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|Ler valores de configuração de variáveis de ambiente e arquivos JSON locais|
|`Microsoft.Extensions.Configuration.Json`|Ler valores de configuração de variáveis de ambiente e arquivos JSON locais
|`WindowsAzure.Storage`|SDK de armazenamento|

## <a name="create-and-configure-the-app-settings-file"></a>Criar e configurar o arquivo de configurações do aplicativo

### <a name="create-appsettingsjson"></a>Criar appSettings. JSON

1. Ir para o**arquivo de texto** **geral** > .
1. Nomeie-o como "appSettings. JSON".
1. Defina a propriedade "copiar para o diretório de saída" do arquivo. JSON como "copiar se for mais recente" (para que o aplicativo possa acessá-lo quando publicado).

### <a name="set-values-in-appsettingsjson"></a>Definir valores em appSettings. JSON

Execute o `az ams account sp create` comando conforme descrito em [APIs de acesso](access-api-cli-how-to.md). O comando retorna JSON que você deve copiar em "appSettings. JSON".
 
## <a name="add-configuration-file"></a>Adicionar o ficheiro de configuração

Para sua conveniência, adicione um arquivo de configuração que seja responsável por ler valores de "appSettings. JSON".

1. Adicione uma nova classe. cs ao seu projeto. Dê-lhe o nome `ConfigWrapper`. 
1. Cole o código a seguir neste arquivo (Este exemplo pressupõe que você tem o namespace `ConsoleApp1`).

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

## <a name="connect-to-the-net-client"></a>Conectar-se ao cliente .NET

Para começar a utilizar as APIs dos Serviços de Multimédia com o .NET, tem de criar um objeto **AzureMediaServicesClient**. Para criar o objeto, tem de fornecer as credenciais necessárias para o cliente se ligar ao Azure com o Azure AD. No código a seguir, a função GetCredentialsAsync cria o objeto createclientcredentials com base nas credenciais fornecidas no arquivo de configuração local.

1. Abra `Program.cs`o.
1. Cole o código a seguir:

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

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symmetric key
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

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Carregar, codificar e transmitir vídeos-.NET](stream-files-tutorial-with-api.md) 
- [Tutorial: Transmitir ao vivo com os serviços de mídia v3-.NET](stream-live-tutorial-with-api.md)
- [Tutorial: Analisar vídeos com os serviços de mídia v3-.NET](analyze-videos-tutorial-with-api.md)
- [Criar uma entrada de trabalho de um arquivo local-.NET](job-input-from-local-file-how-to.md)
- [Criar uma entrada de trabalho de uma URL HTTPS-.NET](job-input-from-http-how-to.md)
- [Codificar com uma transformação personalizada-.NET](customize-encoder-presets-how-to.md)
- [Usar a criptografia dinâmica AES-128 e o serviço de distribuição de chaves-.NET](protect-with-aes128.md)
- [Usar a criptografia dinâmica de DRM e o serviço de entrega de licença-.NET](protect-with-drm.md)
- [Obter uma chave de assinatura da política existente-.NET](get-content-key-policy-dotnet-howto.md)
- [Criar filtros com os serviços de mídia-.NET](filters-dynamic-manifest-dotnet-howto.md)
- [Exemplos avançados de vídeo sob demanda de Azure Functions V2 com os serviços de mídia v3](https://aka.ms/ams3functions)

## <a name="see-also"></a>Consulte também

[.NET reference](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet) (Referência de .NET)
