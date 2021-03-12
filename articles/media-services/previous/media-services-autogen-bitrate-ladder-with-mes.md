---
title: Utilize o Media Encoder Standard para gerar automaticamente uma escada de bitrate - Azure | Microsoft Docs
description: Este tópico mostra como usar o Media Encoder Standard (MES) para gerar automaticamente uma escada de bitrate com base na resolução de entrada e bitrate.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 6ea28d61cc142c3191d591721b92e08d651c7ed5
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103014405"
---
#  <a name="use-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Use o Media Encoder Standard para gerar automaticamente uma escada de bitrate

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

## <a name="overview"></a>Descrição Geral

Este artigo mostra como usar o Media Encoder Standard (MES) para gerar automaticamente uma escada de bitrate (pares de resolução de bitrate) com base na resolução de entrada e bitrate. A predefinição gerada automaticamente nunca excederá a resolução de entrada e o bitrate. Por exemplo, se a entrada for de 720p a 3 Mbps, a produção permanece 720p na melhor das hipóteses, e começará com taxas inferiores a 3 Mbps.

### <a name="encoding-for-streaming-only"></a>Codificação apenas para streaming

Se a sua intenção é codificar o seu vídeo de origem apenas para streaming, então deve utilizar a predefinição "Adaptive Streaming" ao criar uma tarefa de codificação. Ao utilizar a predefinição **de streaming adaptativo,** o codificar MES tapará inteligentemente uma escada de bitrate. No entanto, não será capaz de controlar os custos de codificação, uma vez que o serviço determina quantas camadas utilizar e em que resolução. Pode ver exemplos de camadas de saída produzidas pela MES como resultado da codificação com a predefinição **de Streaming Adaptativo** no final deste artigo. A saída Ativo contém ficheiros MP4 onde o áudio e o vídeo não são intercalados.

### <a name="encoding-for-streaming-and-progressive-download"></a>Codificação para streaming e download progressivo

Se a sua intenção é codificar o seu vídeo de origem para streaming, bem como produzir ficheiros MP4 para download progressivo, então deve utilizar a predefinição "Content Adaptive Multiple Bitrate MP4" ao criar uma tarefa de codificação. Ao utilizar o **conteúdo Adaptive Multiple Bitrate MP4** predefinido, o codificadora MES aplica a mesma lógica de codificação acima, mas agora o ativo de saída conterá ficheiros MP4 onde o áudio e o vídeo estão intercalados. Pode utilizar um destes ficheiros MP4 (por exemplo, a versão bitrate mais alta) como um ficheiro de descarregamento progressivo.

## <a name="encoding-with-media-services-net-sdk"></a><a id="encoding_with_dotnet"></a>Codificação com Serviços de Mídia .NET SDK

O seguinte exemplo de código utiliza os Serviços de Comunicação Social .NET SDK para executar as seguintes tarefas:

- Criar um trabalho de codificação.
- Obtenha uma referência ao codificar Media Encoder Standard.
- Adicione uma tarefa de codificação ao trabalho e especifique para utilizar a predefinição **de Streaming Adaptativo.** 
- Crie um ativo de saída que contenha o ativo codificado.
- Adicione um manipulador de eventos para verificar o progresso do trabalho.
- Submeta o trabalho.

#### <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio

Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento de Serviços de Multimédia com .NET). 

#### <a name="example"></a>Exemplo

```
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace AdaptiveStreamingMESPresest
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using the "Adaptive Streaming" preset.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");

            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            "Adaptive Streaming",
            TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
            AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }
        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:

                    // Cast sender as a job.
                    IJob job = (IJob)sender;

                    // Display or log error details as needed.
                    break;
                default:
                    break;
            }
        }
        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

## <a name="output"></a><a id="output"></a>Saída

Esta secção mostra três exemplos de camadas de saída produzidas pela MES como resultado da codificação com a predefinição **de streaming adaptativo.** 

### <a name="example-1"></a>Exemplo 1
Fonte com altura "1080" e framerate "29.970" produz 6 camadas de vídeo:

|Camada|Height|Width|Bitrate(kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Exemplo 2
Fonte com altura "720" e framerate "23.970" produz 5 camadas de vídeo:

|Camada|Height|Width|Bitrate(kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Exemplo 3
Fonte com altura "360" e framerate "29.970" produz 3 camadas de vídeo:

|Camada|Height|Width|Bitrate(kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consulte também
[Visão geral dos serviços de comunicação social](media-services-encode-asset.md)

