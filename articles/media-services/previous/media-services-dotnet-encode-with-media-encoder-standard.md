---
title: Codificar um recurso com o Media Encoder Standard com .NET | Documentos da Microsoft
description: Este artigo mostra como utilizar o .NET para codificar um recurso com o Media Encoder Standard.
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
ms.author: juliako;anilmur
ms.openlocfilehash: d3eb2affe76374eb35ac724dff0204f43b567e09
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183480"
---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Codificar um recurso com o Media Encoder Standard com .NET  

As tarefas de codificação são uma das operações de processamento mais comuns nos Serviços de Multimédia. Estes Serviços permitem-lhe criar tarefas de codificação para converter ficheiros de multimédia de uma codificação para outra. Ao codificar, pode usar o codificador de multimédia incorporada de serviços de multimédia. Também pode usar um codificador fornecido por um parceiro de serviços de multimédia; codificadores de terceiros estão disponíveis através do Azure Marketplace. 

Este artigo mostra como utilizar o .NET para codificar seus ativos com o Media Encoder Standard (MES). Codificador de multimédia Standard está configurado com um dos pré-visualizando codificadores descrito [aqui](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

É recomendado que sempre codificar seus arquivos de origem para uma conjunto de MP4 de velocidade de transmissão adaptável e, em seguida, converter o conjunto para o formato desejado, com o [empacotamento dinâmico](media-services-dynamic-packaging-overview.md). 

Se o elemento de saída estiver armazenamento encriptado, é necessário configurar a política de entrega de elementos. Para obter mais informações, consulte [configurar a política de entrega de elemento](media-services-dotnet-configure-asset-delivery-policy.md).

> [!NOTE]
> MES produz um ficheiro de saída com um nome que contenha os primeiros 32 carateres do nome do ficheiro de entrada. O nome baseia-se no que é especificado no ficheiro predefinido. Por exemplo, "FileName": "{Nome_de_base} _ {Index} {Extension}". {Nome_de_base} é substituído pelos primeiros 32 carateres do nome do ficheiro de entrada.
> 
> 

### <a name="mes-formats"></a>Formatos MES
[Formatos e codecs do](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>Predefinições MES
Codificador de multimédia Standard está configurado com um dos pré-visualizando codificadores descrito [aqui](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Metadados de entrada e saído
Ao codificar um elemento de entrada (ou ativos) com MES, obtém um recurso de saída a bem-sucedida codificar de conclusão dessa tarefa. O elemento de saída contém vídeo, áudio, miniaturas, manifesto, etc. com base no que utiliza a predefinição de codificação.

O elemento de saída também contém um ficheiro com metadados sobre o elemento de entrada. O nome de ficheiro dos metadados de XML tem o seguinte formato: < asset_id > _metadata.xml (por exemplo, d de 57-8 41114ad3-eb5e - 4C 92-5354e2b7d4a4_metadata.xml), em que < asset_id > é o valor de AssetId do elemento de entrada. O esquema dos metadados entrado XML está descrito [aqui](media-services-input-metadata-schema.md).

O elemento de saída também contém um ficheiro com metadados sobre o elemento de saída. O nome de ficheiro dos metadados de XML tem o seguinte formato: < source_file_name > _manifest.xml (por exemplo, BigBuckBunny_manifest.xml). O esquema dos metadados saída XML está descrito [aqui](media-services-output-metadata-schema.md).

Se desejar examinar qualquer um dos dois arquivos de metadados, pode criar um localizador SAS e transfira o ficheiro para o seu computador local. Pode encontrar um exemplo sobre como criar um localizador SAS e baixar um arquivo usando as extensões de SDK de .NET de serviços de multimédia.

## <a name="download-sample"></a>Transferir exemplo
Pode obter e executar um exemplo que mostra como codificar com MES partir [aqui](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="net-sample-code"></a>Código de exemplo do .NET

O exemplo de código seguinte utiliza o SDK .NET dos Media Services para efetuar as seguintes tarefas:

* Crie uma tarefa de codificação.
* Obter uma referência ao codificador Media Encoder Standard.
* Especifique a utilização a [transmissão em fluxo adaptável](media-services-autogen-bitrate-ladder-with-mes.md) predefinidas. 
* Adicione uma única tarefa de codificação para a tarefa. 
* Especifique o elemento de entrada estar codificado.
* Crie um elemento de saída que contém o elemento codificado.
* Adicione um manipulador de eventos para verificar o progresso da tarefa.
* Submeta a tarefa.

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

## <a name="advanced-encoding-features-to-explore"></a>Funcionalidades de codificação avançadas para explorar
* [Como gerar miniaturas](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Gerar miniaturas durante a codificação](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Recortar vídeos durante a codificação](media-services-crop-video.md)
* [Personalizando as configurações predefinidas de codificação](media-services-custom-mes-presets-with-dotnet.md)
* [Sobrepor ou um vídeo com uma imagem de marca d'água](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos Seguintes
[Como gerar usando o Media Encoder Standard com .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
[descrição de geral do suporte de dados dos serviços de codificação](media-services-encode-asset.md)

