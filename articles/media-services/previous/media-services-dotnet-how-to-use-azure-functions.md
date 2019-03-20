---
title: Desenvolver as funções do Azure com os serviços de multimédia
description: Este tópico mostra como começar a desenvolver as funções do Azure com os serviços de multimédia no portal do Azure.
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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 69099fe1369223871142f74d8f6d1c6a8bd66dab
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57851778"
---
# <a name="develop-azure-functions-with-media-services"></a>Desenvolver as funções do Azure com os serviços de multimédia

Este artigo mostra-lhe como começar com a criação de funções do Azure que utilize serviços de multimédia. A função do Azure definidos neste artigo monitoriza um contentor de conta de armazenamento com o nome **entrada** para novos ficheiros MP4. Depois de um ficheiro de cair para o contentor de armazenamento, o acionador de blob executa a função. Para rever as funções do Azure, consulte [descrição geral](../../azure-functions/functions-overview.md) e outros tópicos do **as funções do Azure** secção.

Se quiser explorar e implementar as funções do Azure existentes que utilizam serviços de multimédia do Azure, veja [as funções do Media Services do Azure](https://github.com/Azure-Samples/media-services-dotnet-functions-integration). Este repositório contém exemplos que utilizam os serviços de multimédia para mostrar os fluxos de trabalho relacionadas com a ingestão de conteúdos diretamente do armazenamento de BLOBs, codificação e escrever conteúdo de volta ao armazenamento de Blobs. Ele também inclui exemplos de como monitorizar as notificações de trabalho através de WebHooks e filas do Azure. Também pode desenvolver as suas funções com base nos exemplos a [as funções do Media Services do Azure](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) repositório. Para implementar as funções, prima a **implementar no Azure** botão.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de poder criar a sua primeira função, tem de ter uma conta do Azure ativa. Se ainda não tiver uma conta do Azure, [estão disponíveis contas gratuitas](https://azure.microsoft.com/free/).
- Se pretender criar funções do Azure que efetuar ações na sua conta de serviços de multimédia do Azure (AMS) ou escutar eventos enviados pelos serviços de multimédia, deve criar uma conta de AMS, conforme descrito [aqui](media-services-portal-create-account.md).
    
## <a name="create-a-function-app"></a>Criar uma aplicação de função

1. Aceda ao [portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.
2. Criar uma aplicação de função, conforme descrito [aqui](../../azure-functions/functions-create-function-app-portal.md).

>[!NOTE]
> Uma conta de armazenamento que especificou na **StorageConnection** variável de ambiente (consulte a próxima etapa) deve estar na mesma região que a sua aplicação.

## <a name="configure-function-app-settings"></a>Configurar definições da aplicação de função

Ao desenvolver funções de serviços de multimédia, é útil para adicionar variáveis de ambiente que irão ser utilizadas nas suas funções. Para configurar as definições da aplicação, clique na ligação de configurar as definições da aplicação. Para obter mais informações, consulte [como configurar as definições de aplicação de função do Azure](../../azure-functions/functions-how-to-use-azure-function-app-settings.md). 

A função, definida neste artigo, parte do princípio de que tem as seguintes variáveis de ambiente nas suas definições de aplicação:

**AMSAADTenantDomain**: Endpoint de inquilino do Azure AD. Para obter mais informações sobre como ligar à AMS API, consulte [isso](media-services-use-aad-auth-to-access-ams-api.md) artigo.

**AMSRESTAPIEndpoint**:  URI que representa o ponto final de REST API. 

**AMSClientId**: ID de cliente de aplicação do Azure AD

**AMSClientSecret**: Segredo de cliente de aplicação de Azure AD.

**StorageConnection**: ligação de armazenamento da conta associada à conta dos serviços de multimédia. Este valor é utilizado no **Function** ficheiro e **csx** ficheiro (descrito abaixo).

## <a name="create-a-function"></a>Criar uma função

Depois de implementada a aplicação de função, poderá encontrá-entre **dos serviços de aplicações** as funções do Azure.

1. Selecione a sua aplicação de função e clique em **nova função**.
2. Escolha o **C#** linguagem e **processamento de dados** cenário.
3. Escolher **BlobTrigger** modelo. Esta função é acionada sempre que um blob é carregado para o **entrada** contentor. O **entrada** nome é especificado no **caminho**, no próximo passo.

    ![ficheiros](./media/media-services-azure-functions/media-services-azure-functions004.png)

4. Depois de selecionar **BlobTrigger**, alguns controles mais aparecem na página.

    ![ficheiros](./media/media-services-azure-functions/media-services-azure-functions005.png)

4. Clique em **Criar**. 

## <a name="files"></a>Ficheiros

A função do Azure está associada a arquivos de código e outros ficheiros que são descritos nesta secção. Ao utilizar o portal do Azure para criar uma função **Function** e **csx** são criados para si. Terá de adicionar ou carregar um **Project** ficheiro. O resto desta secção fornece uma breve explicação de cada arquivo e mostra as respetivas definições.

![ficheiros](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

O ficheiro de Function define os enlaces de função e outras definições de configuração. O tempo de execução utiliza este ficheiro para determinar os eventos a monitorizar e como passar dados para e devolver dados de execução de função. Para obter mais informações, consulte [enlaces de HTTP e webhook das funções do Azure](../../azure-functions/functions-reference.md#function-code).

>[!NOTE]
>Definir o **desativada** propriedade **verdadeiro** para impedir que a função a ser executada. 

Substitua os conteúdos do ficheiro Function existente com o código a seguir:

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

O ficheiro de Project contém dependências. Eis um exemplo de **Project** ficheiro que inclua os pacotes de serviços de multimédia do Azure .NET necessários do Nuget. Tenha em atenção que os números de versão alterar com as atualizações mais recentes para os pacotes, para que deve confirmar as versões mais recentes. 

Adicione a seguinte definição para Project. 

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

Este é o C# código para a função.  A função definida abaixo monitores um contentor de conta de armazenamento com o nome **entrada** (que é o que foi especificado no caminho) para novos ficheiros MP4. Depois de um ficheiro de cair para o contentor de armazenamento, o acionador de blob executa a função.
    
O exemplo definido nesta seção demonstra 

1. como a ingerir um recurso para uma conta de serviços de multimédia (por lidar um blob para um elemento de AMS) e 
2. como para submeter um trabalho de codificação que utiliza o Media Encoder Standard "Transmissão em fluxo adaptável" configuração predefinida.

O cenário da vida real, é mais provável que queira acompanhar o progresso da tarefa e, em seguida, publicar o seu elemento codificado. Para obter mais informações, consulte [WebHooks do Azure de utilização para monitorizar as notificações de trabalho dos serviços de multimédia](media-services-dotnet-check-job-progress-with-webhooks.md). Para obter mais exemplos, consulte [as funções do Media Services do Azure](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).  

Substitua o conteúdo do ficheiro Run. csx existente com o código a seguir: Depois de concluída a definir a função clique **salve e execute**.

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

Para testar a sua função, tem de carregar um ficheiro MP4 para o **entrada** contentor da conta de armazenamento que especificou na cadeia de ligação.  

1. Selecione a conta de armazenamento que especificou na **StorageConnection** variável de ambiente.
2. Clique em **Blobs**.
3. Clique em **+ contentor**. Nome do contentor **entrada**.
4. Prima **carregar** e procure um ficheiro. mp4 que pretende carregar.

>[!NOTE]
> Quando estiver a utilizar um acionador de blob num plano de consumo, pode haver até um atraso de 10 minutos no processamento novos blobs, depois de uma aplicação de funções tornou-se inativo. Depois da aplicação de função está em execução, os blobs são processadas imediatamente. Para obter mais informações, consulte [armazenamento acionadores e enlaces de BLOBs](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob).

## <a name="next-steps"></a>Passos Seguintes

Neste momento, está pronto para começar a desenvolver um aplicativo de serviços de multimédia. 
 
Para obter mais detalhes e exemplos/soluções completas de usar as funções do Azure e o Logic Apps com os serviços de multimédia do Azure para criar fluxos de trabalho de criação de conteúdo personalizado, consulte o [exemplo integração funções de .NET de serviços de mídia no GitHub](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

Além disso, veja [WebHooks do Azure de utilização para monitorizar as notificações de trabalho de serviços de multimédia com .NET](media-services-dotnet-check-job-progress-with-webhooks.md). 

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

