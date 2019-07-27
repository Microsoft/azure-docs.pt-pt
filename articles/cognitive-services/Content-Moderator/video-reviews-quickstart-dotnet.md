---
title: Criar revisões de vídeo usando o .NET-Content Moderator
titleSuffix: Azure Cognitive Services
description: Este artigo fornece informações e exemplos de código para ajudá-lo a começar rapidamente a usar o C# SDK do Content moderator com o para criar revisões de vídeo.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/19/2019
ms.author: sajagtap
ms.openlocfilehash: 74b0ad4b5d8a16c8d46fcf6de67c46dab29ba51d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564295"
---
# <a name="create-video-reviews-using-net"></a>Criar revisões de vídeo usando o .NET

Este artigo fornece informações e exemplos de código para ajudá-lo a começar rapidamente a usar o [SDK do Content moderator com C# ](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) o para:

- Criar uma revisão de vídeo para moderadores humanos
- Adicionar quadros a uma revisão
- Obter os quadros para a revisão
- Obter o status e os detalhes da revisão
- Publicar a revisão

## <a name="prerequisites"></a>Pré-requisitos

- Entre ou crie uma conta no site da [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) de Content Moderator.
- Este artigo pressupõe que você tenha [moderado o vídeo (consulte o início rápido)](video-moderation-api.md) e tenha os dados de resposta. Você precisa dele para criar revisões baseadas em quadros para moderadores humanos.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Certifique-se de que a chave de API pode chamar a API de revisão para a criação de revisões

Depois de concluir os passos anteriores, pode ficar com duas chaves do Content Moderator, se tiver iniciado a partir do portal do Azure.

Se planear utilizar a chave de API dada pelo Azure no seu exemplo de SDK, siga os passos mencionados na secção [Utilizar a chave do Azure com a API de revisão](review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) para permitir que a aplicação chame a API de revisão e crie as revisões.

Se utilizar a chave de avaliação gratuita gerada pela ferramenta de revisão, a sua conta da ferramenta de revisão já conhece a chave e, por conseguinte, não são precisos passos adicionais.

### <a name="prepare-your-video-and-the-video-frames-for-review"></a>Preparar o vídeo e os quadros de vídeo para revisão

O vídeo e os quadros de vídeo de exemplo a serem examinados devem ser publicados online porque você precisa de suas URLs.

> [!NOTE]
> O programa usa capturas de tela salvas manualmente do vídeo com pontuações de adulto/erótico aleatórias para ilustrar o uso da API de revisão. Em uma situação do mundo real, você usa a [saída](video-moderation-api.md#run-the-program-and-review-the-output) de moderação de vídeo para criar imagens e atribuir pontuações. 

Para o vídeo, você precisa de um ponto de extremidade de streaming para que a ferramenta de revisão reproduza o vídeo na exibição do Player.

![Miniatura de demonstração em vídeo](images/ams-video-demo-view.PNG)

- Copie a **URL** nesta página de [demonstração dos serviços de mídia do Azure](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) para a URL do manifesto.

Para os quadros de vídeo (imagens), use as seguintes imagens:

![Miniatura de quadro de vídeo 1](images/ams-video-frame-thumbnails-1.PNG) | ![Miniatura de quadro de vídeo 2](images/ams-video-frame-thumbnails-2.PNG) | ![Miniatura do quadro de vídeo 3](images/ams-video-frame-thumbnails-3.PNG) |
| :---: | :---: | :---: |
[Quadro 1](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame1-00-17.PNG) | [Quadro 2](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-2-01-04.PNG) | [Quadro 3](https://blobthebuilder.blob.core.windows.net/sampleframes/ams-video-frame-3-02-24.PNG) |

## <a name="create-your-visual-studio-project"></a>Criar o projeto do Visual Studio

1. Adicione um novo projeto **Aplicação de consola (.NET Framework)** à sua solução.

1. Nomeie o projeto **VideoReviews**.

1. Selecione este projeto como o projeto de arranque único para a solução.

### <a name="install-required-packages"></a>Instalar pacotes necessários

Instale os seguintes pacotes NuGet para o projeto TermLists.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Atualizar as instruções de utilização do programa

Modifique as instruções de uso do programa da seguinte maneira.

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

Adicione as propriedades particulares a seguir ao namespace VideoReviews, classe Program.

Quando indicado, substitua os valores de exemplo para essas propriedades.

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

### <a name="create-content-moderator-client-object"></a>Criar Content Moderator objeto de cliente

Adicione a seguinte definição de método ao namespace VideoReviews, classe Program.

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

Crie uma revisão de vídeo com **ContentModeratorClient. Reviews. CreateVideoReviews**. Para obter mais informações, veja a [Referência à API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** tem os seguintes parâmetros obrigatórios:
1. Uma cadeia de caracteres que contém um tipo MIME, que deve ser "Application/JSON". 
1. O nome da equipe de Content Moderator.
1. Um **objeto\<> IList CreateVideoReviewsBodyItem** . Cada objeto **CreateVideoReviewsBodyItem** representa uma revisão de vídeo. Este início rápido cria uma análise por vez.

**CreateVideoReviewsBodyItem** tem várias propriedades. No mínimo, você define as seguintes propriedades:
- **Conteúdo**. A URL do vídeo a ser revisado.
- **ContentId**. Uma ID a ser atribuída à revisão de vídeo.
- **Status**. Defina o valor como "não publicado". Se você não defini-lo, o padrão é "Pending", o que significa que a revisão de vídeo é publicada e a revisão humana pendente. Depois que uma revisão de vídeo é publicada, você não pode mais adicionar quadros de vídeo, uma transcrição ou um resultado de moderação de transcrição a ele.

> [!NOTE]
> **CreateVideoReviews** retorna uma cadeia\<de caracteres IList >. Cada uma dessas cadeias de caracteres contém uma ID para uma revisão de vídeo. Essas IDs são GUIDs e não são as mesmas que o valor da propriedade **ContentId** . 

Adicione a seguinte definição de método ao namespace VideoReviews, classe Program.

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

## <a name="add-video-frames-to-the-video-review"></a>Adicionar quadros de vídeo à revisão de vídeo

Você adiciona quadros de vídeo a uma revisão de vídeo com **ContentModeratorClient. Reviews. AddVideoFrameUrl** (se os quadros de vídeo estiverem hospedados online) ou **ContentModeratorClient. Reviews. AddVideoFrameStream** (se os quadros de vídeo estiverem hospedados localmente). Este início rápido assume que os quadros de vídeo estão hospedados online e, portanto, usam **AddVideoFrameUrl**. Para obter mais informações, veja a [Referência à API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b76ae7151f0b10d451fd).

**AddVideoFrameUrl** tem os seguintes parâmetros obrigatórios:
1. Uma cadeia de caracteres que contém um tipo MIME, que deve ser "Application/JSON".
1. O nome da equipe de Content Moderator.
1. A ID de revisão de vídeo retornada por **CreateVideoReviews**.
1. Um **objeto\<> IList VideoFrameBodyItem** . Cada objeto **VideoFrameBodyItem** representa um quadro de vídeo.

**VideoFrameBodyItem** tem as seguintes propriedades:
- **Carimbo de data/hora**. Uma cadeia de caracteres que contém, em segundos, a hora no vídeo do qual o quadro de vídeo foi tirado.
- **FrameImage**. A URL do quadro de vídeo.
- **Metadados**. > IList\<VideoFrameBodyItemMetadataItem. **VideoFrameBodyItemMetadataItem** é simplesmente um par chave/valor. As chaves válidas incluem:
- **reviewRecommended**. True se uma revisão humana do quadro de vídeo for recomendada.
- **adultScore**. Um valor de 0 a 1 que classifica a severidade do conteúdo adulto no quadro de vídeo.
- **a**. True se o vídeo contiver conteúdo adulto.
- **racyScore**. Um valor de 0 a 1 que classifica a severidade do conteúdo de erótico no quadro de vídeo.
- **r**. True se o quadro de vídeo contiver conteúdo erótico.
- **ReviewerResultTags**. > IList\<VideoFrameBodyItemReviewerResultTagsItem. **VideoFrameBodyItemReviewerResultTagsItem** é simplesmente um par chave/valor. Um aplicativo pode usar essas marcas para organizar quadros de vídeo.

> [!NOTE]
> Este início rápido gera valores aleatórios para as propriedades **adultScore** e **racyScore** . Em um aplicativo de produção, você obteria esses valores do [serviço](video-moderation-api.md)de moderação de vídeo, implantado como um serviço de mídia do Azure.

Adicione as seguintes definições de método ao namespace VideoReviews, classe Program.

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

Você pode obter os quadros de vídeo para uma revisão de vídeo com **ContentModeratorClient. Reviews. GetVideoFrames**. **GetVideoFrames** tem os seguintes parâmetros obrigatórios:
1. O nome da equipe de Content Moderator.
1. A ID de revisão de vídeo retornada por **CreateVideoReviews**.
1. O índice de base zero do primeiro quadro de vídeo a ser obtido.
1. O número de quadros de vídeo a serem obtidos.

Adicione a seguinte definição de método ao namespace VideoReviews, classe Program.

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

## <a name="get-video-review-information"></a>Obter informações de revisão de vídeo

Você obtém informações para uma revisão de vídeo com **ContentModeratorClient. Reviews. getreview**. **Getexamine** tem os seguintes parâmetros obrigatórios:
1. O nome da equipe de Content Moderator.
1. A ID de revisão de vídeo retornada por **CreateVideoReviews**.

Adicione a seguinte definição de método ao namespace VideoReviews, classe Program.

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

## <a name="publish-video-review"></a>Publicar revisão de vídeo

Você publica uma revisão de vídeo com **ContentModeratorClient. Reviews. PublishVideoReview**. **PublishVideoReview** tem os seguintes parâmetros obrigatórios:
1. O nome da equipe de Content Moderator.
1. A ID de revisão de vídeo retornada por **CreateVideoReviews**.

Adicione a seguinte definição de método ao namespace VideoReviews, classe Program.

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

Adicione a definição do método **Main** ao namespace VideoReviews, classe Program. Por fim, feche a classe programa e o namespace VideoReviews.

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
Ao executar o aplicativo, você verá uma saída nas seguintes linhas:

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

## <a name="check-out-your-video-review"></a>Confira sua revisão de vídeo

Por fim, você verá a revisão de vídeo em seu Content moderator conta de ferramenta de revisão na tela de**vídeo** de **revisão**>.

![Revisão de vídeo para moderadores humanos](images/ams-video-review.PNG)

## <a name="next-steps"></a>Passos Seguintes

Obtenha o [SDK do .net content moderator](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) e a [solução do Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) para este e outros guias de início rápido Content moderator para .net.

Saiba como adicionar moderação de [transcrição](video-transcript-moderation-review-tutorial-dotnet.md) à revisão de vídeo. 

Confira o tutorial detalhado sobre como desenvolver uma solução de moderação de [vídeo completa](video-transcript-moderation-review-tutorial-dotnet.md).
