---
title: Como criar um processador de multimédia com o SDK de serviços de multimédia do Azure para .NET | Documentos da Microsoft
description: Saiba como criar um componente de processador de multimédia para codificar, converter formato, encriptar ou desencriptar o conteúdo de mídia para serviços de multimédia do Azure. Exemplos de código são escritos em C# e utilizar o SDK de Media Services para .NET.
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
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189049"
---
# <a name="how-to-get-a-media-processor-instance"></a>Como: Obter uma instância de processador de multimédia
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Descrição geral
Nos serviços de multimédia, que um processador de multimédia é um componente que processa uma tarefa de processamento específico, como codificação, formato de conversão, encriptar ou desencriptar o conteúdo de mídia. Normalmente, criar um processador de multimédia quando cria uma tarefa para codificar, encriptar ou converter o formato do conteúdo de mídia.

## <a name="azure-media-processors"></a>Processadores de multimédia do Azure 

O tópico seguinte fornece listas de processadores de multimédia:

* [Processadores de multimédia de codificação](scenarios-and-availability.md#encoding-media-processors)
* [Processadores de multimédia de análise](scenarios-and-availability.md#analytics-media-processors)

## <a name="get-media-processor"></a>Obter o processador de multimédia

O método a seguir mostra como obter uma instância de processador de multimédia. O exemplo de código pressupõe que a utilização de uma variável de nível de módulo com o nome **_context** para referenciar o contexto de servidor, conforme descrito na secção [como: Ligar a serviços de multimédia através de programação](media-services-use-aad-auth-to-access-ams-api.md).

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

## <a name="next-steps"></a>Próximos Passos
Agora que sabe como obter uma instância de processador de multimédia, vá para o [como codificar um recurso](media-services-dotnet-encode-with-media-encoder-standard.md) tópico que mostrará a como usar o Media Encoder Standard para codificar um recurso.

