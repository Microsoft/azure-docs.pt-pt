---
title: O que é a Cache do Azure para Redis?
description: Saiba mais sobre o Azure Cache for Redis para permitir cache-aside, cache de conteúdo, caching de sessão de utilizador, trabalho e mensagem em fila, e transações distribuídas.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 05/12/2020
ms.openlocfilehash: 8ec2a302226e3dc44701209a8cbb47b7814a5a2c
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91874854"
---
# <a name="azure-cache-for-redis"></a>Cache do Azure para Redis
A Azure Cache for Redis fornece uma loja de dados na memória com base no software de código aberto [Redis](https://redis.io/). O Redis melhora o desempenho e a escalabilidade de uma aplicação que utiliza fortemente as lojas de dados de backend. É capaz de processar grandes volumes de pedidos, mantendo dados frequentemente acedidos na memória do servidor que podem ser escritos e lidos rapidamente. A Redis traz uma solução crítica de baixa latência e armazenamento de dados de alto rendimento para aplicações modernas.

Azure Cache para Redis oferece redis como um serviço gerido. Fornece instâncias de servidor Redis seguras e dedicadas e compatibilidade total da API redis. O serviço é operado pela Microsoft, hospedado no Azure, e acessível a qualquer aplicação dentro ou fora do Azure.

A azure Cache para Redis pode ser usado como um cache de dados ou conteúdo distribuído, uma loja de sessão, um corretor de mensagens, e muito mais. Pode ser implantado como um autónomo ou juntamente com outro serviço de base de dados Azure, como Azure SQL ou Cosmos DB.

## <a name="key-scenarios"></a>Cenários-chave
Azure Cache para Redis melhora o desempenho da aplicação apoiando padrões comuns de arquitetura de aplicações. Alguns dos mais comuns incluem o seguinte:

| Padrão      | Descrição                                        |
| ------------ | -------------------------------------------------- |
| [Cache de Dados](cache-web-app-cache-aside-leaderboard.md) | As bases de dados são muitas vezes demasiado grandes para serem carregadas diretamente numa cache. É comum usar o padrão [de cache-aside](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) para carregar dados na cache apenas quando necessário. Quando o sistema faz alterações nos dados, o sistema também pode atualizar a cache, que é depois distribuída a outros clientes. Além disso, o sistema pode definir uma expiração de dados, ou usar uma política de despejo para desencadear atualizações de dados na cache.|
| [Cache de conteúdo](cache-aspnet-output-cache-provider.md) | Muitas páginas web são geradas a partir de modelos que usam conteúdo estático, tais como cabeçalhos, rodapés, banners. Estes itens estáticos não devem mudar com frequência. A utilização de uma cache na memória proporciona acesso rápido a conteúdos estáticos em comparação com as datas de backend. Este padrão reduz o tempo de processamento e a carga do servidor, permitindo que os servidores da Web sejam mais responsivos. Pode permitir-lhe reduzir o número de servidores necessários para lidar com cargas. A Azure Cache for Redis fornece o Fornecedor de Cache de Saída Redis para suportar este padrão com ASP.NET.|
| [Loja de sessão](cache-aspnet-session-state-provider.md) | Este padrão é comumente usado com carrinhos de compras e outros dados de histórico de utilizadores que uma aplicação web pode querer associar com cookies do utilizador. Armazenar demasiada informação num cookie pode ter um impacto negativo no desempenho, uma vez que o tamanho deste cresce à medida que é transmitido e validado com cada pedido. Uma solução típica utiliza o cookie como chave para consultar os dados numa base de dados. Usar uma cache na memória, como a Cache Azure para Redis, associar informações a um utilizador é muito mais rápido do que interagir com uma base de dados relacional completa. |
| Colocação em fila de tarefas e mensagens | As aplicações muitas vezes adicionam tarefas a uma fila quando as operações associadas ao pedido demoram tempo a ser executadas. As operações de execução mais longas são processadas em sequência, muitas vezes por outro servidor.  Este método de diferimento do trabalho é denominado colocação em fila de tarefas. A azure Cache para Redis fornece uma fila distribuída para ativar este padrão na sua aplicação.|
| Transações distribuídas | As aplicações às vezes requerem uma série de comandos contra uma loja de dados de backend para executar como uma única operação atómica. Todos os comandos têm de ser realizados com êxito, caso contrário, devem ser todos revertidos para o estado inicial. A Azure Cache for Redis suporta a execução de um lote de comandos como uma única [transação](https://redis.io/topics/transactions). |

## <a name="redis-versions"></a>Versões Redis

Azure Cache para Redis suporta a versão 4.x do Redis e, como pré-visualização, 6.0. Tomámos a decisão de saltar o Redis 5.0 para te trazer a versão mais recente. Anteriormente, a Azure Cache para Redis apenas mantinha uma única versão Redis. Irá fornecer uma nova atualização de lançamento e pelo menos uma versão estável mais antiga. Pode [escolher qual a versão que](cache-how-to-version.md) funciona melhor para a sua aplicação.

> [!NOTE]
> Redis 6.0 está em pré-visualização - [contacte-nos](mailto:azurecache@microsoft.com) se estiver interessado. Esta pré-visualização é fornecida sem um acordo de nível de serviço, e não é recomendado para cargas de trabalho de produção. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="service-tiers"></a>Escalões de serviço
Azure Cache for Redis está disponível nos seguintes níveis:

| Escalão de serviço | Descrição |
|---|---|
| Básico | Uma cache de nó único. Este nível suporta vários tamanhos de memória (250 MB - 53 GB)e é ideal para trabalhos de desenvolvimento/teste e cargas de trabalho não críticas. O nível básico não tem acordo de nível de serviço (SLA). |
| Standard | Uma cache replicada num nó de dois nós, configuração primária/réplica, gerida por Azure com um [SLA](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)de alta disponibilidade . |
| Premium | O nível Premium é o nível pronto para a Enterprise. As Caches de escalão Premium suportam mais funcionalidades e têm um maior débito com menores latências. As Caches no escalão Premium são implementadas em hardware mais poderoso e fornecem um melhor desempenho comparativamente aos Escalões Básico ou Standard. Esta vantagem significa que a produção para uma cache do mesmo tamanho será maior em Premium em comparação com o nível Standard. |

### <a name="feature-comparison"></a>Comparação de funcionalidades
A [Cache Azure para o Preço redis](https://azure.microsoft.com/pricing/details/cache/) fornece uma comparação detalhada de cada nível. A tabela seguinte ajuda a descrever algumas das funcionalidades suportadas pelo escalão:

| Descrição da Funcionalidade | Premium | Standard | Básico |
| ------------------- | :-----: | :------: | :---: |
| [Contrato de Nível de Serviço (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Persistência de dados de Redis](cache-how-to-premium-persistence.md) |✔|-|-|
| [Cluster de Redis](cache-how-to-premium-clustering.md) |✔|-|-|
| [Segurança através de regras da Firewall](cache-configure.md#firewall) |✔|✔|✔|
| Encriptação de dados em circulação |✔|✔|✔|
| [Segurança e isolamento otimizados com VNet](cache-how-to-premium-vnet.md) |✔|-|-|
| [Importação/Exportação](cache-how-to-import-export-data.md) |✔|-|-|
| [Atualizações agendadas](cache-administration.md#schedule-updates) |✔|✔|✔|
| [Georreplicação](cache-how-to-geo-replication.md) |✔|-|-|
| [Reiniciar](cache-administration.md#reboot) |✔|✔|✔|

### <a name="choosing-the-right-tier"></a>Escolher o escalão certo
Deve considerar o seguinte ao escolher um Cache Azure para o nível Redis.

* **Memória**: Os níveis Básico e Standard oferecem 250 MB – 53 GB. O nível Premium oferece até 1,2 TB (como cluster) ou 120 GB (não agrupados). Para mais informações, consulte [a Cache Azure para redis pricing](https://azure.microsoft.com/pricing/details/cache/).
* **Desempenho da rede**: Se tiver uma carga de trabalho que exija uma elevada produção, o nível Premium oferece mais largura de banda em comparação com Standard ou Basic. Também dentro de cada nível, caches de tamanho maior têm mais largura de banda devido ao VM subjacente que hospeda a cache. Para mais informações, consulte [a Cache Azure para o desempenho do Redis.](cache-planning-faq.md#azure-cache-for-redis-performance)
* **Produção**: O nível Premium oferece o máximo de produção disponível. Se o servidor de cache ou o cliente atingir os limites de largura de banda, poderá receber intervalos de tempo do lado do cliente. Para mais informações, consulte a tabela a seguir.
* **Alta disponibilidade**: Azure Cache for Redis fornece várias opções [de alta disponibilidade.](cache-high-availability.md) Garante que uma cache Standard/Premium está disponível de acordo com o nosso [SLA.](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) O SLA cobre apenas a conectividade com os pontos finais da cache. O SLA não cobre a proteção contra a perda de dados. Recomendamos a utilização da funcionalidade de persistência de dados Redis no nível Premium para aumentar a resiliência contra a perda de dados.
* **Retenção de dados Redis**: O nível Premium permite-lhe persistir os dados da cache numa conta de Armazenamento Azure. Numa cache Basic/Standard, todos os dados são armazenados apenas na memória. As questões subjacentes às infraestruturas podem resultar em potenciais perdas de dados. Recomendamos a utilização da funcionalidade de persistência de dados Redis no nível Premium para aumentar a resiliência contra a perda de dados. A Azure Cache para Redis oferece opções RDB e AOF (pré-visualização) na persistência de Redis. Para obter mais informações, consulte [Como configurar a persistência para uma Cache Premium Azure para Redis](cache-how-to-premium-persistence.md).
* **Redis cluster**: Para criar caches maiores que 120 GB, ou para obter dados de fragmentos em vários nós Redis, pode utilizar o clustering Redis, que está disponível no nível Premium. Cada nó consiste num par de cache primário/réplica para uma elevada disponibilidade. Para obter mais informações, consulte [Como configurar o agrupamento para uma Cache Premium Azure para Redis.](cache-how-to-premium-clustering.md)
* **Segurança reforçada e isolamento de rede**: A implantação da Rede Virtual Azure (VNET) proporciona uma maior segurança e isolamento para o seu Azure Cache para Redis, bem como sub-redes, políticas de controlo de acesso e outras funcionalidades para restringir ainda mais o acesso. Para obter mais informações, consulte [Como configurar o suporte da Rede Virtual para uma Cache Premium Azure para Redis.](cache-how-to-premium-vnet.md)
* **Configure Redis**: Nos níveis Standard e Premium, pode configurar redis para notificações keyspace.
* **Número máximo de ligações**ao cliente : O nível Premium oferece o número máximo de clientes que podem ligar-se ao Redis, com um maior número de ligações para caches de maior dimensão. O agrupamento não aumenta o número de ligações disponíveis para uma cache agrupada. Para mais informações, consulte [a Cache Azure para obter preços de Redis](https://azure.microsoft.com/pricing/details/cache/).
* **Núcleo dedicado para o servidor Redis**: No nível Premium, todos os tamanhos de cache têm um núcleo dedicado para Redis. Nos níveis Básico/Standard, o tamanho C1 e acima têm um núcleo dedicado para o servidor Redis.
* **Processamento de rosca única**: Redis, por design, utiliza apenas um fio para o processamento de comando. A azure Cache para Redis também utiliza núcleos adicionais para o processamento de E/O. Ter mais núcleos melhora o desempenho de produção, mesmo que não produza uma escala linear. Além disso, os tamanhos de VM maiores normalmente vêm com limites de largura de banda mais elevados do que os menores. Isso ajuda-o a evitar a saturação da rede, o que irá causar intervalos na sua aplicação.
* **Melhorias de desempenho**: Caches no nível Premium são implantados em hardware que tem processadores mais rápidos, proporcionando um melhor desempenho em comparação com o nível Básico ou Standard. Caches de nível premium têm maior produção e latências mais baixas. Para mais informações, consulte [Azure Cache para o desempenho de Redis](cache-planning-faq.md#azure-cache-for-redis-performance)

Pode escalar o seu cache até um nível mais alto depois de ter sido criado. A diminuição para um escalão inferior não é suportada. Para obter instruções de escalonamento passo a passo, consulte [Como escalar a cache Azure para redis](cache-how-to-scale.md) e como [automatizar uma operação de escala](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

## <a name="next-steps"></a>Passos seguintes
* [ASP.NET Web App Quickstart](cache-web-app-howto.md) Crie uma simples aplicação web ASP.NET que utilize um Cache Azure para Redis.
* [.NET Quickstart](cache-dotnet-how-to-use-azure-redis-cache.md) Crie uma aplicação .NET que utilize uma Cache Azure para Redis.
* [.NET Core Quickstart](cache-dotnet-core-quickstart.md) Crie uma aplicação .NET Core que utilize uma Cache Azure para Redis.
* [Node.js Quickstart](cache-nodejs-get-started.md) Crie uma aplicação de Node.js simples que utilize um Cache Azure para Redis.
* [Java Quickstart](cache-java-get-started.md) Crie uma aplicação Java simples que usa um Cache Azure para Redis.
* [Python Quickstart](cache-python-get-started.md) Crie uma aplicação Python que utilize um Cache Azure para Redis.
