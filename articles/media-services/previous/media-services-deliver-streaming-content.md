---
title: Publicar conteúdo de serviços de multimédia do Azure com .NET | Documentos da Microsoft
description: Saiba como criar um localizador de que é utilizado para compilar um URL de transmissão em fluxo. Exemplos de código são escritos em C# e utilizar o SDK de Media Services para .NET.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: c53b1f83-4cb1-4b09-840f-9c145b7d6f8d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 49e9fbc3f8d10dd1e272f5f9bc5a542328a3ddb4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181916"
---
# <a name="publish-media-services-content-using-net"></a>Publicar conteúdo de serviços de multimédia com .NET  
> [!div class="op_single_selector"]
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [Portal](media-services-portal-publish.md)
> 
> 

## <a name="overview"></a>Descrição geral
Pode transmitir em fluxo uma MP4 definido através da criação de um localizador de transmissão em fluxo OnDemand e criação de um URL de transmissão em fluxo de velocidade de transmissão adaptável. O [codificação de um recurso de](media-services-encode-asset.md) tópico mostra como a codificar numa conjunto de MP4 de velocidade de transmissão adaptável. 

> [!NOTE]
> Se o seu conteúdo é encriptado, configure a política de entrega de elementos (conforme descrito em [isso](media-services-dotnet-configure-asset-delivery-policy.md) tópico) antes de criar um localizador. 
> 
> 

Também pode utilizar um OnDemand localizador de transmissão em fluxo para criar URLs que apontam para os ficheiros MP4 que podem ser transferidos progressivamente.  

Este tópico mostra como criar um localizador para publicar o elemento e criar um uniforme, MPEG DASH e URLs de transmissão em fluxo HLS de transmissão em fluxo de OnDemand. Ela também mostra frequente para criar URLs de transferência progressiva. 

## <a name="create-an-ondemand-streaming-locator"></a>Criar um OnDemand localizador de transmissão em fluxo
Para criar o localizador de transmissão em fluxo OnDemand e obter os URLs, terá de efetue os seguintes procedimentos:

1. Se o conteúdo é encriptado, defina uma política de acesso.
2. Crie um localizador de transmissão em fluxo de OnDemand.
3. Se planeja transmitir em fluxo, obtenha o ficheiro de manifesto transmissão em fluxo (. ISM) no recurso. 
   
   Se planeia transferir progressivamente, obter os nomes de ficheiros MP4 no elemento.  
4. Crie URLs para o ficheiro de manifesto ou ficheiros MP4. 


>[!NOTE]
>Existe um limite de 1,000,000 políticas para diferentes políticas do AMS (por exemplo, para a política Locator ou ContentKeyAuthorizationPolicy). Utilize o mesmo ID de política se estiver a utilizar sempre os mesmos dias / permissões de acesso. Por exemplo, políticas para localizadores que pretendam permanecem em vigor durante muito tempo (políticas de não carregamento). Para obter mais informações, veja [este](media-services-dotnet-manage-entities.md#limit-access-policies) tópico.

### <a name="use-media-services-net-sdk"></a>Utilizar o .NET SDK dos serviços de multimédia
Criar URLs de transmissão em fluxo 

```csharp
    private static void BuildStreamingURLs(IAsset asset)
    {

        // Create a 30-day readonly access policy. 
          // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create a locator to the streaming content on an origin. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get a reference to the streaming manifest file from the  
        // collection of files in the asset. 
        var manifestFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                    EndsWith(".ism")).
                                    FirstOrDefault();

        // Create a full URL to the manifest file. Use this for playback
        // in streaming media clients. 
        string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
        Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
        Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
        Console.WriteLine();
    }
```

As saídas:

    URL to manifest for client streaming using Smooth Streaming protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest
    URL to manifest for client streaming using HLS protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)
    URL to manifest for client streaming using MPEG DASH protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


> [!NOTE]
> Também pode transmitir o seu conteúdo numa conexão SSL. Para fazer essa abordagem, certifique-se de que o início de URLs de transmissão em fluxo com HTTPS. Atualmente, o AMS não suporta SSL com domínios personalizados.
> 
> 

Criar os URLs de transferência progressiva 

```csharp
    private static void BuildProgressiveDownloadURLs(IAsset asset)
    {
        // Create a 30-day readonly access policy. 
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create an OnDemandOrigin locator to the asset. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get MP4 files.
        IEnumerable<IAssetFile> mp4AssetFiles = asset
            .AssetFiles
            .ToList()
            .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Create a full URL to the MP4 files. Use this to progressively download files.
        foreach (var pd in mp4AssetFiles)
            Console.WriteLine(originLocator.Path + pd.Name);
    }
```
As saídas:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4

    . . . 

### <a name="use-media-services-net-sdk-extensions"></a>Utilizar extensões do SDK de .NET de serviços de multimédia
O código a seguir chama os métodos de extensões do SDK do .NET que criar um localizador e geram a transmissão em fluxo uniforme, HLS e MPEG-DASH URLs para transmissão em fluxo adaptável.
```csharp
    // Create a loctor.
    _context.Locators.Create(
        LocatorType.OnDemandOrigin,
        inputAsset,
        AccessPermissions.Read,
        TimeSpan.FromDays(30));

    // Get the streaming URLs.
    Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
    Uri hlsUri = inputAsset.GetHlsUri();
    Uri mpegDashUri = inputAsset.GetMpegDashUri();

    Console.WriteLine(smoothStreamingUri);
    Console.WriteLine(hlsUri);
    Console.WriteLine(mpegDashUri);
```

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos Seguintes
* [Baixar ativos](media-services-deliver-asset-download.md)
* [Configurar a política de entrega de elemento](media-services-dotnet-configure-asset-delivery-policy.md)

