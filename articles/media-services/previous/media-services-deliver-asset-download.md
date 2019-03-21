---
title: Transfira recursos dos serviços de suporte de dados para o computador - Azure | Documentos da Microsoft
description: Saiba mais sobre download ativos para o seu computador. Exemplos de código são escritos em C# e utilizar o SDK de Media Services para .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 8908a1dd-3ffb-4f18-955d-4c8e2d82fc5d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 21fcc6ae09718ffbb22e1d438926586dd3cde71d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182664"
---
# <a name="how-to-deliver-an-asset-by-download"></a>Como: Entregar um recurso por transferência  
Este artigo discute as opções para fornecer recursos de suporte de dados carregados nos serviços de multimédia. Pode entregar conteúdo de serviços de multimédia em vários cenários de aplicativos. Depois de codificação, baixe os ativos de mídia gerado ou aceder-lhes com um localizador de transmissão em fluxo. Para melhor desempenho e escalabilidade, também pode publicar conteúdo através de uma rede de entrega de conteúdos (CDN).

Este exemplo mostra como transferir os ativos de mídia dos serviços de multimédia para o computador local. O código consulta as tarefas associadas à conta dos serviços de multimédia pelo ID da tarefa e de acessos ao seu **OutputMediaAssets** coleção (que é o conjunto de um ou mais recursos de suporte de dados de saída que resulta da execução de uma tarefa). Este exemplo mostra como transferir os recursos de suporte de dados de saída de uma tarefa, mas pode aplicar a mesma abordagem para transferir os outros ativos.

>[!NOTE]
>Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Utilize o mesmo ID de política se estiver a utilizar sempre os mesmos dias / permissões, por exemplo, políticas para localizadores que pretendam permanecem em vigor durante muito tempo (políticas de não carregamento) de acesso. Para obter mais informações, veja [este](media-services-dotnet-manage-entities.md#limit-access-policies) artigo.

```csharp
    // Download the output asset of the specified job to a local folder.
    static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
    {
        // This method illustrates how to download a single asset. 
        // However, you can iterate through the OutputAssets
        // collection, and download all assets if there are many. 

        // Get a reference to the job. 
        IJob job = GetJob(jobId);

        // Get a reference to the first output asset. If there were multiple 
        // output media assets you could iterate and handle each one.
        IAsset outputAsset = job.OutputMediaAssets[0];

        // Create a SAS locator to download the asset
        IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
        ILocator locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset, accessPolicy);

        BlobTransferClient blobTransfer = new BlobTransferClient
        {
            NumberOfConcurrentTransfers = 20,
            ParallelTransferThreadCount = 20
        };

        var downloadTasks = new List<Task>();
        foreach (IAssetFile outputFile in outputAsset.AssetFiles)
        {
            // Use the following event handler to check download progress.
            outputFile.DownloadProgressChanged += DownloadProgress;

            string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

            Console.WriteLine("File download path:  " + localDownloadPath);

            downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

            outputFile.DownloadProgressChanged -= DownloadProgress;
        }

        Task.WaitAll(downloadTasks.ToArray());

        return outputAsset;
    }

    static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
    {
        Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
    }
```


## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consultar Também
[Fornecer conteúdo de transmissão em fluxo](media-services-deliver-streaming-content.md)

