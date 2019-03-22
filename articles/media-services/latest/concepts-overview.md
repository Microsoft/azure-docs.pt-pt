---
title: Conceitos de serviços de multimédia do Azure - Azure | Documentos da Microsoft
description: Este tópico fornece uma breve descrição geral dos conceitos de Media Services do Azure e fornece ligações para obter detalhes.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: e4a79d7528f2e814e6370a5ead7d77a19057ebbb
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311276"
---
# <a name="media-services-concepts"></a>Conceitos de serviços de multimédia

Este tópico fornece uma breve descrição geral dos conceitos de Media Services do Azure e fornece ligações para artigos com uma explicação detalhada sobre funcionalidades e conceitos do serviços de multimédia v3. Os conceitos fundamentais descritos nos seguintes tópicos devem ser revistos antes de iniciar o desenvolvimento.

> [!NOTE]
> Atualmente, não é possível utilizar o portal do Azure para gerir os recursos de v3. Utilize o [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref), ou um suportadas [SDKs](developers-guide.md).

## <a name="cloud-upload-and-storage"></a>Carregamento e armazenamento na cloud

Para começar a gerir, encriptar, codificar, analisar e transmissão em fluxo conteúdo de multimédia do Azure, terá de criar uma conta de Media Services e carregar os ficheiros digitais num **ativos**.

- [Carregamento na cloud e armazenamento](storage-account-concept.md)
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

Assim que o seu conteúdo está codificado, pode aproveitar **empacotamento dinâmico**. **Ponto final de transmissão em fluxo** é o serviço de empacotamento dinâmico dos serviços de multimédia utilizadas para disponibilizar conteúdo multimédia para jogadores do cliente. Para disponibilizar vídeos no recurso de saída para os clientes para a reprodução, tem de criar uma **localizador de transmissão em fluxo** e, em seguida, crie URLs de transmissão em fluxo. 

Ao criar o **localizador de transmissão em fluxo**, além do nome do elemento, tem de especificar **política de transmissão em fluxo**. **Políticas de transmissão em fluxo** permitem-lhe definir os protocolos de transmissão em fluxo e encriptação de opções (se houver) para a sua **localizadores de transmissão em fluxo**.

Empacotamento dinâmico é utilizado se transmite o seu conteúdo ao vivo ou sob demanda. O diagrama seguinte mostra o streaming sob demanda com o fluxo de trabalho de empacotamento dinâmico.

![Empacotamento dinâmico](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

Com os serviços de multimédia, pode fornecer seu conteúdo ao vivo e sob demanda encriptado dinamicamente com o Advanced Encryption Standard (AES-128) ou / e qualquer um dos três sistemas de gestão (DRM) de direitos digitais principais: Microsoft PlayReady, Widevine da Google e Apple FairPlay. Serviços de multimédia também fornecem um serviço para entrega de chaves AES e o DRM (PlayReady, Widevine e FairPlay) licenças para os clientes autorizados.

Se especificar opções de encriptação no seu fluxo, crie o **política de chave de conteúdo** e associá-lo com sua **localizador de transmissão em fluxo**. O **política de chave de conteúdo** permite-lhe configurar a forma como a chave de conteúdo é entregue para clientes finais.

A imagem seguinte ilustra o fluxo de trabalho de proteção de conteúdo de serviços de multimédia: 

![Proteger conteúdo](./media/content-protection/content-protection.svg)

&#42;a encriptação dinâmica suporta AES-128 "chave não encriptada", CBCS e CENC. 

Pode utilizar os serviços de multimédia **manifestos dinâmica** transmitir apenas uma representação em específica ou subclips do seu vídeo. No exemplo a seguir, um codificador foi utilizado para codificar um recurso de mezanino em sete representações de vídeo de MP4s de ISO (a partir de 180p para 1080p). O elemento codificado pode ser empacotado dinamicamente em qualquer um dos seguintes protocolos de transmissão em fluxo: HLS, MPEG DASH e Smooth.  Na parte superior do diagrama, é mostrado o manifesto HLS para o elemento com sem filtros (que contém todas as representações de sete).  Na parte inferior esquerda, é mostrado o manifesto HLS ao qual foi aplicado um filtro com o nome "ott". Especifica o filtro de "ott" para remover todas as velocidades de transmissão abaixo 1 Mbps, o que resultou nos níveis de qualidade dois na parte inferior a ser eliminados na resposta. Na parte inferior direita, é mostrado o manifesto HLS ao qual foi aplicado um filtro com o nome "móvel". O filtro "móvel" Especifica a remover representações em que a resolução é maior do que 720p, o que resultou em duas representações de 1080p a ser suprimidas.

![Filtragem de representação](./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png)

- [Empacotamento dinâmico](dynamic-packaging-overview.md)
- [Pontos finais de transmissão em fluxo](streaming-endpoint-concept.md)
- [Localizadores de transmissão em fluxo](streaming-locators-concept.md)
- [Políticas de transmissão em fluxo](streaming-policy-concept.md)
- [Diretivas de chave de conteúdo](content-key-policy-concept.md)
- [Proteção de conteúdo](content-protection-overview.md)
- [Manifestos dinâmicos](filters-dynamic-manifest-overview.md)
- [Filtros](filters-concept.md)

## <a name="live-streaming"></a>Transmissão em direto

Serviços de multimédia do Azure permite-lhe fornecer eventos em direto aos seus clientes na cloud do Azure. **Eventos em direto** são responsáveis por ingerir e processar os feeds de vídeo em direto. Quando cria um **evento em direto**, é criado um ponto de final de entrada que pode utilizar para enviar um sinal ao vivo a partir de um codificador remoto. Assim que tiver o fluxo a ser encaminhados para o **evento em direto**, pode começar o evento de transmissão em fluxo através da criação de um **Asset**, **Live saída**, e **localizador de transmissão em fluxo** . **Live saída** irá arquivar a transmissão no **Asset** e disponibilizá-la para os espetadores através da **ponto final de transmissão em fluxo**. R **evento em direto** pode ser um dos dois tipos: **pass-through** e **codificação em direto**.

A imagem seguinte ilustra o fluxo de trabalho do tipo de pass-through:

![pass-through](./media/live-streaming/pass-through.svg)

- [Descrição geral de transmissão em fluxo em direto](live-streaming-overview.md)
- [Eventos em direto e saídas em direto](live-events-outputs-concept.md)

## <a name="monitoring"></a>Monitorização

### <a name="event-grid"></a>Event Grid

Para ver o progresso da tarefa, deve usar **Event Grid**. Serviços de multimédia emite também os tipos de evento em direto. Com o Event Grid, as aplicações podem escutar e reagir a eventos a partir de praticamente todos os serviços do Azure, bem como de origens personalizadas. 

- [Processamento de eventos do Event Grid](reacting-to-media-services-events.md)
- [Schemas](media-services-event-schemas.md)

## <a name="player-clients"></a>Clientes de leitores

Pode utilizar o leitor de multimédia do Azure para reproduzir conteúdo de multimédia em fluxo dos serviços de multimédia numa grande variedade de navegadores e dispositivos. O leitor de multimédia do Azure utiliza padrões da indústria, como HTML5, extensões de origem de mídia (MSE) e Encrypted Media Extensions (EME) para fornecer uma experiência plena de transmissão em fluxo adaptativa. 

- [Descrição geral de leitor de multimédia do Azure](use-azure-media-player.md)

## <a name="next-steps"></a>Passos Seguintes

[Carregar, codificar e transmitir em fluxo através dos Media Services](stream-files-tutorial-with-api.md)
