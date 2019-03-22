---
title: Utilizar o moderador de conteúdo de multimédia do Azure para detetar possíveis conteúdos para adultos | Documentos da Microsoft
description: Moderação de vídeo ajuda a detetar potenciais conteúdos para adultos nos vídeos.
services: media-services
documentationcenter: ''
author: sanjeev3
manager: mikemcca
editor: ''
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: eb16f5e1e72e5a9379ad530ab9677adba2ccbbcd
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57899568"
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Utilizar o moderador de conteúdo de multimédia do Azure para detetar possíveis conteúdos para adultos 

## <a name="overview"></a>Descrição geral
O **moderador de conteúdo de multimédia do Azure** processador de multimédia (MP) permite-lhe utilizar moderação assistida por computador para os seus vídeos. Por exemplo, pode querer detetar possíveis conteúdos para adultos nos vídeos e rever o conteúdo sinalizado por suas equipes de moderação humana.

O **moderador de conteúdo de multimédia do Azure** MP está atualmente em pré-visualização.

Este artigo fornece detalhes sobre **moderador de conteúdo de multimédia do Azure** e mostra como utilizá-lo com o SDK de Media Services para .NET.

## <a name="content-moderator-input-files"></a>Ficheiros de entrada do moderador de conteúdo
Ficheiros de vídeo. Atualmente, são suportados os seguintes formatos: MP4, MOV e WMV.

## <a name="content-moderator-output-files"></a>Ficheiros de saída do conteúdo moderador
A saída moderada no formato JSON inclui capturas detetado automaticamente e quadros-chave. Os quadros-chave é devolvidos com pontuações de confiança para possível conteúdo adultos. Eles também incluem um sinalizador booleano que indica se uma revisão é recomendada. O sinalizador de recomendação de revisão é atribuído a valores com base nos limiares internos para as pontuações para adultos.

## <a name="elements-of-the-output-json-file"></a>Elementos do ficheiro de saída JSON

A tarefa produz um ficheiro de saída do JSON que contém metadados sobre capturas detetadas e quadros-chave e se eles contêm conteúdo de adultos.

A saída JSON inclui os seguintes elementos:

### <a name="root-json-elements"></a>Elementos de JSON de raiz

| Elemento | Descrição |
| --- | --- |
| versão |A versão do Content Moderator. |
| escala temporal |"Ticks" por segundo do vídeo. |
| offset |Desvio de tempo para carimbos de data/hora. Na versão 1.0 de APIs de vídeo, este valor será sempre 0. Este valor podem ser alterados no futuro. |
| framerate |Fotogramas por segundo do vídeo. |
| Largura |A largura do quadro de vídeo de saída, em pixéis.|
| Altura |A altura do quadro de vídeo de saída, em pixéis.|
| totalDuration |A duração da entrada de vídeo, em "ticks". |
| [fragments](#fragments-json-elements) |Os metadados é segmentado cópias em diferentes segmentos chamados fragmentos. Cada fragmento é uma captura automaticamente detetados com um início, a duração, o número do intervalo e o evento (s). |

### <a name="fragments-json-elements"></a>Elementos JSON de fragmentos

|Elemento|Descrição|
|---|---|
| start |A hora de início do primeiro evento na "ticks". |
| duração |O comprimento do fragmento, em "ticks". |
| intervalo |O intervalo de cada entrada de evento dentro do fragmento, na "ticks". |
| [events](#events-json-elements) |Cada evento representa um clip e cada clip contém quadros-chave detetados e monitorizados dentro desse período de tempo. É uma matriz de eventos. A matriz externa representa um intervalo de tempo. A matriz interna é constituída por 0 ou mais eventos que ocorreram nesse ponto no tempo.|

### <a name="events-json-elements"></a>Elementos JSON de eventos

|Elemento|Descrição|
|---|---|
| reviewRecommended | `true` ou `false` consoante esteja a **adultScore** ou **racyScore** exceder os limiares internos. |
| adultScore | Pontuação de confiança para possível conteúdo para adultos, numa escala de 0,00 para 0,99. |
| racyScore | Pontuação de confiança para possível conteúdo para adultos, numa escala de 0,00 para 0,99. |
| índice | índice do quadro numa escala do primeiro quadro de índice para o último índice do quadro. |
| carimbo de data/hora | A localização do quadro em "ticks". |
| shotIndex | Captura o índice do pai. |


## <a name="content-moderation-quickstart-and-sample-output"></a>Saída de exemplo e de início rápido de moderação de conteúdos

### <a name="task-configuration-preset"></a>Configuração da tarefa (predefinição)
Ao criar uma tarefa com **moderador de conteúdo de multimédia do Azure**, tem de especificar uma predefinição de configuração. A predefinição de configuração seguinte é apenas para moderação de conteúdos.

    {
      "version":"2.0"
    }

### <a name="net-code-sample"></a>Exemplo de código do .NET

O seguinte exemplo de código do .NET usa o SDK de .NET de serviços de multimédia para executar uma tarefa do Content Moderator. Demora um suporte de dados de serviços ativo como entrada que contém o vídeo para moderado.
Consulte a [guia de introdução de vídeo Content Moderator](../../cognitive-services/Content-Moderator/video-moderation-api.md) para o código-fonte completo e o projeto do Visual Studio.


```csharp
    /// <summary>
    /// Run the Content Moderator job on the designated Asset from local file or blob storage
    /// </summary>
    /// <param name="asset"></param>
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

For the full source code and the Visual Studio project, check out the [Content Moderator video quickstart](../../cognitive-services/Content-Moderator/video-moderation-api.md).

### JSON output

The following example of a Content Moderator JSON output was truncated.

> [!NOTE]
> Location of a keyframe in seconds = timestamp/timescale

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

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Ligações relacionadas
[Descrição geral da análise dos serviços de multimédia do Azure](media-services-analytics-overview.md)

[Demonstrações de análise de multimédia do Azure](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o Content Moderator [solução e revisão de moderação de vídeo](../../cognitive-services/Content-Moderator/video-moderation-human-review.md).

Obtenha o código-fonte completo e o projeto do Visual Studio a partir da [início rápido de moderação de vídeos](../../cognitive-services/Content-Moderator/video-moderation-api.md). 

Saiba como gerar [revisões de vídeo](../../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) da sua saída moderada, e [moderar as transcrições](../../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) no .NET.

Confira o .NET detalhadas [tutorial e revisão de moderação de vídeo](../../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md). 
