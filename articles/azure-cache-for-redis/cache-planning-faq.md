---
title: Cache Azure para Redis planejando FAQs
description: Aprenda as respostas a perguntas comuns que o ajudam a planear a Azure Cache para Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 13ba529dd3067ae16167f0d9c14c8f72b982f52c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88010874"
---
# <a name="azure-cache-for-redis-planning-faqs"></a>Cache Azure para Redis planejando FAQs

Este artigo fornece respostas a perguntas comuns sobre como planear a Azure Cache para Redis.

## <a name="common-questions-and-answers"></a>Perguntas e respostas comuns
Esta secção abrange as seguintes PERGUNTAS Frequentes:

* [Azure Cache para o desempenho de Redis](#azure-cache-for-redis-performance)
* [Em que região devo localizar o meu cache?](#in-what-region-should-i-locate-my-cache)
* [Onde residem os meus dados em cache?](#where-do-my-cached-data-reside)
* [Como vou cobrar por Azure Cache para Redis?](#how-am-i-billed-for-azure-cache-for-redis)
* [Posso usar a Cache Azure para Redis com Azure Government Cloud, Azure China 21Vianet Cloud ou Microsoft Azure Germany?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany)

### <a name="azure-cache-for-redis-performance"></a>Azure Cache para o desempenho de Redis
A tabela seguinte mostra os valores máximos de largura de banda observados durante o ensaio de vários tamanhos de caches Standard e Premium utilizando `redis-benchmark.exe` de um IaaS VM contra a Cache Azure para o ponto final redis. Para a produção de TLS, o redis-benchmark é usado com atordoado para ligar à Cache Azure para o ponto final redis.

>[!NOTE] 
>Estes valores não estão garantidos e não há SLA para estes números, mas devem ser típicos. Deve carregar a sua própria aplicação para determinar o tamanho certo da cache para a sua aplicação.
>Estes números podem mudar à medida que publicamos resultados mais recentes periodicamente.
>

A partir desta tabela, podemos tirar as seguintes conclusões:

* A produção para os caches que têm o mesmo tamanho é maior no nível Premium em comparação com o nível Standard. Por exemplo, com uma Cache de 6 GB, a produção de P1 é de 180.000 pedidos por segundo (RPS) em comparação com 100.000 RPS para C3.
* Com o agrupamento Redis, a produção aumenta linearmente à medida que aumenta o número de fragmentos (nós) no cluster. Por exemplo, se criar um cluster P4 de 10 fragmentos, então a produção disponível é de 400.000 * 10 = 4 milhões de RPS.
* A produção para tamanhos-chave maiores é maior no nível Premium em comparação com o Standard Tier.

| Escalão de preço | Tamanho | Núcleos de CPU | Largura de banda disponível | Tamanho do valor de 1 KB | Tamanho do valor de 1 KB |
| --- | --- | --- | --- | --- | --- |
| **Tamanhos padrão de cache** | | |**Megabits por seg (Mb/s) / Megabytes por seg (MB/s)** |**Pedidos por segundo (RPS) Non-SSL** |**Pedidos por segundo (RPS) SSL** |
| CO | 250 MB | Partilhado | 100 / 12.5  |  15 000 |   7.500 |
| C1 |   1 GB | 1      | 500 / 62.5  |  38,000 |  20,720 |
| C2 | 2,5 GB | 2      | 500 / 62.5  |  41,000 |  37,000 |
| C3 |   6 GB | 4      | 1000 / 125  | 100.000 |  90.000 |
| C4 |  13 GB | 2      | 500 / 62.5  |  60 000 |  55,000 |
| C5 |  26 GB | 4      | 1,000 / 125 | 102,000 |  93,000 |
| C6 |  53 GB | 8      | 2,000 / 250 | 126,000 | 120,000 |
| **Tamanhos de cache premium** | |**Núcleos de CPU por fragmento** | **Megabits por seg (Mb/s) / Megabytes por seg (MB/s)** |**Pedidos por segundo (RPS) Não-SSL, por fragmento** |**Pedidos por segundo (RPS) SSL, por fragmento** |
| P1 |   6 GB |  2 | 1,500 / 187.5 | 180,000 | 172,000 |
| P2 |  13 GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P3 |  26 GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P4 |  53 GB |  8 | 6,000 / 750   | 400,000 | 373,000 |
| P5 | 120 GB | 20 | 6,000 / 750   | 400,000 | 373,000 |

Para obter instruções sobre a configuração ou o descarregamento das ferramentas Redis, tais `redis-benchmark.exe` como, ver [como posso executar os comandos Redis?](cache-development-faq.md#how-can-i-run-redis-commands)

### <a name="in-what-region-should-i-locate-my-cache"></a>Em que região devo localizar o meu cache?
Para melhor desempenho e menor latência, localize o seu Azure Cache para Redis na mesma região que a sua aplicação de cliente cache.

### <a name="where-do-my-cached-data-reside"></a>Onde residem os meus dados em cache?
A Azure Cache para Redis armazena os dados da sua aplicação na RAM dos VM ou VMs, dependendo do nível, que hospedam a sua cache. Os seus dados residem estritamente na região Azure que selecionou por padrão. Existem dois casos em que os seus dados podem deixar uma região:
* Quando ativar a persistência na cache, o Azure Cache para Redis irá fazer backup dos seus dados numa conta de Armazenamento Azure que possui. Se a conta de armazenamento que fornece estiver noutra região, uma cópia dos seus dados acabará por aí.
* Se configurar a geo-replicação e a sua cache secundária estiver numa região diferente, o que seria o caso normalmente, os seus dados serão replicados para essa região.

Terá de configurar explicitamente a Cache Azure para o Redis utilizar estas funcionalidades. Você também tem controlo total sobre a região que a conta de armazenamento ou cache secundária está localizado.

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Como vou cobrar por Azure Cache para Redis?
Azure Cache para o preço do Redis está [aqui.](https://azure.microsoft.com/pricing/details/cache/) A página de preços lista os preços como uma taxa horária e mensal. Os caches são faturados por minuto a partir do momento em que a cache é criada até ao momento em que uma cache é eliminada. Não há opção para parar ou fazer uma pausa na faturação de uma cache.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-21vianet-cloud-or-microsoft-azure-germany"></a>Posso usar a Cache Azure para Redis com Azure Government Cloud, Azure China 21Vianet Cloud ou Microsoft Azure Germany?
Sim, Azure Cache para Redis está disponível em Azure Government Cloud, Azure China 21Vianet Cloud e Microsoft Azure Germany. Os URLs para aceder e gerir a Cache Azure para Redis são diferentes nestas nuvens em comparação com a Nuvem Pública de Azure.

| Cloud   | Sufixo dns para Redis            |
|---------|---------------------------------|
| Público  | *.redis.cache.windows.net       |
| US Gov  | *.redis.cache.usgovcloudapi.net |
| Alemanha | *.redis.cache.cloudapi.de       |
| China   | *.redis.cache.chinacloudapi.cn  |

Para obter mais informações sobre considerações ao utilizar a Cache Azure para Redis com outras nuvens, consulte os seguintes links.

- [Bases de Dados do Governo de Azure - Azure Cache para Redis](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [Azure China 21Vianet Cloud - Cache Azure para Redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Alemanha](https://azure.microsoft.com/overview/clouds/germany/)

Para obter informações sobre a utilização do Azure Cache para Redis com PowerShell em Azure Government Cloud, Azure China 21Vianet Cloud e Microsoft Azure Germany, consulte [Como ligar-se a outras nuvens - Azure Cache para Redis PowerShell](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [outros Azure Cache para Redis FAQs](cache-faq.md).
