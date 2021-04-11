---
title: Desenvolver funções de Azure com Serviços de Mídia v3
description: Este tópico mostra como começar a desenvolver Funções Azure com Serviços de Mídia v3 usando o portal Azure.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.devlang: dotnet
ms.topic: article
ms.date: 03/22/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 389ad34bb856675dfabd761507ed07cc722c032a
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281969"
---
# <a name="develop-azure-functions-with-media-services-v3"></a>Desenvolver funções de Azure com Serviços de Mídia v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo mostra-lhe como começar a criar Funções Azure que utilizam os Media Services. A Função Azure definida neste artigo monitoriza um recipiente de conta de armazenamento nomeado **entrada** para novos ficheiros MP4. Uma vez que um ficheiro é deixado no recipiente de armazenamento, o gatilho blob executa a função. Para rever as Funções Azure, consulte [a Visão Geral](../../azure-functions/functions-overview.md) e outros tópicos na secção **Funções Azure.**

Se pretende explorar e implementar as Funções Azure existentes que utilizam os Serviços de Mídia Azure, consulte as [Funções Azure dos Serviços de Media](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration). Este repositório contém exemplos que utilizam os Serviços de Mídia para mostrar fluxos de trabalho relacionados com a ingestão de conteúdos diretamente do armazenamento de bolhas, codificação e escrevendo conteúdo de volta para o armazenamento de bolhas.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de poder criar a sua primeira função, tem de ter uma conta do Azure ativa. Se ainda não tiver uma conta do Azure, [estão disponíveis contas gratuitas](https://azure.microsoft.com/free/).
- Se vai criar Funções Azure que executam ações na sua conta Azure Media Services (AMS) ou ouvir eventos enviados pelos Media Services, deverá criar uma conta AMS, como descrito [aqui.](account-create-how-to.md)

## <a name="create-a-function-app"></a>Criar uma aplicação de funções

1. Aceda ao [portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.
2. Crie uma aplicação de função como descrito [aqui.](../../azure-functions/functions-create-function-app-portal.md)

>[!NOTE]
> Uma conta de armazenamento que especifique deve estar na mesma região que a sua aplicação.

## <a name="configure-function-app-settings"></a>Configurar definições de aplicativos de função

Ao desenvolver funções de Media Services, é útil adicionar variáveis ambientais que serão usadas ao longo das suas funções. Para configurar as definições de aplicações, clique no link Configurações de Configurações de Aplicações Configure. Para obter mais informações, consulte  [como configurar as definições da aplicação Azure Function](../../azure-functions/functions-how-to-use-azure-function-app-settings.md).

## <a name="create-a-function"></a>Criar uma função

Uma vez implementada a sua aplicação de função, pode encontrá-la entre as Funções Azure **dos Serviços de Aplicação.**

1. Selecione a sua aplicação de função e clique em **Nova Função.**
1. Escolha o cenário de idioma **C#** e **processamento de dados.**
1. Escolha o modelo **BlobTrigger.** Esta função é ativada sempre que uma bolha é enviada para o recipiente **de entrada.** O nome **de entrada** é especificado no **Caminho,** no passo seguinte.
1. Assim que selecionar **BlobTrigger,** aparecem mais alguns controlos na página.
1. Clique em **Criar**.

## <a name="files"></a>Ficheiros

A sua Função Azure está associada a ficheiros de código e outros ficheiros descritos nesta secção. Quando utilizar o portal Azure para criar uma função, **function.js** e **executar.csx** são criados para si. Tem de adicionar ou carregar uma **project.jsno** ficheiro. O resto desta secção dá uma breve explicação de cada ficheiro e mostra as suas definições.

### <a name="functionjson"></a>function.json

O function.jsno ficheiro define as ligações de funções e outras definições de configuração. O tempo de execução utiliza este ficheiro para determinar os eventos para monitorizar e como transmitir dados e devolver dados da execução da função. Para obter mais informações, veja [Azure Functions HTTP and webhook bindings](../../azure-functions/functions-reference.md#function-code) (Enlaces de HTTP e webhook das Funções do Azure).

>[!NOTE]
>Desative a propriedade **desativada** **para** evitar que a função seja executada.

Substitua o conteúdo do function.jsexistente em ficheiro pelo seguinte código:

```json
{
  "bindings": [
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "input/{filename}.mp4",
      "connection": "ConnectionString"
    }
  ],
  "disabled": false
}
```

### <a name="projectjson"></a>project.jsem

O project.jsno ficheiro contém dependências. Aqui está um exemplo de **project.jsem** ficheiro que inclui os pacotes necessários .NET Azure Media Services da NuGet. De notar que os números da versão mudam com as últimas atualizações para os pacotes, pelo que deverá confirmar as versões mais recentes.

Adicione a seguinte definição para project.js.

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "windowsazure.mediaservices": "4.0.0.4",
        "windowsazure.mediaservices.extensions": "4.0.0.4",
        "Microsoft.IdentityModel.Clients.ActiveDirectory": "3.13.1",
        "Microsoft.IdentityModel.Protocol.Extensions": "1.0.2.206221351"
      }
    }
   }
}

