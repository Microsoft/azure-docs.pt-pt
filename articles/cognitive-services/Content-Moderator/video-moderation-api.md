---
title: Analise o conteúdo de vídeo para materiais C# censuráveis Content moderator
titleSuffix: Azure Cognitive Services
description: Como analisar o conteúdo de vídeo para vários materiais censuráveis usando o SDK do Content Moderator para .NET
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 71858755fe31823d4d7ef8623b915db851530116
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755243"
---
# <a name="analyze-video-content-for-objectionable-material-in-c"></a>Analise o conteúdo de vídeo para materiais censuráveis noC#

Este artigo fornece informações e exemplos de código para ajudá-lo a começar a usar o [SDK do Content moderator para .net](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) para verificar o conteúdo de vídeo para conteúdo adulto ou erótico.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos
- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/)

## <a name="set-up-azure-resources"></a>Configurar recursos do Azure

A funcionalidade de moderação de vídeo do Content Moderator está disponível como um **processador de mídia** de visualização pública gratuito no AMS (serviços de mídia do Azure). Os serviços de mídia do Azure são um serviço especializado do Azure para armazenar e transmitir conteúdo de vídeo. 

### <a name="create-an-azure-media-services-account"></a>Criar uma conta dos Azure Media Services

Siga as instruções em [criar uma conta dos serviços de mídia do Azure](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account) para assinar o AMS e criar uma conta de armazenamento do Azure associada. Nessa conta de armazenamento, crie um novo contêiner de armazenamento de BLOBs.

### <a name="create-an-azure-active-directory-application"></a>Criar um aplicativo Azure Active Directory

Navegue até sua nova assinatura do AMS no portal do Azure e selecione **acesso à API** no menu lateral. Selecione **conectar-se aos serviços de mídia do Azure com a entidade de serviço**. Observe o valor no campo de **ponto de extremidade da API REST** ; Você precisará disso mais tarde.

Na seção **aplicativo do Azure ad** , selecione **criar novo** e nomeie seu novo registro de aplicativo do Azure AD (por exemplo, "VideoModADApp"). Clique em **salvar** e aguarde alguns minutos enquanto o aplicativo está configurado. Em seguida, você deverá ver o novo registro do aplicativo na seção **aplicativo do Azure ad** da página.

Selecione o registro do aplicativo e clique no botão **gerenciar aplicativo** abaixo dele. Observe o valor no campo **ID do aplicativo** ; Você precisará disso mais tarde. Selecione **configurações**  > **chaves**e insira uma descrição para uma nova chave (como "VideoModKey"). Clique em **salvar**e, em seguida, observe o novo valor de chave. Copie essa cadeia de caracteres e salve-a em algum lugar seguro.

Para obter uma explicação mais completa do processo acima, consulte Introdução [à autenticação do Azure ad](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad).

Depois de fazer isso, você pode usar o processador de mídia de moderação de vídeo de duas maneiras diferentes.

## <a name="use-azure-media-services-explorer"></a>Usar o Gerenciador de serviços de mídia do Azure

