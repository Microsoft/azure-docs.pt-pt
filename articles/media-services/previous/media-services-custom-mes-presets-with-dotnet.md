---
title: Personalização de pré-sintonias padrão de Media Encoder | Microsoft Docs
description: Este tópico mostra como executar codificação avançada personalizando predefinições de tarefas Media Encoder Standard. O tópico mostra como usar os Serviços de Comunicação Social .NET SDK para criar uma tarefa e trabalho de codificação. Também mostra como fornecer predefinições personalizadas para o trabalho de codificação.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ec95392f-d34a-4c22-a6df-5274eaac445b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: c2ec7c8fe312787d153c76b0b0194354e1717db7
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016717"
---
# <a name="customizing-media-encoder-standard-presets"></a>Personalização de predefinições padrão media Encoder

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

## <a name="overview"></a>Descrição Geral

Este artigo mostra como realizar codificação avançada com a Media Encoder Standard (MES) utilizando uma predefinição personalizada. O artigo utiliza .NET para criar uma tarefa de codificação e um trabalho que executa esta tarefa.  

Este artigo mostra-lhe como personalizar uma predefinição tomando o [Bitrate Múltiplo H264 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) predefinido e reduzindo o número de camadas. O artigo [de predefinição de pré-sintonias do Media Encoder de Personalização](media-services-advanced-encoding-with-mes.md) demonstra predefinições personalizadas que podem ser usadas para executar tarefas avançadas de codificação.

> [!NOTE]
> As predefinições personalizadas descritas neste artigo não podem ser utilizadas nas transformações [dos Media Services V3](../latest/index.yml) ou nos comandos CLI. Consulte a [orientação de migração de V2 a V3](../latest/migrate-v-2-v-3-migration-introduction.md) para mais detalhes.

## <a name="customizing-a-mes-preset"></a><a id="customizing_presets"></a> Personalizar uma predefinição MES

### <a name="original-preset"></a>Predefinição original

Guarde o JSON definido no artigo [bitrate múltiplo H264 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) em algum ficheiro com extensão .json. Por exemplo, **CustomPreset_JSON.js.**

### <a name="customized-preset"></a>Predefinição personalizada

Abra a **CustomPreset_JSON.jsno** ficheiro e remova as três primeiras camadas dos **H264Layers** para que o seu ficheiro fique assim.

```json 
  {  
    "Version": 1.0,  
    "Codecs": [  
      {  
        "KeyFrameInterval": "00:00:02",  
        "H264Layers": [  
          {  
            "Profile": "Auto",  
            "Level": "auto",  
            "Bitrate": 1000,  
            "MaxBitrate": 1000,  
            "BufferWindow": "00:00:05",  
            "Width": 640,  
            "Height": 360,  
            "BFrames": 3,  
            "ReferenceFrames": 3,  
            "AdaptiveBFrame": true,  
            "Type": "H264Layer",  
            "FrameRate": "0/1"  
          },  
          {  
            "Profile": "Auto",  
            "Level": "auto",  
            "Bitrate": 650,  
            "MaxBitrate": 650,  
            "BufferWindow": "00:00:05",  
            "Width": 640,  
            "Height": 360,  
            "BFrames": 3,  
            "ReferenceFrames": 3,  
            "AdaptiveBFrame": true,  
            "Type": "H264Layer",  
            "FrameRate": "0/1"  
          },  
          {  
            "Profile": "Auto",  
            "Level": "auto",  
            "Bitrate": 400,  
            "MaxBitrate": 400,  
            "BufferWindow": "00:00:05",  
            "Width": 320,  
            "Height": 180,  
            "BFrames": 3,  
            "ReferenceFrames": 3,  
            "AdaptiveBFrame": true,  
            "Type": "H264Layer",  
            "FrameRate": "0/1"  
          }  
        ],  
        "Type": "H264Video"  
      },  
      {  
        "Profile": "AACLC",  
        "Channels": 2,  
        "SamplingRate": 48000,  
        "Bitrate": 128,  
        "Type": "AACAudio"  
      }  
    ],  
    "Outputs": [  
      {  
        "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",  
        "Format": {  
          "Type": "MP4Format"  
        }  
      }  
    ]  
  }  
```

## <a name="encoding-with-media-services-net-sdk"></a><a id="encoding_with_dotnet"></a>Codificação com Serviços de Mídia .NET SDK

O seguinte exemplo de código utiliza os Serviços de Comunicação Social .NET SDK para executar as seguintes tarefas:

- Criar um trabalho de codificação.
- Obtenha uma referência ao codificar Media Encoder Standard.
- Carregue a predefinição JSON personalizada que criou na secção anterior. 

    ```csharp
    // Load the JSON from the local file.
    string configuration = File.ReadAllText(fileName);  
    ```

- Adicione uma tarefa de codificação ao trabalho. 
- Especifique o ativo de entrada a codificar.
- Crie um ativo de saída que contenha o ativo codificado.
- Adicione um manipulador de eventos para verificar o progresso do trabalho.
- Submeta o trabalho.
   
#### <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio

Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento de Serviços de Multimédia com .NET). 

#### <a name="example"></a>Exemplo   

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace CustomizeMESPresests
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

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

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

            // Encode and generate the output using custom presets.
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

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("CustomPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            configuration,
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

## <a name="see-also"></a>Veja também

- [Como codificar com uma transformação personalizada usando o CLI](../latest/custom-preset-cli-howto.md)
- [Codificação com os Serviços de Multimédia do Azure v3](../latest/encoding-concept.md)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