```

### <a name="runcsx"></a>run.csx

Este é o código C# para a sua função.  A função definida abaixo monitoriza um recipiente de conta de armazenamento denominado **entrada** (que é o que foi especificado no caminho) para novos ficheiros MP4. Uma vez que um ficheiro é deixado no recipiente de armazenamento, o gatilho blob executa a função.

O exemplo definido nesta secção demonstra:

1. Como ingerir um ativo numa conta de Serviços de Comunicação (copiando uma bolha num ativo AMS)
2. Como submeter um trabalho de codificação que utiliza a predefinição "Adaptive Streaming" da Media Encoder Standard

Substitua o conteúdo do ficheiro run.csx existente pelo seguinte código: Uma vez terminada a definição da sua função clique em **Guardar e Executar**.

```csharp
#r "Microsoft.WindowsAzure.Storage"
#r "Newtonsoft.Json"
#r "System.Web"

using System;
using System.Net;
using System.Net.Http;
using Newtonsoft.Json;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.IO;
using System.Web;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.Azure.WebJobs;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
  
// Read values from the App.config file.

static readonly string _AADTenantDomain = Environment.GetEnvironmentVariable("AMSAADTenantDomain");
static readonly string _RESTAPIEndpoint = Environment.GetEnvironmentVariable("AMSRESTAPIEndpoint");
 
static readonly string _mediaservicesClientId = Environment.GetEnvironmentVariable("AMSClientId");
static readonly string _mediaservicesClientSecret = Environment.GetEnvironmentVariable("AMSClientSecret");

static readonly string _connectionString = Environment.GetEnvironmentVariable("ConnectionString");  

private static CloudMediaContext _context = null;
private static CloudStorageAccount _destinationStorageAccount = null;

public static void Run(CloudBlockBlob myBlob, string fileName, TraceWriter log)
{
    // NOTE that the variables {fileName} here come from the path setting in function.json
    // and are passed into the  Run method signature above. We can use this to make decisions on what type of file
    // was dropped into the input container for the function. 

    // No need to do any Retry strategy in this function, By default, the SDK calls a function up to 5 times for a 
    // given blob. If the fifth try fails, the SDK adds a message to a queue named webjobs-blobtrigger-poison.

    log.Info($"C# Blob trigger function processed: {fileName}.mp4");
    log.Info($"Media Services REST endpoint : {_RESTAPIEndpoint}");

    try
    {
        AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain,
                            new AzureAdClientSymmetricKey(_mediaservicesClientId, _mediaservicesClientSecret),
                            AzureEnvironments.AzureCloudEnvironment);
 
        AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);
 
        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

        IAsset newAsset = CreateAssetFromBlob(myBlob, fileName, log).GetAwaiter().GetResult();

        // Step 2: Create an Encoding Job

        // Declare a new encoding job with the Standard encoder
        IJob job = _context.Jobs.Create("Azure Function - MES Job");

        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Create a task with the encoding details, using a custom preset
        ITask task = job.Tasks.AddNew("Encode with Adaptive Streaming",
            processor,
            "Adaptive Streaming",
            TaskOptions.None); 

        // Specify the input asset to be encoded.
        task.InputAssets.Add(newAsset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is not encrypted. 
        task.OutputAssets.AddNew(fileName, AssetCreationOptions.None);

        job.Submit();
        log.Info("Job Submitted");

    }
    catch (Exception ex)
    {
        log.Error("ERROR: failed.");
        log.Info($"StackTrace : {ex.StackTrace}");
        throw ex;
    }
}

