---
title: Codificar um ativo com Media Encoder Standard usando .NET | Microsoft Docs
description: Este artigo mostra como usar o .NET para codificar um ativo usando Media Encoder Standard.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 03431b64-5518-478a-a1c2-1de345999274
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 259e32d55f25c4a146b7ff358eb503763dd5fab2
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "69016592"
---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Codificar um ativo com Media Encoder Standard usando o .NET  

As tarefas de codificação são uma das operações de processamento mais comuns nos Serviços de Multimédia. Estes Serviços permitem-lhe criar tarefas de codificação para converter ficheiros de multimédia de uma codificação para outra. Ao codificar, você pode usar o codificador de mídia interno dos serviços de mídia. Você também pode usar um codificador fornecido por um parceiro dos serviços de mídia; os codificadores de terceiros estão disponíveis por meio do Azure Marketplace. 

Este artigo mostra como usar o .NET para codificar seus ativos com o Media Encoder Standard (MES). Media Encoder Standard é configurado usando uma das predefinições de codificadores descritas [aqui](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

É recomendável sempre codificar os arquivos de origem em um conjunto de MP4 de taxa de bits adaptável e, em seguida, converter o conjunto para o formato desejado usando o [empacotamento dinâmico](media-services-dynamic-packaging-overview.md). 

Se o ativo de saída for armazenamento criptografado, você deverá configurar a política de entrega de ativos. Para obter mais informações, consulte Configurando a [política de entrega de ativos](media-services-dotnet-configure-asset-delivery-policy.md).

> [!NOTE]
> MES produz um arquivo de saída com um nome que contém os primeiros 32 caracteres do nome do arquivo de entrada. O nome é baseado no que é especificado no arquivo predefinido. Por exemplo, "FileName": "{basename} _ {index} {Extension}". {Basename} é substituído pelos primeiros 32 caracteres do nome do arquivo de entrada.
> 
> 

### <a name="mes-formats"></a>Formatos de MES
[Formatos e codecs](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>Predefinições MES
Media Encoder Standard é configurado usando uma das predefinições de codificadores descritas [aqui](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Metadados de entrada e saída
Ao codificar um ativo de entrada (ou ativos) usando MES, você obtém um ativo de saída na conclusão bem-sucedida dessa tarefa de codificação. O ativo de saída contém vídeo, áudio, miniaturas, manifesto, etc., com base na predefinição de codificação usada.

O ativo de saída também contém um arquivo com metadados sobre o ativo de entrada. O nome do arquivo XML de metadados tem o seguinte formato: < asset_id > _metadata. XML (por exemplo, 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata. xml), em que < asset_id > é o valor de AssetID do ativo de entrada. O esquema desse XML de metadados de entrada é descrito [aqui](media-services-input-metadata-schema.md).

O ativo de saída também contém um arquivo com metadados sobre o ativo de saída. O nome do arquivo XML de metadados tem o seguinte formato: < source_file_name > _manifest. XML (por exemplo, BigBuckBunny_manifest. xml). O esquema desse XML de metadados de saída é descrito [aqui](media-services-output-metadata-schema.md).

Se você quiser examinar qualquer um dos dois arquivos de metadados, poderá criar um localizador SAS e baixar o arquivo em seu computador local. Você pode encontrar um exemplo de como criar um localizador SAS e baixar um arquivo usando as extensões do SDK do .NET dos serviços de mídia.

## <a name="download-sample"></a>Transferir exemplo
Você pode obter e executar um exemplo que mostra como codificar com MES [aqui](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="net-sample-code"></a>Código de exemplo do .NET

O exemplo de código a seguir usa o SDK do .NET dos serviços de mídia para executar as seguintes tarefas:

* Criar um trabalho de codificação.
* Obtenha uma referência para o codificador de Media Encoder Standard.
* Especifique para usar a predefinição de [streaming adaptável](media-services-autogen-bitrate-ladder-with-mes.md) . 
* Adicione uma única tarefa de codificação ao trabalho. 
* Especifique o ativo de entrada a ser codificado.
* Crie um ativo de saída que contenha o ativo codificado.
* Adicione um manipulador de eventos para verificar o andamento do trabalho.
* Envie o trabalho.

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

## <a name="advanced-encoding-features-to-explore"></a>Recursos avançados de codificação para explorar
* [Como gerar miniaturas](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Gerando miniaturas durante a codificação](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Cortar vídeos durante a codificação](media-services-crop-video.md)
* [Personalizando predefinições de codificação](media-services-custom-mes-presets-with-dotnet.md)
* [Sobreposição ou marca d' água de um vídeo com uma imagem](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos Seguintes
[Como gerar miniaturas usando Media Encoder Standard com](media-services-dotnet-generate-thumbnail-with-mes.md)
[visão geral de codificação do .net Media Services](media-services-encode-asset.md)

