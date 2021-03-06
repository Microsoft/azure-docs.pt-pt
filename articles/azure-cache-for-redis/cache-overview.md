---
title: O que é a Cache do Azure para Redis?
description: Saiba mais sobre o Azure Cache for Redis para permitir cache-aside, cache de conteúdo, caching de sessão de utilizador, trabalho e mensagem em fila, e transações distribuídas.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 02/08/2021
ms.openlocfilehash: 49a697e6c3a6a6c931f2bb9c545647e2d6f1322d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056662"
---
# <a name="about-azure-cache-for-redis"></a>Acerca da Cache do Azure para Redis
A Azure Cache for Redis fornece uma loja de dados na memória com base no software [Redis.](https://redis.io/) O Redis melhora o desempenho e a escalabilidade de uma aplicação que utiliza fortemente as lojas de dados de backend. É capaz de processar grandes volumes de pedidos, mantendo dados frequentemente acedidos na memória do servidor que podem ser escritos e lidos rapidamente. A Redis traz uma solução crítica de baixa latência e armazenamento de dados de alto rendimento para aplicações modernas.

A Azure Cache para Redis oferece tanto a redis open-source (OSS Redis) como um produto comercial da Redis Labs (Redis Enterprise) como um serviço gerido. Fornece instâncias de servidor Redis seguras e dedicadas e compatibilidade total da API redis. O serviço é operado pela Microsoft, hospedado no Azure, e acessível a qualquer aplicação dentro ou fora do Azure.

A azure Cache para Redis pode ser usado como um cache de dados ou conteúdo distribuído, uma loja de sessão, um corretor de mensagens, e muito mais. Pode ser implantado como um autónomo ou juntamente com outros serviços de base de dados Azure, tais como Azure SQL ou Cosmos DB.

## <a name="key-scenarios"></a>Cenários-chave
Azure Cache para Redis melhora o desempenho da aplicação apoiando padrões comuns de arquitetura de aplicações. Alguns dos mais comuns incluem o seguinte:

| Padrão      | Description                                        |
| ------------ | -------------------------------------------------- |
| [Cache de Dados](cache-web-app-cache-aside-leaderboard.md) | As bases de dados são muitas vezes demasiado grandes para serem carregadas diretamente numa cache. É comum usar o padrão [de cache-aside](/azure/architecture/patterns/cache-aside) para carregar dados na cache apenas quando necessário. Quando o sistema faz alterações nos dados, o sistema também pode atualizar a cache, que é depois distribuída a outros clientes. Além disso, o sistema pode definir uma expiração de dados, ou usar uma política de despejo para desencadear atualizações de dados na cache.|
| [Cache de conteúdo](cache-aspnet-output-cache-provider.md) | Muitas páginas web são geradas a partir de modelos que usam conteúdo estático, tais como cabeçalhos, rodapés, banners. Estes itens estáticos não devem mudar com frequência. A utilização de uma cache na memória proporciona acesso rápido a conteúdos estáticos em comparação com as datas de backend. Este padrão reduz o tempo de processamento e a carga do servidor, permitindo que os servidores da Web sejam mais responsivos. Pode permitir-lhe reduzir o número de servidores necessários para lidar com cargas. A Azure Cache for Redis fornece o Fornecedor de Cache de Saída Redis para suportar este padrão com ASP.NET.|
| [Loja de sessão](cache-aspnet-session-state-provider.md) | Este padrão é comumente usado com carrinhos de compras e outros dados de histórico de utilizadores que uma aplicação web pode querer associar com cookies do utilizador. Armazenar demasiada informação num cookie pode ter um impacto negativo no desempenho, uma vez que o tamanho deste cresce à medida que é transmitido e validado com cada pedido. Uma solução típica utiliza o cookie como chave para consultar os dados numa base de dados. Usar uma cache na memória, como a Cache Azure para Redis, associar informações a um utilizador é muito mais rápido do que interagir com uma base de dados relacional completa. |
| Colocação em fila de tarefas e mensagens | As aplicações muitas vezes adicionam tarefas a uma fila quando as operações associadas ao pedido demoram tempo a ser executadas. As operações de execução mais longas são processadas em sequência, muitas vezes por outro servidor.  Este método de diferimento do trabalho é denominado colocação em fila de tarefas. A azure Cache para Redis fornece uma fila distribuída para ativar este padrão na sua aplicação.|
| Transações distribuídas | As aplicações às vezes requerem uma série de comandos contra uma loja de dados de backend para executar como uma única operação atómica. Todos os comandos têm de ser realizados com êxito, caso contrário, devem ser todos revertidos para o estado inicial. A Azure Cache for Redis suporta a execução de um lote de comandos como uma única [transação](https://redis.io/topics/transactions). |

## <a name="redis-versions"></a>Versões Redis

Azure Cache para Redis suporta a versão 4.x do OSS Redis e, como pré-visualização, 6.0. Tomámos a decisão de saltar o Redis 5.0 para te trazer a versão mais recente. Anteriormente, a Azure Cache para Redis apenas mantinha uma única versão Redis. Irá fornecer uma nova atualização de lançamento e pelo menos uma versão estável mais antiga. Pode [escolher qual a versão que](cache-how-to-version.md) funciona melhor para a sua aplicação.


## <a name="service-tiers"></a>Escalões de serviço
Azure Cache for Redis está disponível nos seguintes níveis:

| Escalão de serviço | Description |
|---|---|
| Básico | Um cache OSS Redis a funcionar num único VM. Este nível não tem acordo de nível de serviço (SLA) e é ideal para trabalhos de desenvolvimento/teste e cargas de trabalho não críticas. |
| Standard | Uma cache OSS Redis em funcionamento em dois VMs numa configuração replicada. |
| Premium | Caches OSS Redis de alto desempenho. Este nível oferece maior produção, menor latência, melhor disponibilidade e mais funcionalidades. Caches premium são implantados em VMs mais potentes em comparação com os de caches básicos ou standard. |
| Grandes Empresas | Caches de alto desempenho alimentados pelo software Redis Enterprise da Redis Labs. Este nível suporta módulos Redis, incluindo RediSearch, RedisBloom e RedisTimeSeries. Além disso, oferece uma disponibilidade ainda maior do que o nível Premium. |
| Flash da Empresa | Caches grandes e rentáveis alimentados pelo software Redis Enterprise da Redis Labs. Este nível estende o armazenamento de dados redis a memória não volátil, que é mais barata que a DRAM, num VM. Reduz o custo geral da memória por GB. |

### <a name="feature-comparison"></a>Comparação de funcionalidades
A [Cache Azure para o Preço redis](https://azure.microsoft.com/pricing/details/cache/) fornece uma comparação detalhada de cada nível. A tabela seguinte ajuda a descrever algumas das funcionalidades suportadas pelo escalão:

| Descrição da Funcionalidade | Básica | Standard | Premium | Grandes Empresas | Flash da Empresa |
| ------------------- | :-----: | :------: | :---: | :---: | :---: |
| [Contrato de Nível de Serviço (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |-|✔|✔|✔|✔|
| Encriptação de dados |✔|✔|✔|✔|✔|
| [Isolamento da rede](cache-how-to-premium-vnet.md) |✔|✔|✔|✔|✔|
| [Dimensionamento](cache-how-to-scale.md) |✔|✔|✔|✔|✔|
| [Aglomerado de OSS](cache-how-to-premium-clustering.md) |-|-|✔|✔|✔|
| [Persistência de dados](cache-how-to-premium-persistence.md) |-|-|✔|Pré-visualizar|Pré-visualizar|
| [Redundância entre zonas](cache-how-to-zone-redundancy.md) |-|-|Pré-visualizar|✔|✔|
| [Georreplicação](cache-how-to-geo-replication.md) |-|-|✔|Pré-visualizar|Pré-visualizar|
| [Módulos](https://redis.io/modules) |-|-|-|✔|✔|
| [Importação/Exportação](cache-how-to-import-export-data.md) |-|-|✔|✔|✔|
| [Atualizações agendadas](cache-administration.md#schedule-updates) |✔|✔|✔|-|-|

### <a name="choosing-the-right-tier"></a>Escolher o escalão certo
Deve considerar o seguinte ao escolher uma Cache Azure para o nível Redis:

* **Memória**: Os níveis Básico e Standard oferecem 250 MB – 53 GB; o nível Premium 6 GB - 1.2 TB; os níveis enterprise 12 GB - 14 TB.  Para criar uma cache de nível Premium superior a 120 GB, pode utilizar o cluster Redis OSS. Para mais informações, consulte [a Cache Azure para redis pricing](https://azure.microsoft.com/pricing/details/cache/). Para obter mais informações, consulte [Como configurar o agrupamento para uma Cache Premium Azure para Redis.](cache-how-to-premium-clustering.md)
* **Desempenho**: Caches nos níveis Premium e Enterprise são implantados em hardware que tem processadores mais rápidos, proporcionando um melhor desempenho em comparação com o nível Básico ou Standard. Caches de nível premium têm maior produção e latências mais baixas. Para mais informações, consulte [a Cache Azure para o desempenho do Redis.](cache-planning-faq.md#azure-cache-for-redis-performance)
* **Núcleo dedicado para o servidor Redis**: Todos os caches exceto C0 executam núcleos VM dedicados. Redis, por design, usa apenas um fio para o processamento de comando. A azure Cache para Redis utiliza núcleos adicionais para o processamento de E/O. Ter mais núcleos melhora o desempenho de produção, mesmo que não produza uma escala linear. Além disso, os tamanhos de VM maiores normalmente vêm com limites de largura de banda mais elevados do que os menores. Isso ajuda-o a evitar a saturação da rede, o que irá causar intervalos na sua aplicação.
* **Desempenho da rede**: Se tiver uma carga de trabalho que exija uma elevada produção, o nível Premium ou Enterprise oferece mais largura de banda em comparação com o Básico ou o Standard. Também dentro de cada nível, caches de tamanho maior têm mais largura de banda devido ao VM subjacente que hospeda a cache. Para mais informações, consulte [a Cache Azure para o desempenho do Redis.](cache-planning-faq.md#azure-cache-for-redis-performance)
* **Número máximo de ligações** ao cliente : O nível Premium oferece o número máximo de clientes que podem ligar-se ao Redis, com um maior número de ligações para caches de maior dimensão. O agrupamento não aumenta o número de ligações disponíveis para uma cache agrupada. Para mais informações, consulte [a Cache Azure para obter preços de Redis](https://azure.microsoft.com/pricing/details/cache/).
* **Alta disponibilidade**: Azure Cache for Redis fornece várias opções [de alta disponibilidade.](cache-high-availability.md) Garante que uma cache Standard, Premium ou Enterprise está disponível de acordo com o nosso [SLA.](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) O SLA cobre apenas a conectividade com os pontos finais da cache. O SLA não cobre a proteção contra a perda de dados. Recomendamos a utilização da funcionalidade de persistência de dados Redis nos níveis Premium e Enterprise para aumentar a resiliência contra a perda de dados.
* **Persistência de dados**: Os níveis Premium e Enterprise permitem-lhe persistir os dados de cache numa conta de Armazenamento Azure e num Disco Gerido, respectivamente. As questões subjacentes às infraestruturas podem resultar em potenciais perdas de dados. Recomendamos a utilização da funcionalidade de persistência de dados Redis nestes níveis para aumentar a resiliência contra a perda de dados. A Azure Cache para Redis oferece opções RDB e AOF (pré-visualização). A persistência de dados pode ser ativada através do portal Azure e do CLI. Para o nível Premium, consulte [Como configurar a persistência para uma Cache Premium Azure para Redis](cache-how-to-premium-persistence.md).
* **Isolamento em rede**: As implementações de Ligação Privada azul e rede virtual (VNET) proporcionam um maior isolamento de segurança e tráfego para o seu Cache Azure para Redis. O VNET permite-lhe restringir ainda mais o acesso através de políticas de controlo de acesso à rede. Para obter mais informações, consulte [Azure Cache for Redis com Azure Private Link](cache-private-link.md) e [Como configurar o suporte da Rede Virtual para uma Cache Premium Azure para Redis](cache-how-to-premium-vnet.md).
* **Extensibilidade**: Os níveis de empresa suportam [rediSearch,](https://docs.redislabs.com/latest/modules/redisearch/) [RedisBloom](https://docs.redislabs.com/latest/modules/redisbloom/) e [RedisTimeSeries](https://docs.redislabs.com/latest/modules/redistimeseries/). Estes módulos adicionam novos tipos de dados e funcionalidade ao Redis.

Pode escalar o seu cache do nível Básico até ao Premium depois de ter sido criado. A escala para um nível inferior não é suportada atualmente. Para obter instruções de escalonamento passo a passo, consulte [Como escalar a cache Azure para redis](cache-how-to-scale.md) e como [automatizar uma operação de escala](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="special-considerations-for-enterprise-tiers"></a>Considerações especiais para os níveis da Enterprise

Os níveis da Enterprise dependem da Redis Enterprise, uma variante comercial do Redis da Redis Labs. Os clientes obterão e pagarão uma licença para este software através de uma oferta do Azure Marketplace. A Azure Cache para Redis facilitará a aquisição da licença para que não tenha de o fazer separadamente. Para comprar no Mercado Azure, deve ter os seguintes pré-requisitos:
* A sua subscrição Azure tem um instrumento de pagamento válido. Os créditos Azure ou subscrições gratuitas da MSDN não são suportados.
* A sua organização permite [compras no Azure Marketplace.](../cost-management-billing/manage/ea-azure-marketplace.md#enabling-azure-marketplace-purchases)
* Se utilizar um Marketplace privado, deve conter a oferta da Redis Labs Enterprise.

> [!IMPORTANT]
> Azure Cache para a cache Redis Enterprise requer balanceadores de carga de rede padrão que são carregados separadamente dos próprios casos de cache. Consulte o preço do [Balanceador de Carga](https://azure.microsoft.com/pricing/details/load-balancer/) para obter mais detalhes. Se uma cache Enterprise estiver configurada para várias Zonas de Disponibilidade, a transferência de dados será faturada nas [taxas padrão](https://azure.microsoft.com/pricing/details/bandwidth/) de largura de banda de rede a partir de 1 de julho de 2021.
>
> Além disso, a persistência de dados adiciona Discos Geridos. A utilização destes recursos será gratuita durante a visualização pública da persistência de dados da Enterprise. Isto pode mudar quando a funcionalidade se tornar geralmente disponível.
>
>

## <a name="next-steps"></a>Passos seguintes
* [Criar uma cache Redis de código aberto](quickstart-create-redis.md)
* [Criar uma cache Redis Enterprise](quickstart-create-redis-enterprise.md)
* [Use a cache Azure para Redis numa aplicação web ASP.NET](cache-web-app-howto.md)
* [Utilize cache Azure para Redis em .NET Core](cache-dotnet-core-quickstart.md)
* [Utilize cache Azure para redis em .NET Framework](cache-dotnet-how-to-use-azure-redis-cache.md)
* [Use cache Azure para Redis em Node.js](cache-nodejs-get-started.md)
* [Use cache Azure para Redis em Java](cache-java-get-started.md)
* [Use cache Azure para redis em Python](cache-python-get-started.md)