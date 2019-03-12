---
title: Gerir a CDN do Azure, colocação em cache da política nos serviços de multimédia do Azure | Documentos da Microsoft
description: Saiba como gerir a CDN do Azure, colocação em cache da política nos serviços de multimédia do Azure.
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
ms.openlocfilehash: a747e5064ff0ef99fb43c545c29c2a34535445d0
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57540161"
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Gerir a CDN do Azure, colocação em cache da política nos serviços de multimédia do Azure
Serviços de multimédia do Azure fornece baseada em HTTP transmissão em fluxo adaptável e transferência progressiva. HTTP com base em transmissão em fluxo é altamente dimensionável, com benefícios de colocação em cache em proxy e as camadas CDN, bem como a colocação em cache do lado do cliente. Pontos finais de transmissão em fluxo fornece capacidades de transmissão em fluxo gerais e também a configuração para cabeçalhos de cache HTTP. Pontos finais de transmissão em fluxo define Cache-Control de HTTP: duração máxima e Expires cabeçalhos. Pode obter mais informações para cabeçalhos de cache HTTP partir [W3.org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>Cabeçalhos de colocação em cache predefinida
Por predefinição, pontos finais de transmissão em fluxo aplicam cabeçalhos de cache de 3 dias para dados de transmissão em fluxo a pedido (suporte de dados real fragmentos/segmentos) e manifest(playlist). Para transmissão em fluxo em direto, pontos finais de transmissão em fluxo aplicam-se os cabeçalhos de cache de 3 dias de dados (suporte de dados real fragmentos/segmentos) e cabeçalho para pedidos de manifest(playlist) de cache de 2 segundos. Quando o programa em direto fica a demanda (arquivo live), aplicam-se cabeçalhos de cache de transmissão em fluxo a pedido.

## <a name="azure-cdn-integration"></a>Integração da CDN do Azure
Serviços de multimédia do Azure fornecem [integrado CDN](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) para pontos finais de transmissão em fluxo. Cabeçalhos cache-control aplica-se da mesma forma como pontos finais de transmissão em fluxo para a CDN ativada pontos finais de transmissão em fluxo. O Azure CDN utiliza, ponto final de transmissão em fluxo configurar valores do cache para definir o tempo de vida dos objetos internamente em cache e também utiliza este valor para definir a entrega cabeçalhos de cache. Quando utilizar a CDN ativada pontos finais de transmissão em fluxo não é recomendado para definir os valores do cache pequenas. Definir valores pequeno diminuir o desempenho e reduzir a vantagem da CDN. Não é permitido para definir os cabeçalhos de cache inferior a 600 segundos para CDN ativada pontos finais de transmissão em fluxo.

> [!IMPORTANT]
>Serviços de multimédia do Azure tem integração completa com o CDN do Azure. Com um único clique, pode integrar todos os fornecedores de CDN do Azure disponíveis para o ponto de final de transmissão em fluxo incluindo produtos standard e premium. Para obter mais informações, consulte esta [anúncio](https://azure.microsoft.com/blog/standardstreamingendpoint/).
> 
> Os encargos de dados de transmissão em fluxo ponto final de CDN só obtém desativado se a CDN estiver ativada através de ponto final de APIs de transmissão em fluxo ou utilizando a secção de ponto final de transmissão em fluxo do portal do Azure. Integração manual ou criar diretamente um ponto final da CDN através de APIs da CDN ou secção portal não desative os encargos de dados.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Configurar cabeçalhos de cache com os serviços de multimédia do Azure
Pode utilizar o portal do Azure ou APIs de serviços de multimédia do Azure para configurar os valores de cabeçalho de cache.

1. Para configurar cabeçalhos de cache através do portal do Azure, consulte [como gerir pontos finais de transmissão em fluxo](../media-services/previous/media-services-portal-manage-streaming-endpoints.md) secção Configurar o ponto final de transmissão em fluxo.
2. REST API dos serviços de multimédia do Azure [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl).
3. SDK de .NET, dos serviços de multimédia do Azure [StreamingEndpointCacheControl propriedades](https://go.microsoft.com/fwlink/?LinkId=615302).

## <a name="cache-configuration-precedence-order"></a>Ordem de precedência de configuração de cache
1. Valor de cache configurado os serviços de multimédia do Azure substitui o valor predefinido.
2. Se não houver nenhuma configuração manual, se aplicam a valores predefinidos.
3. Ao cache de 2 segundos padrão cabeçalhos aplica-se ao vivo manifest(playlist) transmissão em fluxo, independentemente da configuração de multimédia do Azure ou o armazenamento do Azure e substituir este valor não está disponível.

