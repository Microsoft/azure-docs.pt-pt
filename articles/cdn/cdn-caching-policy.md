---
title: Gerenciar a política de cache da CDN do Azure nos serviços de mídia do Azure | Microsoft Docs
description: Este artigo explica como gerenciar a política de cache da CDN do Azure nos serviços de mídia do Azure.
services: media-services,cdn
documentationcenter: .NET
author: juliako
manager: erikre
editor: ''
ms.assetid: be33aecc-6dbe-43d7-a056-10ba911e0e94
ms.service: media-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/04/2017
ms.author: juliako
ms.openlocfilehash: dc0482fbcbb1c9d1618ec18e1f48b03f686a6573
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892588"
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Gerenciar a política de cache da CDN do Azure nos serviços de mídia do Azure
Os serviços de mídia do Azure fornecem streaming adaptável baseado em HTTP e download progressivo. O streaming baseado em HTTP é altamente escalonável com os benefícios do cache nas camadas proxy e CDN, bem como o armazenamento em cache do lado do cliente. Os pontos de extremidade de streaming fornecem recursos de streaming gerais e também configuração para cabeçalhos de cache HTTP. Os pontos de extremidade de streaming definem os cabeçalhos cache-Control: Max-age e Expires. Você pode obter mais informações para cabeçalhos de cache HTTP de [w3.org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>Cabeçalhos de cache padrão
Por padrão, os pontos de extremidade de streaming aplicam cabeçalhos de cache de 3 dias para dados de streaming sob demanda (fragmentos/partes de mídia reais) e manifesto (playlist). Para a transmissão ao vivo, os pontos de extremidade de streaming aplicam cabeçalhos de cache de 3 dias para os dados (fragmentos de mídia reais/partes) e o cabeçalho de cache de 2 segundos para solicitações de manifesto (playlist). Quando o programa ao vivo se transforma em sob demanda (arquivamento dinâmico), os cabeçalhos de cache de streaming sob demanda são aplicados.

## <a name="azure-cdn-integration"></a>Integração da CDN do Azure
Os serviços de mídia do Azure fornecem [CDN integrada](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) para pontos de extremidade de streaming. Os cabeçalhos cache-control se aplicam da mesma maneira que os pontos de extremidade de streaming para pontos de extremidade de streaming habilitados para CDN. A CDN do Azure usa valores de cache configurados de ponto de extremidade de streaming para definir o tempo de vida dos objetos internamente armazenados em cache e também usa esse valor para definir os cabeçalhos do cache de entrega. Ao usar pontos de extremidade de streaming habilitados para CDN, não é recomendável definir valores de cache pequenos. A definição de valores pequenos diminui o desempenho e reduz o benefício da CDN. Não é permitido definir cabeçalhos de cache com menos de 600 segundos para pontos de extremidade de streaming habilitados para CDN.

> [!IMPORTANT]
>Os serviços de mídia do Azure têm integração completa com a CDN do Azure. Com um único clique, você pode integrar todos os provedores de CDN do Azure disponíveis ao seu ponto de extremidade de streaming, incluindo produtos Standard e Premium. Para obter mais informações, consulte este [comunicado](https://azure.microsoft.com/blog/standardstreamingendpoint/).
> 
> Os encargos de dados do ponto de extremidade de streaming para a CDN só serão desabilitados se a CDN estiver habilitada por meio de APIs de ponto de extremidade de streaming ou portal do Azure usando a seção de A integração manual ou a criação direta de um ponto de extremidade CDN usando APIs ou a seção do portal da CDN não desabilita os encargos de dados.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Configurando cabeçalhos de cache com os serviços de mídia do Azure
Você pode usar portal do Azure ou as APIs dos serviços de mídia do Azure para configurar os valores de cabeçalho de cache.

1. Para configurar os cabeçalhos de cache usando portal do Azure, consulte a seção [como gerenciar pontos](../media-services/previous/media-services-portal-manage-streaming-endpoints.md) de extremidade de streaming Configurando o ponto de extremidades de streaming.
2. API REST de Serviços de Mídia do Azure, [StreamingEndpoint](/rest/api/media/operations/streamingendpoint#StreamingEndpointCacheControl).
3. SDK do .NET dos serviços de mídia do Azure, [Propriedades de StreamingEndpointCacheControl](https://go.microsoft.com/fwlink/?LinkId=615302).

## <a name="cache-configuration-precedence-order"></a>Ordem de precedência da configuração do cache
1. O valor de cache configurado dos serviços de mídia do Azure substitui o valor padrão.
2. Se não houver nenhuma configuração manual, os valores padrão serão aplicados.
3. Por padrão, os cabeçalhos de cache de 2 segundos se aplicam ao manifesto de transmissão ao vivo (playlist), independentemente da mídia do Azure ou da configuração do armazenamento do Azure, e a substituição desse valor não está disponível.

