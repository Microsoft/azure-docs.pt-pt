---
title: Utilizar o Azure Media Encoder Standard para gerar automaticamente uma escala de bits | Documentos da Microsoft
description: Este tópico mostra como usar o Media Encoder Standard (MES) para gerar automaticamente uma escala de bits com base na resolução de entrada e de velocidade de transmissão. A resolução de entrada e a velocidade de transmissão nunca serão excedidas. Por exemplo, se a entrada é 720p em 3 Mbps, a saída irá permanecer 720p na melhor e será iniciado às tarifas inferiores a 3 Mbps.
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
ms.openlocfilehash: bbaf4d490fcebb4cd741a9b83ffc5d7e85699755
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57991867"
---
#  <a name="use-azure-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Utilizar o Azure Media Encoder Standard para gerar automaticamente uma escala de bits  

## <a name="overview"></a>Descrição geral

Este artigo mostra como usar o Media Encoder Standard (MES) para gerar automaticamente uma escala de bits (pares de resolução de velocidade de transmissão) com base na resolução de entrada e de velocidade de transmissão. A configuração predefinida de gerado automaticamente nunca irá exceder a resolução de entrada e a velocidade de transmissão. Por exemplo, se a entrada for 720p a 3 Mbps, saída permanece 720p na melhor e será iniciado às tarifas inferiores a 3 Mbps.

### <a name="encoding-for-streaming-only"></a>Codificação de transmissão em fluxo apenas

Se sua intenção é codificar o seu vídeo de origem apenas para transmissão em fluxo, em seguida, deve usar o "transmissão em fluxo adaptável" configuração predefinida durante a criação de uma tarefa de codificação. Ao utilizar o **transmissão em fluxo adaptável** configuração predefinida, o codificador MES será multiplexado limite uma escala de bits. No entanto, não será capaz de para controlar a codificação de custos, uma vez que o serviço determina quantas camadas para utilizar e que uma resolução. Pode ver exemplos de camadas de saída produzidos pelo MES como resultado de codificação com o **transmissão em fluxo adaptável** predefinidos no final deste artigo. A saída que Asset contém os ficheiros MP4 em que o áudio e vídeo não está a ser intercalada.

### <a name="encoding-for-streaming-and-progressive-download"></a>Codificação para transmissão em fluxo e transferência progressiva

Se sua intenção for para codificar o seu vídeo de origem para transmissão em fluxo, bem como para produzir ficheiros MP4 para transferência progressiva, em seguida, deve usar o "conteúdo adaptável vários velocidade de transmissão MP4" configuração predefinida durante a criação de uma tarefa de codificação. Ao utilizar o **conteúdo MP4 de velocidade de transmissão adaptável vários** configuração predefinida, o codificador MES aplica-se a mesma lógica de codificação, como mostrado acima, mas agora o elemento de saída irá conter ficheiros MP4 em que o áudio e vídeo é intercalado. Pode utilizar um destes ficheiros MP4 (por exemplo, a versão de velocidade de transmissão mais alta) como um ficheiro de transferência progressiva.

## <a id="encoding_with_dotnet"></a>Encoding com Media Services .NET SDK

O exemplo de código seguinte utiliza o SDK .NET dos Media Services para efetuar as seguintes tarefas:

- Crie uma tarefa de codificação.
- Obter uma referência ao codificador Media Encoder Standard.
- Adicionar uma tarefa de codificação para a tarefa e especifique a utilização a **transmissão em fluxo adaptável** predefinidas. 
- Crie um elemento de saída que contém o elemento codificado.
- Adicione um manipulador de eventos para verificar o progresso da tarefa.
- Submeta a tarefa.

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

## <a id="output"></a>Saída

Esta secção mostra três exemplos de camadas de saída produzidos pelo MES como resultado de codificação com o **transmissão em fluxo adaptável** predefinidas. 

### <a name="example-1"></a>Exemplo 1
A origem com altura "1080" e a framerate "29.970" produz 6 camadas de vídeo:

|Camada|Altura|Largura|Bitrate(Kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Exemplo 2
A origem com altura "720" e a framerate "23.970" produz 5 camadas de vídeo:

|Camada|Altura|Largura|Bitrate(Kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Exemplo 3
A origem com altura "360" e a framerate "29.970" produz 3 camadas de vídeo:

|Camada|Altura|Largura|Bitrate(Kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consultar Também
[Descrição geral da codificação de serviços de multimédia](media-services-encode-asset.md)