private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}

public static async Task<IAsset> CreateAssetFromBlob(CloudBlockBlob blob, string assetName, TraceWriter log){
    IAsset newAsset = null;

    try{
        Task<IAsset> copyAssetTask = CreateAssetFromBlobAsync(blob, assetName, log);
        newAsset = await copyAssetTask;
        log.Info($"Asset Copied : {newAsset.Id}");
    }
    catch(Exception ex){
        log.Info("Copy Failed");
        log.Info($"ERROR : {ex.Message}");
        throw ex;
    }

    return newAsset;
}

/// <summary>
/// Creates a new asset and copies blobs from the specifed storage account.
/// </summary>
/// <param name="blob">The specified blob.</param>
/// <returns>The new asset.</returns>
public static async Task<IAsset> CreateAssetFromBlobAsync(CloudBlockBlob blob, string assetName, TraceWriter log)
{
     //Get a reference to the storage account that is associated with the Media Services account. 
    _destinationStorageAccount = CloudStorageAccount.Parse(_connectionString);

    // Create a new asset. 
    var asset = _context.Assets.Create(blob.Name, AssetCreationOptions.None);
    log.Info($"Created new asset {asset.Name}");

    IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
    TimeSpan.FromHours(4), AccessPermissions.Write);
    ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
    CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

    // Get the destination asset container reference
    string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
    CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);

    try{
    assetContainer.CreateIfNotExists();
    }
    catch (Exception ex)
    {
    log.Error ("ERROR:" + ex.Message);
    }

    log.Info("Created asset.");

    // Get hold of the destination blob
    CloudBlockBlob destinationBlob = assetContainer.GetBlockBlobReference(blob.Name);

    // Copy Blob
    try
    {
    using (var stream = await blob.OpenReadAsync()) 
    {            
        await destinationBlob.UploadFromStreamAsync(stream);          
    }

    log.Info("Copy Complete.");

    var assetFile = asset.AssetFiles.Create(blob.Name);
    assetFile.ContentFileSize = blob.Properties.Length;
    assetFile.IsPrimary = true;
    assetFile.Update();
    asset.Update();
    }
    catch (Exception ex)
    {
    log.Error(ex.Message);
    log.Info (ex.StackTrace);
    log.Info ("Copy Failed.");
    throw;
    }

    destinationLocator.Delete();
    writePolicy.Delete();

    return asset;
}
```

## <a name="test-your-function"></a>Testar a função

Para testar a sua função, é necessário enviar um ficheiro MP4 para o recipiente de **entrada** da conta de armazenamento que especificou na cadeia de ligação.  

1. Selecione a conta de armazenamento especificada.
2. Clique **em Blobs**.
3. Clique em **+ Contentor**. Nomeie a **entrada** do recipiente .
4. Prima **upload** e navegue num ficheiro .mp4 que pretende carregar.

>[!NOTE]
> Quando se está a usar um gatilho de bolhas num plano de Consumo, pode haver até um atraso de 10 minutos no processamento de novas bolhas depois de uma aplicação de função ter ficado inativa. Após o funcionamento da aplicação de função, as bolhas são processadas imediatamente. Para obter mais informações, consulte [os gatilhos e encadernações de armazenamento Blob](../../azure-functions/functions-bindings-storage-blob.md).

## <a name="next-steps"></a>Passos seguintes

Neste momento, está pronto para começar a desenvolver uma aplicação de Serviços de Mídia.

Para mais detalhes e amostras/soluções completas de utilização de Funções Azure e Aplicações Lógicas com Serviços Azure Media para criar fluxos de trabalho de criação de conteúdo personalizado, consulte as [Funções Azure dos Serviços de Media](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration)
