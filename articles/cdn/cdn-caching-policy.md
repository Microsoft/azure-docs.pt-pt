---
title: Gerir a política de caching da Azure CDN na Azure Media Services Microsoft Docs
description: Este artigo explica como gerir a política de caching da Azure CDN na Azure Media Services.
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
ms.topic: how-to
ms.date: 02/04/2017
ms.author: juliako
ms.openlocfilehash: 5807d38e22d8cecf40b5ad4262f9e4662b77ec4c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779140"
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Gerir a política de caching da Azure CDN nos Serviços Azure Media
A Azure Media Services fornece streaming adaptável baseado em HTTP e download progressivo. O streaming baseado em HTTP é altamente escalável com benefícios de caching em camadas proxy e CDN, bem como caching do lado do cliente. Os pontos finais de streaming fornecem capacidades gerais de streaming e também configuração para cabeçalhos de cache HTTP. Os pontos finais de streaming definem HTTP Cache-Control: cabeçalhos de idade máxima e expira. Pode obter mais informações para cabeçalhos http cache a partir de [W3.org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>Cabeçalhos de caching padrão
Por padrão, os pontos finais de streaming aplicam cabeçalhos de cache de 3 dias para dados de streaming a pedido (fragmentos/pedaços de mídia reais) e manifesto (lista de reprodução). Para o streaming ao vivo, os pontos finais de streaming aplicam cabeçalhos de cache de 3 dias para dados (fragmentos/pedaços de mídia reais) e 2 segundos de cabeçalho de cache para pedidos manifesto(playlist). Quando o programa ao vivo se volta para o live-demand (live archive), então os cabeçalhos de cache de streaming a pedido aplicam-se.

## <a name="azure-cdn-integration"></a>Integração do Azure CDN
A Azure Media Services fornece [CDN integrado](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) para pontos finais de streaming. Os cabeçalhos de controlo de cache aplicam-se da mesma forma que os pontos finais de streaming para os pontos finais de streaming ativados pela CDN. O Azure CDN utiliza valores de cache configurados de ponto final de streaming para definir o tempo de vida dos objetos em cache internos e também utiliza este valor para definir os cabeçalhos de cache de entrega. Ao utilizar os pontos finais de streaming ativados pela CDN, não é aconselhável definir pequenos valores de cache. A definição de pequenos valores diminui o desempenho e reduz o benefício da CDN. Não é permitido definir cabeçalhos de cache inferiores a 600 segundos para os pontos finais de streaming ativados pela CDN.

> [!IMPORTANT]
>A Azure Media Services tem integração completa com a Azure CDN. Com um único clique, pode integrar todos os fornecedores Azure CDN disponíveis no seu ponto final de streaming, incluindo produtos standard e premium. Para mais informações, consulte este [anúncio.](https://azure.microsoft.com/blog/standardstreamingendpoint/)
> 
> As cargas de dados do ponto final de streaming para a CDN só ficam desativadas se o CDN estiver ativado através de APIs de ponta de streaming ou utilizar a secção de ponto final de streaming do portal Azure. A integração manual ou a criação direta de um ponto final do CDN utilizando APIs cdn ou secção de portal não desativa as taxas de dados.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Configurar cabeçalhos de cache com Azure Media Services
Pode utilizar o portal Azure ou as APIs dos Serviços de Media Azure para configurar valores do cabeçalho de cache.

1. Para configurar os cabeçalhos de cache utilizando o portal Azure, consulte a secção Como Gerir os [pontos finais de streaming](../media-services/previous/media-services-portal-manage-streaming-endpoints.md) Configurando o Ponto final de streaming.
2. Azure Media Services REST API, [StreamingEndpoint](/rest/api/media/operations/streamingendpoint#StreamingEndpointCacheControl).
3. Azure Media Services .NET SDK, [streamingEndpointCacheControl Properties](/dotnet/api/microsoft.windowsazure.mediaservices.client.streamingendpointcachecontrol).

## <a name="cache-configuration-precedence-order"></a>Ordem de precedência de configuração de cache
1. O valor configurado da Azure Media Services sobrepõe-se ao valor predefinido.
2. Se não houver configuração manual, aplicam-se valores predefinidos.
3. Por predefinição, os cabeçalhos de cache de 2 segundos aplicam-se ao manifesto de streaming ao vivo (lista de reprodução), independentemente da configuração de Azure Media ou Azure Storage e não esteja disponível a sobreposição deste valor.