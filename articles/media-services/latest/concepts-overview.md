---
title: Terminologia e conceitos de Serviços de Media
titleSuffix: Azure Media Services
description: Conheça a terminologia e conceitos para a Azure Media Services.
services: media-servicesgit
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/18/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 02d0897774261a25a2fccb70a31d0f264c458740
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79500085"
---
# <a name="media-services-terminology-and-concepts"></a>Terminologia e conceitos de Serviços de Media

Este tema dá uma breve visão geral da terminologia e conceitos da Azure Media Services. O artigo também fornece links para artigos com uma explicação aprofundada dos conceitos e funcionalidades do Media Services v3.

Os conceitos fundamentais descritos nestes tópicos devem ser revistos antes de iniciar o desenvolvimento.

> [!NOTE]
> Atualmente, pode utilizar o [portal Azure](https://portal.azure.com/) para: gerir os Media Services v3 [Live Events,](live-events-outputs-concept.md)visualizar (não gerir) [os Ativos](assets-concept.md)v3, e [obter informações sobre o acesso a APIs](access-api-portal.md).
> Para todas as outras tarefas de gestão (por exemplo, [Transforms and Jobs](transforms-jobs-concept.md) e [Proteção de Conteúdos),](content-protection-overview.md)utilize o [REST API,](https://aka.ms/ams-v3-rest-ref) [CLI,](https://aka.ms/ams-v3-cli-ref)ou um dos [SDKs](media-services-apis-overview.md#sdks)suportados.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>Terminologia v3 dos Serviços de Mídia

|Termo|Descrição|
|---|---|
|Evento ao Vivo|Um **Evento Ao Vivo** representa um oleoduto para ingerir, transcodificar (opcionalmente) e embalar transmissões ao vivo de metadados de vídeo, áudio e em tempo real.<br/><br/>Para clientes que migram de Media Services v2 APIs, o **Live Event** substitui a entidade **do Canal** em v2. Para mais informações, consulte [Migrating de v2 a v3](migrate-from-v2-to-v3.md).|
|Ponto final de streaming/embalagem/origem|Um **Streaming Endpoint** representa um serviço dinâmico de embalagem e origem (just-in-time) que pode entregar o seu conteúdo ao vivo e a pedido diretamente a uma aplicação de jogador cliente. Utiliza um dos protocolos comuns de streaming de meios de comunicação (HLS ou DASH). Além disso, o **Streaming Endpoint** fornece encriptação dinâmica (just-in-time) aos sistemas de gestão de direitos digitais líderes na indústria (DDR).<br/><br/>Na indústria de streaming de meios de comunicação, este serviço é comumente referido como **Um Embalador** ou **Origem.**  Outros termos comuns na indústria para esta capacidade incluem JITP (just-in-time-packager) ou JITE (just-in-time-encryption).

## <a name="media-services-v3-concepts"></a>Conceitos v3 dos Media Services

|Conceitos|Descrição|Ligações|
|---|---|---|
|Ativos e carregamento de conteúdos|Para começar a gerir, encriptar, codificar, analisar e transmitir conteúdos de mídia no Azure, é necessário criar uma conta de Media Services e fazer upload dos seus ficheiros digitais em **Ativos**.|[Carregamento e armazenamento na cloud](storage-account-concept.md)<br/><br/>[Conceito de ativos](assets-concept.md)|
|Codificar conteúdo|Assim que enviar os seus ficheiros de mídia digital de alta qualidade para Ativos, pode codificar em formatos que podem ser reproduzidos numa grande variedade de navegadores e dispositivos. <br/><br/>Para codificar com os Serviços de Media v3, é necessário criar **Transforms** and **Jobs.**|[Transformações e Tarefas](transforms-jobs-concept.md)<br/><br/>[Codificação com serviços de media](encoding-concept.md)|
|Analisar conteúdo (Video Indexer)|O Media Services v3 permite extrair informações dos seus ficheiros de vídeo e áudio utilizando predefinições de Media Services v3. Para analisar o seu conteúdo utilizando predefinições de Media Services v3, precisa de criar **Transforms** and **Jobs**.<br/><br/>Se quiser informações mais detalhadas, utilize o [Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) de Vídeo diretamente.|[Analisar ficheiros de vídeo e áudio](analyzing-video-audio-files-concept.md)|
|Empacotamento e entrega|Uma vez codificado o seu conteúdo, pode tirar partido da **Embalagem Dinâmica**. Nos Serviços de Media, um **Streaming Endpoint** é o serviço de embalagem dinâmico utilizado para entregar conteúdo sonoro aos jogadores clientes. Para disponibilizar vídeos no ativo de saída aos clientes para reprodução, tem de criar um Localizador de **Streaming** e, em seguida, construir URLs de streaming. <br/><br/>Ao criar o Localizador de **Streaming,** para além do nome do ativo, tem de especificar a Política de **Streaming**. **As Políticas de Streaming** permitem-lhe definir protocolos de streaming e opções de encriptação (se houver) para os seus **Localizadores de Streaming**. A Dynamic Packaging é utilizada quer transmita o seu conteúdo ao vivo ou a pedido. <br/><br/>Pode utilizar **Manifestos Dinâmicos** de Serviços de Media para transmitir apenas uma versão ou subclips específicos do seu vídeo.|[Empacotamento dinâmico](dynamic-packaging-overview.md)<br/><br/>[Pontos Finais de Transmissão em fluxo](streaming-endpoint-concept.md)<br/><br/>[Localizadores de Transmissão em Fluxo](streaming-locators-concept.md)<br/><br/>[Políticas de Transmissão em Fluxo](streaming-policy-concept.md)<br/><br/>[Manifestos dinâmicos](filters-dynamic-manifest-overview.md)<br/><br/>[Filtros](filters-concept.md)|
|Proteção de conteúdo|Com os Media Services, pode fornecer o seu conteúdo ao vivo e a pedido encriptado dinamicamente com Advanced Encryption Standard (AES-128) ou/e qualquer um dos três principais sistemas DEDR: Microsoft PlayReady, Google Widevine e Apple FairPlay. A Media Services também fornece um serviço para a entrega de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) a clientes autorizados. <br/><br/>Se especificar opções de encriptação no seu stream, crie a **Política de Chave** de Conteúdo e associe-a ao seu Localizador de **Streaming**. A **Política chave de conteúdo** permite-lhe configurar como a chave de conteúdo é entregue aos clientes finais.<br/><br/> Tente reutilizar as políticas sempre que forem necessárias as mesmas opções.| [Políticas de Chave de Conteúdo](content-key-policy-concept.md)<br/><br/>[Proteção de conteúdos](content-protection-overview.md)|
|Transmissão em direto|A Media Services permite-lhe entregar eventos ao vivo aos seus clientes na nuvem Azure. Os **Eventos em Direto** são responsáveis pela ingestão e o processamento dos feeds de vídeos em direto. Quando cria um **Evento Ao Vivo,** é criado um ponto final de entrada que pode utilizar para enviar um sinal ao vivo a partir de um codificador remoto. Assim que tiver o fluxo fluindo para o **Live Event,** pode iniciar o evento de streaming criando um **Asset**, **Live Output**e **Streaming Locator.** **A Live Output** irá arquivar o stream no **Ativo** e disponibilizá-lo aos espectadores através do **Streaming Endpoint**. Um evento ao vivo pode ser definido para um *pass-through* (um codificador ao vivo no local envia um fluxo de bitrate múltiplo) ou *codificação ao vivo* (um codificador ao vivo no local envia um único fluxo de bitrate). |[Visão geral do streaming ao vivo](live-streaming-overview.md)<br/><br/>[Eventos em Direto e Saídas em Direto](live-events-outputs-concept.md)|
|Monitorização com grelha de eventos|Para ver o progresso do trabalho, use a Grelha de **Eventos.** A Media Services também emite os tipos de eventos ao vivo. Com o Event Grid, as aplicações podem escutar e reagir a eventos a partir de praticamente todos os serviços do Azure, bem como de origens personalizadas. |[A processar eventos do Event Grid](reacting-to-media-services-events.md)<br/><br/>[Rio Schemas](media-services-event-schemas.md)|
|Monitorização com monitor Azure|Monitorize métricas e registos de diagnóstico que o ajudam a entender como as suas aplicações estão a funcionar com o Monitor Azure.|[Métricas e registos de diagnósticos](media-services-metrics-diagnostic-logs.md)<br/><br/>[Esquemas dos registos de diagnósticos](media-services-diagnostic-logs-schema.md)|
|Clientes de leitores|Pode utilizar o Azure Media Player para reproduzir conteúdos de mídia transmitidos pela Media Services numa grande variedade de navegadores e dispositivos. O Azure Media Player utiliza padrões industriais, tais como HTML5, Extensões de Fonte de Media (MSE) e Extensões de Meios Encriptados (EME) para proporcionar uma experiência de streaming adaptável enriquecida. |[Descrição geral do Leitor de Multimédia do Azure](use-azure-media-player.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Confira o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Serviços de Media.

## <a name="next-steps"></a>Passos seguintes

* [Codificar o ficheiro remoto e transmitir o vídeo - REST](stream-files-tutorial-with-rest.md)
* [Codificar o ficheiro carregado e transmitir o vídeo - .NET](stream-files-tutorial-with-api.md)
* [Transmitir em direto - .NET](stream-live-tutorial-with-api.md)
* [Analisar o seu vídeo - .NET](analyze-videos-tutorial-with-api.md)
* [Encriptação dinâmica AES-128 - .NET](protect-with-aes128.md)
* [Criptografe dinamicamente com multi-DRM - .NET](protect-with-drm.md)
