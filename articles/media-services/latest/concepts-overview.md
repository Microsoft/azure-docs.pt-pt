---
title: Terminologia e conceitos de Serviços de Mídia
description: Conheça a terminologia e conceitos para a Azure Media Services.
services: media-servicesgit
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: b425cd9268b336a8926e4fad9cb1f288f4fe3e87
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897922"
---
# <a name="media-services-terminology-and-concepts"></a>Terminologia e conceitos de Serviços de Mídia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este tópico apresenta uma breve visão geral da terminologia e conceitos da Azure Media Services. O artigo também fornece links para artigos com uma explicação aprofundada dos conceitos e funcionalidades dos Media Services v3.

Os conceitos fundamentais descritos nestes tópicos devem ser revistos antes do início do desenvolvimento.

> [!NOTE]
> Atualmente, pode utilizar o [portal Azure](https://portal.azure.com/) para: gerir os Serviços de Mídia v3 [Live Events,](live-events-outputs-concept.md)ver (não gerir) v3 [Ativos,](assets-concept.md)e [obter informações sobre o acesso às APIs.](./access-api-howto.md)
> Para todas as outras tarefas de gestão (por exemplo, [Transformações e Empregos](transforms-jobs-concept.md) e [Proteção de Conteúdos),](content-protection-overview.md)utilize o [REST API](/rest/api/media/accountfilters), [CLI](/cli/azure/ams)ou um dos [SDKs suportados.](media-services-apis-overview.md#sdks)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="media-services-v3-terminology"></a>Terminologia dos Serviços de Mídia v3

|Termo|Descrição|
|---|---|
|Evento ao Vivo|Um **Evento Ao Vivo** representa um oleoduto para ingerir, transcoding (opcionalmente) e embalar streams ao vivo de vídeo, áudio e metadados em tempo real.<br/><br/>Para os clientes que migram dos Media Services v2 APIs, o **Live Event** substitui a entidade **do Canal** em v2. Para obter mais informações, consulte [migração de v2 para v3](migrate-v-2-v-3-migration-introduction.md).|
|Streaming Ponto Final/Embalagem/Origem|Um **Serviço de Endpoint streaming** representa um serviço dinâmico (just-in-time) de embalagem e origem que pode entregar o seu conteúdo ao vivo e a pedido diretamente a uma aplicação de leitor cliente. Utiliza um dos protocolos comuns de streaming (HLS ou DASH). Além disso, o **Streaming Endpoint** fornece encriptação dinâmica (just-in-time) aos sistemas de gestão de direitos digitais líderes do setor (DRMs).<br/><br/>Na indústria de streaming de meios de comunicação, este serviço é comumente referido como **Um Embalador** ou **Origem.**  Outros termos comuns na indústria para esta capacidade incluem JITP (pacoter just-in-time) ou JITE (encriptação just-in-time).

## <a name="media-services-v3-concepts"></a>Conceitos de Serviços de Mídia v3

|Conceitos|Descrição|Ligações|
|---|---|---|
|Ativos e conteúdo sonoro|Para começar a gerir, encriptar, codificar, analisar e transmitir conteúdos de mídia em Azure, é necessário criar uma conta de Media Services e enviar os seus ficheiros digitais para **o Ativo**.|[Carregamento e armazenamento na cloud](storage-account-concept.md)<br/><br/>[Conceito de ativos](assets-concept.md)|
|Codificar conteúdo|Assim que enviar os seus ficheiros de meios digitais de alta qualidade para o Assets, pode codificá-los em formatos que podem ser reproduzidos numa grande variedade de navegadores e dispositivos. <br/><br/>Para codificar com os Media Services v3, é necessário criar **Transforms** and **Jobs**.|[Transformações e Empregos](transforms-jobs-concept.md)<br/><br/>[Codificação com Serviços de Media](encoding-concept.md)|
|Analisar conteúdo (Video Indexer)|O Media Services v3 permite extrair informações dos seus ficheiros de vídeo e áudio utilizando predefinições de Media Services v3. Para analisar o seu conteúdo utilizando predefinições de Serviços de Comunicação v3, é necessário criar **Transforms** and **Jobs**.<br/><br/>Se quiser informações mais detalhadas, utilize o [Índice de Vídeo](../video-indexer/index.yml) diretamente.|[Analisar ficheiros de vídeo e áudio](analyzing-video-audio-files-concept.md)|
|Empacotamento e entrega|Uma vez codificado o seu conteúdo, poderá tirar partido da **Dinâmica de Embalagem.** Nos Serviços de Comunicação Social, um **Streaming Endpoint** é o serviço de embalagem dinâmico utilizado para entregar conteúdo sonoro aos clientes. Para disponibilizar vídeos no ativo de saída para os clientes para reprodução, tem de criar um **Localizador de Streaming** e, em seguida, construir URLs de streaming. <br/><br/>Ao criar o **Localizador de Streaming,** para além do nome do ativo, tem de especificar a **Política de Streaming**. **As Políticas de Streaming** permitem-lhe definir protocolos de streaming e opções de encriptação (se houver) para os seus **Localizadores de Streaming.** A Dynamic Packaging é utilizada quer transmita o seu conteúdo ao vivo ou a pedido. <br/><br/>Pode utilizar os Media Services **Dynamic Manifests** para transmitir apenas uma rendição específica ou subclips do seu vídeo.|[Empacotamento dinâmico](dynamic-packaging-overview.md)<br/><br/>[Streaming Endpoints](streaming-endpoint-concept.md)<br/><br/>[Localizadores de streaming](streaming-locators-concept.md)<br/><br/>[Políticas de streaming](streaming-policy-concept.md)<br/><br/>[Manifestos dinâmicos](filters-dynamic-manifest-overview.md)<br/><br/>[Filtros](filters-concept.md)|
|Proteção de conteúdo|Com os Media Services, pode fornecer o seu conteúdo ao vivo e a pedido encriptado dinamicamente com o Advanced Encryption Standard (AES-128) ou/e qualquer um dos três principais sistemas DEDM: Microsoft PlayReady, Google Widevine e Apple FairPlay. Os Media Services também fornecem um serviço para a entrega de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) a clientes autorizados. <br/><br/>Se especificar opções de encriptação no seu fluxo, crie a **Política de Chave de Conteúdo** e associe-a ao seu **Localizador de Streaming**. A **Política de Chave de Conteúdo** permite-lhe configurar a forma como a chave de conteúdo é entregue aos clientes finais.<br/><br/> Tente reutilizar as políticas sempre que forem necessárias as mesmas opções.| [Políticas-chave de conteúdo](content-key-policy-concept.md)<br/><br/>[Proteção de conteúdos](content-protection-overview.md)|
|Transmissão em direto|Os Serviços de Media permitem-lhe entregar eventos ao vivo aos seus clientes na nuvem Azure. Os **Eventos em Direto** são responsáveis pela ingestão e o processamento dos feeds de vídeos em direto. Quando cria um **Live Event,** é criado um ponto final de entrada que pode utilizar para enviar um sinal ao vivo a partir de um codificar remoto. Assim que tiver o stream fluindo para o **Live Event,** pode iniciar o evento de streaming criando um **Ativo,** **Live Output** e **Streaming Localizador**. **A Live Output** irá arquivar o fluxo no **Ativo** e disponibilizá-lo-á aos espectadores através do **Streaming Endpoint.** Um evento ao vivo pode ser definido para um *passe-through* (um codificadora ao vivo no local envia um fluxo bitrate múltiplo) ou *codificação ao vivo* (um codificante ao vivo no local envia um único fluxo de bitrate). |[Visão geral do streaming ao vivo](live-streaming-overview.md)<br/><br/>[Live Events and Live Outputs](live-events-outputs-concept.md) (Eventos em Direto e Saídas em Direto)|
|Monitorização com Grade de Eventos|Para ver o progresso do trabalho, use a **Grade de Eventos.** Os Serviços de Comunicação Social também emitam os tipos de eventos ao vivo. Com o Event Grid, as aplicações podem escutar e reagir a eventos a partir de praticamente todos os serviços do Azure, bem como de origens personalizadas. |[A processar eventos do Event Grid](reacting-to-media-services-events.md)<br/><br/>[Esquemas](media-services-event-schemas.md)|
|Monitorização com monitor Azure Monitor|Monitorize métricas e registos de diagnóstico que o ajudem a entender como as suas aplicações estão a funcionar com o Azure Monitor.|[Métricas e registos de diagnósticos](media-services-metrics-diagnostic-logs.md)<br/><br/>[Esquemas dos registos de diagnósticos](media-services-diagnostic-logs-schema.md)|
|Clientes de leitores|Pode utilizar o Azure Media Player para reproduzir conteúdo sonoro transmitido pelos Media Services numa grande variedade de navegadores e dispositivos. O Azure Media Player utiliza padrões da indústria, tais como HTML5, Extensões de Fonte de Mídia (MSE) e Extensões de Mídia Encriptadas (EME) para proporcionar uma experiência de streaming adaptativa enriquecida. |[Descrição geral do Leitor de Multimédia do Azure](use-azure-media-player.md)|

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Consulte o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Media Services.

## <a name="next-steps"></a>Próximos passos

* [Codificar o ficheiro remoto e transmitir o vídeo - REST](stream-files-tutorial-with-rest.md)
* [Codificar o ficheiro carregado e transmitir o vídeo - .NET](stream-files-tutorial-with-api.md)
* [Transmitir em direto - .NET](stream-live-tutorial-with-api.md)
* [Analisar o seu vídeo - .NET](analyze-videos-tutorial-with-api.md)
* [Encriptação dinâmica AES-128 - .NET](protect-with-aes128.md)
* [Criptografe dinamicamente com multi-DRM - .NET](protect-with-drm.md)