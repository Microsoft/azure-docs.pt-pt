---
title: Descrição geral da transmissão em direto através dos serviços de multimédia do Azure | Documentos da Microsoft
description: Isso permite o artigo uma visão geral do live a transmissão em fluxo através dos serviços de multimédia do Azure v3.
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
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: ab8d4fb9b46573d58fd93fc5121a4fc1918cc69d
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879391"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Transmissão em direto com Media Services do Azure v3

Serviços de multimédia do Azure permite-lhe fornecer eventos em direto aos seus clientes na cloud do Azure. Para transmitir os seus eventos em direto com Media Services, precisa do seguinte:  

- Uma câmera que é utilizada para capturar o evento em direto.<br/>Para obter ideias de configuração, confira [programa de configuração de engrenagem de vídeo de evento simples e portátil]( https://link.medium.com/KNTtiN6IeT).
- Um codificador vídeo em direto que converte sinais de uma câmera (ou outro dispositivo, como um laptop) numa contribuição feed, que é enviada para os serviços de multimédia. O feed de contribuição pode incluir sinais relacionadas com a publicidade, como marcadores SCTE 35.<br/>Para obter uma lista de recomendados codificadores de transmissão em fluxo em direto, consulte [live codificadores de transmissão em fluxo](recommended-on-premises-live-encoders.md). Além disso, consulte este blogue: [Transmissão em fluxo em direto em produção com OBS](https://link.medium.com/ttuwHpaJeT).
- Componentes nos serviços de multimédia, permitem-lhe ingerir, pré-visualização, do pacote, gravam, encriptar e transmitir o evento em direto para os seus clientes ou para uma CDN para uma maior distribuição.

Este artigo fornece uma descrição geral e a documentação de orientação de transmissão em fluxo em direto com serviços de multimédia e links para outros artigos relevantes.

> [!NOTE]
> Atualmente, não pode utilizar o portal do Azure para gerir recursos v3. Utilize a [API REST](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref) ou um dos [SDKs](developers-guide.md) suportados.

## <a name="dynamic-packaging"></a>Empacotamento dinâmico

Com os serviços de multimédia, pode tirar partido de Packaging](dynamic-packaging-overview.md) dinâmica, o que permite-lhe visualizar e difusão de transmissões em direto no [formatos MPEG DASH, HLS e Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) de contribuição feed que envia para o serviço. Os visualizadores têm acesso podem reproduzir a transmissão em direto com qualquer leitores compatível com HLS, TRAÇO ou transmissão em fluxo uniforme. Pode usar [leitor de multimédia do Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) no seu web ou aplicações móveis para fornecer a sua transmissão em fluxo em qualquer um desses protocolos.

## <a name="dynamic-encryption"></a>Encriptação dinâmica

A encriptação dinâmica permite-lhe encriptar dinamicamente o seu conteúdo ao vivo ou sob demanda com AES-128 ou qualquer um dos três sistemas de gestão (DRM) de direitos digitais principais: Microsoft PlayReady, Widevine da Google e Apple FairPlay. Serviços de multimédia também fornecem um serviço para entrega de chaves AES e o DRM (PlayReady, Widevine e FairPlay) licenças para os clientes autorizados. Para obter mais informações, consulte [encriptação dinâmica](content-protection-overview.md).

## <a name="dynamic-manifest"></a>Manifesto dinâmico

Filtragem dinâmica é utilizado para controlar o número de faixas, formatos, velocidades de transmissão e janelas de tempo de apresentação que são enviadas para os jogadores. Para obter mais informações, consulte [filtros e dos manifestos dinâmicos](filters-dynamic-manifest-overview.md).

## <a name="live-event-types"></a>Tipos de evento em direto

Um evento em direto pode ser um dos dois tipos: codificação de pass-through e em direto. Para obter detalhes sobre a transmissão em fluxo em direto no Serviços de multimédia v3, consulte [eventos ao vivo e saídas de Live](live-events-outputs-concept.md).

### <a name="pass-through"></a>Pass-through

![pass-through](./media/live-streaming/pass-through.svg)

Quando utilizar o pass-through **Evento em Direto**, conta com o codificador em direto no local para gerar uma transmissão em fluxo de vídeo de velocidade múltipla e enviá-la como um feed de contribuição para o Evento em Direto (através do protocolo RTMP ou MP4 fragmentado). O Evento em Direto realiza as transmissões em fluxo de vídeo de entrada sem qualquer processamento adicional. Tal um pass-through evento em direto é otimizado para eventos em direto de longa execução ou transmissão em direto lineares 24 x 365. 

### <a name="live-encoding"></a>Live Encoding  

![codificação do Live](./media/live-streaming/live-encoding.svg)

Ao utilizar a codificação em direto com os Serviços de Multimédia, configuraria o codificador em direto no local para enviar um vídeo de velocidade de transmissão única como o feed de contribuição para o Evento em Direto (através dos protocolos RTMP ou Fragmented-Mp4.) O Evento em Direto codifica essa transmissão em fluxo de velocidade única recebia numa [transmissão de vídeo em fluxo de velocidade múltipla](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) e disponibiliza-a para entrega nos dispositivos de reprodução através de protocolos como MPEG-DASH, HLS e Smooth Streaming. 

## <a name="live-streaming-workflow"></a>Fluxo de trabalho de transmissão em fluxo em direto

Para compreender o fluxo de trabalho de transmissão em fluxo em direto em serviços de multimédia v3, tem de primeira revisão e compreender os seguintes conceitos: 

- [Pontos Finais de Transmissão em fluxo](streaming-endpoint-concept.md)
- [Eventos em Direto e Saídas em Direto](live-events-outputs-concept.md)
- [Localizadores de Transmissão em Fluxo](streaming-locators-concept.md)

### <a name="general-steps"></a>Passos gerais

1. Na sua conta de Media Services, certifique-se de que o **ponto final de transmissão em fluxo** está em execução. 
2. Criar uma [evento em direto](live-events-outputs-concept.md). <br/>Ao criar o evento, pode especificar para início automático-lo. Em alternativa, pode iniciar o evento quando estiver pronto para começar a transmissão em fluxo.<br/> Quando o início automático está definido como true, o evento em direto será iniciado correto após a criação. A faturação tem início assim que o evento Live começa a ser executado. Tem de chamar explicitamente Stop do recurso de evento em direto para parar a faturação ainda mais. Para obter mais informações, consulte [Estados de evento em direto e de faturação](live-event-states-billing.md).
3. Obtenha os URLs de ingestão e configurar seu codificador no local para utilizar o URL para enviar a contribuição do feed.<br/>Ver [recomendado codificadores em direto](recommended-on-premises-live-encoders.md).
4. Obter o URL de pré-visualização e utilizá-lo para verificar que a entrada do codificador, na verdade, está a ser recebida.
5. Criar uma nova **Asset** objeto.
6. Criar uma **Live saída** e utilize o nome de recurso que criou.<br/>O **saída Live** irá arquivar a transmissão para o **Asset**.
7. Criar uma **localizador de transmissão em fluxo** com o incorporado **política de transmissão em fluxo** tipos.<br/>Se pretende encriptar o seu conteúdo, consulte [descrição geral da proteção de conteúdo](content-protection-overview.md).
8. Listar os caminhos na **localizador de transmissão em fluxo** para recuperar os URLs para utilizar (esses são determinísticos).
9. Obter o nome do anfitrião para o **ponto final de transmissão em fluxo** que deseja transmitir em fluxo do.
10. Combine o URL do passo 8 com o nome de anfitrião no passo 9 para obter o URL completo.
11. Se pretender parar a tornar sua **evento em direto** podem ser visualizados, terá de parar a transmissão em fluxo o evento e a eliminar a **localizador de transmissão em fluxo**.

## <a name="other-important-articles"></a>Outros artigos importantes

- [Codificadores em direto recomendados](recommended-on-premises-live-encoders.md)
- [Utilizar um DVR na cloud](live-event-cloud-dvr.md)
- [Comparação de recursos de tipos de evento em direto](live-event-types-comparison.md)
- [Estados e faturação](live-event-states-billing.md)
- [Latência](live-event-latency.md)

## <a name="next-steps"></a>Passos Seguintes

* [Tutorial de transmissão em fluxo em direto](stream-live-tutorial-with-api.md)
* [Orientações de migração para mover de serviços de multimédia v2 para v3](migrate-from-v2-to-v3.md)
