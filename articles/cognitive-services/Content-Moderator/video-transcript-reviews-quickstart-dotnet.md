---
title: Criar as revisões de transcrição de vídeo com o .NET - Content Moderator
titlesuffix: Azure Cognitive Services
description: Criar as revisões de transcrição de vídeo com o SDK de moderador de conteúdo para .NET
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 2e33f94486fe295fffa1f0b4bbd298b15d9271f4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113738"
---
# <a name="create-video-transcript-reviews-using-net"></a>Criar as revisões de transcrição de vídeo com o .NET

Este artigo fornece informações e exemplos de código para ajudá-lo a rapidamente começar a utilizar o [SDK de moderador de conteúdo com o c#](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) para:

- Criar uma revisão de vídeo para moderadores humanos
- Adicionar uma transcrição moderada à revisão
- Publicar a revisão

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que tenha [moderado o vídeo](video-moderation-api.md) e [criada a revisão de vídeo](video-reviews-quickstart-dotnet.md) na ferramenta de revisão para tomada de decisões humana. Agora pretende adicionar transcrições de vídeo moderadas na ferramenta de revisão.

Este artigo também assume que já está familiarizado com o Visual Studio e c#.

## <a name="sign-up-for-content-moderator"></a>Inscrever-se no Content Moderator

Antes de poder utilizar os serviços do Content Moderator através da API REST ou do SDK, precisa de uma chave de subscrição. Siga as instruções em [Create a Cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) (Criar uma conta dos Serviços Cognitivos) para subscrever o Content Moderator e obter a sua chave.

## <a name="sign-up-for-a-review-tool-account-if-not-completed-in-the-previous-step"></a>Inscrever-se numa conta da ferramenta de revisão, se não o tiver feito no passo anterior

Se chegou ao Content Moderator a partir do portal do Azure, [inscreva-se na conta da ferramenta de revisão](https://contentmoderator.cognitive.microsoft.com/) e crie uma equipa de revisão. Irá precisar do ID da equipa e da ferramenta de revisão para chamar a API de revisão, para iniciar uma Tarefa e ver as revisões na ferramenta de revisão.

## <a name="ensure-your-api-key-can-call-the-review-api-job-creation"></a>Certifique-se de que a chave de API pode chamar a API de revisão (criação da tarefa)

Depois de concluir os passos anteriores, pode ficar com duas chaves do Content Moderator, se tiver iniciado a partir do portal do Azure. 

Se planear utilizar a chave de API dada pelo Azure no seu exemplo de SDK, siga os passos mencionados na secção [Utilizar a chave do Azure com a API de revisão](review-tool-user-guide/credentials.md#use-the-azure-account-with-the-review-tool-and-review-api) para permitir que a aplicação chame a API de revisão e crie as revisões.

Se utilizar a chave de avaliação gratuita gerada pela ferramenta de revisão, a sua conta da ferramenta de revisão já conhece a chave e, por conseguinte, não são precisos passos adicionais.

## <a name="prepare-your-video-for-review"></a>Preparar o seu vídeo para revisão

Adicione a transcrição uma análise de vídeo. O vídeo tem de ser publicado online. É necessário o seu ponto final de transmissão em fluxo. O ponto final de transmissão em fluxo permite que o leitor de vídeo de ferramenta de revisão reproduzir o vídeo.

![Miniatura de vídeo de demonstração](images/ams-video-demo-view.PNG)

- Copiar o **URL** nisso [dos serviços de multimédia do Azure de demonstração](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) página para o URL do manifesto.

## <a name="create-your-visual-studio-project"></a>Criar o projeto do Visual Studio

1. Adicione um novo projeto **Aplicação de consola (.NET Framework)** à sua solução.

1. Nomeie o projeto **VideoTranscriptReviews**.

1. Selecione este projeto como o projeto de arranque único para a solução.

### <a name="install-required-packages"></a>Instalar pacotes necessários

Instale os seguintes pacotes de NuGet para o projeto de TermLists.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Atualizar as instruções de utilização do programa

Modifique o programa do instruções using da seguinte forma.

    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;
    using Microsoft.Azure.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using Newtonsoft.Json;


### <a name="add-private-properties"></a>Adicionar propriedades privadas

Adicione as seguintes propriedades privadas ao espaço de nomes VideoTranscriptReviews, classe Program.

Sempre que for indicado, substitua os valores de exemplo para estas propriedades.


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


### <a name="create-content-moderator-client-object"></a>Criar objeto de cliente de moderador conteúdo

Adicione a seguinte definição de método ao espaço de nomes VideoTranscriptReviews, classe Program.

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

## <a name="create-a-video-review"></a>Criar uma revisão de vídeo

Criar uma revisão de vídeo com **ContentModeratorClient.Reviews.CreateVideoReviews**. Para obter mais informações, veja a [Referência à API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** tem os seguintes parâmetros obrigatórios:
1. Uma cadeia que contém um tipo MIME, o que deve ser "application/json". 
1. O nome de equipa do Content Moderator.
1. Uma **IList<CreateVideoReviewsBodyItem>**  objeto. Cada **CreateVideoReviewsBodyItem** objeto representa uma revisão de vídeo. Este início rápido cria uma revisão de cada vez.

**CreateVideoReviewsBodyItem** tem várias propriedades. No mínimo, defina as propriedades seguintes:
- **Conteúdo**. O URL do vídeo para ser revisto.
- **ContentId**. Um ID para atribuir a revisão de vídeo.
- **Estado**. Definir o valor para "Unpublished." Se não defini-lo, ele é predefinida como "Pendentes", que significa que a revisão de vídeo é publicada e pendentes de revisão humana. Assim que for publicada uma revisão de vídeo, já não pode adicionar quadros de vídeo, transcrição ou um resultado de moderação de transcrição para ele.

> [!NOTE]
> **CreateVideoReviews** retorna um IList<string>. Cada uma destas cadeias contém um ID para uma revisão de vídeo. Essas IDs são GUIDs e não são os mesmos que o valor do **ContentId** propriedade. 

Adicione a seguinte definição de método ao espaço de nomes VideoReviews, classe Program.

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

> [!NOTE]
> A chave do serviço do Content Moderator tem um limite de velocidade de pedidos por segundo (RPS). Se ultrapassar o limite, o SDK emite uma exceção com o código de erro 429. 
>
> Uma chave de escalão gratuito tem um limite de velocidade de um RPS.

## <a name="add-transcript-to-video-review"></a>Adicionar transcrição à revisão de vídeo

Adicionar uma transcrição uma análise de vídeo com **ContentModeratorClient.Reviews.AddVideoTranscript**. **AddVideoTranscript** tem os seguintes parâmetros obrigatórios:
1. ID da sua equipa do Content Moderator.
1. O ID de revisão de vídeo devolvido por **CreateVideoReviews**.
1. R **Stream** objeto que contém a transcrição.

A transcrição tem de estar no formato WebVTT. Para obter mais informações, consulte [WebVTT: O texto de vídeo da Web controla o formato](https://www.w3.org/TR/webvtt1/).

> [!NOTE]
> O programa usa uma transcrição de exemplo no formato VTT. Uma solução do mundo real, vai utilizar o serviço do Azure Media Indexer [gerar uma transcrição](https://docs.microsoft.com/azure/media-services/media-services-index-content) de um vídeo.

Adicione a seguinte definição de método ao espaço de nomes VideotranscriptReviews, classe Program.

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

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Adicionar um resultado de moderação de transcrição para revisão de vídeo

Além de adicionar uma transcrição uma análise de vídeo, também adicionar o resultado da moderating esse transcrição. Fazer isso com **ContentModeratorClient.Reviews.AddVideoTranscriptModerationResult**. Para obter mais informações, veja a [Referência à API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**AddVideoTranscriptModerationResult** tem os seguintes parâmetros obrigatórios:
1. Uma cadeia que contém um tipo MIME, o que deve ser "application/json". 
1. O nome de equipa do Content Moderator.
1. O ID de revisão de vídeo devolvido por **CreateVideoReviews**.
1. Um IList<TranscriptModerationBodyItem>. R **TranscriptModerationBodyItem** tem as seguintes propriedades:
1. **Termos**. Um IList<TranscriptModerationBodyItemTermsItem>. R **TranscriptModerationBodyItemTermsItem** tem as seguintes propriedades:
1. **Índice**. O índice baseado em zero do termo.
1. **Termo**. Uma cadeia que contém o termo.
1. **Timestamp**. Uma cadeia que contém, em segundos, o tempo na transcrição em que os termos encontram-se.

A transcrição tem de estar no formato WebVTT. Para obter mais informações, consulte [WebVTT: O texto de vídeo da Web controla o formato](https://www.w3.org/TR/webvtt1/).

Adicione a seguinte definição de método ao espaço de nomes VideoTranscriptReviews, classe Program. Este método envia uma transcrição de acordo com o **ContentModeratorClient.TextModeration.ScreenText** método. Ele também converte o resultado num IList<TranscriptModerationBodyItem>e é submetida **AddVideoTranscriptModerationResult**.

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


## <a name="publish-video-review"></a>Publicar vídeo revisão

Publicar uma revisão de vídeo com **ContentModeratorClient.Reviews.PublishVideoReview**. **PublishVideoReview** tem os seguintes parâmetros obrigatórios:
1. O nome de equipa do Content Moderator.
1. O ID de revisão de vídeo devolvido por **CreateVideoReviews**.

Adicione a seguinte definição de método ao espaço de nomes VideoReviews, classe Program.

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

## <a name="putting-it-all-together"></a>Juntar tudo

Adicionar a **Main** definição de método ao espaço de nomes VideoTranscriptReviews, classe de programa. Por fim, feche a classe de programa e o espaço de nomes de VideoTranscriptReviews.

> [!NOTE]
> O programa usa uma transcrição de exemplo no formato VTT. Uma solução do mundo real, vai utilizar o serviço do Azure Media Indexer [gerar uma transcrição](https://docs.microsoft.com/azure/media-services/media-services-index-content) de um vídeo. 

    static void Main(string[] args)
    {
        using (ContentModeratorClient client = NewClient())
        {
            // Create a review with the content pointing to a streaming endpoint (manifest)
            var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
            string review_id = CreateReview(client, "review1", streamingcontent);

            var transcript = @"WEBVTT

            01:01.000 --> 02:02.000
            First line with a crap word in a transcript.

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

## <a name="run-the-program-and-review-the-output"></a>Executar o programa e rever o resultado

Quando executar o aplicativo, verá um resultado nas seguintes linhas:

    Creating a video review.
    Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Open your Content Moderator Dashboard and select Review > Video to see the review.
    Press any key to close the application.

## <a name="navigate-to-your-video-transcript-review"></a>Navegue até à sua revisão de transcrição de vídeo

Ir para a revisão de transcrição de vídeo na sua ferramenta de revisão do Content Moderator sobre o **reveja**>**vídeo**>**transcrição** ecrã.

Consulte as seguintes funcionalidades:
- As duas linhas de transcrição que adicionou
- O termo de linguagem inapropriada encontrados e realçado pelo serviço de moderação de texto
- Selecionar um texto de transcrição começa o vídeo a partir desse timestamp

![Revisão de transcrição de vídeo para moderadores humanos](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>Passos Seguintes

Obter o [SDK de .NET do conteúdo moderador](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) e o [solução do Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) para esse e outros guias de introdução do Content Moderator para .NET.

Saiba como gerar [revisões de vídeo](video-reviews-quickstart-dotnet.md) na ferramenta de revisão.

Veja o tutorial detalhado sobre como desenvolver um [concluir a solução de moderação de vídeos](video-transcript-moderation-review-tutorial-dotnet.md).
