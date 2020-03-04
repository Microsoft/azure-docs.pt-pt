---
title: Visão geral do streaming ao vivo com a Azure Media Services v3  Microsoft Docs
description: Este artigo dá uma visão geral do streaming ao vivo usando o Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/12/2019
ms.author: juliako
ms.openlocfilehash: b366262ce7849658eb84444d873956c25ab38804
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78244666"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Streaming ao vivo com Azure Media Services v3

A Azure Media Services permite-lhe entregar eventos ao vivo aos seus clientes na nuvem Azure. Para transmitir os seus eventos ao vivo com os Media Services, precisa do seguinte:  

- Uma câmara que é usada para capturar o evento ao vivo.<br/>Para ideias de configuração, confira configuração de equipamento de [vídeo de evento simples e portátil]( https://link.medium.com/KNTtiN6IeT).

    Se não tiver acesso a uma câmara, ferramentas como [a Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) podem ser usadas para gerar um feed ao vivo a partir de um ficheiro de vídeo.
- Um codificador de vídeo ao vivo que converte sinais de uma câmara (ou outro dispositivo, como um portátil) num feed de contribuição que é enviado para os Media Services. O feed de contribuição pode incluir sinais relacionados com publicidade, tais como marcadores SCTE-35.<br/>Para obter uma lista de codificadores de streaming recomendados, consulte [codificadores de streaming ao vivo](recommended-on-premises-live-encoders.md). Além disso, confira este blog: Produção de [streaming ao vivo com OBS](https://link.medium.com/ttuwHpaJeT).
- Componentes em Serviços de Media, que lhe permitem ingerir, pré-visualizar, embalar, gravar, encriptar e transmitir o evento ao vivo aos seus clientes, ou a um CDN para posterior distribuição.

Este artigo dá uma visão geral e orientação de streaming ao vivo com os Media Services e ligações a outros artigos relevantes.
 
> [!NOTE]
> Atualmente, não pode utilizar o portal do Azure para gerir recursos v3. Utilize a [API REST](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref) ou um dos [SDKs](media-services-apis-overview.md#sdks) suportados.

## <a name="dynamic-packaging"></a>Embalagem Dinâmica

Com os Media Services, pode usufruir da [Dynamic Packaging,](dynamic-packaging-overview.md)que lhe permite visualizar e transmitir os seus streams ao vivo em [formatos MPEG DASH, HLS e Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) a partir do feed de contribuição que está a ser enviado para o serviço. Os seus espectadores podem reproduzir o live stream com quaisquer jogadores compatíveis hls, DASH ou Smooth Streaming. Pode utilizar [o Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) nas suas aplicações web ou móveis para entregar o seu stream em qualquer um destes protocolos.

## <a name="dynamic-encryption"></a>Encriptação dinâmica

A encriptação dinâmica permite-lhe encriptar de forma dinâmica os seus conteúdos ao vivo ou a pedido com a AES-128 ou qualquer um dos três principais sistemas de gestão de direitos digitais (DRM): Microsoft PlayReady, Google Widevine e Apple FairPlay. Serviços de multimédia também fornecem um serviço para entrega de chaves AES e o DRM (PlayReady, Widevine e FairPlay) licenças para os clientes autorizados. Para mais informações, consulte [encriptação Dinâmica](content-protection-overview.md).

> [!NOTE]
> A Widevine é um serviço prestado pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="dynamic-manifest"></a>Manifesto Dinâmico

A filtragem dinâmica é usada para controlar o número de faixas, formatos, bitrates e janelas de tempo de apresentação que são enviadas aos jogadores. Para mais informações, consulte [filtros e manifestos dinâmicos](filters-dynamic-manifest-overview.md).

## <a name="live-event-types"></a>Tipos de eventos ao vivo

Os [Eventos em Direto](https://docs.microsoft.com/rest/api/media/liveevents) são responsáveis pela ingestão e o processamento dos feeds de vídeos em direto. Um evento ao vivo pode ser definido para um *pass-through* (um codificador ao vivo no local envia um fluxo de bitrate múltiplo) ou *codificação ao vivo* (um codificador ao vivo no local envia um único fluxo de bitrate). Para mais detalhes sobre o streaming em direto nos Media Services v3, consulte [Eventos ao Vivo e Saídas ao Vivo.](live-events-outputs-concept.md)

### <a name="pass-through"></a>Pass-through

![pass-through](./media/live-streaming/pass-through.svg)

Ao utilizar o evento **live**pass-through, você confia no seu codificador ao vivo no local para gerar um fluxo de vídeo múltiplo bitrate e enviá-lo como feed de contribuição para o Live Event (usando o protocolo de entrada RTMP ou MP4 fragmentado). O Live Event transporta então através dos streams de vídeo que chegam ao embalador dinâmico (Streaming Endpoint) sem qualquer transcodificação adicional. Tal evento ao vivo pass-through está otimizado para eventos ao vivo de longa duração ou streaming linear 24x365. 

### <a name="live-encoding"></a>Live Encoding  

![codificação em direto](./media/live-streaming/live-encoding.svg)

Ao utilizar a codificação em nuvem com os Media Services, configuraria o seu codificador ao vivo no local para enviar um único vídeo bitrate como feed de contribuição (até 32Mbps agregado) para o Live Event (utilizando o protocolo de entrada RTMP ou MP4 fragmentado). O Live Event transcodifica o fluxo de bitrate único em [vários streams](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) de vídeo bitrate em várias resoluções para melhorar a entrega e disponibiliza-o para entrega em dispositivos de reprodução através de protocolos padrão da indústria como MPEG-DASH, Apple HTTP Live Streaming (HLS) e Microsoft Smooth Streaming. 

### <a name="live-transcription-preview"></a>Transcrição ao vivo (pré-visualização)

A transcrição ao vivo é uma funcionalidade que pode utilizar com Eventos Ao Vivo que são pass-through ou codificação ao vivo. Para mais informações, consulte a [transcrição ao vivo.](live-transcription.md) Quando esta funcionalidade está ativada, o serviço utiliza a funcionalidade [Discurso-Texto](../../cognitive-services/speech-service/speech-to-text.md) dos Serviços Cognitivos para transcrever as palavras faladas no áudio que está a chegar em texto. Este texto é então disponibilizado para entrega juntamente com vídeo e áudio nos protocolos MPEG-DASH e HLS.

> [!NOTE]
> Atualmente, a transcrição ao vivo está disponível como uma funcionalidade de pré-visualização no West US 2.

## <a name="live-streaming-workflow"></a>Fluxo de trabalho de streaming ao vivo

Para compreender o fluxo de trabalho em streaming ao vivo nos Serviços de Media v3, tem de rever e compreender primeiro os seguintes conceitos: 

- [Streaming Endpoints](streaming-endpoint-concept.md) (Pontos Finais de Transmissão em Fluxo)
- [Live Events and Live Outputs](live-events-outputs-concept.md) (Eventos em Direto e Saídas em Direto)
- [Streaming Locators](streaming-locators-concept.md) (Localizadores de Transmissão em Fluxo)

### <a name="general-steps"></a>Passos gerais

1. Na sua conta de Media Services, certifique-se de que o **Ponto Final de Streaming** (Origem) está em funcionamento. 
2. Criar um [Evento Ao Vivo.](live-events-outputs-concept.md) <br/>Ao criar o evento, pode especificar para iniciar automaticamente. Em alternativa, pode iniciar o evento quando estiver pronto para começar a transmitir.<br/> Quando o arranque automático estiver definido para ser verdade, o Evento Ao Vivo será iniciado logo após a criação. A faturação começa assim que o Evento Ao Vivo começar a correr. Deve ligar explicitamente para parar o recurso Stop on the Live Event para parar a faturação adicional. Para mais informações, consulte os estados do [Live Event e a faturação.](live-event-states-billing.md)
3. Obtenha os URL(s) ingerir e configure o seu codificador no local para utilizar o URL para enviar o feed de contribuição.<br/>Consulte [os codificadores ao vivo recomendados](recommended-on-premises-live-encoders.md).
4. Obtenha o URL de pré-visualização e use-o para verificar se a entrada do codificador está realmente a ser recebida.
5. Criar um novo objeto **De Ativo.** 

    Cada Saída Ao Vivo está associada a um ativo, que utiliza para gravar o vídeo no recipiente de armazenamento de blob Azure associado. 
6. Crie uma **Saída Ao Vivo** e use o nome de ativo que criou para que o fluxo possa ser arquivado no ativo.

    As Saídas ao Vivo começam na criação e param quando eliminadas. Ao eliminar a Saída Ao Vivo, não está a eliminar o ativo e o conteúdo subjacentes no ativo.
7. Crie um Localizador de **Streaming** com os [tipos de Política de Streaming incorporados](streaming-policy-concept.md).

    Para publicar a Saída ao Vivo, tem de criar um Localizador de Streaming para o ativo associado. 
8. Enumere os caminhos no Localizador de **Streaming** para recuperar os URLs para usar (estes são determinísticos).
9. Obtenha o nome de anfitrião para o **Streaming Endpoint** (Origem) de que deseja transmitir.
10. Combine o URL do passo 8 com o nome de anfitrião no passo 9 para obter o URL completo.
11. Se pretender deixar de tornar o seu **Evento Ao Vivo** visível, tem de parar de transmitir o evento e eliminar o Localizador de **Streaming**.
12. Se terminar a transmissão em fluxo de eventos e pretende limpar os recursos aprovisionados anteriormente, siga o procedimento seguinte.

    * Termine o envio da transmissão em fluxo do codificador.
    * Pare o Evento Ao Vivo. Uma vez que o Evento Ao Vivo seja interrompido, não incorrerá em nenhuma acusação. Quando quiser reiniciar a transmissão, esta terá o mesmo URL de inserção, desta forma, não terá de reconfigurar o codificador.
    * Pode parar o seu Ponto Final de Transmissão em Fluxo, a menos que pretenda continuar a fornecer o arquivo do seu evento em direto como uma transmissão em fluxo a pedido. Se o Evento Ao Vivo estiver em estado de paragem, não incorrerá em nenhuma acusação.

O ativo a que a saída ao vivo está a arquivar, torna-se automaticamente um ativo a pedido quando a saída ao vivo é eliminada. Deve eliminar todas as saídas ao vivo antes que um evento ao vivo possa ser interrompido. Pode utilizar uma bandeira opcional [removendoOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) para remover automaticamente as saídas ao vivo em paragem. 

> [!TIP]
> Ver tutorial de [streaming ao vivo,](stream-live-tutorial-with-api.md)o artigo examina o código que implementa os passos acima descritos.

## <a name="other-important-articles"></a>Outros artigos importantes

- [Codificadores ao vivo recomendados](recommended-on-premises-live-encoders.md)
- [Using a cloud DVR](live-event-cloud-dvr.md) (Utilizar um DVR na cloud)
- [Os tipos de eventos ao vivo apresentam comparação](live-event-types-comparison.md)
- [Estados e faturação](live-event-states-billing.md)
- [Latência](live-event-latency.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Confira o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Serviços de Media.

## <a name="next-steps"></a>Passos seguintes

* [Tutorial de streaming ao vivo](stream-live-tutorial-with-api.md)
* [Orientação de migração para a mudança dos Serviços de Media v2 para v3](migrate-from-v2-to-v3.md)
