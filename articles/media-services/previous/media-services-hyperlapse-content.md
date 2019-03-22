---
title: Ficheiros de Hyperlapse de multimédia com Hyperlapse de multimédia do Azure | Documentos da Microsoft
description: Hyperlapse de multimédia do Azure cria vídeos com time lapse uniformes de conteúdo primeira pessoa ou ação câmara. Este tópico mostra como utilizar o indexador de multimédia.
services: media-services
documentationcenter: ''
author: asolanki
manager: johndeu
editor: ''
ms.assetid: 37d54db6-9cf3-4ae9-b3c6-0d29c744e965
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: adsolank
ms.openlocfilehash: a57194a0c7b0cd82ce3d9426211b0cf6fc0d1182
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188055"
---
# <a name="hyperlapse-media-files-with-azure-media-hyperlapse"></a>Ficheiros de Hyperlapse de multimédia com Hyperlapse de multimédia do Azure

> [!NOTE]
> Em breve, será descontinuada esta funcionalidade de pré-visualização dos serviços de multimédia do Azure. A partir de 19 de Dezembro de 2018, os serviços de multimédia já não fará alterações ou melhorias no Hyperlapse de multimédia. 29 de Março de 2019 será extinto e já não está disponível.

Hyperlapse de multimédia do Azure é um suporte de dados de processador (MP) que cria vídeos com time lapse uniformes de conteúdo de primeira pessoa ou ação câmara.  O colateral baseado na nuvem para [Hyperlapse Pro de área de trabalho e Hyperlapse Mobile baseado no telefone do Microsoft Research](https://aka.ms/hyperlapse), Microsoft Hyperlapse para serviços de multimédia do Azure utiliza o dimensionamento em massa de processamento do suporte de dados do Azure suporte de dados serviços plataforma horizontalmente dimensionar e colocar em paralelo em massa Hyperlapse de processamento.

> [!IMPORTANT]
> Microsoft Hyperlapse foi projetado para funcionar melhor em primeira pessoa conteúdo com uma câmera de movimentação. Embora ainda podem funcionar filmagens de câmaras ainda, o desempenho e qualidade do processador de multimédia de Hyperlapse de multimédia do Azure não podem ser garantidas para outros tipos de conteúdo.
> 
> 

Um Hyperlapse de multimédia do Azure tarefa aceita como entrada um arquivo de recurso MP4, MOV ou WMV, juntamente com um ficheiro de configuração que especifica quais fotogramas do vídeo devem ser com time lapse e a velocidade de que (por exemplo, 10 000 primeiro enquadra-se em 2 x).  O resultado é uma representação estabilizada e com time lapse e do vídeo de entrada.

## <a name="hyperlapse-an-asset"></a>Hyperlapse um recurso
Primeiro terá de carregar o ficheiro de entrada desejado para serviços de multimédia do Azure.  Para saber mais sobre os conceitos envolvidos com o carregamento e a gestão de conteúdo, veja a [artigo de gerenciamento de conteúdo](media-services-portal-vod-get-started.md).

### <a id="configuration"></a>Configuração predefinida de configuração para o Hyperlapse
Assim que o conteúdo estiver na sua conta de Media Services, terá de construir sua configuração predefinida de configuração.  A tabela seguinte explica os campos especificado pelo utilizador:

| Campo | Descrição |
| --- | --- |
| StartFrame |O quadro no qual deve começar o processamento de Microsoft Hyperlapse. |
| NumFrames |O número de quadros para processar |
| Velocidade |O fator com a qual a acelerar o vídeo de entrada. |

Segue-se um exemplo de um ficheiro de configuração compatível em XML e JSON:

**Configuração predefinida XML:**
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
        <Sources>
            <Source StartFrame="0" NumFrames="10000" />
        </Sources>
        <Options>
            <Speed>12</Speed>
        </Options>
    </Preset>
```

**Configuração predefinida JSON:**
```json
    {
        "Version":1.0,
        "Sources": [
            {
                "StartFrame":0,
                "NumFrames":2147483647
            }
        ],
        "Options": {
            "Speed":1,
            "Stabilize":false
        }
    }
```

### <a id="sample_code"></a> Microsoft Hyperlapse com o SDK de .NET do AMS
O método seguinte carrega um ficheiro de suporte de dados como um recurso e cria uma tarefa com o processador de multimédia de Hyperlapse de multimédia do Azure.

> [!NOTE]
> Já deverá ter um CloudMediaContext no âmbito com o nome "contexto" para este código funcione.  Para obter mais informações, leia os [artigo de gerenciamento de conteúdo](media-services-dotnet-get-started.md).
> 
> [!NOTE]
> O argumento de cadeia de caracteres "hyperConfig" deve ser uma configuração compatível predefinida em JSON ou XML, conforme descrito acima.
> 
> 

```csharp
        static bool RunHyperlapseJob(string input, string output, string hyperConfig)
        {
            // create asset with input file
            IAsset asset = context
            .Assets
            .CreateAssetAndUploadSingleFile(input, "My Hyperlapse Input", AssetCreationOptions.None);

            // grab instances of Azure Media Hyperlapse MP
            IMediaProcessor mp = context
            .MediaProcessors
            .GetLatestMediaProcessorByName("Azure Media Hyperlapse");

            // create Job with Hyperlapse task
            IJob job = context
            .Jobs
            .Create(String.Format("Hyperlapse {0}", input));

            if (String.IsNullOrEmpty(hyperConfig))
            {
            // config cannot be empty
            return false;
            }

            hyperConfig = File.ReadAllText(hyperConfig);

            ITask hyperlapseTask = job.Tasks.AddNew("Hyperlapse task",
            mp,
            hyperConfig,
            TaskOptions.None);
            hyperlapseTask.InputAssets.Add(asset);
            hyperlapseTask.OutputAssets.AddNew("Hyperlapse output",
            AssetCreationOptions.None);

            job.Submit();

            // Create progress printing and querying tasks
            Task progressPrintTask = new Task(() =>
            {

            IJob jobQuery = null;
            do
            {
                var progressContext = context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                .First();
                Console.WriteLine(string.Format("{0}\t{1}\t{2}",
                DateTime.Now,
                jobQuery.State,
                jobQuery.Tasks[0].Progress));
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
                return false;                  
            }

        DownloadAsset(job.OutputMediaAssets.First(), output);
        return true;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }


    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }
```

### <a id="file_types"></a>Tipos de ficheiro suportados
* MP4
* MOV
* WMV

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Ligações relacionadas
[Descrição geral da análise dos serviços de multimédia do Azure](media-services-analytics-overview.md)

[Demonstrações de análise de multimédia do Azure](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

