---
title: Gerir a política de cache do Azure CDN nos Serviços de Media Azure [ Microsoft Docs
description: Este artigo explica como gerir a política de cache do Azure CDN na Azure Media Services.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74892588"
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Gerir a política de cache do Azure CDN nos Serviços de Mídia Azure
A Azure Media Services fornece streaming adaptativo baseado em HTTP e download progressivo. O streaming baseado em HTTP é altamente escalável com benefícios de cache em camadas proxy e CDN, bem como cache do lado do cliente. Os pontos finais de streaming proporcionam capacidades gerais de streaming e também configuração para cabeçalhos de cache HTTP. Os pontos finais de streaming estabelecem HTTP Cache-Control: cabeçalhos de idade máxima e expira. Pode obter mais informações para cabeçalhos http cache a partir de [W3.org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>Cabeçalhos de caching padrão
Por padrão, os pontos finais de streaming aplicam cabeçalhos de cache de 3 dias para dados de streaming a pedido (fragmentos/pedaços reais de mídia) e manifesto (lista de reprodução). Para o streaming ao vivo, os pontos finais de streaming aplicam cabeçalhos de cache de 3 dias para dados (fragmentos/pedaços reais de mídia) e 2 segundos de cabeçalho cache para pedidos manifestos (playlist). Quando o programa ao vivo se volta para a procura (arquivo ao vivo), em seguida, os cabeçalhos de cache de streaming a pedido se aplicam.

## <a name="azure-cdn-integration"></a>Integração Azure CDN
A Azure Media Services fornece [CDN integrado](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) para pontos de streaming. Os cabeçalhos de controlo de cache aplicam-se da mesma forma que os pontos finais de streaming para os pontos finais de streaming ativados pela CDN. O Azure CDN utiliza valores de cache configurados em ponto final para definir o tempo de vida dos objetos internos e também utiliza este valor para definir os cabeçalhos de cache de entrega. Ao utilizar pontos finais de streaming ativados por CDN, não é aconselhável definir pequenos valores de cache. A definição de pequenos valores diminui o desempenho e reduz o benefício da CDN. Não é permitido definir cabeçalhos de cache inferiores a 600 segundos para os pontos finais de streaming ativados pela CDN.

> [!IMPORTANT]
>A Azure Media Services tem total integração com a Azure CDN. Com um único clique, pode integrar todos os fornecedores de CDN Do Azure disponíveis no seu ponto final de streaming, incluindo produtos standard e premium. Para mais informações, consulte este [anúncio.](https://azure.microsoft.com/blog/standardstreamingendpoint/)
> 
> As cargas de dados do ponto final de streaming para a CDN só são desativadas se o CDN estiver ativado através de APIs de ponto final de streaming ou utilizando a secção final do portal Azure. A integração manual ou a criação direta de um ponto final do CDN utilizando APIs de CDN ou secção do portal não desativa os encargos de dados.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Configurar cabeçalhos de cache com serviços de mídia Azure
Pode utilizar o portal Azure ou a Azure Media Services APIs para configurar valores de cabeçalho em cache.

1. Para configurar os cabeçalhos de cache utilizando o portal Azure, consulte como gerir a secção de [pontos finais](../media-services/previous/media-services-portal-manage-streaming-endpoints.md) de streaming, configurando o ponto final de streaming.
2. Azure Media Services REST API, [StreamingEndpoint](/rest/api/media/operations/streamingendpoint#StreamingEndpointCacheControl).
3. Azure Media Services .NET SDK, [StreamingEndpointCacheControl Properties](https://go.microsoft.com/fwlink/?LinkId=615302).

## <a name="cache-configuration-precedence-order"></a>Ordem de precedência de configuração cache
1. O valor configurado da Azure Media Services sobrepõe-se ao valor predefinido.
2. Se não houver configuração manual, aplicam-se valores predefinidos.
3. Por padrão, os cabeçalhos de cache de 2 segundos aplicam-se ao manifesto de streaming ao vivo (lista de reprodução), independentemente da configuração Azure Media ou Azure Storage e não está disponível sobreposição deste valor.

