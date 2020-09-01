---
title: Utilize o Azure Media Content Content Moderador para detetar possíveis conteúdos adultos e picantes Microsoft Docs
description: O processador de mídia Azure Media Content Moderador ajuda a detetar potenciais conteúdos adultos e picantes em vídeos.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 395d2239970e7efbf6973d6262df0e049306584b
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266702"
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Utilize o Moderador de Conteúdo de Mídia Azure para detetar possíveis conteúdos adultos e picantes

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> O processador de mídia **Azure Media Content Moderador** será retirado. Para a data da aposentadoria, consulte o tema dos [componentes do legado.](legacy-components.md)

## <a name="overview"></a>Descrição geral
O processador de mídia **Azure Media Content (MP)** permite-lhe utilizar moderação assistida por máquinas para os seus vídeos. Por exemplo, poderá querer detetar possível conteúdo para adultos nos vídeos e rever o conteúdo sinalizado pelas suas equipas de moderação humana.

O **Moderador de Conteúdo de Azure Media** ESTÁ atualmente em Pré-Visualização.

Este artigo dá detalhes sobre  **o Azure Media Content Moderador** e mostra como usá-lo com a Media Services SDK para .NET.

## <a name="content-moderator-input-files"></a>Ficheiros de entrada de moderadores de conteúdo
Ficheiros de vídeo. Atualmente, os seguintes formatos são suportados: MP4, MOV e WMV.

## <a name="content-moderator-output-files"></a>Ficheiros de saída do Moderador de Conteúdo
A saída moderada no formato JSON inclui imagens e quadros automáticos detetados. Os quadros-chave são devolvidos com pontuações de confiança para um possível conteúdo adulto ou picante. Eles também incluem uma bandeira booleana indicando se uma revisão é recomendada. A bandeira de recomendação de revisão é atribuída valores baseados nos limiares internos para pontuações adultas e picantes.

## <a name="elements-of-the-output-json-file"></a>Elementos do ficheiro JSON de saída

O trabalho produz um ficheiro de saída JSON que contém metadados sobre imagens e quadros-chave detetados e se contêm conteúdo adulto ou picante.

A saída JSON inclui os seguintes elementos:

### <a name="root-json-elements"></a>Elementos raiz JSON

| Elemento | Descrição |
| --- | --- |
| versão |A versão do Moderador de Conteúdo. |
| escala de tempo |"Carrapatos" por segundo do vídeo. |
| offset |Desvio de tempo para carimbos de data/hora. Na versão 1.0 das APIs de Vídeo, este valor será sempre 0. Este valor pode mudar no futuro. |
| framerate |Fotogramas por segundo do vídeo. |
| largura |A largura da moldura de vídeo de saída, em pixels.|
| altura |A altura da estrutura de vídeo de saída, em pixels.|
| totalDuração |A duração do vídeo de entrada, em "tiques". |
| [fragmentos](#fragments-json-elements) |Os metadados são divididos em diferentes segmentos chamados fragmentos. Cada fragmento é um tiro auto-detectado com início, duração, número de intervalo e eventos. |

### <a name="fragments-json-elements"></a>Fragmentos elementos JSON

|Elemento|Descrição|
|---|---|
| iniciar |A hora de início do primeiro evento em "tiques". |
| duration |O comprimento do fragmento, em "carrapatos". |
| intervalo |O intervalo de cada entrada de evento dentro do fragmento, em "tiques". |
| [eventos](#events-json-elements) |Cada evento representa um clip e cada clip contém quadros de chaves detetados e rastreados dentro dessa duração. É uma variedade de eventos. A matriz externa representa um intervalo de tempo. A matriz interna é constituída por 0 ou mais eventos que ocorreram nesse ponto no tempo.|

### <a name="events-json-elements"></a>Elementos JSON de eventos

|Elemento|Descrição|
|---|---|
| reviewRecomumended | `true` ou `false` dependendo se o **adultScore** ou **racyScore** excedem os limiares internos. |
| adultScore | Pontuação de confiança para possível conteúdo adulto, numa escala de 0,00 a 0,99. |
| racyScore | Pontuação de confiança para possível conteúdo picante, numa escala de 0,00 a 0,99. |
| índice | índice do quadro numa escala do primeiro frame index para o último índice de quadro. |
| carimbo de data/hora | A localização da moldura em "tiques". |
| shotIndex | O índice da foto dos pais. |


## <a name="content-moderation-quickstart-and-sample-output"></a>Conteúdo Moderação quickstart e saída de amostra

### <a name="task-configuration-preset"></a>Configuração de tarefa (predefinição)
Ao criar uma tarefa com **o Azure Media Content Moderador,** tem de especificar uma pré-sintonia de configuração. A pré-configuração a seguir é apenas para moderação de conteúdo.

```json
{
    "version":"2.0"
}
```

### <a name="net-code-sample"></a>amostra de código .NET

A seguinte amostra de código .NET utiliza os Serviços de Comunicação Social .NET SDK para executar uma função de Moderador de Conteúdo. É preciso um serviço de comunicação Ativo como a entrada que contém o vídeo para ser moderado.
Consulte o [vídeo do Moderador de Conteúdo para](../../cognitive-services/Content-Moderator/video-moderation-api.md) obter o código fonte completo e o projeto Visual Studio.


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
```

Para obter o código fonte completo e o projeto Visual Studio, confira o [quickstart](../../cognitive-services/Content-Moderator/video-moderation-api.md)de vídeo do Content Moderator .

### <a name="json-output"></a>Saída JSON

O exemplo seguinte de uma saída JSON moderador de conteúdo foi truncado.

> [!NOTE]
> Localização de um quadro-chave em segundos = relógio/escala de tempo

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

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Ligações relacionadas
[Visão geral da Azure Media Services Analytics](media-services-analytics-overview.md)

[Azure Media Analytics demos](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a solução de [moderação e revisão](../../cognitive-services/Content-Moderator/video-moderation-human-review.md)de vídeo do Content Moderador.

Obtenha o código fonte completo e o projeto Visual Studio a partir do arranque rápido de [moderação de vídeo](../../cognitive-services/Content-Moderator/video-moderation-api.md). 

Saiba como gerar [avaliações](../../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) de vídeo a partir da sua saída moderada e [transcrições moderadas](../../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) em .NET.

Confira o tutorial detalhado de [moderação e revisão de vídeo](../../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md).NET . 
