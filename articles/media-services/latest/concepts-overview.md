---
title: Conceitos e terminologia dos serviços de mídia do Azure – Azure | Microsoft Docs
description: Este tópico fornece uma breve visão geral da terminologia e dos conceitos dos serviços de mídia do Azure e fornece links para obter mais detalhes.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/10/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 47c7e35f71fd33cc53d498867ef015364252d5ea
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910300"
---
# <a name="media-services-concepts"></a>Conceitos dos serviços de mídia

Este tópico fornece uma breve visão geral da terminologia e dos conceitos dos serviços de mídia do Azure. O artigo também fornece links para artigos com uma explicação detalhada dos conceitos e da funcionalidade dos serviços de mídia v3. 

Os conceitos fundamentais descritos nestes tópicos devem ser revisados antes de iniciar o desenvolvimento.

> [!NOTE]
> Atualmente, não pode utilizar o portal do Azure para gerir recursos v3. Utilize a [API REST](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref) ou um dos [SDKs](media-services-apis-overview.md#sdks) suportados.

## <a name="terminology"></a>Terminologia

Esta seção mostra como alguns termos comuns do setor são mapeados para a API dos serviços de mídia v3.

### <a name="live-event"></a>Evento Live

Um **evento ao vivo** representa um pipeline para ingestão, transcodificação (opcionalmente) e empacotamento de fluxos ao vivo de vídeo, áudio e metadados em tempo real.

Para clientes que migram de APIs do Media Services v2, o **evento ao vivo** substitui a entidade de **canal** na v2. Para obter mais informações, consulte [migrando de v2 para v3](migrate-from-v2-to-v3.md).

### <a name="streaming-endpoint-packaging-and-origin"></a>Ponto de extremidade de streaming (empacotamento e origem)

Um **ponto de extremidade de streaming** representa um serviço de empacotamento e origem (just-in-time) dinâmico que pode entregar seu conteúdo ao vivo e sob demanda diretamente a um aplicativo de player de cliente, usando um dos protocolos de mídia de streaming comuns (HLS ou Dash). Além disso, o **ponto de extremidade de streaming** fornece criptografia dinâmica (just-in-time) para a DRMs líder do setor.

No setor de streaming de mídia, esse serviço é comumente conhecido como um **empacotador** ou **origem**.  Outros termos comuns do setor para esse recurso incluem JITP (just-in-time-Packager) ou JITE (codificação just-in-time). 
 
## <a name="cloud-upload-and-storage"></a>Carregamento e armazenamento na cloud

Para começar a gerenciar, criptografar, codificar, analisar e transmitir conteúdo de mídia no Azure, você precisa criar uma conta de serviços de mídia e carregar seus arquivos digitais em **ativos**.

- [Carregamento e armazenamento na cloud](storage-account-concept.md)
- [Conceito de ativos](assets-concept.md)

## <a name="encoding"></a>Codificação

Depois de carregar seus arquivos de mídia digital de alta qualidade em ativos, você pode codificá-los em formatos que podem ser reproduzidos em uma ampla variedade de navegadores e dispositivos. 

Para codificar com os serviços de mídia v3, você precisa criar **transformações** e **trabalhos**.

![Transformações](./media/encoding/transforms-jobs.png)

- [Transformações e trabalhos](transforms-jobs-concept.md)
- [Codificação com os serviços de mídia](encoding-concept.md)

## <a name="media-analytics"></a>Análise de multimédia

Para analisar os arquivos de vídeo e áudio, você também precisa criar **transformações** e **trabalhos**.

- [Analisando arquivos de áudio e vídeo](analyzing-video-audio-files-concept.md)

## <a name="packaging-delivery-protection"></a>Empacotamento, entrega, proteção

Depois que o conteúdo é codificado, você pode tirar proveito do **empacotamento dinâmico**. Nos serviços de mídia, um **ponto de extremidade de streaming**/Origin é o serviço de empacotamento dinâmico usado para fornecer conteúdo de mídia aos players cliente. Para disponibilizar vídeos no ativo de saída para os clientes para reprodução, você precisa criar um **localizador de streaming** e, em seguida, criar URLs de streaming. 

Ao criar o **localizador de streaming**, além do nome do ativo, você precisa especificar a **política de streaming**. **As políticas de streaming** permitem definir protocolos de streaming e opções de criptografia (se houver) para seus **localizadores de streaming**.

O empacotamento dinâmico é usado se você transmite seu conteúdo ao vivo ou sob demanda. O diagrama a seguir mostra o streaming sob demanda com fluxo de trabalho de empacotamento dinâmico.

![Empacotamento dinâmico](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Com os serviços de mídia, você pode entregar seu conteúdo ao vivo e sob demanda criptografado dinamicamente com criptografia AES (AES-128) ou/e qualquer um dos três principais sistemas de DRM (gerenciamento de direitos digitais): Microsoft PlayReady, Google Widevine e Apple FairPlay. Serviços de multimédia também fornecem um serviço para entrega de chaves AES e o DRM (PlayReady, Widevine e FairPlay) licenças para os clientes autorizados.

Se especificar as opções de criptografia em seu fluxo, crie a **política de chave de conteúdo** e associe-a ao seu **localizador de streaming**. A **política de chave de conteúdo** permite que você configure como a chave de conteúdo é entregue aos clientes finais.

A imagem seguinte ilustra o fluxo de trabalho de proteção de conteúdo de serviços de multimédia: 

![Proteger conteúdo](./media/content-protection/content-protection.svg)

&#42;a criptografia dinâmica dá suporte ao AES-128 "Clear Key", CBCS e CENC. 

Você pode usar **manifestos dinâmicos** dos serviços de mídia para transmitir apenas uma rendição ou subclipes específicos do seu vídeo. No exemplo a seguir, um codificador foi usado para codificar um ativo de mezanino em sete representações de vídeo ISO MP4s (de 180p para 1080p). O ativo codificado pode ser empacotado dinamicamente em qualquer um dos seguintes protocolos de streaming: HLS, MPEG DASH e Smooth.  Na parte superior do diagrama, o manifesto HLS para o ativo sem filtros é mostrado (ele contém todas as sete representações).  Na parte inferior esquerda, o manifesto HLS ao qual um filtro chamado "Ott" foi aplicado é mostrado. O filtro "Ott" especifica a remoção de todas as taxas de bits abaixo de 1 Mbps, o que resultou na remoção dos dois níveis de qualidade inferiores na resposta. No canto inferior direito, o manifesto HLS ao qual um filtro chamado "Mobile" foi aplicado é mostrado. O filtro "móvel" especifica a remoção de representações em que a resolução é maior que 720p, o que resultou nas duas rendições 1080p sendo eliminadas.

![Filtragem de representação](./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png)

- [Empacotamento dinâmico](dynamic-packaging-overview.md)
- [Streaming Endpoints](streaming-endpoint-concept.md) (Pontos Finais de Transmissão em Fluxo)
- [Streaming Locators](streaming-locators-concept.md) (Localizadores de Transmissão em Fluxo)
- [Streaming Policies](streaming-policy-concept.md) (Políticas de Transmissão em Fluxo)
- [Content Key Policies](content-key-policy-concept.md) (Políticas de Chaves de Conteúdos)
- [Proteção de conteúdo](content-protection-overview.md)
- [Dynamic manifests](filters-dynamic-manifest-overview.md) (Manifestos dinâmicos)
- [Filtros](filters-concept.md)

## <a name="live-streaming"></a>Transmissão em fluxo em direto

Os serviços de mídia do Azure permitem que você forneça eventos ao vivo para seus clientes na nuvem do Azure. Os **Eventos em Direto** são responsáveis pela ingestão e o processamento dos feeds de vídeos em direto. Quando você cria um **evento ao vivo**, é criado um ponto de extremidade de entrada que pode ser usado para enviar um sinal ao vivo de um codificador remoto. Depois que o fluxo fluir para o **evento ao vivo**, você poderá iniciar o evento de streaming criando um **ativo**, uma **saída ao vivo**e um **localizador de streaming**. A **saída ao vivo** arquivará o fluxo no **ativo** e o tornará disponível para os visualizadores por meio do ponto de **extremidade de streaming**. Um **evento ao vivo** pode ser um dos dois tipos: **codificação ativa**e de **passagem** .

A imagem a seguir ilustra o fluxo de trabalho do tipo de passagem:

![pass-through](./media/live-streaming/pass-through.svg)

- [Visão geral da transmissão ao vivo](live-streaming-overview.md)
- [Live Events and Live Outputs](live-events-outputs-concept.md) (Eventos em Direto e Saídas em Direto)

## <a name="monitoring"></a>Monitorização

### <a name="event-grid"></a>Event Grid

Para ver o progresso do trabalho, você deve usar a **grade de eventos**. Os serviços de mídia também emite os tipos de evento ao vivo. Com o Event Grid, as aplicações podem escutar e reagir a eventos a partir de praticamente todos os serviços do Azure, bem como de origens personalizadas. 

- [Manipulando eventos de grade de eventos](reacting-to-media-services-events.md)
- [Esquemas](media-services-event-schemas.md)

### <a name="azure-monitor"></a>Azure Monitor

Monitore métricas e logs de diagnóstico que ajudam você a entender como seus aplicativos estão sendo executados com o Azure Monitor.

- [Métricas e logs de diagnóstico](media-services-metrics-diagnostic-logs.md)
- [Esquemas de logs de diagnóstico](media-services-diagnostic-logs-schema.md)

## <a name="player-clients"></a>Clientes de leitores

Você pode usar Player de Mídia do Azure para reproduzir conteúdo de mídia transmitido pelos serviços de mídia em uma ampla variedade de navegadores e dispositivos. O Player de Mídia do Azure utiliza padrões do setor, como HTML5, extensões de origem de mídia (MSE) e EME (extensões de mídia criptografada) para fornecer uma experiência de streaming adaptável aprimorada. 

- [Azure Media Player overview](use-azure-media-player.md) (Descrição geral do Leitor de Multimédia do Azure)

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, envie comentários, obtenha atualizações

Confira o artigo [da Comunidade dos serviços de mídia do Azure](media-services-community.md) para ver diferentes maneiras que você pode fazer perguntas, fornecer comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Passos Seguintes

* [Codificar o arquivo remoto e o vídeo de fluxo – REST](stream-files-tutorial-with-rest.md)
* [Codificar arquivo carregado e vídeo de fluxo-.NET](stream-files-tutorial-with-api.md)
* [Fluxo dinâmico-.NET](stream-live-tutorial-with-api.md)
* [Analise seu vídeo-.NET](analyze-videos-tutorial-with-api.md)
* [Criptografia dinâmica AES-128-.NET](protect-with-aes128.md)
* [Criptografar dinamicamente com multi-DRM-.NET](protect-with-drm.md) 
