---
title: Visão geral dos codificadores de meios de comunicação a pedido da Azure | Microsoft Docs
description: A Azure Media Services oferece múltiplas opções para a codificação de meios na nuvem. Este artigo apresenta uma visão geral dos codificadores de meios de comunicação a pedido da Azure on-demand.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 0fcf3aaa830c092dfa225f55fef882e2263b16cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103013555"
---
# <a name="overview-of-azure-on-demand-media-encoders"></a>Visão geral dos codificadores de meios de comunicação a pedido da Azure on-demand

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Confira a versão mais recente, [Media Services v3](../latest/index.yml). Além disso, consulte [a orientação de migração de v2 para v3](../latest/migrate-v-2-v-3-migration-introduction.md)

A Azure Media Services oferece múltiplas opções para a codificação de meios na nuvem.

Ao começar com os Media Services, é importante entender a diferença entre codecs e formatos de ficheiros.
Os codecs são o software que implementa os algoritmos de compressão/descompressão, enquanto os formatos de ficheiros são contentores que seguram o vídeo comprimido.

Os Media Services fornecem embalagens dinâmicas que lhe permitem fornecer o seu conteúdo codificado de bitrate adaptável MP4 ou Smooth Streaming em formatos de streaming suportados por Media Services (MPEG DASH, HLS, Smooth Streaming) sem ter de voltar a embalar estes formatos de streaming.

Quando a sua conta De Serviços de Media é criada, um ponto final de streaming **padrão** é adicionado à sua conta no estado **Stop.** Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. A faturação para os pontos finais de streaming ocorre sempre que o ponto final está em um estado **de Funcionamento.**

Os Serviços de Comunicação Social apoiam o seguinte codificadores a pedido:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)

Este artigo apresenta uma breve visão geral dos codificadores de meios de comunicação a pedido e ligações a artigos com informações mais detalhadas.

Por predefinição, cada conta de Serviços de Mídia pode ter uma tarefa de codificação ativa de cada vez. Pode reservar unidades de codificação que lhe permitam ter múltiplas tarefas de codificação em execução em simultâneo, uma para cada unidade reservada de codificação que adquiriu. Para obter informações, consulte [as unidades de codificação de escala.](media-services-scale-media-processing-overview.md)

## <a name="media-encoder-standard"></a>Media Encoder Standard

### <a name="how-to-use"></a>Como utilizar
[Como codificar com o Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Formatos
[Formatos e codecs](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Predefinições
O Media Encoder Standard está configurado utilizando uma das predefinições codificantes descritas [aqui](./media-services-mes-presets-overview.md).

### <a name="input-and-output-metadata"></a>Metadados de entrada e saída
Os metadados de entrada dos codificadores são descritos [aqui.](media-services-input-metadata-schema.md)

Os metadados de saída dos codificadores são descritos [aqui.](media-services-output-metadata-schema.md)

### <a name="generate-thumbnails"></a>Gerar miniaturas
Para obter informações, consulte [como gerar miniaturas utilizando o Media Encoder Standard](media-services-advanced-encoding-with-mes.md).

### <a name="trim-videos-clipping"></a>Vídeos de corte (recorte)
Para obter informações, consulte [como aparar vídeos utilizando o Media Encoder Standard](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Criar sobreposições
Para obter informações, consulte [Como criar sobreposições utilizando o Media Encoder Standard](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Ver também
[O blog Media Services](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

### <a name="known-issues"></a>Problemas conhecidos
Se o seu vídeo de entrada não contiver legendas fechadas, o Ativo de saída ainda conterá um ficheiro TTML vazio.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Artigos relacionados
* [Execute tarefas avançadas de codificação personalizando predefinições padrão media Encoder](media-services-custom-mes-presets-with-dotnet.md)
* [Quotas e Limitações](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/