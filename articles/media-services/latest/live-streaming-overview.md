---
title: Visão geral da transmissão ao vivo com os serviços de mídia do Azure v3 | Microsoft Docs
description: Este artigo fornece uma visão geral da transmissão ao vivo usando os serviços de mídia do Azure v3.
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
ms.openlocfilehash: 8d7db428d7f71383abf5425d7cc1ddbbab3b7a52
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037883"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Transmissão ao vivo com os serviços de mídia do Azure v3

Os serviços de mídia do Azure permitem que você forneça eventos ao vivo para seus clientes na nuvem do Azure. Para transmitir seus eventos ao vivo com os serviços de mídia, você precisará do seguinte:  

- Uma câmera que é usada para capturar o evento ao vivo.<br/>Para ideias de instalação, confira [configuração simples e portátil de monitoração de vídeo de eventos]( https://link.medium.com/KNTtiN6IeT).

    Se você não tiver acesso a uma câmera, as ferramentas como [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) poderão ser usadas para gerar um feed ao vivo de um arquivo de vídeo.
- Um codificador de vídeo ao vivo que converte sinais de uma câmera (ou outro dispositivo, como um laptop) em um feed de contribuição enviado aos serviços de mídia. O feed de contribuição pode incluir sinais relacionados a anúncios, como marcadores SCTE-35.<br/>Para obter uma lista de codificadores de transmissão ao vivo recomendados, consulte [codificadores de transmissão ao vivo](recommended-on-premises-live-encoders.md). Além disso, confira este blog: [produção de transmissão ao vivo com Obs](https://link.medium.com/ttuwHpaJeT).
- Componentes nos serviços de mídia, que permitem ingerir, Visualizar, empacotar, registrar, criptografar e transmitir o evento ao vivo para seus clientes ou para uma CDN para uma distribuição adicional.

Este artigo fornece uma visão geral e diretrizes de transmissão ao vivo com os serviços de mídia e links para outros artigos relevantes.
 
> [!NOTE]
> Atualmente, não pode utilizar o portal do Azure para gerir recursos v3. Utilize a [API REST](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref) ou um dos [SDKs](media-services-apis-overview.md#sdks) suportados.

## <a name="dynamic-packaging"></a>Empacotamento dinâmico

Com os serviços de mídia, você pode aproveitar o [empacotamento dinâmico](dynamic-packaging-overview.md), que permite que você visualize e transmita suas transmissões ao vivo nos [formatos MPEG Dash, HLS e Smooth streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) do feed de contribuição que está sendo enviado para o serviço. Seus visualizadores podem reproduzir a transmissão ao vivo com qualquer player compatível com HLS, DASH ou Smooth Streaming. Você pode usar [player de mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) em seus aplicativos Web ou móveis para entregar seu fluxo em qualquer um desses protocolos.

## <a name="dynamic-encryption"></a>Criptografia dinâmica

A criptografia dinâmica permite que você criptografe dinamicamente seu conteúdo ao vivo ou sob demanda com o AES-128 ou qualquer um dos três principais sistemas de DRM (gerenciamento de direitos digitais): Microsoft PlayReady, Google Widevine e Apple FairPlay. Serviços de multimédia também fornecem um serviço para entrega de chaves AES e o DRM (PlayReady, Widevine e FairPlay) licenças para os clientes autorizados. Para obter mais informações, consulte [criptografia dinâmica](content-protection-overview.md).

## <a name="dynamic-manifest"></a>Manifesto dinâmico

A filtragem dinâmica é usada para controlar o número de faixas, formatos, taxas de bits e janelas de tempo de apresentação que são enviados para os jogadores. Para obter mais informações, consulte [filtros e manifestos dinâmicos](filters-dynamic-manifest-overview.md).

## <a name="live-event-types"></a>Tipos de eventos ao vivo

Os [Eventos em Direto](https://docs.microsoft.com/rest/api/media/liveevents) são responsáveis pela ingestão e o processamento dos feeds de vídeos em direto. Um evento ao vivo pode ser um dos dois tipos: codificação ativa e de passagem. Para obter detalhes sobre a transmissão ao vivo nos serviços de mídia v3, consulte [eventos ao vivo e saídas dinâmicas](live-events-outputs-concept.md).

### <a name="pass-through"></a>Pass-through

![pass-through](./media/live-streaming/pass-through.svg)

Ao usar o evento de passagem **ao vivo**, você depende de seu codificador ao vivo local para gerar um fluxo de vídeo com várias taxas de bits e enviá-lo como o feed de contribuição para o evento ao vivo (usando o protocolo de entrada RTMP ou MP4 fragmentado). O evento ao vivo então executa os fluxos de vídeo de entrada para o empacotador dinâmico (ponto de extremidade de streaming) sem nenhuma transcodificação adicional. Esse evento ao vivo de passagem é otimizado para eventos ao vivo de execução longa ou transmissão ao vivo linear 24x365. 

### <a name="live-encoding"></a>Live Encoding  

![codificação em direto](./media/live-streaming/live-encoding.svg)

Ao usar a codificação de nuvem com os serviços de mídia, você configuraria seu codificador ao vivo local para enviar um vídeo de taxa de bits única como o feed de contribuição (até 32Mbps agregado) para o evento ao vivo (usando o protocolo de entrada RTMP ou MP4 fragmentado). O evento ao vivo codifica o fluxo de taxa de bits única de entrada em [fluxos de vídeo de taxa](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) de bits múltipla em diferentes resoluções para melhorar a entrega e o disponibiliza para entrega a dispositivos de reprodução por meio de protocolos padrão do setor, como MPEG-Dash, Apple http Live streaming (hls) e Microsoft Smooth streaming. 

### <a name="live-transcription"></a>Transcrição ao vivo

A transcrição ao vivo é um recurso que você pode usar com eventos ao vivo que são de passagem ou codificação ativa. Para obter mais informações, consulte [transcrição ao vivo](live-transcription.md). Quando esse recurso é habilitado, o serviço usa o recurso de [conversão de fala em texto](../../cognitive-services/speech-service/speech-to-text.md) de serviços cognitivas para transcrever as palavras faladas no áudio de entrada em texto. Esse texto é disponibilizado para entrega junto com vídeo e áudio em protocolos MPEG-DASH e HLS.

> [!NOTE]
> Atualmente, a transcrição ao vivo está disponível como um recurso de visualização no oeste dos EUA 2.

## <a name="live-streaming-workflow"></a>Fluxo de trabalho de transmissão ao vivo

Para entender o fluxo de trabalho de transmissão ao vivo nos serviços de mídia v3, você precisa primeiro examinar e entender os seguintes conceitos: 

- [Streaming Endpoints](streaming-endpoint-concept.md) (Pontos finais de transmissão em fluxo)
- [Live Events and Live Outputs](live-events-outputs-concept.md) (Eventos em Direto e Saídas em Direto)
- [Streaming Locators](streaming-locators-concept.md) (Localizadores de Transmissão em Fluxo)

### <a name="general-steps"></a>Etapas gerais

1. Em sua conta de serviços de mídia, verifique se o **ponto de extremidade de streaming** (origem) está em execução. 
2. Crie um [evento ao vivo](live-events-outputs-concept.md). <br/>Ao criar o evento, você pode especificar para iniciá-lo. Como alternativa, você pode iniciar o evento quando estiver pronto para iniciar o streaming.<br/> Quando AutoStart é definido como true, o evento ao vivo será iniciado logo após a criação. A cobrança começa assim que o evento ao vivo começa a ser executado. Você deve chamar Stop explicitamente no recurso de evento ao vivo para interromper mais cobranças. Para obter mais informações, consulte [Live Event States e Billing](live-event-states-billing.md).
3. Obtenha as URLs de ingestão e configure seu codificador local para usar a URL para enviar o feed de contribuição.<br/>Consulte [codificadores ao vivo recomendados](recommended-on-premises-live-encoders.md).
4. Obtenha a URL de visualização e use-a para verificar se a entrada do codificador está realmente sendo recebida.
5. Crie um novo objeto de **ativo** . 

    Cada saída ao vivo é associada a um ativo, que ele usa para gravar o vídeo no contêiner de armazenamento de BLOBs do Azure associado. 
6. Crie uma **saída ao vivo** e use o nome do ativo que você criou para que o fluxo possa ser arquivado no ativo.

    As saídas ao vivo começam na criação e param quando excluídas. Ao excluir a saída ao vivo, você não está excluindo o ativo subjacente e o conteúdo no ativo.
7. Crie um **localizador de streaming** com os [tipos de política de streaming internos](streaming-policy-concept.md).

    Para publicar a saída ao vivo, você deve criar um localizador de streaming para o ativo associado. 
8. Liste os caminhos no **localizador de streaming** para recuperar as URLs a serem usadas (elas são determinísticas).
9. Obtenha o nome do host para o **ponto de extremidade de streaming** (origem) do qual você deseja transmitir.
10. Combine a URL da etapa 8 com o nome do host na etapa 9 para obter a URL completa.
11. Se você quiser parar de tornar seu **evento ao vivo** visível, será necessário parar de transmitir o evento e excluir o **localizador de streaming**.
12. Se terminar a transmissão em fluxo de eventos e pretende limpar os recursos aprovisionados anteriormente, siga o procedimento seguinte.

    * Termine o envio da transmissão em fluxo do codificador.
    * Pare o evento ao vivo. Depois que o evento ao vivo for interrompido, ele não incorrerá em nenhum encargo. Quando quiser reiniciar a transmissão, esta terá o mesmo URL de inserção, desta forma, não terá de reconfigurar o codificador.
    * Pode parar o seu Ponto Final de Transmissão em Fluxo, a menos que pretenda continuar a fornecer o arquivo do seu evento em direto como uma transmissão em fluxo a pedido. Se o evento ao vivo estiver no estado parado, ele não incorrerá em nenhum encargo.

O ativo no qual a saída ao vivo está sendo arquivada torna-se automaticamente um ativo sob demanda quando a saída dinâmica é excluída. Você deve excluir todas as saídas ao vivo antes que um evento ao vivo possa ser interrompido. Você pode usar um sinalizador opcional [removeOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) para remover automaticamente as saídas dinâmicas ao parar. 

> [!TIP]
> Consulte o [tutorial de transmissão ao vivo](stream-live-tutorial-with-api.md), o artigo examina o código que implementa as etapas descritas acima.

## <a name="other-important-articles"></a>Outros artigos importantes

- [Codificadores ao vivo recomendados](recommended-on-premises-live-encoders.md)
- [Using a cloud DVR](live-event-cloud-dvr.md) (Utilizar um DVR na cloud)
- [Comparação de recursos de tipos de eventos ao vivo](live-event-types-comparison.md)
- [Estados e faturação](live-event-states-billing.md)
- [Latência](live-event-latency.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, envie comentários, obtenha atualizações

Confira o artigo [da Comunidade dos serviços de mídia do Azure](media-services-community.md) para ver diferentes maneiras que você pode fazer perguntas, fornecer comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Passos seguintes

* [Tutorial de transmissão ao vivo](stream-live-tutorial-with-api.md)
* [Diretrizes de migração para mudar dos serviços de mídia v2 para v3](migrate-from-v2-to-v3.md)
