---
title: Como criar um processador de mídia utilizando o Azure Media Services SDK para .NET; Microsoft Docs
description: Saiba como criar um componente de processador de mídia para codificar, converter formato, encriptar ou desencriptar conteúdos de mídia para o Azure Media Services. As amostras de código são escritas em C# e utilizam o SDK de Serviços de Media para .NET.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: dbf9496f-c6f0-42a7-aa36-70f89dcb8ea2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: af6badda426f1bb81d8528cfda9b8c02d55712b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61463853"
---
# <a name="how-to-get-a-media-processor-instance"></a>Como: Obter uma instância de Processador de Mídia
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Descrição geral
Nos Serviços de Media, um processador de media é um componente que lida com uma tarefa de processamento específica, como codificação, conversão de formato, encriptação ou desencriptação de conteúdos de mídia. Normalmente cria um processador de mídia quando está a criar uma tarefa para codificar, encriptar ou converter o formato de conteúdo dos media.

## <a name="azure-media-processors"></a>Processadores de mídia Azure 

O seguinte tópico fornece listas de processadores de mídia:

* [Processadores de multimédia de codificação](scenarios-and-availability.md#encoding-media-processors)
* [Processadores de multimédia de análise](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>Obtenha processador de mídia

O método seguinte mostra como obter uma instância de processador de mídia. O exemplo do código pressupõe a utilização de uma variável de nível de módulo, nomeada **_context** para referenciar o contexto do servidor como descrito na secção [Como: Ligar aos Serviços de Media Programmaticamente](media-services-use-aad-auth-to-access-ams-api.md).

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
        ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
        throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }


## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passos Seguintes
Agora que sabe como obter uma instância de processador de mídia, vá ao tópico [Como Codificar um Tópico de Ativo](media-services-dotnet-encode-with-media-encoder-standard.md) que lhe mostrará como usar o Media Encoder Standard para codificar um ativo.

