---
title: Use Media Encoder Standard para gerar automaticamente uma escada bitrate - Azure [ Microsoft Docs
description: Este tópico mostra como usar media Encoder Standard (MES) para gerar automaticamente uma escada bitrate com base na resolução de entrada e bitrate.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: b7f0b77ba11a0c9c1670ec240caf45fcf61a934d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74896026"
---
#  <a name="use-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Use Media Encoder Standard para gerar automaticamente uma escada bitrate  

## <a name="overview"></a>Descrição geral

Este artigo mostra como usar media Encoder Standard (MES) para gerar automaticamente uma escada bitrate (pares de resolução bitrate) com base na resolução de entrada e bitrate. O preset gerado automaticamente nunca excederá a resolução de entrada e o bitrate. Por exemplo, se a entrada for de 720p a 3 Mbps, a saída permanece 720p na melhor das hipóteses, e começará a taxas inferiores a 3 Mbps.

### <a name="encoding-for-streaming-only"></a>Codificação apenas para streaming

Se a sua intenção é codificar o seu vídeo de origem apenas para streaming, então deve utilizar o predefinido "Adaptive Streaming" ao criar uma tarefa de codificação. Ao utilizar o predefinido **de Streaming Adaptativo,** o codificador MES irá ligar inteligentemente uma escada de bitrate. No entanto, não será capaz de controlar os custos de codificação, uma vez que o serviço determina quantas camadas usar e em que resolução. Pode ver exemplos de camadas de saída produzidas por MES como resultado da codificação com o predefinido **de Streaming Adaptativo** no final deste artigo. O Ativo de saída contém ficheiros MP4 onde o áudio e o vídeo não são interleaved.

### <a name="encoding-for-streaming-and-progressive-download"></a>Codificação para streaming e download progressivo

Se a sua intenção é codificar o seu vídeo de origem para streaming, bem como produzir ficheiros MP4 para download progressivo, então deve utilizar o predefinido "Content Adaptive Multiple Bitrate MP4" ao criar uma tarefa de codificação. Ao utilizar o predefinido **de MP4 de bitrate múltiplo** adaptável de conteúdo, o codificador MES aplica a mesma lógica de codificação que acima, mas agora o ativo de saída conterá ficheiros MP4 onde o áudio e o vídeo são interleaved. Pode utilizar um destes ficheiros MP4 (por exemplo, a versão bitrate mais alta) como ficheiro de descarregamento progressivo.

## <a name="encoding-with-media-services-net-sdk"></a><a id="encoding_with_dotnet"></a>Codificação com Serviços de Media .NET SDK

O exemplo de código que se segue utiliza media services .NET SDK para executar as seguintes tarefas:

- Criar um trabalho de codificação.
- Obtenha uma referência ao codificador Padrão do Codificador De Media.
- Adicione uma tarefa de codificação ao trabalho e especifique para utilizar o pré-definido **de Streaming Adaptativo.** 
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

Esta secção mostra três exemplos de camadas de saída produzidas por MES como resultado da codificação com o predefinido **de Streaming Adaptativo.** 

### <a name="example-1"></a>Exemplo 1
Fonte com altura "1080" e framerate "29.970" produz 6 camadas de vídeo:

|Camada|Altura|Largura|Bitrate (kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Exemplo 2
Fonte com altura "720" e framerate "23.970" produz 5 camadas de vídeo:

|Camada|Altura|Largura|Bitrate (kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Exemplo 3
Fonte com altura "360" e framerate "29.970" produz 3 camadas de vídeo:

|Camada|Altura|Largura|Bitrate (kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Veja também
[Visão geral de codificação de serviços de mídia](media-services-encode-asset.md)

