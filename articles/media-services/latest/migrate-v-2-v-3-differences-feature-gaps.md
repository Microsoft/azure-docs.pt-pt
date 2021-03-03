---
title: Lacunas de características entre a Azure Media Services V2 e v3
description: Este artigo descreve as lacunas de funcionalidade entre a Azure Media Services V2 a v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: f77a41a172bc868ad056d74d018b50f734286f0f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101702458"
---
# <a name="feature-gaps-between-azure-media-services-v2-and-v3"></a>Lacunas de características entre a Azure Media Services V2 e v3

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![etapas de migração 2](./media/migration-guide/steps-2.svg)

Esta parte da orientação de migração dá-lhe informações detalhadas sobre as diferenças entre as APIs V2 e V3.

## <a name="feature-gaps-between-v2-and-v3-apis"></a>Lacunas de características entre APIs V2 e V3

A API V3 tem as seguintes lacunas de características com a API V2. Algumas das funcionalidades avançadas da Norma Media Encoder em APIs V2 não estão atualmente disponíveis em V3:

- Inserir uma faixa de áudio silenciosa quando a entrada não tem áudio, uma vez que já não é necessário com o Azure Media Player.

- Inserir uma faixa de vídeo quando a entrada não tem vídeo.

- Eventos ao vivo com transcoding atualmente não suportam inserção de ardósia a meio do fluxo e inserção de marcador de anúncios através da chamada API.

- A Azure Media Premium Encoder deixará de ser suportado na V2. Se estiver a usá-lo para codificação HEVC de 8 bits, utilize o novo suporte HEVC no Standard Encoder. 
    - Adicionámos suporte para mapeamento de canais de áudio ao codificar Standard.  Consulte [o Áudio nos Serviços de Comunicação Encoding Swagger documentação](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json).
    - Se estiver a utilizar funcionalidades avançadas ou formatos de saída do produto licenciado por terceiros, como mXF ou ProRes, utilize a solução Azure Partner da Telestream, que será transacional no momento da reforma do V2. Alternativamente, pode utilizar a Imagine Communications, ou [Bitmovin](http://bitmovin.com).

- A propriedade "conjunto de disponibilidade" no Streaming Endpoint em V2 já não é suportada. Consulte o projeto de amostra e orientação para a entrega [de VOD de Alta Disponibilidade](./media-services-high-availability-encoding.md) na API V3.

- Nos Serviços de Comunicação Social V3, o FairPlay IV não pode ser especificado. Embora não tenha impacto nos clientes que utilizam os Media Services tanto para a embalagem como para a entrega de licenças, pode ser um problema quando se utiliza um sistema de DRM de terceiros para entregar as licenças FairPlay (modo híbrido).

- A encriptação de armazenamento do lado do cliente para proteção dos ativos em repouso foi removida na API V3 e substituída pela encriptação do serviço de armazenamento para dados em repouso. As APIs V3 continuam a trabalhar com os ativos encriptados de armazenamento existentes, mas não permitem a criação de novos.

## <a name="next-steps"></a>Passos seguintes

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]