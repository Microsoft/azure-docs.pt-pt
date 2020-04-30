---
title: O que é a Cache do Azure para Redis?
description: Saiba mais sobre o Azure Cache para redis para permitir o cache-aside, o cache-caching, o cache da sessão do utilizador, a fila de trabalho e mensagens, e transações distribuídas.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 03/11/2020
ms.openlocfilehash: cd4e7c8e2693c25f3fc092fb53874a97cfd62434
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82113209"
---
# <a name="azure-cache-for-redis-description"></a>Descrição da Cache do Azure para Redis

O Azure Cache for Redis fornece uma loja de dados em memória baseada no software de código aberto [Redis](https://redis.io/). Quando usado como cache, redis melhora o desempenho e escalabilidade de sistemas que dependem fortemente de lojas de dados de backend. O desempenho é melhorado copiando dados frequentemente acedidos a armazenamento rápido localizado perto da aplicação. Com Azure Cache para Redis, este armazenamento rápido está localizado na memória em vez de ser carregado do disco por uma base de dados.

O Azure Cache para Redis pode ser usado como uma loja de estrutura de dados na memória, uma base de dados não relacional distribuída e um corretor de mensagens. O desempenho da aplicação é melhorado ao tirar partido do desempenho da latência baixa e do alto débito do motor de Redis.

Azure Cache para Redis dá acesso a uma cache Redis segura e dedicada. O Azure Cache for Redis é gerido pela Microsoft, hospedado dentro do Azure, e acessível a qualquer aplicação dentro ou fora do Azure. Além disso, o Azure Redis para cache utiliza a estratégia de replicação sem discos, potenciando a compatibilidade com a indústria dos cartões de pagamento.

## <a name="using-azure-cache-for-redis"></a>Usando cache azure para redis

Azure Cache for Redis melhora o desempenho da aplicação apoiando padrões comuns de arquitetura de aplicação. Alguns dos mais comuns incluem o seguinte:

| Padrão      | Descrição                                        |
| ------------ | -------------------------------------------------- |
| [Cache-aside](cache-web-app-cache-aside-leaderboard.md) | As bases de dados são muitas vezes demasiado grandes para serem carregadas diretamente numa cache. É comum utilizar o padrão de [cache-aside](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) para carregar dados na cache apenas se necessário. Quando o sistema faz alterações aos dados, o sistema também pode atualizar a cache, que é depois distribuída a outros clientes. Além disso, o sistema pode definir uma expiração de dados, ou usar uma política de despejo para desencadear atualizações de dados para a cache.|
| [Colocação em Cache de Conteúdos](cache-aspnet-output-cache-provider.md) | Muitas páginas web são geradas a partir de modelos que usam conteúdo estático, tais como cabeçalhos, rodapés, banners. Estes itens estáticos não devem mudar com frequência. A utilização de uma cache na memória proporciona um acesso rápido ao conteúdo estático em comparação com as reservas de dados de backend. Este padrão reduz o tempo de processamento e a carga do servidor, permitindo que os servidores web sejam mais responsivos. Pode permitir-lhe reduzir o número de servidores necessários para lidar com cargas. O Azure Cache for Redis fornece ao Redis Output Cache Provider para suportar este padrão com ASP.NET.|
| [Colocação em cache da sessão de utilizador](cache-aspnet-session-state-provider.md) | Este padrão é comumente usado com carrinhos de compras e outros dados do histórico do utilizador que uma aplicação web pode querer associar com cookies de utilizador. Armazenar demasiada informação num cookie pode ter um impacto negativo no desempenho, uma vez que o tamanho deste cresce à medida que é transmitido e validado com cada pedido. Uma solução típica utiliza o cookie como chave para consultar os dados numa base de dados. Usar uma cache em memória, como O Cache Azure para redis, para associar informações a um utilizador é muito mais rápido do que interagir com uma base de dados relacional completa. |
| Colocação em fila de tarefas e mensagens | As aplicações muitas vezes adicionam tarefas a uma fila quando as operações associadas ao pedido demoram tempo a executar. As operações de execução mais longas são em fila para serem processadas em sequência, muitas vezes por outro servidor.  Este método de diferimento do trabalho é denominado colocação em fila de tarefas. O Azure Cache para redis fornece uma fila distribuída para ativar este padrão na sua aplicação.|
| Transações distribuídas | As aplicações exigem, por vezes, uma série de comandos contra uma loja de dados de backend para executar como uma única operação atómica. Todos os comandos têm de ser realizados com êxito, caso contrário, devem ser todos revertidos para o estado inicial. O Azure Cache para redis suporta a execução de um lote de comandos como uma [única transação](https://redis.io/topics/transactions). |

## <a name="azure-cache-for-redis-offerings"></a>Azure Cache para ofertas redis

Azure Cache for Redis está disponível nos seguintes níveis:

| Escalão | Descrição |
|---|---|
Básico | Uma cache de nó único. Este nível suporta múltiplos tamanhos de memória (250 MB - 53 GB)e é ideal para trabalhos de desenvolvimento/teste e cargas de trabalho não críticas. O escalão Básico não tem nenhum contrato de nível de serviço (SLA) |
| Standard | Uma cache replicada numa configuração primária/secundária de dois nós gerida pelo Azure com um SLA de alta disponibilidade (99,9%) |
| Premium | O nível Premium é o nível pronto para a Enterprise. As Caches de escalão Premium suportam mais funcionalidades e têm um maior débito com menores latências. As Caches no escalão Premium são implementadas em hardware mais poderoso e fornecem um melhor desempenho comparativamente aos Escalões Básico ou Standard. Esta vantagem significa que a entrada para uma cache do mesmo tamanho será maior em Premium em comparação com o nível Standard. |

> [!TIP]
> Para obter mais informações sobre tamanho, entrada e largura de banda com caches premium, consulte [Azure Cache para Redis FAQ](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).
>

Pode escalar o seu cache para um nível mais alto depois de ter sido criado. A diminuição para um escalão inferior não é suportada. Para obter instruções de escala passo a passo, consulte Como escalar o [Cache Azure para Redis](cache-how-to-scale.md) e [como automatizar uma operação](cache-how-to-scale.md#how-to-automate-a-scaling-operation)de escala .

### <a name="feature-comparison"></a>Comparação de funcionalidades

A página [Azure Cache for Redis Pricing](https://azure.microsoft.com/pricing/details/cache/) fornece uma comparação detalhada de cada nível. A tabela seguinte ajuda a descrever algumas das funcionalidades suportadas pelo escalão:

| Descrição da Funcionalidade | Premium | Standard | Básico |
| ------------------- | :-----: | :------: | :---: |
| [Contrato de Nível de Serviço (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Persistência de dados de Redis](cache-how-to-premium-persistence.md) |✔|-|-|
| [Cluster de Redis](cache-how-to-premium-clustering.md) |✔|-|-|
| [Segurança através de regras da Firewall](cache-configure.md#firewall) |✔|✔|✔|
| Encriptação de dados em circulação |✔|✔|✔|
| [Segurança e isolamento otimizados com VNet](cache-how-to-premium-vnet.md) |✔|-|-|
| [Importação/Exportação](cache-how-to-import-export-data.md) |✔|-|-|
| [Atualizações programadas](cache-administration.md#schedule-updates) |✔|✔|✔|
| [Georreplicação](cache-how-to-geo-replication.md) |✔|-|-|
| [Reiniciar](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>Passos seguintes

* [ASP.NET Web App Quickstart](cache-web-app-howto.md) Crie uma aplicação web simples ASP.NET que utilize um Azure Cache para Redis.
* [.NET Quickstart](cache-dotnet-how-to-use-azure-redis-cache.md) Crie uma aplicação .NET que utilize um Azure Cache para Redis.
* [.NET Core Quickstart](cache-dotnet-core-quickstart.md) Crie uma aplicação .NET Core que utilize um Azure Cache para Redis.
* [Node.js Quickstart](cache-nodejs-get-started.md) Crie uma aplicação simples noNode.js que usa um Azure Cache para Redis.
* [Java Quickstart](cache-java-get-started.md) Crie uma simples aplicação Java que utilize um Azure Cache para Redis.
* [Python Quickstart](cache-python-get-started.md) Crie uma aplicação Python que utilize um Azure Cache para Redis.
