---
title: Usar Media Encoder Standard para gerar automaticamente uma escada de taxa de bits-Azure | Microsoft Docs
description: Este tópico mostra como usar Media Encoder Standard (MES) para gerar automaticamente uma escada de taxa de bits com base na resolução de entrada e na taxa de bits. A resolução de entrada e a taxa de bits nunca serão excedidas. Por exemplo, se a entrada for 720p em 3Mbps, a saída permanecerá 720p na melhor das hipóteses e começará com taxas inferiores a 3Mbps.
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
ms.openlocfilehash: 14575e0c95acf1345fc3358b323083d86d8eedee
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543550"
---
#  <a name="use-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Usar Media Encoder Standard para gerar automaticamente uma escada de taxa de bits  

## <a name="overview"></a>Descrição geral

Este artigo mostra como usar o Media Encoder Standard (MES) para gerar automaticamente uma escada de taxa de bits (pares de resolução de taxa de bits) com base na resolução de entrada e taxa de bits. A predefinição gerada automaticamente nunca excederá a resolução de entrada e a taxa de bits. Por exemplo, se a entrada for 720p a 3 Mbps, saída permanece 720p na melhor e será iniciado às tarifas inferiores a 3 Mbps.

### <a name="encoding-for-streaming-only"></a>Codificação somente para streaming

Se sua intenção é codificar o vídeo de origem somente para streaming, você deve usar a predefinição "streaming adaptável" ao criar uma tarefa de codificação. Ao usar a predefinição de **streaming adaptável** , o codificador mes limitará de forma inteligente uma escada de taxa de bits. No entanto, você não poderá controlar os custos de codificação, pois o serviço determina quantas camadas usar e em qual resolução. Você pode ver exemplos de camadas de saída produzidas por MES como resultado da codificação com a predefinição de **streaming adaptável** no final deste artigo. O ativo de saída contém arquivos MP4 nos quais áudio e vídeo não são intercalados.

### <a name="encoding-for-streaming-and-progressive-download"></a>Codificação para streaming e download progressivo

Se sua intenção é codificar seu vídeo de origem para streaming, bem como para produzir arquivos MP4 para download progressivo, você deve usar a predefinição "conteúdo adaptável com várias taxas de bits MP4" ao criar uma tarefa de codificação. Ao usar a predefinição **MP4 de taxa de bits múltipla adaptável ao conteúdo** , o codificador mes aplica a mesma lógica de codificação acima, mas agora o ativo de saída conterá arquivos MP4 onde áudio e vídeo são intercalados. Você pode usar um desses arquivos MP4 (por exemplo, a versão de taxa de bits mais alta) como um arquivo de download progressivo.

## <a id="encoding_with_dotnet"></a>Codificação com o SDK do .NET dos serviços de mídia

O exemplo de código a seguir usa o SDK do .NET dos serviços de mídia para executar as seguintes tarefas:

- Criar um trabalho de codificação.
- Obtenha uma referência para o codificador de Media Encoder Standard.
- Adicione uma tarefa de codificação ao trabalho e especifique para usar a predefinição de **streaming adaptável** . 
- Crie um ativo de saída que contenha o ativo codificado.
- Adicione um manipulador de eventos para verificar o andamento do trabalho.
- Envie o trabalho.

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

## <a id="output"></a>Der

Esta seção mostra três exemplos de camadas de saída produzidas por MES como resultado da codificação com a predefinição de **streaming adaptável** . 

### <a name="example-1"></a>Exemplo 1
A origem com altura "1080" e a framerate "29.970" produz 6 camadas de vídeo:

|Camada|Altura|Largura|Taxa de bits (Kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Exemplo 2
A origem com altura "720" e a framerate "23.970" produz 5 camadas de vídeo:

|Camada|Altura|Largura|Taxa de bits (Kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Exemplo 3
A origem com altura "360" e a framerate "29.970" produz 3 camadas de vídeo:

|Camada|Altura|Largura|Taxa de bits (Kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consultar Também
[Visão geral da codificação dos serviços de mídia](media-services-encode-asset.md)

