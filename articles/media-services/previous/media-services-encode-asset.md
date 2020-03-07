---
title: Visão geral dos media azure on-demand codificadores  Microsoft Docs
description: A Azure Media Services oferece múltiplas opções para a codificação de meios na nuvem. Este artigo dá uma visão geral dos codificadores de meios de comunicação azure on-demand.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: d5d5b8a7328ee82e94d494795617832cb0258667
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78392950"
---
# <a name="overview-of-azure-on-demand-media-encoders"></a>Visão geral dos codificadores de meios de comunicação azure on-demand 

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Veja a versão mais recente, [Serviços de Multimédia v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte também [a orientação de migração da v2 para a v3](../latest/migrate-from-v2-to-v3.md)

A Azure Media Services oferece múltiplas opções para a codificação de meios na nuvem.

Ao começar com os Serviços de Media, é importante entender a diferença entre códigos e formatos de ficheiros.
Os codecs são o software que implementa os algoritmos de compressão/descompressão, enquanto os formatos de ficheiros são recipientes que possuem o vídeo comprimido.

A Media Services fornece uma embalagem dinâmica que lhe permite entregar o seu conteúdo codificado bitrate adaptativo MP4 ou Smooth Streaming em formatos de streaming suportados por Media Services (MPEG DASH, HLS, Smooth Streaming) sem ter de voltar a embalar estes formatos de streaming.

Quando a conta dos Serviços de Multimédia é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à mesma, no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. A faturação para pontos finais de streaming ocorre sempre que o ponto final está em estado de **Corrida.**

A Media Services apoia os seguintes codificadores a pedido que são descritos neste artigo:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Fluxo de Trabalho Premium do Codificador de Multimédia](media-services-encode-asset.md#media-encoder-premium-workflow)

Este artigo dá uma breve visão geral dos codificadores de meios de comunicação sobre a procura e fornece links para artigos que dão informações mais detalhadas. O tópico também fornece comparação dos codificadores.

Por padrão, cada conta de Media Services pode ter uma tarefa de codificação ativa de cada vez. Pode reservar unidades de codificação que lhe permitam ter múltiplas tarefas de codificação em funcionamento simultaneamente, uma para cada unidade reservada de codificação que adquirir. Para obter informações, consulte unidades de [codificação de escala](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Media Encoder Standard

### <a name="how-to-use"></a>Como utilizar
[Como codificar com media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Formatos
[Formatos e codecs](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Predefinições
Media Encoder Standard é configurado utilizando uma das predefinições codificadoras descritas [aqui](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Metadados de entrada e saída
Os metadados de entrada dos codificadores são descritos [aqui](media-services-input-metadata-schema.md).

Os metadados de saída dos codificadores são descritos [aqui](media-services-output-metadata-schema.md).

### <a name="generate-thumbnails"></a>Gerar miniaturas
Para obter informações, consulte Como gerar miniaturas utilizando o [Media Encoder Standard](media-services-advanced-encoding-with-mes.md#thumbnails).

### <a name="trim-videos-clipping"></a>Vídeos de corte (recorte)
Para obter informações, consulte Como aparar vídeos utilizando o [Media Encoder Standard](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Criar sobreposições
Para obter informações, consulte Como criar sobreposições utilizando o [Media Encoder Standard](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Consulte também
[O blog media services](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Fluxo de Trabalho Premium de Codificador de Multimédia
### <a name="overview"></a>Descrição geral
[Introdução de Codificação Premium nos Serviços De Mídia Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>Como utilizar
Media Encoder Premium Workflow é configurado usando fluxos de trabalho complexos. Os ficheiros workflow podem ser criados e atualizados utilizando a ferramenta [Workflow Designer.](media-services-workflow-designer.md)

[Como utilizar a codificação premium nos serviços de mídia azure](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Problemas conhecidos
Se o seu vídeo de entrada não contiver legendas fechadas, o Ativo de saída ainda conterá um ficheiro TTML vazio.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Artigos relacionados
* [Execute tarefas avançadas de codificação personalizando predefinições padrão do Media Encoder](media-services-custom-mes-presets-with-dotnet.md)
* [Quotas e Limitações](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
