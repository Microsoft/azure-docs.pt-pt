---
title: Criar as revisões de vídeo com o .NET - Content Moderator
titlesuffix: Azure Cognitive Services
description: Este artigo fornece informações e exemplos de código para ajudá-lo a rapidamente começar a utilizar o SDK de moderador de conteúdo com o C# para criar as revisões de vídeo.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/19/2019
ms.author: sajagtap
ms.openlocfilehash: e4dd7299907168bb50ac8ebdf90b381c0bac01f2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60608755"
---
# <a name="create-video-reviews-using-net"></a>Criar as revisões de vídeo com o .NET

Este artigo fornece informações e exemplos de código para ajudá-lo a rapidamente começar a utilizar o [SDK de moderador de conteúdo com o c#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) para:

- Criar uma revisão de vídeo para moderadores humanos
- Adicionar quadros para uma revisão
- Obtenha os quadros para a revisão
- Obter o estado e os detalhes da revisão
- Publicar a revisão

## <a name="prerequisites"></a>Pré-requisitos

- Inicie sessão ou crie uma conta no Content Moderator [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) site.
- Este artigo pressupõe que tenha [moderado o vídeo (consulte o guia de introdução)](video-moderation-api.md) e ter os dados de resposta. Precisa para a criação baseados em frame revisões para moderadores humanos.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Certifique-se de que a chave de API pode chamar a API de revisão para a criação de revisões

Depois de concluir os passos anteriores, pode ficar com duas chaves do Content Moderator, se tiver iniciado a partir do portal do Azure.

Se planear utilizar a chave de API dada pelo Azure no seu exemplo de SDK, siga os passos mencionados na secção [Utilizar a chave do Azure com a API de revisão](review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) para permitir que a aplicação chame a API de revisão e crie as revisões.

Se utilizar a chave de avaliação gratuita gerada pela ferramenta de revisão, a sua conta da ferramenta de revisão já conhece a chave e, por conseguinte, não são precisos passos adicionais.

### <a name="prepare-your-video-and-the-video-frames-for-review"></a>Preparar o seu vídeo e os quadros de vídeo para revisão

Os quadros de vídeo vídeo e de exemplo para rever tem de ser publicados online porque tem de suas URLs.

> [!NOTE]
> O programa usa manualmente guardadas capturas de ecrã do vídeo com pontuações aleatórias de adulto/ousado para ilustrar o uso da API de revisão. Numa situação de mundo real, utilize o [saída de moderação de vídeos](video-moderation-api.md#run-the-program-and-review-the-output) para criar imagens e atribuir as pontuações. 

Para o vídeo, terá de um ponto de final de transmissão em fluxo para que a ferramenta de revisão reproduz o vídeo na vista do jogador.

![Miniatura de vídeo de demonstração](images/ams-video-demo-view.PNG)

- Copiar o **URL** nisso [dos serviços de multimédia do Azure de demonstração](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) página para o URL do manifesto.

Para os quadros de vídeo (imagens), utilize as seguintes imagens:

![Miniatura do quadro de vídeo 1](images/ams-video-frame-thumbnails-1.PNG) | ![Miniatura do quadro de vídeo 2](images/ams-video-frame-thumbnails-2.PNG) | ![Miniatura do quadro de vídeo 3](images/ams-video-frame-thumbnails-3.PNG) |
| :---: | :---: | :---: |
[Intervalo de 1](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG) | [Quadro 2](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG) | [Período de 3](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG) |

## <a name="create-your-visual-studio-project"></a>Criar o projeto do Visual Studio

1. Adicione um novo projeto **Aplicação de consola (.NET Framework)** à sua solução.

1. Nomeie o projeto **VideoReviews**.

1. Selecione este projeto como o projeto de arranque único para a solução.

### <a name="install-required-packages"></a>Instalar pacotes necessários

Instale os seguintes pacotes de NuGet para o projeto de TermLists.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Atualizar as instruções de utilização do programa

Modifique o programa do instruções using da seguinte forma.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator;
using Microsoft.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
```

### <a name="add-private-properties"></a>Adicionar propriedades privadas

Adicione as seguintes propriedades privadas ao espaço de nomes VideoReviews, classe Program.

Sempre que for indicado, substitua os valores de exemplo para estas propriedades.

```csharp
namespace VideoReviews
{
    class Program
    {
        // NOTE: Replace this example location with the location for your Content Moderator account.
        /// <summary>
        /// The region/location for your Content Moderator account, 
        /// for example, westus.
        /// </summary>
        private static readonly string AzureRegion = "YOUR CONTENT MODERATOR REGION";

        // NOTE: Replace this example key with a valid subscription key.
        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "YOUR CONTENT MODERATOR KEY";

        // NOTE: Replace this example team name with your Content Moderator team name.
        /// <summary>
        /// The name of the team to assign the job to.
        /// </summary>
        /// <remarks>This must be the team name you used to create your 
        /// Content Moderator account. You can retrieve your team name from
        /// the Content Moderator web site. Your team name is the Id associated 
        /// with your subscription.</remarks>
        private const string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

        /// <summary>
        /// The base URL fragment for Content Moderator calls.
        /// </summary>
        private static readonly string AzureBaseURL =
            $"{AzureRegion}.api.cognitive.microsoft.com";

        /// <summary>
        /// The minimum amount of time, in milliseconds, to wait between calls
        /// to the Content Moderator APIs.
        /// </summary>
        private const int throttleRate = 2000;
```

### <a name="create-content-moderator-client-object"></a>Criar objeto de cliente de moderador conteúdo

Adicione a seguinte definição de método ao espaço de nomes VideoReviews, classe Program.

```csharp
/// <summary>
/// Returns a new Content Moderator client for your subscription.
/// </summary>
/// <returns>The new client.</returns>
/// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
/// When you have finished using the client,
/// you should dispose of it either directly or indirectly. </remarks>
public static ContentModeratorClient NewClient()
{
    return new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey))
    {
        Endpoint = AzureBaseURL
    };
}
```

## <a name="create-a-video-review"></a>Criar uma revisão de vídeo

Criar uma revisão de vídeo com **ContentModeratorClient.Reviews.CreateVideoReviews**. Para obter mais informações, veja a [Referência à API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** tem os seguintes parâmetros obrigatórios:
1. Uma cadeia que contém um tipo MIME, o que deve ser "application/json". 
1. O nome de equipa do Content Moderator.
1. Uma **IList\<CreateVideoReviewsBodyItem >** objeto. Cada **CreateVideoReviewsBodyItem** objeto representa uma revisão de vídeo. Este início rápido cria uma revisão de cada vez.

**CreateVideoReviewsBodyItem** tem várias propriedades. No mínimo, defina as propriedades seguintes:
- **Conteúdo**. O URL do vídeo para ser revisto.
- **ContentId**. Um ID para atribuir a revisão de vídeo.
- **Estado**. Definir o valor para "Unpublished." Se não defini-lo, ele é predefinida como "Pendentes", que significa que a revisão de vídeo é publicada e pendentes de revisão humana. Assim que for publicada uma revisão de vídeo, já não pode adicionar quadros de vídeo, transcrição ou um resultado de moderação de transcrição para ele.

> [!NOTE]
> **CreateVideoReviews** retorna um IList<string>. Cada uma destas cadeias contém um ID para uma revisão de vídeo. Essas IDs são GUIDs e não são os mesmos que o valor do **ContentId** propriedade. 

Adicione a seguinte definição de método ao espaço de nomes VideoReviews, classe Program.

```csharp
/// <summary>
/// Create a video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="id">The ID to assign to the video review.</param>
/// <param name="content">The URL of the video to review.</param>
/// <returns>The ID of the video review.</returns>
private static string CreateReview(ContentModeratorClient client, string id, string content)
{
    Console.WriteLine("Creating a video review.");

    List<CreateVideoReviewsBodyItem> body = new List<CreateVideoReviewsBodyItem>() {
        new CreateVideoReviewsBodyItem
        {
            Content = content,
            ContentId = id,
            /* Note: to create a published review, set the Status to "Pending".
            However, you cannot add video frames or a transcript to a published review. */
            Status = "Unpublished",
        }
    };

    var result = client.Reviews.CreateVideoReviews("application/json", TeamName, body);

    Thread.Sleep(throttleRate);

    // We created only one review.
    return result[0];
}
```

> [!NOTE]
> A chave de serviço do Content Moderator tem um limite de velocidade de pedidos por segundo (RPS) e, se ultrapassar o limite, o SDK emite uma exceção com o código de erro 429.
>
> Uma chave de escalão gratuito tem um limite de velocidade de um RPS.

## <a name="add-video-frames-to-the-video-review"></a>Adicionar quadros de vídeo para a revisão de vídeo

Adicionar quadros de vídeo para uma revisão de vídeo com **ContentModeratorClient.Reviews.AddVideoFrameUrl** (se seus quadros de vídeo estão alojados online) ou **ContentModeratorClient.Reviews.AddVideoFrameStream** ( Se estiverem alojados seus quadros de vídeo localmente). Este guia de introdução pressupõe seus quadros de vídeo estão alojados online e então, usa **AddVideoFrameUrl**. Para obter mais informações, veja a [Referência à API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd).

**AddVideoFrameUrl** tem os seguintes parâmetros obrigatórios:
1. Uma cadeia que contém um tipo MIME, o que deve ser "application/json".
1. O nome de equipa do Content Moderator.
1. O ID de revisão de vídeo devolvido por **CreateVideoReviews**.
1. Uma **IList\<VideoFrameBodyItem >** objeto. Cada **VideoFrameBodyItem** objeto representa um quadro de vídeo.

**VideoFrameBodyItem** tem as seguintes propriedades:
- **Timestamp**. Uma cadeia que contém, em segundos, o tempo em que o vídeo a partir do qual o quadro de vídeo foi tirado.
- **FrameImage**. O URL do quadro do vídeo.
- **Metadados**. Um IList\<VideoFrameBodyItemMetadataItem >. **VideoFrameBodyItemMetadataItem** é simplesmente um par chave/valor. Chaves válidas incluem:
- **reviewRecommended**. VERDADEIRO se recomenda uma revisão humana do quadro do vídeo.
- **adultScore**. Um valor entre 0 e 1 que classifica a gravidade do conteúdo para adultos no quadro do vídeo.
- **a**. VERDADEIRO se o vídeo contém conteúdo para adultos.
- **racyScore**. Um valor entre 0 e 1 que classifica a gravidade do conteúdo para adultos no quadro do vídeo.
- **r**. VERDADEIRO se o quadro de vídeo contém conteúdo para adultos.
- **ReviewerResultTags**. Um IList\<VideoFrameBodyItemReviewerResultTagsItem >. **VideoFrameBodyItemReviewerResultTagsItem** é simplesmente um par chave/valor. Uma aplicação pode utilizar estas etiquetas para organizar os quadros de vídeo.

> [!NOTE]
> Este guia de introdução gera valores aleatórios para o **adultScore** e **racyScore** propriedades. Num aplicativo de produção, pode obter estes valores a partir da [serviço de moderação de vídeos](video-moderation-api.md), implementado como um serviço de multimédia do Azure.

Adicione as seguintes definições de método ao espaço de nomes VideoReviews, classe Program.

```csharp
<summary>
/// Create a video frame to add to a video review after the video review is created.
/// </summary>
/// <param name="url">The URL of the video frame image.</param>
/// <returns>The video frame.</returns>
private static VideoFrameBodyItem CreateFrameToAddToReview(string url, string timestamp_seconds)
{
    // We generate random "adult" and "racy" scores for the video frame.
    Random rand = new Random();

    var frame = new VideoFrameBodyItem
    {
        // The timestamp is measured in milliseconds. Convert from seconds.
        Timestamp = (int.Parse(timestamp_seconds) * 1000).ToString(),
        FrameImage = url,

        Metadata = new List<VideoFrameBodyItemMetadataItem>
        {
            new VideoFrameBodyItemMetadataItem("reviewRecommended", "true"),
            new VideoFrameBodyItemMetadataItem("adultScore", rand.NextDouble().ToString()),
            new VideoFrameBodyItemMetadataItem("a", "false"),
            new VideoFrameBodyItemMetadataItem("racyScore", rand.NextDouble().ToString()),
            new VideoFrameBodyItemMetadataItem("r", "false")
        },

        ReviewerResultTags = new List<VideoFrameBodyItemReviewerResultTagsItem>()
        {
            new VideoFrameBodyItemReviewerResultTagsItem("tag1", "value1")
        }
    };

    return frame;
}
```

```csharp
/// <summary>
/// Add a video frame to the indicated video review. For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="url">The URL of the video frame image.</param>
static void AddFrame(ContentModeratorClient client, string review_id, string url, string timestamp_seconds)
{
    Console.WriteLine("Adding a frame to the review with ID {0}.", review_id);

    var frames = new List<VideoFrameBodyItem>()
    {
        CreateFrameToAddToReview(url, timestamp_seconds)
    };
        
    client.Reviews.AddVideoFrameUrl("application/json", TeamName, review_id, frames);

    Thread.Sleep(throttleRate);
```

## <a name="get-video-frames-for-video-review"></a>Obter quadros de vídeo para revisão de vídeo

Pode obter os quadros de vídeo para uma revisão de vídeo com **ContentModeratorClient.Reviews.GetVideoFrames**. **GetVideoFrames** tem os seguintes parâmetros obrigatórios:
1. O nome de equipa do Content Moderator.
1. O ID de revisão de vídeo devolvido por **CreateVideoReviews**.
1. O índice baseado em zero do primeiro quadro de vídeo para obter.
1. O número de quadros de vídeo para obter.

Adicione a seguinte definição de método ao espaço de nomes VideoReviews, classe Program.

```csharp
/// <summary>
/// Get the video frames assigned to the indicated video review.  For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7ba43e7151f0b10d45200
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
static void GetFrames(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Getting frames for the review with ID {0}.", review_id);

    Frames result = client.Reviews.GetVideoFrames(TeamName, review_id, 0);
    Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="get-video-review-information"></a>Obtenha informações de vídeo de revisão

Obter informações sobre uma revisão de vídeo com **ContentModeratorClient.Reviews.GetReview**. **GetReview** tem os seguintes parâmetros obrigatórios:
1. O nome de equipa do Content Moderator.
1. O ID de revisão de vídeo devolvido por **CreateVideoReviews**.

Adicione a seguinte definição de método ao espaço de nomes VideoReviews, classe Program.

```csharp
/// <summary>
/// Get the information for the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void GetReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Getting the status for the review with ID {0}.", review_id);

    var result = client.Reviews.GetReview(ModeratorHelper.Clients.TeamName, review_id);
    Console.WriteLine(JsonConvert.SerializeObject(result, Formatting.Indented));

    Thread.Sleep(throttleRate);
}
```

## <a name="publish-video-review"></a>Publicar vídeo revisão

Publicar uma revisão de vídeo com **ContentModeratorClient.Reviews.PublishVideoReview**. **PublishVideoReview** tem os seguintes parâmetros obrigatórios:
1. O nome de equipa do Content Moderator.
1. O ID de revisão de vídeo devolvido por **CreateVideoReviews**.

Adicione a seguinte definição de método ao espaço de nomes VideoReviews, classe Program.

```csharp
/// <summary>
/// Publish the indicated video review. For more information, see the reference API:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7bb29e7151f0b10d45201
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
private static void PublishReview(ContentModeratorClient client, string review_id)
{
    Console.WriteLine("Publishing the review with ID {0}.", review_id);
    client.Reviews.PublishVideoReview(TeamName, review_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="putting-it-all-together"></a>Juntar tudo

Adicionar a **Main** definição de método ao espaço de nomes VideoReviews, classe de programa. Por fim, feche a classe de programa e o espaço de nomes de VideoReviews.

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var frame1_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG";
        var frame2_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG";
        var frame3_url = "https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG";

        // Add the frames from 17, 64, and 144 seconds.
        AddFrame(client, review_id, frame1_url, "17");
        AddFrame(client, review_id, frame2_url, "64");
        AddFrame(client, review_id, frame3_url, "144");

        // Get frames information and show
        GetFrames(client, review_id);
        GetReview(client, review_id);

        // Publish the review
        PublishReview(client, review_id);

        Console.WriteLine("Open your Content Moderator Dashboard and select Review > Video to see the review.");
        Console.WriteLine("Press any key to close the application.");
        Console.ReadKey();
    }
}
```

## <a name="run-the-program-and-review-the-output"></a>Executar o programa e rever o resultado
Quando executar o aplicativo, verá um resultado nas seguintes linhas:

```json
Creating a video review.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Adding a frame to the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Getting frames for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
{
    "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
    "VideoFrames": [
    {
        "Timestamp": "17000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_17000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.808312381528463"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.846378884206702"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
    ]
    },
    {
        "Timestamp": "64000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_64000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.576078300166912"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.244768953064815"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
    ]
    },
    {
        "Timestamp": "144000",
        "FrameImage": "https://reviewcontentprod.blob.core.windows.net/testreview6/FRM_201801v3212bda70ced4928b2cd7459c290c7dc_144000.PNG",
        "Metadata": [
        {
            "Key": "reviewRecommended",
            "Value": "true"
        },
        {
            "Key": "adultScore",
            "Value": "0.664480847150311"
        },
        {
            "Key": "a",
            "Value": "false"
        },
        {
            "Key": "racyScore",
            "Value": "0.933817870418456"
        },
        {
            "Key": "r",
            "Value": "false"
        }
        ],
        "ReviewerResultTags": [
        {
            "Key": "tag1",
            "Value": "value1"
        }
        ]
    }
    ]
}

Getting the status for the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
{
    "ReviewId": "201801v3212bda70ced4928b2cd7459c290c7dc",
    "SubTeam": "public",
    "Status": "UnPublished",
    "ReviewerResultTags": [],
    "CreatedBy": "testreview6",
    "Metadata": [
    {
        "Key": "FrameCount",
        "Value": "3"
    }
    ],
    "Type": "Video",
    "Content": "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
    "ContentId": "review1",
    "CallbackEndpoint": null
}

Publishing the review with ID 201801v3212bda70ced4928b2cd7459c290c7dc.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="check-out-your-video-review"></a>Verifique a sua revisão de vídeo

Por último, verá que a revisão de vídeo no seu Content Moderator reveja a conta de ferramenta na **reveja**>**vídeo** ecrã.

![Revisão de vídeo para moderadores humanos](images/ams-video-review.PNG)

## <a name="next-steps"></a>Passos Seguintes

Obter o [SDK de .NET do conteúdo moderador](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) e o [solução do Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) para esse e outros guias de introdução do Content Moderator para .NET.

Saiba como adicionar [moderação de transcrição](video-transcript-moderation-review-tutorial-dotnet.md) para a revisão de vídeo. 

Veja o tutorial detalhado sobre como desenvolver um [concluir a solução de moderação de vídeos](video-transcript-moderation-review-tutorial-dotnet.md).
