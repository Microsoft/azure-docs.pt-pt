---
title: Criar comentários de transcrição de vídeo usando .NET - Moderador de Conteúdo
titleSuffix: Azure Cognitive Services
description: Saiba como criar comentários de transcrição de vídeo utilizando o Moderador de Conteúdo de Serviços Cognitivos Azure SDK para .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 326fc2cc162a2ab54b40888250fbeef55ad8800a
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853463"
---
# <a name="create-video-transcript-reviews-using-net"></a>Criar comentários de transcrição de vídeo usando .NET

Este artigo fornece informações e amostras de código para ajudá-lo a começar rapidamente a usar o [Moderador de Conteúdo SDK com C#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) para:

- Crie uma revisão em vídeo para moderadores humanos
- Adicione uma transcrição moderada à revisão
- Publicar a opiniãoda

## <a name="prerequisites"></a>Pré-requisitos

- Faça o sessão ou crie uma conta no site da [ferramenta Content](https://contentmoderator.cognitive.microsoft.com/) Moderator Review se ainda não o fez.
- Este artigo assume que [moderaste o vídeo](video-moderation-api.md) e [criaste a análise](video-reviews-quickstart-dotnet.md) de vídeo na ferramenta de revisão para a tomada de decisões humanas. Agora pretende adicionar transcrições de vídeo moderadas na ferramenta de revisão.

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>Certifique-se de que a sua chave API pode chamar a API de revisão (criação de emprego)

Depois de concluir os passos anteriores, pode ficar com duas chaves do Content Moderator, se tiver iniciado a partir do portal do Azure.

Se planear utilizar a chave de API dada pelo Azure no seu exemplo de SDK, siga os passos mencionados na secção [Utilizar a chave do Azure com a API de revisão](./review-tool-user-guide/configure.md#use-your-azure-account-with-the-review-apis) para permitir que a aplicação chame a API de revisão e crie as revisões.

Se utilizar a chave de avaliação gratuita gerada pela ferramenta de revisão, a sua conta da ferramenta de revisão já conhece a chave e, por conseguinte, não são precisos passos adicionais.

## <a name="prepare-your-video-for-review"></a>Prepare o seu vídeo para revisão

Adicione a transcrição a uma revisão de vídeo. O vídeo deve ser publicado online. Precisas do seu ponto final de streaming. O ponto final de streaming permite que o leitor de vídeo da ferramenta de revisão reproduza o vídeo.

![Miniatura de demonstração de vídeo](images/ams-video-demo-view.PNG)

- Copie o **URL** nesta página [de demonstração do Azure Media Services](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) para o URL manifesto.

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

Modifique as declarações do programa da seguinte forma.


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

Adicione as seguintes propriedades privadas ao espaço de nome **VideoTranscriptReviews,** **programa** de classe . Atualize os `AzureEndpoint` campos e os campos com os `CMSubscriptionKey` valores do URL do ponto final e da chave de subscrição. Pode encontrá-las no separador **Início Rápido** do seu recurso no portal Azure.

```csharp
namespace VideoReviews
{
    class Program
    {
        // NOTE: Enter a valid endpoint URL
        /// <summary>
        /// The endpoint URL of your subscription
        /// </summary>
        private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

        // NOTE: Enter a valid subscription key.
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
        /// The minimum amount of time, in milliseconds, to wait between calls
        /// to the Content Moderator APIs.
        /// </summary>
        private const int throttleRate = 2000;
```

### <a name="create-content-moderator-client-object"></a>Criar objeto de cliente moderador de conteúdo

Adicione a seguinte definição de método ao espaço de nome VideoTranscriptReviews, programa de classe.

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
        Endpoint = AzureEndpoint
    };
}
```

## <a name="create-a-video-review"></a>Criar uma análise de vídeo

Crie uma revisão de vídeo com **ContentModeratorClient.Reviews.CreateVideoReviews**. Para obter mais informações, veja a [Referência à API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**A CreateVideoReviews** tem os seguintes parâmetros necessários:
1. Uma corda que contém um tipo MIME, que deve ser "aplicação/json". 
1. O nome da sua equipa de moderador de conteúdo.
1. Um objeto **IList. \<CreateVideoReviewsBodyItem>** Cada objeto **CreateVideoReviewsBodyItem** representa uma revisão de vídeo. Este quickstart cria uma revisão de cada vez.

**A CreateVideoReviewsBodyItem** tem várias propriedades. No mínimo, define as seguintes propriedades:
- **Conteúdo**. A URL do vídeo a rever.
- **ContentId**. Uma identificação para atribuir à revisão de vídeo.
- **Estado**. Desaprova o valor de "Não publicado". Se não o definir, não tem "Pendente", o que significa que a revisão de vídeo é publicada e pendente de revisão humana. Uma vez publicada uma revisão de vídeo, já não é possível adicionar quadros de vídeo, uma transcrição ou um resultado de moderação de transcrição.

> [!NOTE]
> **CreateVideoReviews** devolve uma Lista IList \<string> . Cada uma destas cordas contém uma identificação para uma revisão de vídeo. Estes IDs são GUIDs e não são os mesmos que o valor da propriedade **ContentId.**

Adicione a seguinte definição de método ao espaço de nome VideoReviews, programa de classe.

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

Adicione uma transcrição a uma revisão de vídeo com **ContentModeratorClient.Reviews.AddVideoTranscript**. **AddVideoTranscript** tem os seguintes parâmetros necessários:
1. A sua equipa de moderadores de conteúdo identifica.
1. O ID de revisão de vídeo devolvido pela **CreateVideoReviews**.
1. Um objeto **stream** que contém a transcrição.

A transcrição deve estar no formato WebVTT. Para obter mais informações, consulte [WebVTT: O formato de faixas de texto de vídeo web](https://www.w3.org/TR/webvtt1/).

> [!NOTE]
> O programa utiliza uma transcrição de amostra no formato VTT. Numa solução real, você usa o serviço Azure Media Indexer para [gerar uma transcrição](../../media-services/previous/media-services-index-content.md) de um vídeo.

Adicione a seguinte definição de método ao namespace VideotranscriptReviews, programa de classe.

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

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Adicione um resultado de moderação de transcrição à revisão de vídeo

Além de adicionar uma transcrição a uma revisão de vídeo, também adiciona o resultado de moderar essa transcrição. Fá-lo com **o ContentModeratorClient.Reviews.AddVideoTranscriptModerationResult**. Para obter mais informações, veja a [Referência à API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**AddVideoTranscriptModerationResult** tem os seguintes parâmetros necessários:
1. Uma corda que contém um tipo MIME, que deve ser "aplicação/json". 
1. O nome da sua equipa de moderador de conteúdo.
1. O ID de revisão de vídeo devolvido pela **CreateVideoReviews**.
1. Uma \<TranscriptModerationBodyItem> Lista. A **TranscriptModerationBodyItem** tem as seguintes propriedades:
1. **Termos**. Uma \<TranscriptModerationBodyItemTermsItem> Lista. A **TranscriptModerationBodyItemTermsItem** tem as seguintes propriedades:
1. **Índice**. O índice baseado em zero do termo.
1. **Prazo**. Uma corda que contém o termo.
1. **Tempotam**. Uma corda que contém, em segundos, o tempo na transcrição em que os termos são encontrados.

A transcrição deve estar no formato WebVTT. Para obter mais informações, consulte [WebVTT: O formato de faixas de texto de vídeo web](https://www.w3.org/TR/webvtt1/).

Adicione a seguinte definição de método ao espaço de nome VideoTranscriptReviews, programa de classe. Este método submete uma transcrição ao método **ContentModeratorClient.TextModeration.ScreenText.** Também traduz o resultado numa \<TranscriptModerationBodyItem> IList, e submete-se a **AddVideoTranscriptModerationResult**.

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

## <a name="publish-video-review"></a>Publicar análise de vídeo

Publica uma análise de vídeo com **ContentModeratorClient.Reviews.PublishVideoReview**. **A PublishVideoReview** tem os seguintes parâmetros necessários:
1. O nome da sua equipa de moderador de conteúdo.
1. O ID de revisão de vídeo devolvido pela **CreateVideoReviews**.

Adicione a seguinte definição de método ao espaço de nome VideoReviews, programa de classe.

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

Adicione a definição de método **principal** ao espaço de nome VideoTranscriptReviews, programa de classe. Por fim, feche a classe Program e o espaço de nomes VideoTranscriptReviews.

> [!NOTE]
> O programa utiliza uma transcrição de amostra no formato VTT. Numa solução real, você usa o serviço Azure Media Indexer para [gerar uma transcrição](../../media-services/previous/media-services-index-content.md) de um vídeo.

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

Quando executam a aplicação, vemos uma saída nas seguintes linhas:

```console
Creating a video review.
Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
Open your Content Moderator Dashboard and select Review > Video to see the review.
Press any key to close the application.
```

## <a name="navigate-to-your-video-transcript-review"></a>Navegue para a sua revisão de transcrição de vídeo

Aceda à revisão da transcrição de vídeo na sua ferramenta de revisão do Moderador de Conteúdo no ecrã **de Transcrição** de Vídeo de > **Video** > **Transcript** Revisão.

Vê as seguintes características:
- As duas linhas de transcrição que acrescentou
- O termo profanidade encontrado e realçado pelo serviço de moderação de texto
- Selecionando um texto de transcrição começa o vídeo a partir desse timetamp

![Revisão da transcrição de vídeo para moderadores humanos](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>Passos seguintes

Obtenha o [Moderador de Conteúdo .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) e a [solução Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) para este e outros rápidos moderadores de conteúdo para .NET.

Saiba como gerar [avaliações de vídeo](video-reviews-quickstart-dotnet.md) na ferramenta de revisão.
