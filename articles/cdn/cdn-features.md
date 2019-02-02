---
title: Comparar as funcionalidades do produto Rede de Entrega de Conteúdos (CDN) do Azure | Microsoft Docs
description: Saiba mais sobre as funcionalidades que cada produto de Rede de Entrega de Conteúdos (CDN) do Azure suporta.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/01/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 3fc9ad8f183a5e565a17b57b2c9c9149b6753205
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/02/2019
ms.locfileid: "55663541"
---
# <a name="compare-azure-cdn-product-features"></a>Comparar as funcionalidades do produto CDN do Azure

Azure Content Delivery Network (CDN) inclui quatro produtos: **CDN Standard do Azure da Microsoft**, **CDN Standard do Azure da Akamai**, **CDN Standard do Azure da Verizon**, e **CDN do Azure Premium da Verizon**. 

Para obter informações sobre como migrar um perfil do **CDN do Azure Standard da Verizon** para o **CDN do Azure Premium da Verizon**, veja [Migrar um perfil do CDN do Azure Standard da Verizon para Premium da Verizon](cdn-migrate.md).

A tabela seguinte compara as funcionalidades disponíveis com cada produto.

| **Funções e otimizações de desempenho** | **Standard da Microsoft** | **Standard da Akamai** | **Standard da Verizon** | **Premium da Verizon** |
| --- | --- | --- | --- | --- |
| [Aceleração de site dinâmico](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  |  | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Aceleração de site dinâmico - compressão de imagem adaptável](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Aceleração de Site Dinâmico - Obtenção Prévia de Objeto](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  |  | **&#x2713;**  |  |  |
| [Otimização da transmissão em fluxo de vídeo](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | \* | **&#x2713;**  | \* |  \* |
| [Otimização de ficheiros grandes](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | \* | **&#x2713;**  | \* |  \* |
| [Balanceamento de carga de servidor global (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Remoção rápida](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Pré-carregamento de recursos](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Definições de cache/cabeçalho (utilizando o [colocação de regras em cache](cdn-caching-rules.md))  |  |**&#x2713;** |**&#x2713;** | |
| Definições de cache/cabeçalho (utilizando o [motor de regras](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| [Colocação em cache de cadeias de consulta](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Pilha dupla de IPv4/IPv6 | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Suporte HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Segurança** | **Standard da Microsoft** | **Standard da Akamai** | **Standard da Verizon** | **Premium da Verizon** | 
| Suporta HTTPS com ponto final CDN | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTPS de domínio personalizado](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Suporte de nomes de domínio personalizado](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Filtro geográfico](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Autenticação de tokens](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [Proteção contra DDOS](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Traga o seu próprio certificado](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) |**&#x2713;** |  |  |  |
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
| Gestão através da [API REST](https://msdn.microsoft.com/library/mt634456.aspx), do [.NET](cdn-app-dev-net.md), do [Node.js](cdn-app-dev-node.md) ou do [PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Motor de entrega de conteúdo personalizável com base em regras](cdn-rules-engine.md)  |  | | |**&#x2713;** |
| Redirecionar/reescrever URL (utilizando o [motor de regras](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Regras de dispositivos móveis (utilizando o [motor de regras](cdn-rules-engine.md))  |  | | |**&#x2713;** |

\*A Microsoft e a Verizon suportam o fornecimento de ficheiros grandes e multimédia diretamente através da otimização da general web delivery.



