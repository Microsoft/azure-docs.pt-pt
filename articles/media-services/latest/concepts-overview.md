---
title: Terminologia de serviços de multimédia do Azure e de conceitos - Azure | Documentos da Microsoft
description: Este tópico fornece uma breve descrição geral dos conceitos e terminologia-serviços de multimédia do Azure e fornece ligações para obter mais detalhes.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/13/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 1e76569c7f5157dce681d15ec8d499b90e080102
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65762315"
---
# <a name="media-services-concepts"></a>Conceitos de serviços de multimédia

Este tópico apresenta uma breve descrição geral dos conceitos e terminologia-serviços de multimédia do Azure. O artigo também fornece ligações para artigos com uma explicação detalhada sobre funcionalidades e conceitos do serviços de multimédia v3. 

Os conceitos fundamentais descritos nos seguintes tópicos devem ser revistos antes de iniciar o desenvolvimento.

> [!NOTE]
> Atualmente, não pode utilizar o portal do Azure para gerir recursos v3. Utilize a [API REST](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref) ou um dos [SDKs](media-services-apis-overview.md#sdks) suportados.

## <a name="terminology"></a>Terminologia

Esta secção mostra como o mapeamento de alguns termos comuns da indústria, para a API dos serviços de multimédia v3.

### <a name="live-event"></a>Evento Live

R **evento em direto** representa um pipeline de ingestão, transcodificação (opcionalmente) e empacotamento de transmissões em direto dos metadados do vídeo, áudio e em tempo real.

Para os clientes a migrar a partir dos serviços de multimédia v2 APIs, o **evento em direto** substitui o **canal** entidade no v2. Para obter mais informações, consulte [migrando do v2 para v3](migrate-from-v2-to-v3.md).

### <a name="streaming-endpoint-packaging-and-origin"></a>Ponto final de transmissão em fluxo (empacotamento e a origem)

R **ponto final de transmissão em fluxo** representa um dinâmico (just-in-time) empacotamento e a origem do serviço que pode distribuir os seus conteúdos em direto e a pedido diretamente a uma aplicação de leitor de cliente, utilizando um dos protocolos comuns de suporte de dados streaming (HLS ou DASH). Além disso, o **ponto final de transmissão em fluxo** fornece a encriptação dinâmica (just-in-time) para DRMs de líder da indústria.

O suporte de dados da indústria de transmissão em fluxo, este serviço é frequentemente referido como um **Packager** ou **origem**.  Outros termos comuns da indústria para esta capacidade incluem JITP (Just-em-tempo-packager) ou JITE (Just-em-tempo-encriptação). 
 
## <a name="cloud-upload-and-storage"></a>Carregamento e armazenamento na cloud

Para começar a gerir, encriptar, codificar, analisar e transmissão em fluxo conteúdo de multimédia do Azure, terá de criar uma conta de Media Services e carregar os ficheiros digitais num **ativos**.

- [Carregamento e armazenamento na cloud](storage-account-concept.md)
- [Conceito de ativos](assets-concept.md)

## <a name="encoding"></a>Codificação

Depois de carregar os ficheiros de multimédia digital de alta qualidade nos ativos, pode codificá-los em formatos que podem ser jogados numa grande variedade de navegadores e dispositivos. 

Codificar com serviços de multimédia v3, tem de criar **transforma** e **tarefas**.

![Transformações](./media/encoding/transforms-jobs.png)

- [Transformações e tarefas](transforms-jobs-concept.md)
- [Encoding com Media Services](encoding-concept.md)

## <a name="media-analytics"></a>Análise de multimédia

Para analisar seus arquivos de áudio e vídeos, terá também de criar **transforma** e **tarefas**.

- [Analisar ficheiros de áudio e vídeos](analyzing-video-audio-files-concept.md)

## <a name="packaging-delivery-protection"></a>Empacotamento, entrega, proteção

Assim que o seu conteúdo está codificado, pode aproveitar **empacotamento dinâmico**. Nos Media Services, um **ponto final de transmissão em fluxo**  /origem é o serviço de empacotamento dinâmico utilizado para disponibilizar conteúdo multimédia para jogadores do cliente. Para disponibilizar vídeos no recurso de saída para os clientes para a reprodução, tem de criar uma **localizador de transmissão em fluxo** e, em seguida, crie URLs de transmissão em fluxo. 

Ao criar o **localizador de transmissão em fluxo**, além do nome do elemento, tem de especificar **política de transmissão em fluxo**. **Políticas de transmissão em fluxo** permitem-lhe definir os protocolos de transmissão em fluxo e encriptação de opções (se houver) para a sua **localizadores de transmissão em fluxo**.

Empacotamento dinâmico é utilizado se transmite o seu conteúdo ao vivo ou sob demanda. O diagrama seguinte mostra o streaming sob demanda com o fluxo de trabalho de empacotamento dinâmico.

![Empacotamento dinâmico](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

Com os serviços de multimédia, pode fornecer seu conteúdo ao vivo e sob demanda encriptado dinamicamente com o Advanced Encryption Standard (AES-128) ou / e qualquer um dos três sistemas de gestão (DRM) de direitos digitais principais: Microsoft PlayReady, Widevine da Google e Apple FairPlay. Serviços de multimédia também fornecem um serviço para entrega de chaves AES e o DRM (PlayReady, Widevine e FairPlay) licenças para os clientes autorizados.

Se especificar opções de encriptação no seu fluxo, crie o **política de chave de conteúdo** e associá-lo com sua **localizador de transmissão em fluxo**. O **política de chave de conteúdo** permite-lhe configurar a forma como a chave de conteúdo é entregue para clientes finais.

A imagem seguinte ilustra o fluxo de trabalho de proteção de conteúdo de serviços de multimédia: 

![Proteger conteúdo](./media/content-protection/content-protection.svg)

&#42;a encriptação dinâmica suporta AES-128 "chave não encriptada", CBCS e CENC. 

Pode utilizar os serviços de multimédia **manifestos dinâmica** transmitir apenas uma representação em específica ou subclips do seu vídeo. No exemplo a seguir, um codificador foi utilizado para codificar um recurso de mezanino em sete representações de vídeo de MP4s de ISO (a partir de 180p para 1080p). O elemento codificado pode ser empacotado dinamicamente em qualquer um dos seguintes protocolos de transmissão em fluxo: HLS, MPEG DASH e Smooth.  Na parte superior do diagrama, é mostrado o manifesto HLS para o elemento com sem filtros (que contém todas as representações de sete).  Na parte inferior esquerda, é mostrado o manifesto HLS ao qual foi aplicado um filtro com o nome "ott". Especifica o filtro de "ott" para remover todas as velocidades de transmissão abaixo 1 Mbps, o que resultou nos níveis de qualidade dois na parte inferior a ser eliminados na resposta. Na parte inferior direita, é mostrado o manifesto HLS ao qual foi aplicado um filtro com o nome "móvel". O filtro "móvel" Especifica a remover representações em que a resolução é maior do que 720p, o que resultou em duas representações de 1080p a ser suprimidas.

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

Serviços de multimédia do Azure permite-lhe fornecer eventos em direto aos seus clientes na cloud do Azure. Os **Eventos em Direto** são responsáveis pela ingestão e o processamento dos feeds de vídeos em direto. Quando cria um **evento em direto**, é criado um ponto de final de entrada que pode utilizar para enviar um sinal ao vivo a partir de um codificador remoto. Assim que tiver o fluxo a ser encaminhados para o **evento em direto**, pode começar o evento de transmissão em fluxo através da criação de um **Asset**, **Live saída**, e **localizador de transmissão em fluxo** . **Live saída** irá arquivar a transmissão no **Asset** e disponibilizá-la para os espetadores através da **ponto final de transmissão em fluxo**. R **evento em direto** pode ser um dos dois tipos: **pass-through** e **codificação em direto**.

A imagem seguinte ilustra o fluxo de trabalho do tipo de pass-through:

![pass-through](./media/live-streaming/pass-through.svg)

- [Descrição geral de transmissão em fluxo em direto](live-streaming-overview.md)
- [Live Events and Live Outputs](live-events-outputs-concept.md) (Eventos em Direto e Saídas em Direto)

## <a name="monitoring"></a>Monitorização

### <a name="event-grid"></a>Grelha de Eventos

Para ver o progresso da tarefa, deve usar **Event Grid**. Serviços de multimédia emite também os tipos de evento em direto. Com o Event Grid, as aplicações podem escutar e reagir a eventos a partir de praticamente todos os serviços do Azure, bem como de origens personalizadas. 

- [Processamento de eventos do Event Grid](reacting-to-media-services-events.md)
- [Schemas](media-services-event-schemas.md)

### <a name="azure-monitor"></a>Azure Monitor

Monitorizar as métricas e registos de diagnóstico que o ajudam a compreender o desempenho das suas aplicações com o Azure Monitor.

- [Métricas e registos de diagnóstico](media-services-metrics-diagnostic-logs.md)
- [Esquemas de registos de diagnóstico](media-services-diagnostic-logs-schema.md)

## <a name="player-clients"></a>Clientes de leitores

Pode utilizar o leitor de multimédia do Azure para reproduzir conteúdo de multimédia em fluxo dos serviços de multimédia numa grande variedade de navegadores e dispositivos. O leitor de multimédia do Azure utiliza padrões da indústria, como HTML5, extensões de origem de mídia (MSE) e Encrypted Media Extensions (EME) para fornecer uma experiência plena de transmissão em fluxo adaptativa. 

- [Azure Media Player overview](use-azure-media-player.md) (Descrição geral do Leitor de Multimédia do Azure)

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, comentários, obter atualizações

Veja a [Comunidade dos serviços de multimédia do Azure](media-services-community.md) artigo para ver formas diferentes, pode fazer perguntas, comentários e obter atualizações sobre os serviços de multimédia.

## <a name="next-steps"></a>Passos Seguintes

* [Codificar o ficheiro remoto e o fluxo de vídeo – REST](stream-files-tutorial-with-rest.md)
* [Codificar o ficheiro carregado e fluxo de vídeo - .NET](stream-files-tutorial-with-api.md)
* [Stream em direto - .NET](stream-live-tutorial-with-api.md)
* [Analisar o seu vídeo - .NET](analyze-videos-tutorial-with-api.md)
* [Encriptação dinâmica AES-128 - .NET](protect-with-aes128.md)
* [Encriptar dinamicamente com múltipla DRM - .NET](protect-with-drm.md) 
