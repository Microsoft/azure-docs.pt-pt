---
title: Componentes herdados dos serviços de mídia do Azure | Microsoft Docs
description: Este tópico aborda os componentes herdados dos serviços de mídia do Azure.
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
ms.date: 09/26/2019
ms.author: juliako
ms.openlocfilehash: e4ff157f58a68c68f8610c6c473f5d69897650ad
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338421"
---
# <a name="azure-media-services-legacy-components"></a>Componentes herdados dos serviços de mídia do Azure

Com o tempo, houve aprimoramentos e aprimoramentos contínuos nos componentes do serviço de mídia. Como resultado, alguns componentes herdados foram desativados. Você pode encontrar as instruções sobre como migrar seu aplicativo do componente herdado para um componente atual nos artigos a seguir.

## <a name="legacy-components-and-migration-guidance"></a>Componentes herdados e diretrizes de migração

Estamos anunciando a reprovação dos processadores de mídia do *Windows Azure Media Encoder* (WAME) e *do Azure Media Encoder* (ame). Esses processadores estão sendo desativados em 30 de novembro de 2019.

* [Migrar do codificador de mídia do Windows Azure para Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrar do Azure Media Encoder para Media Encoder Standard](migrate-azure-media-encoder.md)

Também estamos anunciando a reprovação da versão prévia do *Azure Media indexer v1* e *Azure Media indexer v2*. O processador de mídia [Azure Media indexer v1](media-services-index-content.md) será desativado em 1º de outubro de 2020. Os processadores de mídia de visualização do [[Azure Media indexer v2](media-services-process-content-with-indexer2.md) serão desativados em 1º de janeiro de 2019.  Os [serviços de mídia do Azure Video indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) substitui esses processadores de mídia herdados.

* [Migre do Azure Media indexer v1 e Azure Media indexer v2 para os serviços de mídia do Azure Video indexer](migrate-indexer-v1-v2.md).

## <a name="next-steps"></a>Passos seguintes

[Diretrizes de migração para mudar dos serviços de mídia v2 para v3](../latest/migrate-from-v2-to-v3.md)
