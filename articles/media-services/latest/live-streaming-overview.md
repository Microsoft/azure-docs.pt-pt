---
title: Descrição geral do Live transmissão em fluxo com serviços de multimédia do Azure v3 | Documentos da Microsoft
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
ms.date: 06/16/2019
ms.author: juliako
ms.openlocfilehash: 0abc3eec380cccae2672d0e9aa4a3a4c7199362f
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295667"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Transmissão em direto com Media Services do Azure v3

Serviços de multimédia do Azure permite-lhe fornecer eventos em direto aos seus clientes na cloud do Azure. Para transmitir os seus eventos em direto com Media Services, precisa do seguinte:  

- Uma câmera que é utilizada para capturar o evento em direto.<br/>Para obter ideias de configuração, confira [programa de configuração de engrenagem de vídeo de evento simples e portátil]( https://link.medium.com/KNTtiN6IeT).

    Se não tiver acesso a uma câmera, as ferramentas como [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) pode ser utilizado gerar ao live feed a partir de um ficheiro de vídeo.
- Um codificador vídeo em direto que converte sinais de uma câmera (ou outro dispositivo, como um laptop) numa contribuição feed, que é enviada para os serviços de multimédia. O feed de contribuição pode incluir sinais relacionadas com a publicidade, como marcadores SCTE 35.<br/>Para obter uma lista de recomendados codificadores de transmissão em fluxo em direto, consulte [live codificadores de transmissão em fluxo](recommended-on-premises-live-encoders.md). Além disso, consulte este blogue: [Transmissão em fluxo em direto em produção com OBS](https://link.medium.com/ttuwHpaJeT).
- Componentes nos serviços de multimédia, permitem-lhe ingerir, pré-visualização, do pacote, gravam, encriptar e transmitir o evento em direto para os seus clientes ou para uma CDN para uma maior distribuição.

Este artigo fornece uma descrição geral e a documentação de orientação de transmissão em fluxo em direto com serviços de multimédia e links para outros artigos relevantes.
 
> [!NOTE]
> Atualmente, não pode utilizar o portal do Azure para gerir recursos v3. Utilize a [API REST](https://aka.ms/ams-v3-rest-ref), a [CLI](https://aka.ms/ams-v3-cli-ref) ou um dos [SDKs](media-services-apis-overview.md#sdks) suportados.

## <a name="dynamic-packaging"></a>Empacotamento dinâmico

Com os Media Services, pode aproveitar [empacotamento dinâmico](dynamic-packaging-overview.md), que permite-lhe visualizar e difusão de transmissões em direto no [formatos MPEG DASH, HLS e Smooth Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) de contribuição de feed que está a ser enviado para o serviço. Os visualizadores têm acesso podem reproduzir a transmissão em direto com qualquer leitores compatível com HLS, TRAÇO ou transmissão em fluxo uniforme. Pode usar [leitor de multimédia do Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) no seu web ou aplicações móveis para fornecer a sua transmissão em fluxo em qualquer um desses protocolos.

## <a name="dynamic-encryption"></a>Encriptação dinâmica

A encriptação dinâmica permite-lhe encriptar dinamicamente o seu conteúdo ao vivo ou sob demanda com AES-128 ou qualquer um dos três sistemas de gestão (DRM) de direitos digitais principais: Microsoft PlayReady, Widevine da Google e Apple FairPlay. Serviços de multimédia também fornecem um serviço para entrega de chaves AES e o DRM (PlayReady, Widevine e FairPlay) licenças para os clientes autorizados. Para obter mais informações, consulte [encriptação dinâmica](content-protection-overview.md).

## <a name="dynamic-manifest"></a>Manifesto dinâmico

Filtragem dinâmica é utilizado para controlar o número de faixas, formatos, velocidades de transmissão e janelas de tempo de apresentação que são enviadas para os jogadores. Para obter mais informações, consulte [filtros e dos manifestos dinâmicos](filters-dynamic-manifest-overview.md).

## <a name="live-event-types"></a>Tipos de evento em direto

Os [Eventos em Direto](https://docs.microsoft.com/rest/api/media/liveevents) são responsáveis pela ingestão e o processamento dos feeds de vídeos em direto. Um evento em direto pode ser um dos dois tipos: codificação de pass-through e em direto. Para obter detalhes sobre a transmissão em fluxo em direto no Serviços de multimédia v3, consulte [eventos ao vivo e saídas de Live](live-events-outputs-concept.md).

### <a name="pass-through"></a>Pass-through

![pass-through](./media/live-streaming/pass-through.svg)

Ao utilizar o pass-through **evento em direto**, contar com o codificador em direto de locais para gerar um fluxo de vídeo de velocidade de transmissão múltiplas e enviar como a contribuição feed para o evento em direto (usando o protocolo de entrada RTMP ou MP4 fragmentado). O evento em direto, em seguida, continua até os fluxos de vídeo de entrada para o empacotador dinâmico (transmissão em fluxo o ponto de extremidade) sem qualquer transcodificação adicional. Tal um pass-through evento em direto é otimizado para eventos em direto de longa execução ou transmissão em direto lineares 24 x 365. 

### <a name="live-encoding"></a>Live Encoding  

![codificação em direto](./media/live-streaming/live-encoding.svg)

Ao utilizar a cloud encoding com Media Services, poderia configurar a seu codificador em direto de locais para enviar uma velocidade de transmissão única vídeo como a contribuição feed (até 32Mbps agregado) para o evento em direto (usando o protocolo de entrada RTMP ou MP4 fragmentado). Transcodifica o evento em direto a velocidade de transmissão única entrada transmitir para o [vários fluxos de vídeo de velocidade de transmissão](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) em resoluções diferentes para melhorar a entrega e disponibiliza-a disponibilização em dispositivos de reprodução através de protocolos padrão do setor como MPEG-DASH, Apple HTTP Live Streaming (HLS) e Microsoft Smooth Streaming. 

## <a name="live-streaming-workflow"></a>Fluxo de trabalho de transmissão em fluxo em direto

Para compreender o fluxo de trabalho de transmissão em fluxo em direto em serviços de multimédia v3, tem de primeira revisão e compreender os seguintes conceitos: 

- [Streaming Endpoints](streaming-endpoint-concept.md) (Pontos Finais de Transmissão em Fluxo)
- [Live Events and Live Outputs](live-events-outputs-concept.md) (Eventos em Direto e Saídas em Direto)
- [Streaming Locators](streaming-locators-concept.md) (Localizadores de Transmissão em Fluxo)

### <a name="general-steps"></a>Passos gerais

1. Na sua conta de Media Services, certifique-se de que o **ponto final de transmissão em fluxo** (origem) está em execução. 
2. Criar uma [evento em direto](live-events-outputs-concept.md). <br/>Ao criar o evento, pode especificar para início automático-lo. Em alternativa, pode iniciar o evento quando estiver pronto para começar a transmissão em fluxo.<br/> Quando o início automático está definido como true, o evento em direto será iniciado correto após a criação. A faturação tem início assim que o evento Live começa a ser executado. Tem de chamar explicitamente Stop do recurso de evento em direto para parar a faturação ainda mais. Para obter mais informações, consulte [Estados de evento em direto e de faturação](live-event-states-billing.md).
3. Obtenha os URLs de ingestão e configurar seu codificador no local para utilizar o URL para enviar a contribuição do feed.<br/>Ver [recomendado codificadores em direto](recommended-on-premises-live-encoders.md).
4. Obter o URL de pré-visualização e utilizá-lo para verificar que a entrada do codificador, na verdade, está a ser recebida.
5. Criar uma nova **Asset** objeto.
6. Criar uma **Live saída** e utilize o nome de recurso que criou.<br/>O **saída Live** irá arquivar a transmissão para o **Asset**.
7. Criar uma **localizador de transmissão em fluxo** com o [tipos internos da política de transmissão em fluxo](streaming-policy-concept.md)
8. Listar os caminhos na **localizador de transmissão em fluxo** para recuperar os URLs para utilizar (esses são determinísticos).
9. Obter o nome do anfitrião para o **ponto final de transmissão em fluxo** (origem) que deseja transmitir em fluxo do.
10. Combine o URL do passo 8 com o nome de anfitrião no passo 9 para obter o URL completo.
11. Se pretender parar a tornar sua **evento em direto** podem ser visualizados, terá de parar a transmissão em fluxo o evento e a eliminar a **localizador de transmissão em fluxo**.

## <a name="other-important-articles"></a>Outros artigos importantes

- [Recomendado codificadores em direto](recommended-on-premises-live-encoders.md)
- [Using a cloud DVR](live-event-cloud-dvr.md) (Utilizar um DVR na cloud)
- [Comparação de recursos de tipos de evento em direto](live-event-types-comparison.md)
- [States and billing](live-event-states-billing.md) (Estados e faturação)
- [Latência](live-event-latency.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Faça perguntas, comentários, obter atualizações

Veja a [Comunidade dos serviços de multimédia do Azure](media-services-community.md) artigo para ver formas diferentes, pode fazer perguntas, comentários e obter atualizações sobre os serviços de multimédia.

## <a name="next-steps"></a>Passos Seguintes

* [Tutorial de transmissão em fluxo em direto](stream-live-tutorial-with-api.md)
* [Orientações de migração para mover de serviços de multimédia v2 para v3](migrate-from-v2-to-v3.md)
