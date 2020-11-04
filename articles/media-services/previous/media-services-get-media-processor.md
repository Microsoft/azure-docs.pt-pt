---
title: Como criar um processador de mídia utilizando o Azure Media Services SDK para .NET Microsoft Docs
description: Saiba como criar um componente de processador de mídia para codificar, converter formato, encriptar ou desencriptar conteúdos de mídia para a Azure Media Services. As amostras de código são escritas em C# e utilizam o SDK dos Serviços de Mídia para .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 3215eef337ca1c9a84f2f4d917dbea8ea2e090f0
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93346694"
---
# <a name="how-to-get-a-media-processor-instance"></a>Como: Obter uma instância de processador de mídia

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)

## <a name="overview"></a>Descrição geral

Nos Serviços de Mídia um processador de mídia é um componente que lida com uma tarefa de processamento específica, como codificação, conversão de formato, encriptação ou desencriptação de conteúdos de mídia. Normalmente cria um processador de mídia quando está a criar uma tarefa para codificar, encriptar ou converter o formato de conteúdo sonoro.

## <a name="azure-media-processors"></a>Processadores de mídia Azure

O seguinte tópico fornece listas de processadores de mídia:

* [Processadores de multimédia de codificação](scenarios-and-availability.md)
* [Processadores de multimédia de análise](scenarios-and-availability.md)

## <a name="get-media-processor"></a>Obter processador de mídia

O seguinte método mostra como obter uma instância de processador de mídia. O exemplo de código pressupõe a utilização de uma variável de nível de módulo denominada **_context** para referenciar o contexto do servidor como descrito na secção [Como: Ligar-se a Serviços de Mídia Programáticamente](media-services-use-aad-auth-to-access-ams-api.md).

```csharp
private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}
```

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos Seguintes

Agora que sabe como obter uma instância de processador de mídia, vá ao tópico [Como Codificar um Ativo](media-services-dotnet-encode-with-media-encoder-standard.md) que lhe mostrará como usar o Media Encoder Standard para codificar um ativo.