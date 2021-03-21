---
title: Visão geral do streaming ao vivo
description: Este artigo apresenta uma visão geral do streaming ao vivo utilizando o Azure Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: b552dcc0e6766316e55e9cdda6e462b2d4abfd2b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955924"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Streaming em direto com a Azure Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A Azure Media Services permite-lhe entregar eventos ao vivo aos seus clientes na nuvem Azure. Para transmitir os seus eventos ao vivo com os Media Services, precisa do seguinte:  

- Uma câmara que é usada para capturar o evento ao vivo.<br/>Para obter ideias de configuração, confira [a configuração de equipamento de vídeo de evento simples e portátil]( https://link.medium.com/KNTtiN6IeT).

    Se não tiver acesso a uma câmara, ferramentas como [o Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) podem ser usadas para gerar uma transmissão em direto a partir de um ficheiro de vídeo.
- Um codificadores de vídeo ao vivo que converte sinais de uma câmara (ou de outro dispositivo, como um portátil) num feed de contribuição que é enviado para os Media Services. O feed de contribuição pode incluir sinais relacionados com a publicidade, tais como marcadores SCTE-35.<br/>Para obter uma lista de codificadores de streaming ao vivo recomendados, consulte [os codificadores de streaming ao vivo.](recommended-on-premises-live-encoders.md) Confira também este blog: [Produção de streaming ao vivo com OBS](https://link.medium.com/ttuwHpaJeT).
- Componentes em Media Services, que lhe permitem ingerir, pré-visualizar, embalar, gravar, encriptar e transmitir o evento ao vivo para os seus clientes, ou para um CDN para posterior distribuição.

Para clientes que procuram entregar conteúdo a grandes audiências de internet, recomendamos que você ative a CDN no [ponto final](streaming-endpoint-concept.md)de streaming .

Este artigo dá uma visão geral e orientação de streaming ao vivo com os Media Services e links para outros artigos relevantes.
 
> [!NOTE]
> Pode utilizar o [portal Azure](https://portal.azure.com/) para gerir v3 [Live Events,](live-events-outputs-concept.md)ver [ativos](assets-concept.md)v3, obter informações sobre o acesso às APIs. Para todas as outras tarefas de gestão (por exemplo, Transformações e Empregos), utilize o [REST API,](/rest/api/media/) [CLI,](/cli/azure/ams)ou um dos [SDKs suportados.](media-services-apis-overview.md#sdks)

## <a name="dynamic-packaging-and-delivery"></a>Embalagem e entrega dinâmicas

Com os Media Services, pode aproveitar [a embalagem dinâmica,](dynamic-packaging-overview.md)que lhe permite visualizar e transmitir os seus streams ao vivo nos [formatos MPEG DASH, HLS e Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) a partir do feed de contribuição que está a ser enviado para o serviço. Os seus espectadores podem reproduzir o live stream com qualquer hls, DASH ou jogadores compatíveis com o Streaming Suave. Pode utilizar [o Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) na sua web ou aplicações móveis para entregar o seu stream em qualquer um destes protocolos.

## <a name="dynamic-encryption"></a>Encriptação dinâmica

A encriptação dinâmica permite-lhe encriptar dinamicamente o seu conteúdo ao vivo ou a pedido com o AES-128 ou qualquer um dos três principais sistemas de gestão de direitos digitais (DRM): Microsoft PlayReady, Google Widevine e Apple FairPlay. Os Media Services também fornecem um serviço para a entrega de chaves AES e licenças DRM (PlayReady, Widevine e FairPlay) a clientes autorizados. Para obter mais informações, consulte [a encriptação dinâmica.](content-protection-overview.md)

> [!NOTE]
> Widevine é um serviço fornecido pela Google Inc. e sujeito aos termos de serviço e Política de Privacidade da Google, Inc.

## <a name="dynamic-filtering"></a>Filtragem dinâmica

A filtragem dinâmica é usada para controlar o número de faixas, formatos, bitrates e janelas de tempo de apresentação que são enviadas para os jogadores. Para mais informações, consulte [filtros e manifestos dinâmicos.](filters-dynamic-manifest-overview.md)

## <a name="live-event-types"></a>Tipos de eventos ao vivo

[Os eventos](/rest/api/media/liveevents) ao vivo são responsáveis por ingerir e processar os feeds de vídeo ao vivo. Um evento ao vivo pode ser definido para um *passe-through* (um codificadora ao vivo no local envia um fluxo bitrate múltiplo) ou *codificação ao vivo* (um codificante ao vivo no local envia um único fluxo de bitrate). Para mais detalhes sobre o streaming em direto nos Media Services v3, consulte [eventos ao vivo e saídas ao vivo.](live-events-outputs-concept.md)

### <a name="pass-through"></a>Pass-through

![Diagrama mostrando como o vídeo e o áudio se alimentam de um Evento Ao Vivo que passa são ingeridos e processados.](./media/live-streaming/pass-through.svg)

Ao utilizar o **Live Event** de passagem, confia no codificadora ao vivo no local para gerar um fluxo de vídeo bitrate múltiplo e enviar-o como feed de contribuição para o Live Event (utilizando o protocolo de entrada RTMP ou MP4 fragmentado). O Live Event transporta então os streams de vídeo de entrada para o pacote dinâmico (Streaming Endpoint) sem qualquer transcoding adicional. Este evento live pass-through é otimizado para eventos ao vivo de longa duração ou 24x365 streaming linear ao vivo. 

### <a name="live-encoding"></a>Live Encoding  

![codificação em direto](./media/live-streaming/live-encoding.svg)

Ao utilizar a codificação em nuvem com os Media Services, configuraria o seu codificadora ao vivo no local para enviar um único vídeo bitrate como feed de contribuição (até 32Mbps agregado) para o Live Event (utilizando o protocolo de entrada RTMP ou fragmentado-MP4). O Live Event transcofica o fluxo bitrate único de entrada em [vários streams de vídeo bitrate](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) em resoluções variadas para melhorar a entrega e disponibiliza-o para entrega a dispositivos de reprodução através de protocolos padrão da indústria como MPEG-DASH, Apple HTTP Live Streaming (HLS) e Microsoft Smooth Streaming. 

### <a name="live-transcription-preview"></a>Transcrição ao vivo (pré-visualização)

A transcrição ao vivo é uma característica que pode usar com eventos ao vivo que são de passagem ou codificação ao vivo. Para mais informações, consulte [a transcrição em direto.](live-transcription.md) Quando esta funcionalidade está ativada, o serviço utiliza a funcionalidade [Discurso-Texto](../../cognitive-services/speech-service/speech-to-text.md) dos Serviços Cognitivos para transcrever as palavras faladas no áudio de entrada em texto. Este texto é então disponibilizado para entrega juntamente com vídeo e áudio nos protocolos MPEG-DASH e HLS.

> [!NOTE]
> Atualmente, a transcrição ao vivo está disponível como uma funcionalidade de pré-visualização no West US 2.

## <a name="live-streaming-workflow"></a>Fluxo de trabalho de streaming ao vivo

Para compreender o fluxo de trabalho em streaming ao vivo nos Serviços de Media v3, tem primeiro de rever e compreender os seguintes conceitos: 

- [Pontos finais de transmissões em fluxo](streaming-endpoint-concept.md)
- [Eventos em direto e saídas em direto](live-events-outputs-concept.md)
- [Localizadores de transmissão em fluxo](streaming-locators-concept.md)

### <a name="general-steps"></a>Etapas gerais

1. Na sua conta de Media Services, certifique-se de que o **ponto final de streaming** (origem) está em execução. 
2. Criar um [evento ao vivo.](live-events-outputs-concept.md) <br/>Ao criar o evento, pode especificar para o iniciar automaticamente. Em alternativa, pode iniciar o evento quando estiver pronto para começar o streaming.<br/> Quando o arranque automático estiver definido para ser verdade, o Live Event será iniciado logo após a criação. A faturação começa assim que o Live Event começa a funcionar. Você deve explicitamente ligar para parar o recurso do evento ao vivo para parar mais faturação. Para mais informações, consulte [estados de eventos ao vivo e faturação.](live-event-states-billing.md)
3. Obtenha o (s) inger(s) e configuure o seu codificadora no local para usar o URL para enviar o feed de contribuição.<br/>Consulte [os codificadores vivos recomendados.](recommended-on-premises-live-encoders.md)
4. Obtenha o URL de pré-visualização e use-o para verificar se a entrada do codificante está realmente a ser recebida.
5. Criar um novo objeto **de ativo.** 

    Cada saída ao vivo está associada a um ativo, que utiliza para gravar o vídeo no recipiente de armazenamento de bolhas Azure associado. 
6. Crie uma **saída ao vivo** e use o nome de ativo que criou para que o fluxo possa ser arquivado no ativo.

    As Saídas Ao Vivo começam na criação e param quando apagadas. Quando elimina a Saída Ao Vivo, não está a excluir o ativo e o conteúdo subjacentes no ativo.
7. Crie um **localizador de streaming** com os [tipos de política de streaming incorporados](streaming-policy-concept.md).

    Para publicar a saída ao vivo, tem de criar um localizador de streaming para o ativo associado. 
8. Listar os caminhos no **localizador de streaming** para recuperar os URLs a utilizar (estes são determinísticos).
9. Obtenha o nome de anfitrião para o **ponto final de streaming** (Origin) a partir do stream de.
10. Combine o URL do passo 8 com o nome anfitrião no passo 9 para obter o URL completo.
11. Se desejar deixar de visualizar o seu **evento ao vivo,** tem de parar de transmitir o evento e eliminar o **localizador de streaming**.
12. Se terminar a transmissão em fluxo de eventos e pretende limpar os recursos aprovisionados anteriormente, siga o procedimento seguinte.

    * Termine o envio da transmissão em fluxo do codificador.
    * Pare o evento ao vivo. Uma vez que o evento ao vivo seja interrompido, não incorrerá em quaisquer acusações. Quando quiser reiniciar a transmissão, esta terá o mesmo URL de inserção, desta forma, não terá de reconfigurar o codificador.
    * Pode parar o seu ponto final de streaming, a menos que queira continuar a fornecer o arquivo do seu evento ao vivo como um fluxo on-demand. Se o evento ao vivo estiver parado, não incorrerá em nenhuma acusação.

O ativo a que a saída ao vivo está a arquivar, torna-se automaticamente um ativo a pedido quando a saída ao vivo é eliminada. Deve eliminar todas as saídas ao vivo antes de um evento ao vivo poder ser interrompido. Pode utilizar uma proteção de bandeira [opcionalOutputsOnStop](/rest/api/media/liveevents/stop#request-body) para remover automaticamente as saídas vivas no stop. 

> [!TIP]
> Ver [tutorial de streaming ao vivo,](stream-live-tutorial-with-api.md)o artigo examina o código que implementa os passos acima descritos.

## <a name="other-important-articles"></a>Outros artigos importantes

- [Codificadores em direto recomendados](recommended-on-premises-live-encoders.md)
- [Using a cloud DVR](live-event-cloud-dvr.md) (Utilizar um DVR na cloud)
- [Comparação de tipos de eventos ao vivo](live-event-types-comparison.md)
- [States and billing](live-event-states-billing.md) (Estados e faturação)
- [Latência](live-event-latency.md)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

Consulte o artigo [perguntas frequentes.](frequently-asked-questions.md#live-streaming)

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, dê feedback, obtenha atualizações

Consulte o artigo da [comunidade Azure Media Services](media-services-community.md) para ver diferentes formas de fazer perguntas, dar feedback e obter atualizações sobre os Media Services.

## <a name="next-steps"></a>Passos seguintes

* [Arranque rápido de streaming ao vivo](live-events-wirecast-quickstart.md)
* [Tutorial de streaming ao vivo](stream-live-tutorial-with-api.md)
* [Orientação de migração para passar dos Serviços de Comunicação v2 para v3](migrate-v-2-v-3-migration-introduction.md)