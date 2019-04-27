---
title: Analisar o conteúdo de vídeo para obter informações sobre objetáveis no C# -moderador de conteúdo
titlesuffix: Azure Cognitive Services
description: Como analisar o conteúdo de vídeo para diversos material objetáveis utilizando o SDK do moderador de conteúdo para .NET
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 7e987c1249360b14fddf8af57c61fdd1a46ee6c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60605326"
---
# <a name="analyze-video-content-for-objectionable-material-in-c"></a>Analisar o conteúdo de vídeo para objetáveis material doC#

Este artigo fornece informações e exemplos de código para ajudá-lo a começar a utilizar o [moderador de conteúdo SDK para .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) para analisar o conteúdo de vídeo para o conteúdo de adultos.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos
- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/)

## <a name="set-up-azure-resources"></a>Configurar recursos do Azure

A capacidade de moderação de vídeos do Content Moderator está disponível como pré-visualização pública gratuita **processador de multimédia** no Azure Media Services (AMS). Serviços de multimédia do Azure é um serviço do Azure especializado para armazenar e transmitir em fluxo conteúdo de vídeo. 

### <a name="create-an-azure-media-services-account"></a>Criar uma conta dos Azure Media Services

Siga as instruções em [criar uma conta de Media Services do Azure](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account) para subscrever o AMS e criar uma conta de armazenamento do Azure associado. Essa conta de armazenamento, crie um novo contentor de armazenamento de Blobs.

### <a name="create-an-azure-active-directory-application"></a>Criar uma aplicação do Azure Active Directory

Navegue até à sua nova subscrição de AMS no portal do Azure e selecione **acesso à API** no menu do lado. Selecione **ligar a serviços de multimédia do Azure com o principal de serviço**. Observe o valor de **ponto final da API de REST** campo; irá precisar deste mais tarde.

Na **aplicação do Azure AD** secção, selecione **criar nova** e dê o nome do novo registo de aplicação do Azure AD (por exemplo, "VideoModADApp"). Clique em **guardar** e aguarde alguns minutos enquanto a aplicação está configurada. Em seguida, deverá ver seu novo registo de aplicação sob o **aplicação do Azure AD** secção da página.

Selecione o registo de aplicação e clique nas **Gerir aplicação** botão abaixo do mesmo. Observe o valor de **ID da aplicação** campo; irá precisar deste mais tarde. Selecione **configurações** > **chaves**e introduza uma descrição para uma nova chave (por exemplo, "VideoModKey"). Clique em **guardar**e, em seguida, tenha em atenção o novo valor da chave. Copiar essa cadeia de caracteres e guardar num local seguro.

Para obter instruções mais completa do processo acima, consulte [introdução à autenticação do Azure AD](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad).

Depois de fazer isso, pode usar o processador de multimédia de moderação de vídeos de duas formas diferentes.

## <a name="use-azure-media-services-explorer"></a>Utilizar o Explorador dos serviços de multimédia do Azure

