---
title: Codificar um ativo com o Media Encoder Standard utilizando .NET | Microsoft Docs
description: Este artigo mostra como usar .NET para codificar um ativo usando o Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 03431b64-5518-478a-a1c2-1de345999274
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: anilmur
ms.custom: devx-track-csharp
ms.openlocfilehash: b94ec0b50182381d2a4e1bca09f4c8bc482d13ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014099"
---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Codificar um ativo com Media Encoder Standard usando .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

Os postos de trabalho na codificação são uma das operações de processamento mais comuns nos Serviços de Comunicação Social. Cria-se trabalhos de codificação para converter ficheiros de meios de comunicação de uma codificação para outra. Ao codificar, pode utilizar o Media Services incorporado Media Encoder. Também pode utilizar um codificader fornecido por um parceiro de Serviços de Comunicação Social; os codificadores de terceiros estão disponíveis através do Azure Marketplace. 

Este artigo mostra como usar .NET para codificar os seus ativos com a Media Encoder Standard (MES). O Media Encoder Standard está configurado utilizando uma das predefinições dos codificadores [aqui](./media-services-mes-presets-overview.md)descritas.

É aconselhável codificar sempre os seus ficheiros de origem num conjunto de MP4 de bitrate adaptativo e, em seguida, converter o conjunto para o formato pretendido utilizando a [Embalagem Dinâmica](media-services-dynamic-packaging-overview.md). 

Se o seu ativo de saída estiver encriptado, tem de configurar a política de entrega de ativos. Para obter mais informações, consulte [a política de entrega de ativos configurado.](media-services-dotnet-configure-asset-delivery-policy.md)

> [!NOTE]
> O MES produz um ficheiro de saída com um nome que contém os primeiros 32 caracteres do nome do ficheiro de entrada. O nome baseia-se no que está especificado no ficheiro predefinido. Por exemplo, "FileName": "{Basename}_{Index}{Extension}". {Nome base} é substituído pelos primeiros 32 caracteres do nome do ficheiro de entrada.
> 
> 

### <a name="mes-formats"></a>Formatos MES
[Formatos e codecs](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>Predefinições MES
O Media Encoder Standard está configurado utilizando uma das predefinições dos codificadores [aqui](./media-services-mes-presets-overview.md)descritas.

### <a name="input-and-output-metadata"></a>Metadados de entrada e saída
Quando codifica um ativo de entrada (ou ativos) utilizando o MES, obtém-se um ativo de saída na conclusão bem-sucedida dessa tarefa de codificação. O ativo de saída contém vídeo, áudio, miniaturas, manifesto, etc. com base na predefinição codificante que utiliza.

O ativo de saída também contém um ficheiro com metadados sobre o ativo de entrada. O nome do ficheiro XML dos metadados tem o seguinte formato: <asset_id>_metadata.xml (por exemplo, 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), onde <asset_id> é o valor AssetId do ativo de entrada. O esquema deste metadados de entrada XML é descrito [aqui](media-services-input-metadata-schema.md).

O ativo de saída também contém um ficheiro com metadados sobre o ativo de saída. O nome do ficheiro XML dos metadados tem o seguinte formato: <source_file_name>_manifest.xml (por exemplo, BigBuckBunny_manifest.xml). O esquema desta saída de metadados XML é descrito [aqui](media-services-output-metadata-schema.md).

Se quiser examinar qualquer um dos dois ficheiros de metadados, pode criar um localizador SAS e transferir o ficheiro para o seu computador local. Pode encontrar um exemplo sobre como criar um localizador SAS e descarregar um ficheiro Utilizando as Extensões Net SDK dos Serviços de Comunicação Social.NET.

## <a name="download-sample"></a>Transferir exemplo
Você pode obter e executar uma amostra que mostra como codificar com MES a partir [daqui.](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/)

## <a name="net-sample-code"></a>.NET código de amostra

O seguinte exemplo de código utiliza os Serviços de Comunicação Social .NET SDK para executar as seguintes tarefas:

* Criar um trabalho de codificação.
* Obtenha uma referência ao codificar Media Encoder Standard.
* Especifique para utilizar a predefinição [de streaming adaptativo.](media-services-autogen-bitrate-ladder-with-mes.md) 
* Adicione uma única tarefa de codificação ao trabalho. 
* Especifique o ativo de entrada a codificar.
* Crie um ativo de saída que contenha o ativo codificado.
* Adicione um manipulador de eventos para verificar o progresso do trabalho.
* Submeta o trabalho.

#### <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto de Visual Studio

Configure o seu ambiente de desenvolvimento e preencha o ficheiro app.config com informações da ligação, conforme descrito em [Media Services development with .NET](media-services-dotnet-how-to-use.md) (Desenvolvimento de Serviços de Multimédia com .NET). 

#### <a name="example"></a>Exemplo 

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace MediaEncoderStandardSample
{
    class Program
    {
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

        private static readonly string _supportFiles =
            Path.GetFullPath(@"../..\Media");

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

            // Create a task with the encoding details, using a string preset.
            // In this case "Adaptive Streaming" preset is used.
            ITask task = job.Tasks.AddNew("My encoding task",
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

## <a name="advanced-encoding-features-to-explore"></a>Funcionalidades avançadas de codificação para explorar
* [Como gerar miniaturas](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Gerar miniaturas durante a codificação](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Vídeos de colheita durante a codificação](media-services-crop-video.md)
* [Personalizar predefinições codificadoras](media-services-custom-mes-presets-with-dotnet.md)
* [Sobreposição ou marca de água um vídeo com uma imagem](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos seguintes
[Como gerar miniatura usando Media Encoder Standard com .NET](media-services-dotnet-generate-thumbnail-with-mes.md) 
 [Visão geral dos serviços de comunicação social](media-services-encode-asset.md)