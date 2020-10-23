---
title: Compare as funcionalidades do produto da Rede de Entrega de Conteúdos Azure (CDN)
description: Saiba mais sobre as funcionalidades que cada produto de Rede de Entrega de Conteúdos (CDN) do Azure suporta.
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: overview
ms.date: 11/15/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: c8d8b01e8c5f4ea3054e639ecc9dd8b14bbf048b
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148726"
---
# <a name="what-are-the-comparisons-between-azure-cdn-product-features"></a>Quais são as comparações entre as características do produto Azure CDN?

A Azure Content Delivery Network (CDN) inclui quatro produtos: 

* **Azure CDN Standard da Microsoft**
* **Azure CDN Standard da Akamai**
* **Azure CDN Standard de Verizon**
* **Azure CDN Premium da Verizon.** 

A tabela seguinte compara as funcionalidades disponíveis com cada produto.

| **Funções e otimizações de desempenho** | **Standard Microsoft** | **Standard Akamai** | **Verizon Standard** | **Premium Verizon** |
| --- | --- | --- | --- | --- |
| [Aceleração de site dinâmico](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  | Oferecido via [Azure Front Door Service](https://docs.microsoft.com/azure/frontdoor/front-door-overview) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Aceleração de site dinâmico - compressão de imagem adaptável](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Aceleração de Site Dinâmico - Obtenção Prévia de Objeto](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Otimização geral da entrega web](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;**, Selecione este tipo de otimização se o seu tamanho médio de ficheiro for inferior a 10 MB  | **&#x2713;** |  **&#x2713;** |
| [Otimização da transmissão em fluxo de vídeo](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | via Entrega Geral da Web | **&#x2713;**  | via Entrega Geral da Web |  via Entrega Geral da Web |
| [Otimização de ficheiros grandes](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | via Entrega Geral da Web | **&#x2713;**, Selecione este tipo de otimização se o seu tamanho médio de ficheiro for superior a 10 MB   | via Entrega Geral da Web |  via Entrega Geral da Web |
| Alterar tipo de otimização | |**&#x2713;** | | |
| Porto de Origem |Todas as portas TCP |[Portas de origem permitidas](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Todas as portas TCP |Todas as portas TCP |
| [Balanceamento de carga de servidor global (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Remoção rápida](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;**, Purgar tudo e a purga wildcard não são suportados pela Azure CDN da Akamai atualmente |**&#x2713;** |**&#x2713;** |
| [Pré-carregamento de recursos](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Definições de cache/cabeçalho (utilizando o [colocação de regras em cache](cdn-caching-rules.md))  |**&#x2713;** usando [o motor de regras standard](cdn-standard-rules-engine.md)  |**&#x2713;** |**&#x2713;** | |
| Motor de entrega de conteúdo personalizado e baseado em regras |**&#x2713;** usando [o motor de regras standard](cdn-standard-rules-engine.md)  | | |**&#x2713;** usando [o motor de regras](cdn-rules-engine.md) |
| Configurações cache/cabeçalho  |**&#x2713;** usando [o motor de regras standard](cdn-standard-rules-engine.md) | | |**&#x2713;** usando [o motor de regras Premium](cdn-rules-engine.md) |
| Redirecionamento/reescrita de URL |**&#x2713;** usando [o motor de regras standard](cdn-standard-rules-engine.md)  | | |**&#x2713;** usando [o motor de regras Premium](cdn-rules-engine.md) |
| Regras do dispositivo móvel  |**&#x2713;** usando [o motor de regras standard](cdn-standard-rules-engine.md) | | |**&#x2713;** usando [o motor de regras Premium](cdn-rules-engine.md) |
| [Caching de cordas de consulta](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Pilha dupla de IPv4/IPv6 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Suporte HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Segurança** | **Standard Microsoft** | **Standard Akamai** | **Verizon Standard** | **Premium Verizon** | 
| Suporta HTTPS com ponto final CDN | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTPS de domínio personalizado](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;**, requer CNAME direto para ativar |**&#x2713;** |**&#x2713;** |
| [Suporte de nomes de domínio personalizado](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Filtro geográfico](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Autenticação de token](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [Proteção DDOS](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Traga o seu próprio certificado](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
| Versões TLS suportadas | TLS 1.2, TLS 1.0/1.1 - [Configurável](https://docs.microsoft.com/rest/api/cdn/customdomains/enablecustomhttps#usermanagedhttpsparameters) | TLS 1.2 | TLS 1.2 | TLS 1.2 |
||||
| **Análises e relatórios** | **Standard Microsoft** | **Standard Akamai** | **Verizon Standard** | **Premium Verizon** | 
| [Registos de diagnóstico do Azure](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Relatórios centrais de Verizon](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Relatórios personalizados de Verizon](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Relatórios HTTP Avançados](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Estatísticas em tempo real](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Desempenho do nó edge](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Alertas em tempo real](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Facilidade de utilização** | **Standard Microsoft** | **Standard Akamai** | **Verizon Standard** | **Premium Verizon** | 
| Fácil integração com os serviços do Azure, como, por exemplo, o [Armazenamento](cdn-create-a-storage-account-with-cdn.md), as [Aplicações Web](cdn-add-to-web-app.md) e os [Serviços de Multimédia](../media-services/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Gestão através da [API REST](/rest/api/cdn/), do [.NET](cdn-app-dev-net.md), do [Node.js](cdn-app-dev-node.md) ou do [PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Tipos de MIME de compressão](https://docs.microsoft.com/azure/cdn/cdn-improve-performance)  |Apenas padrão |Configurável |Configurável  |Configurável  |
| Codificações de compressão  |gzip, brotli |gzip |gzip, esvaziar, bzip2, brotili  |gzip, esvaziar, bzip2, brotili  |

## <a name="migration"></a>Migração

Para obter informações sobre como migrar um perfil do **CDN do Azure Standard da Verizon** para o **CDN do Azure Premium da Verizon**, veja [Migrar um perfil do CDN do Azure Standard da Verizon para Premium da Verizon](cdn-migrate.md). 

> [!NOTE]
> Existe uma rota de upgrade da Standard Verizon para a Premium Verizon, não existe nenhum mecanismo de conversão entre outros produtos neste momento.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [a Azure CDN](cdn-overview.md).



