---
title: Visão geral dos codificadores de mídia sob demanda do Azure | Microsoft Docs
description: Este tópico fornece uma visão geral dos codificadores de mídia sob demanda do Azure.
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
ms.openlocfilehash: 5c55b419b88a66d2e1acf1687478ab35d9f0a059
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019034"
---
# <a name="overview-of-azure-on-demand-media-encoders"></a>Visão geral dos codificadores de mídia sob demanda do Azure 

> [!NOTE]
> Não serão adicionadas novas funcionalidades aos Serviços de Multimédia v2. <br/>Veja a versão mais recente, [Serviços de Multimédia v3](https://docs.microsoft.com/azure/media-services/latest/). Além disso, consulte [diretrizes de migração de v2 para v3](../latest/migrate-from-v2-to-v3.md)

Os serviços de mídia do Azure fornecem várias opções para a codificação de mídia na nuvem.

Ao começar com os serviços de mídia, é importante entender a diferença entre codecs e formatos de arquivo.
Os codecs são o software que implementa os algoritmos de compactação/descompactação, enquanto os formatos de arquivo são contêineres que mantêm o vídeo compactado.

Os serviços de mídia fornecem um empacotamento dinâmico que permite que você forneça seu conteúdo codificado para MP4 de taxa de bits adaptável ou Smooth Streaming em formatos de streaming com suporte dos serviços de mídia (MPEG DASH, HLS, Smooth Streaming) sem precisar reempacotar nesses formatos de streaming.

Quando a conta dos Serviços de Multimédia é criada, é adicionado um ponto final de transmissão em fluxo **predefinido** à mesma, no estado **Parado**. Para começar a transmitir o seu conteúdo em fluxo e a tirar partido do empacotamento e encriptação dinâmicos, o ponto final de transmissão em fluxo a partir do qual quer transmitir conteúdo tem de estar no estado **Em execução**. A cobrança para pontos de extremidade de streaming ocorre sempre que o ponto de extremidade está em um estado de **execução** .

Os serviços de mídia oferecem suporte aos seguintes codificadores sob demanda descritos neste artigo:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Fluxo de Trabalho Premium do Codificador de Multimédia](media-services-encode-asset.md#media-encoder-premium-workflow)

Este artigo fornece uma breve visão geral dos codificadores de mídia sob demanda e fornece links para artigos que fornecem informações mais detalhadas. O tópico também fornece a comparação dos codificadores.

Por padrão, cada conta dos serviços de mídia pode ter uma tarefa de codificação ativa por vez. Você pode reservar unidades de codificação que permitem que você tenha várias tarefas de codificação em execução simultaneamente, uma para cada unidade reservada de codificação comprada. Para obter informações, consulte [dimensionando unidades de codificação](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Media Encoder Standard

### <a name="how-to-use"></a>Como utilizar
[Como codificar com Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Forma
[Formatos e codecs](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Predefinições
Media Encoder Standard é configurado usando uma das predefinições do codificador descritas [aqui](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Metadados de entrada e saída
Os metadados de entrada de codificadores são descritos [aqui](media-services-input-metadata-schema.md).

Os metadados de saída dos codificadores são descritos [aqui](media-services-output-metadata-schema.md).

### <a name="generate-thumbnails"></a>Gerar miniaturas
Para obter informações, consulte [como gerar miniaturas usando Media Encoder Standard](media-services-advanced-encoding-with-mes.md#thumbnails).

### <a name="trim-videos-clipping"></a>Cortar vídeos (recorte)
Para obter informações, consulte [como cortar vídeos usando Media Encoder Standard](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Criar sobreposições
Para obter informações, consulte [como criar sobreposições usando Media Encoder Standard](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Consulte também
[O blog dos serviços de mídia](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Fluxo de Trabalho Premium de Codificador de Multimédia
### <a name="overview"></a>Descrição geral
[Introdução à codificação Premium nos serviços de mídia do Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>Como utilizar
O Media Encoder Premium Workflow é configurado usando fluxos de trabalho complexos. Os arquivos de fluxo de trabalho podem ser criados e atualizados usando a ferramenta de [Designer de fluxo de trabalho](media-services-workflow-designer.md) .

[Como usar a codificação Premium nos serviços de mídia do Azure](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Problemas conhecidos
Se o vídeo de entrada não contiver legendas codificadas, o ativo de saída ainda conterá um arquivo TTML vazio.

## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Artigos relacionados
* [Executar tarefas de codificação avançadas Personalizando predefinições de Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
* [Cotas e limitações](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