O Explorador de serviços de multimédia do Azure é um front-end amigável para o AMS. Utilizá-la para procurar a sua conta do AMS, carregar vídeos e analisar o conteúdo com o processador de multimédia do Content Moderator. Transfira e instale-o a partir [GitHub](https://github.com/Azure/Azure-Media-Services-Explorer/releases), ou consulte a [mensagem de blogue do Explorador de serviços de multimédia do Azure](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) para obter mais informações.

![Explorador dos serviços de multimédia do Azure com o Content Moderator](images/ams-explorer-content-moderator.PNG)

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

1. No Visual Studio, crie um novo **aplicação de consola (.NET Framework)** do projeto e nomeie- **VideoModeration**. 
1. Se houver outros projetos na sua solução, selecione esta como o único projeto de arranque.
1. Obtenha os pacotes NuGet necessários. Clique com o botão direito do rato no seu projeto no Explorador de Soluções e selecione **Manage NuGet Packages** (Gerir Pacotes NuGet); em seguida, localize e instale os pacotes seguintes:
    - windowsazure.mediaservices
    - windowsazure.mediaservices.extensions

## <a name="add-video-moderation-code"></a>Adicione o código de moderação de vídeos

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

### <a name="set-up-resource-references"></a>Configurar referências aos recursos

Adicione os seguintes campos estáticos à classe **Programa** em _Program.cs_. Estes campos contêm as informações necessárias para ligar à sua subscrição do AMS. Preenchê-las com os valores obtidos nos passos acima. Tenha em atenção que `CLIENT_ID` é o **ID da aplicação** o valor da sua aplicação do Azure AD, e `CLIENT_SECRET` é o valor do "VideoModKey" que criou para essa aplicação.

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

Se pretender utilizar um ficheiro de vídeo local (caso mais simples), adicione-o ao projeto e introduza o caminho como o `INPUT_FILE` valor (caminhos relativos são relativos ao diretório de execução).

Também terá de criar o _preset.json_ de ficheiros no diretório atual e utilizá-la para especificar um número de versão. Por exemplo:

```JSON
{
    "version": "2.0"
}
```

### <a name="load-the-input-videos"></a>Carregar a entrada video(s)

O **Main** método o **programa** classe irá criar um contexto de suporte de dados do Azure e, em seguida, um contexto de armazenamento do Azure (no caso de seus vídeos estão no armazenamento de BLOBs). O código restante analisa um vídeo de uma pasta local, blob ou vários blobs dentro de um contentor de armazenamento do Azure. Pode tentar todas as opções, comente as outras linhas de código.

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

### <a name="create-an-azure-media-context"></a>Criar um contexto de multimédia do Azure

Adicione o seguinte método à classe **Programa**. Esta opção utiliza as suas credenciais de AMS para permitir a comunicação com o AMS.

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

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Adicione o código para criar um contexto de armazenamento do Azure

Adicione o seguinte método à classe **Programa**. Utilize o contexto de armazenamento, criado a partir de suas credenciais de armazenamento, para aceder ao seu armazenamento de Blobs.

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

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Adicione o código para criar elementos de multimédia do Azure a partir de locais de arquivo e BLOBs

O processador de multimédia do Content Moderator executa tarefas em **ativos** dentro da plataforma de serviços de multimédia do Azure.
Esses métodos criam os recursos a partir de um ficheiro local ou um blob associado.

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

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>Adicione o código para analisar uma coleção de vídeos (como blobs) dentro de um contêiner

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

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Adicione o método para executar a tarefa de moderador de conteúdo

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

Esses métodos transferir o ficheiro de saída do Content Moderator (JSON) a partir do recurso de serviços de multimédia do Azure e ajudar a monitorizar o estado da tarefa de moderação, para que o programa pode registar um Estado em execução para a consola.

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

Depois de concluída a tarefa de moderação de conteúdos, analise a resposta JSON. Esta é composta por um destes elementos:

- Resumidas de informações de vídeo
- **Capturas** como "**fragmentos**"
- **Quadros da chave** como "**eventos**" com um **reviewRecommended "(= true ou false)"** sinalizador com base no **para adultos** e **Racy** pontuações
- **Inicie**, **duração**, **totalDuration**, e **timestamp** estão em "ticks". Divida por **escala temporal** para obter o número em segundos.
 
> [!NOTE]
> - `adultScore` representa a pontuação de presença e a previsão de potencial de conteúdo que pode ser considerada sexualmente explícita ou para adultos em determinadas situações.
> - `racyScore` representa a pontuação de presença e a previsão de potencial de conteúdo que pode ser considerada sexualmente suggestive ou madura e em determinadas situações.
> - `adultScore` e `racyScore` são entre 0 e 1. Quanto maior for a pontuação, maior será o modelo é a previsão que a categoria pode ser aplicável. Esta pré-visualização depende de um modelo estatístico em vez de resultados codificados manualmente. Recomendamos que teste com o seu próprio conteúdo para determinar a forma como cada categoria alinha os seus requisitos.
> - `reviewRecommended` é true ou false dependendo da pontuação interna limiares. Os clientes devem avaliar se pretende utilizar este valor ou opte por utilizar limiares personalizados com base em suas diretivas de conteúdo.

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

## <a name="next-steps"></a>Passos Seguintes

Saiba como gerar [revisões de vídeo](video-reviews-quickstart-dotnet.md) da sua saída de moderação.

Adicione [moderação de transcrição](video-transcript-moderation-review-tutorial-dotnet.md) para suas revisões de vídeo.

Veja o tutorial detalhado sobre como criar uma [concluir a solução de moderação de vídeo e transcrição](video-transcript-moderation-review-tutorial-dotnet.md).

[Transferir a solução do Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) para esse e outros guias de introdução do Content Moderator para .NET.