O Gerenciador de serviços de mídia do Azure é um front-end amigável para o AMS. Use-o para procurar sua conta do AMS, carregar vídeos e verificar o conteúdo com o processador de mídia Content Moderator. Baixe e instale-o no [GitHub](https://github.com/Azure/Azure-Media-Services-Explorer/releases)ou consulte a [postagem no blog do Gerenciador de serviços de mídia do Azure](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) para obter mais informações.

![Gerenciador de serviços de mídia do Azure com Content Moderator](images/ams-explorer-content-moderator.PNG)

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

1. No Visual Studio, crie um novo projeto de **aplicativo de console (.NET Framework)** e nomeie-o **VideoModeration**. 
1. Se houver outros projetos na sua solução, selecione esta como o único projeto de arranque.
1. Obtenha os pacotes NuGet necessários. Clique com o botão direito do rato no seu projeto no Explorador de Soluções e selecione **Manage NuGet Packages** (Gerir Pacotes NuGet); em seguida, localize e instale os pacotes seguintes:
    - WindowsAzure. mediaservices
    - extensões de WindowsAzure. mediaservices.

## <a name="add-video-moderation-code"></a>Adicionar código de moderação de vídeo

Depois, vai copiar e colar o código neste guia no projeto, para implementar um cenário de moderação de conteúdos simples.

### <a name="update-the-programs-using-statements"></a>Atualizar as instruções de utilização do programa

Adicione as declarações `using` seguintes à parte superior do ficheiro _Program.cs_.

```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using System.Collections.Generic;
```

### <a name="set-up-resource-references"></a>Configurar referências de recurso

Adicione os seguintes campos estáticos à classe **Programa** em _Program.cs_. Esses campos mantêm as informações necessárias para se conectar à sua assinatura do AMS. Preencha-os com os valores que você obteve nas etapas acima. Observe que `CLIENT_ID` é o valor da **ID do aplicativo** do seu aplicativo do Azure AD e `CLIENT_SECRET` é o valor do "VideoModKey" que você criou para esse aplicativo.

```csharp
// declare constants and globals
private static CloudMediaContext _context = null;
private static CloudStorageAccount _StorageAccount = null;

// Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
// a list of Blobs to be processed.
static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

private static StorageCredentials _StorageCredentials = null;

// Azure Media Services authentication. 
private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
private const string CLIENT_ID = "YOUR CLIENT ID";
private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

// REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

// Content Moderator Media Processor Nam
private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

// Input and Output files in the current directory of the executable
private const string INPUT_FILE = "VIDEO FILE NAME";
private const string OUTPUT_FOLDER = "";

// JSON settings file
private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

```

Se você quiser usar um arquivo de vídeo local (caso mais simples), adicione-o ao projeto e insira seu caminho como o valor de `INPUT_FILE` (caminhos relativos são relativos ao diretório de execução).

Você também precisará criar o arquivo _predefinido. JSON_ no diretório atual e usá-lo para especificar um número de versão. Por exemplo:

```JSON
{
    "version": "2.0"
}
```

### <a name="load-the-input-videos"></a>Carregar vídeo (s) de entrada

O método **Main** da classe **Program** criará um contexto de mídia do Azure e, em seguida, um contexto de armazenamento do Azure (caso seus vídeos estejam no armazenamento de BLOBs). O código restante examina um vídeo de uma pasta local, BLOB ou vários BLOBs dentro de um contêiner de armazenamento do Azure. Você pode experimentar todas as opções comentando as outras linhas de código.

```csharp
// Create Azure Media Context
CreateMediaContext();

// Create Storage Context
CreateStorageContext();

// Use a file as the input.
IAsset asset = CreateAssetfromFile();

// -- OR ---

// Or a blob as the input
// IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

// Then submit the asset to Content Moderator
RunContentModeratorJob(asset);

//-- OR ----

// Just run the content moderator on all blobs in a list (from a Blob Container)
// RunContentModeratorJobOnBlobs();
```

### <a name="create-an-azure-media-context"></a>Criar um contexto de mídia do Azure

Adicione o seguinte método à classe **Programa**. Isso usa suas credenciais do AMS para permitir a comunicação com o AMS.

```csharp
// Creates a media context from azure credentials
static void CreateMediaContext()
{
    // Get Azure AD credentials
    var tokenCredentials = new AzureAdTokenCredentials(AZURE_AD_TENANT_NAME,
        new AzureAdClientSymmetricKey(CLIENT_ID, CLIENT_SECRET),
        AzureEnvironments.AzureCloudEnvironment);

    // Initialize an Azure AD token
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

    // Create a media context
    _context = new CloudMediaContext(new Uri(REST_API_ENDPOINT), tokenProvider);
}
```

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Adicionar o código para criar um contexto de armazenamento do Azure

Adicione o seguinte método à classe **Programa**. Você usa o contexto de armazenamento, criado com base nas suas credenciais de armazenamento, para acessar o armazenamento de BLOBs.

```csharp
// Creates a storage context from the AMS associated storage name and key
static void CreateStorageContext()
{
    // Get a reference to the storage account associated with a Media Services account. 
    if (_StorageCredentials == null)
    {
        _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
    }
    _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
}
```

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Adicione o código para criar ativos de mídia do Azure do arquivo local e do blob

O processador de mídia Content Moderator executa trabalhos em **ativos** na plataforma de serviços de mídia do Azure.
Esses métodos criam os ativos de um arquivo local ou de um blob associado.

```csharp
// Creates an Azure Media Services Asset from the video file
static IAsset CreateAssetfromFile()
{
    return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
}

// Creates an Azure Media Services asset from your blog storage
static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
{
    // Create asset from the FIRST blob in the list and return it
    return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
}
```

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>Adicione o código para verificar uma coleção de vídeos (como BLOBs) dentro de um contêiner

```csharp
// Runs the Content Moderator Job on all Blobs in a given container name
static void RunContentModeratorJobOnBlobs()
{
    // Get the reference to the list of Blobs. See the following method.
    var blobList = GetBlobsList();

    // Iterate over the Blob list items or work on specific ones as needed
    foreach (var sourceBlob in blobList)
    {
        // Create an Asset
        IAsset asset = _context.Assets.CreateFromBlob((CloudBlockBlob)sourceBlob,
                            _StorageCredentials, AssetCreationOptions.None);
        asset.Update();

        // Submit to Content Moderator
        RunContentModeratorJob(asset);
    }
}

// Get all blobs in your container
static IEnumerable<IListBlobItem> GetBlobsList()
{
    // Get a reference to the Container within the Storage Account
    // that has the files (blobs) for moderation
    CloudBlobClient CloudBlobClient = _StorageAccount.CreateCloudBlobClient();
    CloudBlobContainer MediaBlobContainer = CloudBlobClient.GetContainerReference(STORAGE_CONTAINER_NAME);

    // Get the reference to the list of Blobs 
    var blobList = MediaBlobContainer.ListBlobs();
    return blobList;
}
```

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Adicionar o método para executar o trabalho de Content Moderator

```csharp
// Run the Content Moderator job on the designated Asset from local file or blob storage
static void RunContentModeratorJob(IAsset asset)
{
    // Grab the presets
    string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

    // grab instance of Azure Media Content Moderator MP
    IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

    // create Job with Content Moderator task
    IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
            asset.AssetFiles.First() + "_" + Guid.NewGuid()));

    ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
            mp, configuration,
            TaskOptions.None);
    contentModeratorTask.InputAssets.Add(asset);
    contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
        AssetCreationOptions.None);

    job.Submit();


    // Create progress printing and querying tasks
    Task progressPrintTask = new Task(() =>
    {
        IJob jobQuery = null;
        do
        {
            var progressContext = _context;
            jobQuery = progressContext.Jobs
            .Where(j => j.Id == job.Id)
                .First();
                Console.WriteLine(string.Format("{0}\t{1}",
                DateTime.Now,
                jobQuery.State));
                Thread.Sleep(10000);
            }
            while (jobQuery.State != JobState.Finished &&
            jobQuery.State != JobState.Error &&
            jobQuery.State != JobState.Canceled);
    });
    progressPrintTask.Start();

    Task progressJobTask = job.GetExecutionProgressTask(
    CancellationToken.None);
    progressJobTask.Wait();

    // If job state is Error, the event handling 
    // method for job progress should log errors.  Here we check 
    // for error state and exit if needed.
    if (job.State == JobState.Error)
    {
        ErrorDetail error = job.Tasks.First().ErrorDetails.First();
        Console.WriteLine(string.Format("Error: {0}. {1}",
        error.Code,
        error.Message));
    }

    DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
}
```

### <a name="add-helper-functions"></a>Adicionar funções auxiliares

Esses métodos baixam o arquivo de saída Content Moderator (JSON) do ativo dos serviços de mídia do Azure e ajudam a acompanhar o status do trabalho de moderação para que o programa possa registrar em log um status de execução no console.

```csharp
static void DownloadAsset(IAsset asset, string outputDirectory)
{
    foreach (IAssetFile file in asset.AssetFiles)
    {
        file.Download(Path.Combine(outputDirectory, file.Name));
    }
}

// event handler for Job State
static void StateChanged(object sender, JobStateChangedEventArgs e)
{
    Console.WriteLine("Job state changed event:");
    Console.WriteLine("  Previous state: " + e.PreviousState);
    Console.WriteLine("  Current state: " + e.CurrentState);
    switch (e.CurrentState)
    {
        case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("Job finished.");
            break;
        case JobState.Canceling:
        case JobState.Queued:
        case JobState.Scheduled:
        case JobState.Processing:
            Console.WriteLine("Please wait...\n");
            break;
        case JobState.Canceled:
            Console.WriteLine("Job is canceled.\n");
            break;
        case JobState.Error:
            Console.WriteLine("Job failed.\n");
            break;
        default:
            break;
    }
}
```

### <a name="run-the-program-and-review-the-output"></a>Executar o programa e rever o resultado

Depois que o trabalho de moderação de conteúdo for concluído, analise a resposta JSON. Ele consiste nesses elementos:

- Resumo das informações do vídeo
- **Capturas** como "**fragmentos**"
- **Quadros-chave** como "**eventos**" com um sinalizador de **reviewRecommended "(= true ou false)"** com base nas pontuações de **adulto** e **erótico**
- **início**, **duração**, **totalDuration**e **carimbo de data/hora** estão em "tiques". Divida por **escala** de tempo para obter o número em segundos.
 
> [!NOTE]
> - `adultScore` representa a possível presença e a pontuação de previsão de conteúdo que pode ser considerada sexualmente explícita ou adulto em determinadas situações.
> - `racyScore` representa a possível presença e a pontuação de previsão de conteúdo que pode ser considerada sexualmente sugerida ou amadurece em determinadas situações.
> - `adultScore` e `racyScore` estão entre 0 e 1. Quanto maior a pontuação, mais alto o modelo está prevendo que a categoria pode ser aplicável. Essa visualização depende de um modelo estatístico em vez de resultados codificados manualmente. É recomendável testar com seu próprio conteúdo para determinar como cada categoria se alinha aos seus requisitos.
> - `reviewRecommended` é true ou false, dependendo dos limites de Pontuação internos. Os clientes devem avaliar se esse valor deve ser usado ou decidir sobre limites personalizados com base em suas políticas de conteúdo.

```json
{
"version": 2,
"timescale": 90000,
"offset": 0,
"framerate": 50,
"width": 1280,
"height": 720,
"totalDuration": 18696321,
"fragments": [
{
    "start": 0,
    "duration": 18000
},
{
    "start": 18000,
    "duration": 3600,
    "interval": 3600,
    "events": [
    [
        {
        "reviewRecommended": false,
        "adultScore": 0.00001,
        "racyScore": 0.03077,
        "index": 5,
        "timestamp": 18000,
        "shotIndex": 0
        }
    ]
    ]
},
{
    "start": 18386372,
    "duration": 119149,
    "interval": 119149,
    "events": [
    [
        {
        "reviewRecommended": true,
        "adultScore": 0.00000,
        "racyScore": 0.91902,
        "index": 5085,
        "timestamp": 18386372,
        "shotIndex": 62
        }
    ]
    ]
}
]
}
```

## <a name="next-steps"></a>Passos seguintes

Saiba como gerar [revisões de vídeo](video-reviews-quickstart-dotnet.md) de sua saída de moderação.

Adicione [moderação de transcrição](video-transcript-moderation-review-tutorial-dotnet.md) às suas revisões de vídeo.

Confira o tutorial detalhado sobre como criar uma [solução de moderação de vídeo e transcrição completa](video-transcript-moderation-review-tutorial-dotnet.md).

[Baixe a solução do Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) para este e outros guias de início rápido Content moderator para .net.
