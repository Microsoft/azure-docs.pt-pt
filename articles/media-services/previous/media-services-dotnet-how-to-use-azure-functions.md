---
title: Desenvolver funções azure com serviços de mídia
description: Este tópico mostra como começar a desenvolver funções Azure com serviços de mídia utilizando o portal Azure.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 618acae10b874eb5ebd5b6da7fe081368528dbd8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78397236"
---
# <a name="develop-azure-functions-with-media-services"></a>Desenvolver funções azure com serviços de mídia

Este artigo mostra-lhe como começar com a criação de Funções Azure que utilizam os Serviços de Media. A Função Azure definida neste artigo monitoriza um recipiente de conta de armazenamento denominado **entrada** para novos ficheiros MP4. Uma vez que um ficheiro é lançado no recipiente de armazenamento, o gatilho de bolha executa a função. Para rever as funções do Azure, consulte a [visão geral](../../azure-functions/functions-overview.md) e outros tópicos na secção **de funções Do Azure.**

Se quiser explorar e implementar as funções azure existentes que utilizam os Serviços Azure Media, consulte as [Funções Azure dos Media Services](https://github.com/Azure-Samples/media-services-dotnet-functions-integration). Este repositório contém exemplos que utilizam os Serviços de Media para mostrar fluxos de trabalho relacionados com a ingestão de conteúdos diretamente do armazenamento de blob, codificação e conteúdo de escrita de volta ao armazenamento de blob. Também inclui exemplos de como monitorizar notificações de emprego através de WebHooks e Filas Azure. Também pode desenvolver as suas Funções com base nos exemplos do repositório [de Funções Azure dos Serviços de Media.](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) Para implementar as funções, prima o botão **'Deploy' para Azure.**

## <a name="prerequisites"></a>Pré-requisitos

- Antes de poder criar a sua primeira função, tem de ter uma conta do Azure ativa. Se ainda não tiver uma conta do Azure, [estão disponíveis contas gratuitas](https://azure.microsoft.com/free/).
- Se vai criar Funções Azure que realizem ações na sua conta Azure Media Services (AMS) ou ouça os eventos enviados pelos Media Services, deverá criar uma conta AMS, como [aqui](media-services-portal-create-account.md)descrito .
    
## <a name="create-a-function-app"></a>Criar uma aplicação de função

1. Aceda ao [portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.
2. Crie uma aplicação de função como descrito [aqui](../../azure-functions/functions-create-function-app-portal.md).

>[!NOTE]
> Uma conta de armazenamento que especifica na variável ambiente **StorageConnection** (ver o próximo passo) deve estar na mesma região que a sua app.

## <a name="configure-function-app-settings"></a>Configurar as definições da aplicação de funções

Ao desenvolver funções de Serviços de Media, é útil adicionar variáveis ambientais que serão usadas em todas as suas funções. Para configurar as definições da aplicação, clique no link Configurar definições de aplicações. Para mais informações, consulte [como configurar as definições da aplicação Azure Function](../../azure-functions/functions-how-to-use-azure-function-app-settings.md). 

A função, definida neste artigo, assume que tem as seguintes variáveis ambientais nas definições da sua aplicação:

**AMSAADTenantDomain**: Ponto final do inquilino Azure AD. Para mais informações sobre a ligação à AMS API, consulte [este](media-services-use-aad-auth-to-access-ams-api.md) artigo.

**AMSRESTAPIEndpoint**: URI que representa o ponto final da API REST. 

**AMSClientId**: Id do cliente da aplicação Azure AD.

**AMSClientSecret**: Segredo de cliente de aplicação Azure AD.

**ArmazenamentoConexa:** ligação de armazenamento da conta associada à conta media Services. Este valor é utilizado no ficheiro **função.json** e no ficheiro **run.csx** (descrito abaixo).

## <a name="create-a-function"></a>Criar uma função

Uma vez implementada a sua aplicação de funções, pode encontrá-la entre as Funções Do Azure **dos Serviços de Aplicação.**

1. Selecione a sua aplicação de funções e clique em **New Function**.
2. Escolha **C#** o cenário de idioma e processamento de **dados.**
3. Escolha o modelo **BlobTrigger.** Esta função é ativada sempre que uma bolha é colocada no recipiente de **entrada.** O nome de **entrada** é especificado no **Caminho,** no passo seguinte.

    ![files](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. Uma vez selecionado o **BlobTrigger,** aparecem mais alguns controlos na página.

    ![files](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. Clique em **Criar**. 

## <a name="files"></a>Ficheiros

A sua função Azure está associada a ficheiros de código e outros ficheiros descritos nesta secção. Quando utilizar o portal Azure para criar uma função, **função.json** e **run.csx** são criados para si. Precisa adicionar ou carregar um ficheiro **project.json.** O resto desta secção dá uma breve explicação de cada ficheiro e mostra as suas definições.

![files](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

O ficheiro função.json define as encadernações de função e outras definições de configuração. O tempo de execução utiliza este ficheiro para determinar os eventos para monitorizar e como transmitir dados e devolver dados da execução da função. Para mais informações, consulte as [funções Azure HTTP e ligações webhook](../../azure-functions/functions-reference.md#function-code).

>[!NOTE]
>Desative a propriedade **para deficientes** **para** evitar que a função seja executada. 

Substitua o conteúdo do ficheiro função.json existente pelo seguinte código:

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

### <a name="projectjson"></a>project.json

O ficheiro project.json contém dependências. Aqui está um exemplo de ficheiro **project.json** que inclui os pacotes necessários .NET Azure Media Services da Nuget. Note que os números da versão mudam com as últimas atualizações para os pacotes, pelo que deverá confirmar as versões mais recentes. 

Adicione a seguinte definição ao project.json. 

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

Este é C# o código para a sua função.  A função definida abaixo monitoriza um recipiente de conta de armazenamento denominado **entrada** (isto é o que foi especificado no caminho) para novos ficheiros MP4. Uma vez que um ficheiro é lançado no recipiente de armazenamento, o gatilho de bolha executa a função.
    
O exemplo definido nesta secção demonstra 

1. como ingerir um ativo numa conta de Media Services (ao lidar com uma bolha num ativo da AMS) e 
2. como submeter um trabalho de codificação que utilize o predefinido "Adaptive Streaming" da Media Encoder Standard.

No cenário da vida real, provavelmente quer acompanhar o progresso do emprego e depois publicar o seu ativo codificado. Para mais informações, consulte [use Azure WebHooks para monitorizar as notificações](media-services-dotnet-check-job-progress-with-webhooks.md)de emprego dos Serviços de Media . Para mais exemplos, consulte [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).  

Substitua o conteúdo do ficheiro run.csx existente pelo seguinte código: Assim que terminar a definição da função clique em **Guardar e Executar**.

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

Para testar a sua função, é necessário fazer o upload de um ficheiro MP4 para o recipiente de **entrada** da conta de armazenamento que especificou na cadeia de ligação.  

1. Selecione a conta de armazenamento que especificou na variável ambiente **StorageConnection.**
2. Clique em **Blobs**.
3. Clique em **+ Contentor**. Nomeie a **entrada**do recipiente .
4. Prima **Upload** e navegue num ficheiro .mp4 que pretende carregar.

>[!NOTE]
> Quando estiver a usar um gatilho de bolha num plano de consumo, pode haver até 10 minutos de atraso no processamento de novas bolhas depois de uma aplicação de função ter ficado inativa. Após a execução da aplicação de função, as bolhas são processadas imediatamente. Para mais informações, consulte [os gatilhos e encadernações de armazenamento blob](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob).

## <a name="next-steps"></a>Passos seguintes

Neste momento, está pronto para começar a desenvolver uma aplicação de Serviços de Media. 
 
Para mais detalhes e amostras/soluções completas de utilização de Funções Azure e Aplicações Lógicas com Serviços De Mídia Azure para criar fluxos de trabalho de criação de conteúdo personalizado, consulte a Amostra de Integração de [Funções media .NET no GitHub](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

Consulte Também [o Use Azure WebHooks para monitorizar as notificações](media-services-dotnet-check-job-progress-with-webhooks.md)de emprego dos Media Services com .NET . 

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

