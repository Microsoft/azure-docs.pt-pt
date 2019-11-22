---
title: Comparar as funcionalidades do produto Rede de Entrega de Conteúdos (CDN) do Azure | Microsoft Docs
description: Saiba mais sobre as funcionalidades que cada produto de Rede de Entrega de Conteúdos (CDN) do Azure suporta.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: mdgattuso
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 64b906c8a6b52d9c9655f3fe2b13d504d8eed4cb
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278078"
---
# <a name="compare-azure-cdn-product-features"></a>Comparar as funcionalidades do produto CDN do Azure

A Rede de Entrega de Conteúdos (CDN) do Azure inclui quatro produtos: **CDN do Azure Standard da Microsoft**, **CDN do Azure Standard da Akamai**, **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**. Para obter informações sobre como migrar um perfil do **CDN do Azure Standard da Verizon** para o **CDN do Azure Premium da Verizon**, veja [Migrar um perfil do CDN do Azure Standard da Verizon para Premium da Verizon](cdn-migrate.md). Observe que, embora haja um caminho de atualização da Verizon padrão para a Premium Verizon, não há nenhum mecanismo de conversão entre outros produtos neste momento.

A tabela seguinte compara as funcionalidades disponíveis com cada produto.

| **Funções e otimizações de desempenho** | **Standard da Microsoft** | **Standard da Akamai** | **Standard da Verizon** | **Premium da Verizon** |
| --- | --- | --- | --- | --- |
| [Aceleração de site dinâmico](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  | Oferecido por meio [do serviço de porta frontal do Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Aceleração de site dinâmico - compressão de imagem adaptável](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Aceleração de Site Dinâmico - Obtenção Prévia de Objeto](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Otimização de entrega na Web geral](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;** , Selecione esse tipo de otimização se o tamanho médio do arquivo for menor do que 10 MB  | **&#x2713;** |  **&#x2713;** |
| [Otimização da transmissão em fluxo de vídeo](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | por meio da entrega na Web geral | **&#x2713;**  | por meio da entrega na Web geral |  por meio da entrega na Web geral |
| [Otimização de ficheiros grandes](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | por meio da entrega na Web geral | **&#x2713;** , Selecione esse tipo de otimização se o tamanho médio do arquivo for maior que 10 MB   | por meio da entrega na Web geral |  por meio da entrega na Web geral |
| Alterar tipo de otimização | |**&#x2713;** | | |
| Porta de origem |Todas as portas TCP |[Portas de origem permitidas](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Todas as portas TCP |Todas as portas TCP |
| [Balanceamento de carga de servidor global (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Remoção rápida](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** , Limpar tudo e a limpeza de curinga não têm suporte da CDN do Azure do Akamai no momento |**&#x2713;** |**&#x2713;** |
| [Pré-carregamento de recursos](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Definições de cache/cabeçalho (utilizando o [colocação de regras em cache](cdn-caching-rules.md))  |**&#x2713;** usando o [mecanismo de regras padrão](cdn-standard-rules-engine.md)  |**&#x2713;** |**&#x2713;** | |
| Mecanismo de distribuição de conteúdo baseado em regras personalizável |**&#x2713;** usando o [mecanismo de regras padrão](cdn-standard-rules-engine.md)  | | |**&#x2713;** usando o [mecanismo de regras](cdn-rules-engine.md) |
| Configurações de cache/cabeçalho  |**&#x2713;** usando o [mecanismo de regras padrão](cdn-standard-rules-engine.md) | | |**&#x2713;** usando o [mecanismo de regras Premium](cdn-rules-engine.md) |
| Redirecionamento/reescrita de URL |**&#x2713;** usando o [mecanismo de regras padrão](cdn-standard-rules-engine.md)  | | |**&#x2713;** usando o [mecanismo de regras Premium](cdn-rules-engine.md) |
| Regras de dispositivo móvel  |**&#x2713;** usando o [mecanismo de regras padrão](cdn-standard-rules-engine.md) | | |**&#x2713;** usando o [mecanismo de regras Premium](cdn-rules-engine.md) |
| [Colocação em cache de cadeias de consulta](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Pilha dupla de IPv4/IPv6 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Suporte HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Segurança** | **Standard da Microsoft** | **Standard da Akamai** | **Standard da Verizon** | **Premium da Verizon** | 
| Suporta HTTPS com ponto final CDN | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTPS de domínio personalizado](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;** , Requer o CNAME direto para habilitar |**&#x2713;** |**&#x2713;** |
| [Suporte de nomes de domínio personalizado](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Filtro geográfico](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Autenticação de tokens](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [Proteção contra DDOS](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Traga o seu próprio certificado](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
||||
| **Análises e relatórios** | **Standard da Microsoft** | **Standard da Akamai** | **Standard da Verizon** | **Premium da Verizon** | 
| [Registos de diagnóstico do Azure](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Relatórios de núcleos da Verizon](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Relatórios personalizados da Verizon](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Relatórios HTTP avançados](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Estatísticas em tempo real](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Desempenho do nó edge](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Alertas em tempo real](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Facilidade de Utilização** | **Standard da Microsoft** | **Standard da Akamai** | **Standard da Verizon** | **Premium da Verizon** | 
| Fácil integração com os serviços do Azure, como, por exemplo, o [Armazenamento](cdn-create-a-storage-account-with-cdn.md), as [Aplicações Web](cdn-add-to-web-app.md) e os [Serviços de Multimédia](../media-services/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Gestão através da [API REST](/rest/api/cdn/), do [.NET](cdn-app-dev-net.md), do [Node.js](cdn-app-dev-node.md) ou do [PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Tipos MIME de compactação](https://docs.microsoft.com/azure/cdn/cdn-improve-performance)  |Somente padrão |Configurável |Configurável  |Configurável  |
| Codificações de compactação  |gzip, brotli |gzip |gzip, deflate, BZIP2, brotili  |gzip, deflate, BZIP2, brotili  |






