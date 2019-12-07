---
title: Usar o Content Moderator de mídia do Azure para detectar possíveis conteúdo adulto e erótico | Microsoft Docs
description: O processador de mídia do Azure Media Content Moderator ajuda a detectar possíveis conteúdos de conteúdo adulto e erótico em vídeos.
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
ms.openlocfilehash: a8560df6120dd773e13dbfc7427d9a16e6f6c83b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896006"
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Usar o Content Moderator de mídia do Azure para detectar possíveis conteúdo adulto e erótico 

## <a name="overview"></a>Visão geral
O MP (processador de mídia) do **Azure media Content moderator** permite que você use a moderação assistida por computador para seus vídeos. Por exemplo, poderá querer detetar possível conteúdo para adultos nos vídeos e rever o conteúdo sinalizado pelas suas equipas de moderação humana.

O **Azure Media Content moderator** MP está atualmente em visualização.

Este artigo fornece detalhes sobre o **Content moderator de mídia do Azure** e mostra como usá-lo com o SDK dos serviços de mídia para .net.

## <a name="content-moderator-input-files"></a>Content Moderator arquivos de entrada
Arquivos de vídeo. Atualmente, há suporte para os seguintes formatos: MP4, MOV e WMV.

## <a name="content-moderator-output-files"></a>Content Moderator arquivos de saída
A saída moderada no formato JSON inclui capturas e quadros-chave detectados automaticamente. Os quadros-chave são retornados com pontuações de confiança para conteúdo adulto ou erótico possível. Eles também incluem um sinalizador booliano que indica se uma revisão é recomendada. O sinalizador de recomendação de revisão recebe valores com base nos limites internos para pontuações de adulto e erótico.

## <a name="elements-of-the-output-json-file"></a>Elementos do arquivo JSON de saída

O trabalho produz um arquivo de saída JSON que contém metadados sobre capturas e quadros-chave detectados e se eles contêm conteúdo adulto ou erótico.

O JSON de saída inclui os seguintes elementos:

### <a name="root-json-elements"></a>Elementos JSON raiz

| Elemento | Descrição |
| --- | --- |
| versão |A versão do Content Moderator. |
| timescale |"Tiques" por segundo do vídeo. |
| desvio |Desvio de tempo para carimbos de data/hora. Na versão 1,0 de APIs de vídeo, esse valor será sempre 0. Esse valor pode ser alterado no futuro. |
| quadros |Fotogramas por segundo do vídeo. |
| largura |A largura do quadro de vídeo de saída, em pixels.|
| altura |A altura do quadro de vídeo de saída, em pixels.|
| totalDuration |A duração do vídeo de entrada, em "tiques". |
| [fragments](#fragments-json-elements) |Os metadados são divididos em segmentos diferentes chamados fragmentos. Cada fragmento é uma captura detectada automaticamente com início, duração, número de intervalo e evento (s). |

### <a name="fragments-json-elements"></a>Fragmentos de elementos JSON

|Elemento|Descrição|
|---|---|
| start |A hora de início do primeiro evento em "tiques". |
| duration |O comprimento do fragmento, em "tiques". |
| intervalo |O intervalo de cada entrada de evento dentro do fragmento, em "tiques". |
| [LostFocus](#events-json-elements) |Cada evento representa um clipe e cada clipe contém quadros-chave detectados e acompanhados dentro dessa duração de tempo. É uma matriz de eventos. A matriz externa representa um intervalo de tempo. A matriz interna é constituída por 0 ou mais eventos que ocorreram nesse ponto no tempo.|

### <a name="events-json-elements"></a>Elementos JSON de eventos

|Elemento|Descrição|
|---|---|
| reviewRecommended | `true` ou `false` dependendo se **adultScore** ou **racyScore** excedem os limites internos. |
| adultScore | Pontuação de confiança para conteúdo adulto possível, em uma escala de 0, 0 a 0,99. |
| racyScore | Pontuação de confiança para conteúdo de erótico possível, em uma escala de 0, 0 a 0,99. |
| index | índice do quadro em uma escala do primeiro índice de quadro para o último índice de quadro. |
| carimbo de data/hora | O local do quadro em "tiques". |
| shotIndex | O índice da captura pai. |


## <a name="content-moderation-quickstart-and-sample-output"></a>Início rápido de moderação de conteúdo e saída de exemplo

### <a name="task-configuration-preset"></a>Configuração de tarefa (predefinição)
Ao criar uma tarefa com o **Content moderator de mídia do Azure**, você deve especificar uma predefinição de configuração. A predefinição de configuração a seguir é apenas para moderação de conteúdo.

    {
      "version":"2.0"
    }

### <a name="net-code-sample"></a>Exemplo de código .NET

O exemplo de código .NET a seguir usa o SDK do .NET dos serviços de mídia para executar um trabalho Content Moderator. Ele usa um ativo de serviços de mídia como a entrada que contém o vídeo a ser moderado.
Consulte o guia de [início rápido de vídeo Content moderator](../../cognitive-services/Content-Moderator/video-moderation-api.md) para obter o código-fonte completo e o projeto do Visual Studio.


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

## <a name="related-links"></a>Hiperligações relacionadas
[Visão geral da análise dos serviços de mídia do Azure](media-services-analytics-overview.md)

[Demonstrações de Análise de Mídia do Azure](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a [solução de análise e moderação de vídeo](../../cognitive-services/Content-Moderator/video-moderation-human-review.md)de Content Moderator.

Obtenha o código-fonte completo e o projeto do Visual Studio do guia de [início rápido de moderação do vídeo](../../cognitive-services/Content-Moderator/video-moderation-api.md). 

Saiba como gerar [revisões de vídeo](../../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) de sua saída moderada e [transcrições moderadas](../../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) no .net.

Confira o tutorial detalhado de [moderação e análise de vídeo](../../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md).net. 
