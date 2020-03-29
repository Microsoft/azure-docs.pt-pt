---
title: Migrar do Indexer v1 e v2 para o Indexer de Vídeo dos Serviços de Mídia Azure [ Microsoft Docs
description: Este tema discute como migrar do Azure Media Indexer v1 e v2 para o Azure Media Services Video Indexer.
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
ms.date: 09/20/2019
ms.author: juliako
ms.openlocfilehash: 2268c074480f99ca23117ca2ffd2c87c1dbb10a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513240"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>Migrar do Media Indexer e do Media Indexer 2 para o Indexer de Vídeo

O processador de mídia [Azure Media Indexer](media-services-index-content.md) e os processadores de mídia [Azure Media Indexer 2 estão](media-services-process-content-with-indexer2.md) a ser retirados. Para as datas de aposentadoria, consulte este tema de [componentes legados.](legacy-components.md) [O Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) substitui estes processadores de mídia legados.

O Indexer de Vídeo azure Media Services é construído em Azure Media Analytics, Azure Cognitive Search, Cognitive Services (como a API Facial, Microsoft Tradutor, a Computer Vision API e o Custom Speech Service). Esta aplicação permite-lhe extrair as informações dos seus vídeos através dos modelos de vídeo e áudio do Video Indexer. Para ver em que cenários o Video Indexer pode ser usado, quais as funcionalidades que oferece e como começar, consulte os modelos de [vídeo e áudio do Video Indexer](../video-indexer/video-indexer-overview.md). 

Pode extrair informações dos seus ficheiros de vídeo e áudio utilizando as [predefinições do analisador Azure Media Services v3](../latest/analyzing-video-audio-files-concept.md) ou diretamente utilizando as APIs do [Indexer](https://api-portal.videoindexer.ai/)de Vídeo . Atualmente, existe uma sobreposição entre as funcionalidades oferecidas pelas APIs do Indexer de Vídeo e pelos Media Services v3 APIs.

> [!NOTE]
> Para entender quando pretende utilizar predefinições de analisadores de vídeo Indexer vs. Media Services, confira o documento de [comparação](../video-indexer/compare-video-indexer-with-media-services-presets.md). 

Este artigo discute os passos para a migração do Indexante azure media e do Indexer 2 do Azure Media Indexer 2 para o Azure Media Services Video Indexer.  

## <a name="migration-options"></a>Opções de migração 

|Se precisar  |então |
|---|---|
|uma solução que forneça uma transcrição de discurso a texto para qualquer formato de ficheiro de mídia em formatos de ficheiros de legenda fechada: VTT, SRT ou TTML<br/>bem como informações áudio adicionais tais como: palavras-chave, inferência de tópicos, eventos acústicos, diarização de altifalantes, extração e tradução de entidades| atualize as suas aplicações para utilizar as capacidades do Indexante de Vídeo Azure através do API do Indexante de Vídeo v2 REST ou do pré-definido do Analisador de Áudio Azure Media Services v3.|
|capacidades de fala-a-texto| use a API do Discurso dos Serviços Cognitivos diretamente.|  

## <a name="getting-started-with-video-indexer"></a>Começando com O Indexante de Vídeo

A secção seguinte aponta-o para links relevantes: [Como posso começar com o Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/video-indexer-overview#how-can-i-get-started-with-video-indexer) de Vídeo? 

## <a name="getting-started-with-media-services-v3-apis"></a>Começar com Media Services v3 APIs

A Azure Media Services v3 API permite extrair insights dos seus ficheiros de vídeo e áudio através dos [presets do analisador Azure Media Services v3](../latest/analyzing-video-audio-files-concept.md). 

**O AudioAnalyzerPreset** permite extrair várias informações áudio de um ficheiro de áudio ou vídeo. A saída inclui um ficheiro VTT ou TTML para a transcrição áudio e um ficheiro JSON (com todas as informações de áudio adicionais). Os conhecimentos áudio incluem palavras-chave, indexação de altifalantes e análise do sentimento da fala. O AudioAnalyzerPreset também suporta a deteção de idiomas para idiomas específicos. Para obter informações detalhadas, consulte [Transforms](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset).

### <a name="get-started"></a>Introdução

Para começar a ver:

* [Tutorial](../latest/analyze-videos-tutorial-with-api.md)
* Amostras AudioAnalyzerPreset: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) ou [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* Amostras de VideoAnalyzerPreset: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) ou [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Começar com serviços de fala de serviços cognitivos

[O Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/) fornece um serviço de fala-a-texto que transcreve streams de áudio para texto em tempo real que as suas aplicações, ferramentas ou dispositivos podem consumir ou exibir. Pode usar o discurso ao texto para [personalizar o seu próprio modelo acústico, modelo de linguagem ou modelo de pronúncia.](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md) Para mais informações, consulte o [discurso do Serviço Cognitivo no texto.](../../cognitive-services/speech-service/speech-to-text.md) 

> [!NOTE] 
> O serviço de voz a texto não pega em formatos de ficheiros de vídeo e só leva [certos formatos de áudio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#audio-formats). 

Para obter mais informações sobre o serviço de texto-a-fala e como começar, veja [o que é o discurso ao texto?](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-to-text)

## <a name="known-differences-from-deprecated-services"></a>Diferenças conhecidas dos serviços precados 

Você vai descobrir que o Indexer de Vídeo, Azure Media Services v3 AudioAnalyzerPreset, e os serviços de fala de serviços cognitivos são mais fiáveis e produzem uma melhor produção de qualidade do que os processadores aposentados do Azure Media Indexer 1 e Azure Media Indexer 2.  

Algumas diferenças conhecidas incluem: 

* Os Serviços Cognitivos de Fala não suportam a extração de palavras-chave. No entanto, o Indexante de Vídeo e os Serviços de Media v3 AudioAnalyzerPreset oferecem um conjunto mais robusto de palavras-chave no formato de ficheiro JSON. 

## <a name="need-help"></a>Precisa de ajuda?

Você pode abrir um bilhete de apoio navegando para [novo pedido](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) de apoio

## <a name="next-steps"></a>Passos seguintes

* [Componentes legados](legacy-components.md)
* [Página de preços](https://azure.microsoft.com/pricing/details/media-services/#encoding)


