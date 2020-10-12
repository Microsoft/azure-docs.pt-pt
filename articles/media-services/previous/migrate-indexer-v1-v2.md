---
title: Migrar do Indexer v1 e v2 para Azure Media Services Video Indexer Microsoft Docs
description: Este tópico discute como migrar do Azure Media Indexer v1 e v2 para Azure Media Services Video Indexer.
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
ms.openlocfilehash: 3da6dd877a94da64f85f093ae98a0b8a109ab2e1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89255108"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>Migrar do Indexador de Media e do Indexador de Media 2 para Indexador de Vídeo

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

O processador de mídia [Azure Media Indexer](media-services-index-content.md) e os processadores [Azure Media Indexer 2 Preview](media-services-process-content-with-indexer2.md) estão a ser retirados. Para as datas de aposentadoria, consulte este tema [dos componentes do legado.](legacy-components.md) [O Azure Media Services Video Indexer](../video-indexer/index.yml) substitui estes processadores de mídia legado.

O Azure Media Services Video Indexer é construído em Azure Media Analytics, Azure Cognitive Search, Cognitive Services (como a API Facial, o Microsoft Tradutor, a API de Visão de Computador e o Serviço de Fala Personalizada). Esta aplicação permite-lhe extrair as informações dos seus vídeos através dos modelos de vídeo e áudio do Video Indexer. Para ver em que cenários o Video Indexer pode ser usado, quais as funcionalidades que oferece e como começar, consulte [os modelos de vídeo indexer de vídeo e áudio](../video-indexer/video-indexer-overview.md). 

Pode extrair informações dos seus ficheiros de vídeo e áudio utilizando as [predefinições do analisador Azure Media Services v3](../latest/analyzing-video-audio-files-concept.md) ou diretamente utilizando as [APIs do Indexador de Vídeo](https://api-portal.videoindexer.ai/). Atualmente, existe uma sobreposição entre as funcionalidades oferecidas pelas APIs do Indexante de Vídeo e as APIs dos Media Services v3.

> [!NOTE]
> Para entender quando pretende utilizar o Video Indexer vs. Media Services analisando predefinições, consulte o [documento de comparação](../video-indexer/compare-video-indexer-with-media-services-presets.md). 

Este artigo discute os passos para migrar do Azure Media Indexer e do Azure Media Indexer 2 para O Azure Media Services Video Indexer.  

## <a name="migration-options"></a>Opções de migração 

|Se precisar  |então |
|---|---|
|uma solução que fornece uma transcrição de voz para texto para qualquer formato de ficheiro de mídia em formatos de ficheiro de legenda fechada: VTT, SRT ou TTML<br/>bem como informações áudio adicionais tais como: palavras-chave, inferenculação de tópicos, eventos acústicos, diarização de altifalantes, extração e tradução de entidades| atualize as suas aplicações para utilizar as capacidades do Indexador de Vídeo Azure através do Índice de Vídeo v2 REST API ou da predefinição do Azure Media Services v3 Audio Analyzer.|
|capacidades de falar para texto| utilizar a API de Discurso dos Serviços Cognitivos diretamente.|  

## <a name="getting-started-with-video-indexer"></a>Começando com o Video Indexer

A seguinte secção aponta-o para links [relevantes: Como posso começar com o Video Indexer?](../video-indexer/video-indexer-overview.md#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>Começando com Os Serviços de Media v3 APIs

A Azure Media Services v3 API permite extrair insights dos seus ficheiros de vídeo e áudio através das [predefinições do analisador Azure Media Services v3](../latest/analyzing-video-audio-files-concept.md). 

**AudioAnalyzerPreset** permite extrair várias informações áudio de um ficheiro áudio ou vídeo. A saída inclui um ficheiro VTT ou TTML para a transcrição áudio e um ficheiro JSON (com todas as informações áudio adicionais). As informações áudio incluem palavras-chave, indexação de colunas e análise de sentimento de fala. AudioAnalyzerPreset também suporta a deteção de linguagem para idiomas específicos. Para obter informações [detalhadas,](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)consulte Transforms .

### <a name="get-started"></a>Introdução

Para começar, consulte:

* [Tutorial](../latest/analyze-videos-tutorial-with-api.md)
* Amostras AudioAnalyzerPreset: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) ou [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* Amostras do VideoAnalyzerPreset: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) ou [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Começar com serviços de fala de serviços cognitivos

[A Azure Cognitive Services](../../cognitive-services/index.yml) fornece um serviço de voz a texto que transcreve streams de áudio para texto em tempo real que as suas aplicações, ferramentas ou dispositivos podem consumir ou exibir. Pode utilizar o modelo [acústico, o modelo de linguagem ou o modelo de pronúncia.](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md) Para obter mais informações, consulte [os Serviços Cognitivos de voz a texto.](../../cognitive-services/speech-service/speech-to-text.md) 

> [!NOTE] 
> O serviço de voz para texto não toma formatos de ficheiros de vídeo e apenas toma [certos formatos áudio](../../cognitive-services/speech-service/rest-speech-to-text.md#audio-formats). 

Para obter mais informações sobre o serviço de texto-a-voz e como começar, veja [o que é falar-a-texto?](../../cognitive-services/speech-service/speech-to-text.md)

## <a name="known-differences-from-deprecated-services"></a>Diferenças conhecidas dos serviços precítados 

Você verá que os serviços de Indexer de Vídeo, Azure Media Services v3 AudioAnalyzerPreset e Serviços de Fala de Serviços Cognitivos são mais fiáveis e produzem uma melhor produção de qualidade do que os processadores Azure Media Indexer 1 e Azure Media Indexer 2.  

Algumas diferenças conhecidas incluem: 

* Os Serviços de Fala de Serviços Cognitivos não suportam a extração de palavras-chave. No entanto, o Video Indexer e os Media Services v3 AudioAnalyzerPreset oferecem um conjunto mais robusto de palavras-chave no formato de ficheiro JSON. 

## <a name="need-help"></a>Precisa de ajuda?

Você pode abrir um bilhete de apoio navegando para [novo pedido de apoio](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Passos seguintes

* [Componentes legados](legacy-components.md)
* [Página de preços](https://azure.microsoft.com/pricing/details/media-services/#encoding)
