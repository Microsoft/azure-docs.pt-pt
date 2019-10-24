---
title: Criar análises de transcrição de vídeo usando .NET-Content Moderator
titleSuffix: Azure Cognitive Services
description: Criar análises de transcrição de vídeo usando o SDK do Content Moderator para .NET
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: pafarley
ms.openlocfilehash: 7fe254aa6e78133102a295c5e60a10d29f6382a4
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757174"
---
# <a name="create-video-transcript-reviews-using-net"></a>Criar revisões de transcrição de vídeo usando o .NET

Este artigo fornece informações e exemplos de código para ajudá-lo a começar rapidamente a usar o [SDK do Content moderator com C# ](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) o para:

- Criar uma revisão de vídeo para moderadores humanos
- Adicionar uma transcrição moderada à revisão
- Publicar a revisão

## <a name="prerequisites"></a>Pré-requisitos

- Entre ou crie uma conta no site da [ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) de Content moderator, se você ainda não tiver feito isso.
- Este artigo pressupõe que você tenha [moderado o vídeo](video-moderation-api.md) e [criou a revisão de vídeo](video-reviews-quickstart-dotnet.md) na ferramenta de revisão para tomada de decisão humana. Agora você deseja adicionar transcrições de vídeo moderadas na ferramenta de revisão.

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>Verifique se sua chave de API pode chamar a API de revisão (criação de trabalho)

Depois de concluir os passos anteriores, pode ficar com duas chaves do Content Moderator, se tiver iniciado a partir do portal do Azure.

Se planear utilizar a chave de API dada pelo Azure no seu exemplo de SDK, siga os passos mencionados na secção [Utilizar a chave do Azure com a API de revisão](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) para permitir que a aplicação chame a API de revisão e crie as revisões.

Se utilizar a chave de avaliação gratuita gerada pela ferramenta de revisão, a sua conta da ferramenta de revisão já conhece a chave e, por conseguinte, não são precisos passos adicionais.

## <a name="prepare-your-video-for-review"></a>Preparar seu vídeo para análise

Adicione a transcrição a uma revisão de vídeo. O vídeo deve ser publicado online. Você precisa de seu ponto de extremidade de streaming. O ponto de extremidade de streaming permite que o player de vídeo da ferramenta de revisão reproduza o vídeo.

![Miniatura de demonstração em vídeo](images/ams-video-demo-view.PNG)

- Copie a **URL** nesta página de [demonstração dos serviços de mídia do Azure](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) para a URL do manifesto.

## <a name="create-your-visual-studio-project"></a>Criar o projeto do Visual Studio

1. Adicione um novo projeto **Aplicação de consola (.NET Framework)** à sua solução.

1. Nomeie o projeto **VideoTranscriptReviews**.

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
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
```

### <a name="add-private-properties"></a>Adicionar propriedades privadas

Adicione as propriedades particulares a seguir ao namespace VideoTranscriptReviews, classe Program.

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

Adicione a seguinte definição de método ao namespace VideoTranscriptReviews, classe Program.

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
1. Uma **IList\<objeto de > CreateVideoReviewsBodyItem** . Cada objeto **CreateVideoReviewsBodyItem** representa uma revisão de vídeo. Este início rápido cria uma análise por vez.

**CreateVideoReviewsBodyItem** tem várias propriedades. No mínimo, você define as seguintes propriedades:
- **Conteúdo**. A URL do vídeo a ser revisado.
- **ContentId**. Uma ID a ser atribuída à revisão de vídeo.
- **Status**. Defina o valor como "não publicado". Se você não defini-lo, o padrão é "Pending", o que significa que a revisão de vídeo é publicada e a revisão humana pendente. Depois que uma revisão de vídeo é publicada, você não pode mais adicionar quadros de vídeo, uma transcrição ou um resultado de moderação de transcrição a ele.

> [!NOTE]
> **CreateVideoReviews** retorna uma IList\<cadeia de caracteres >. Cada uma dessas cadeias de caracteres contém uma ID para uma revisão de vídeo. Essas IDs são GUIDs e não são as mesmas que o valor da propriedade **ContentId** .

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
> A chave do serviço do Content Moderator tem um limite de velocidade de pedidos por segundo (RPS). Se ultrapassar o limite, o SDK emite uma exceção com o código de erro 429.
>
> Uma chave de escalão gratuito tem um limite de velocidade de um RPS.

## <a name="add-transcript-to-video-review"></a>Adicionar transcrição à revisão de vídeo

Você adiciona uma transcrição a uma revisão de vídeo com **ContentModeratorClient. Reviews. AddVideoTranscript**. **AddVideoTranscript** tem os seguintes parâmetros obrigatórios:
1. Sua ID de equipe do Content Moderator.
1. A ID de revisão de vídeo retornada por **CreateVideoReviews**.
1. Um objeto de **fluxo** que contém a transcrição.

A transcrição deve estar no formato WebVTT. Para obter mais informações, consulte [WebVTT: o formato de faixas de texto de vídeo da Web](https://www.w3.org/TR/webvtt1/).

> [!NOTE]
> O programa usa uma transcrição de exemplo no formato VTT. Em uma solução do mundo real, você usa o serviço de Azure Media Indexer para [gerar uma transcrição](https://docs.microsoft.com/azure/media-services/media-services-index-content) de um vídeo.

Adicione a seguinte definição de método ao namespace VideotranscriptReviews, classe Program.

```csharp
/// <summary>
/// Add a transcript to the indicated video review.
/// The transcript must be in the WebVTT format.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b8b2e7151f0b10d451fe
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscript(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript to the review with ID {0}.", review_id);
    client.Reviews.AddVideoTranscript(TeamName, review_id, new MemoryStream(System.Text.Encoding.UTF8.GetBytes(transcript)));
    Thread.Sleep(throttleRate);
}
```

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Adicionar um resultado de moderação de transcrição à revisão de vídeo

Além de adicionar uma transcrição a uma revisão de vídeo, você também adiciona o resultado de moderar que a transcrição. Você faz isso com **ContentModeratorClient. Reviews. AddVideoTranscriptModerationResult**. Para obter mais informações, veja a [Referência à API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**AddVideoTranscriptModerationResult** tem os seguintes parâmetros obrigatórios:
1. Uma cadeia de caracteres que contém um tipo MIME, que deve ser "Application/JSON". 
1. O nome da equipe de Content Moderator.
1. A ID de revisão de vídeo retornada por **CreateVideoReviews**.
1. Um IList\<TranscriptModerationBodyItem >. Um **TranscriptModerationBodyItem** tem as seguintes propriedades:
1. **Termos**. Um IList\<TranscriptModerationBodyItemTermsItem >. Um **TranscriptModerationBodyItemTermsItem** tem as seguintes propriedades:
1. **Índice**. O índice de base zero do termo.
1. **Termo**. Uma cadeia de caracteres que contém o termo.
1. **Carimbo de data/hora**. Uma cadeia de caracteres que contém, em segundos, a hora na transcrição na qual os termos são encontrados.

A transcrição deve estar no formato WebVTT. Para obter mais informações, consulte [WebVTT: o formato de faixas de texto de vídeo da Web](https://www.w3.org/TR/webvtt1/).

Adicione a seguinte definição de método ao namespace VideoTranscriptReviews, classe Program. Esse método envia uma transcrição para o método **ContentModeratorClient. Textmoderation. ScreenText** . Ele também converte o resultado em uma IList\<TranscriptModerationBodyItem > e envia ao **AddVideoTranscriptModerationResult**.

```csharp
/// <summary>
/// Add the results of moderating a video transcript to the indicated video review.
/// For more information, see the API reference:
/// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="review_id">The video review ID.</param>
/// <param name="transcript">The video transcript.</param>
static void AddTranscriptModerationResult(ContentModeratorClient client, string review_id, string transcript)
{
    Console.WriteLine("Adding a transcript moderation result to the review with ID {0}.", review_id);

    // Screen the transcript using the Text Moderation API. For more information, see:
    // https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f
    Screen screen = client.TextModeration.ScreenText("eng", "text/plain", transcript);

    // Map the term list returned by ScreenText into a term list we can pass to AddVideoTranscriptModerationResult.
    List<TranscriptModerationBodyItemTermsItem> terms = new List<TranscriptModerationBodyItemTermsItem>();
    if (null != screen.Terms)
    {
        foreach (var term in screen.Terms)
        {
            if (term.Index.HasValue)
            {
                terms.Add(new TranscriptModerationBodyItemTermsItem(term.Index.Value, term.Term));
            }
        }
    }

    List<TranscriptModerationBodyItem> body = new List<TranscriptModerationBodyItem>()
    {
        new TranscriptModerationBodyItem()
        {
            Timestamp = "0",
            Terms = terms
        }
    };

    client.Reviews.AddVideoTranscriptModerationResult("application/json", TeamName, review_id, body);

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

Adicione a definição do método **Main** ao namespace VideoTranscriptReviews, classe Program. Por fim, feche a classe programa e o namespace VideoTranscriptReviews.

> [!NOTE]
> O programa usa uma transcrição de exemplo no formato VTT. Em uma solução do mundo real, você usa o serviço de Azure Media Indexer para [gerar uma transcrição](https://docs.microsoft.com/azure/media-services/media-services-index-content) de um vídeo.

```csharp
static void Main(string[] args)
{
    using (ContentModeratorClient client = NewClient())
    {
        // Create a review with the content pointing to a streaming endpoint (manifest)
        var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
        string review_id = CreateReview(client, "review1", streamingcontent);

        var transcript = @"WEBVTT

        01:01.000 --> 02:02.000
        First line with a negative word in a transcript.

        02:03.000 --> 02:25.000
        This is another line in the transcript.
        ";

        AddTranscript(client, review_id, transcript);

        AddTranscriptModerationResult(client, review_id, transcript);

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

```console
Creating a video review.
Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="navigate-to-your-video-transcript-review"></a>Navegue até sua revisão de transcrição de vídeo

Vá para a revisão de transcrição de vídeo em sua ferramenta de revisão de Content Moderator na tela **Revisar**>**vídeo**>**transcrição** .

Você verá os seguintes recursos:
- As duas linhas de transcrição que você adicionou
- O termo de profanação encontrado e realçado pelo serviço de moderação de texto
- A seleção de um texto de transcrição inicia o vídeo desse carimbo de data/hora

![Análise de transcrição de vídeo para moderadores humanos](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>Passos seguintes

Obtenha o [SDK do .net content moderator](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) e a [solução do Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) para este e outros guias de início rápido Content moderator para .net.

Saiba como gerar [revisões de vídeo](video-reviews-quickstart-dotnet.md) na ferramenta de revisão.

Confira o tutorial detalhado sobre como desenvolver uma [solução de moderação de vídeo completa](video-transcript-moderation-review-tutorial-dotnet.md).
