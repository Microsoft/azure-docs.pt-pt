---
title: O que é a Cache do Azure para Redis?
description: Saiba mais sobre o Azure Cache for Redis para permitir cache-aside, cache de conteúdo, caching de sessão de utilizador, trabalho e mensagem em fila, e transações distribuídas.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.date: 05/12/2020
ms.openlocfilehash: 75c6f8ec8410ff90b3da4fb6a50c9ef8ba7d1618
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86184624"
---
# <a name="azure-cache-for-redis"></a>Cache do Azure para Redis

A Azure Cache for Redis fornece uma loja de dados na memória com base no software de código aberto [Redis](https://redis.io/). Quando usado como cache, o Redis melhora o desempenho e a escalabilidade de sistemas que dependem fortemente de lojas de dados de backend. O desempenho é melhorado copiando dados frequentemente acedidos para armazenamento rápido localizado perto da aplicação. Com Azure Cache para Redis, este armazenamento rápido está localizado na memória em vez de ser carregado do disco por uma base de dados.

A azure Cache para Redis pode ser usado como uma cache de dados distribuída, uma loja de sessão e um corretor de mensagens. O desempenho da aplicação é melhorado ao tirar partido do desempenho da latência baixa e do alto débito do motor de Redis.

A azure Cache para Redis oferece acesso a uma cache redis segura e dedicada. É gerido pela Microsoft, hospedado no Azure, e acessível a qualquer aplicação dentro ou fora do Azure.

## <a name="using-azure-cache-for-redis"></a>Usando cache Azure para Redis

Azure Cache para Redis melhora o desempenho da aplicação apoiando padrões comuns de arquitetura de aplicações. Alguns dos mais comuns incluem o seguinte:

| Padrão      | Descrição                                        |
| ------------ | -------------------------------------------------- |
| [Cache-Aside](cache-web-app-cache-aside-leaderboard.md) | As bases de dados são muitas vezes demasiado grandes para serem carregadas diretamente numa cache. É comum usar o padrão [de cache-aside](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) para carregar dados na cache apenas quando necessário. Quando o sistema faz alterações nos dados, o sistema também pode atualizar a cache, que é depois distribuída a outros clientes. Além disso, o sistema pode definir uma expiração de dados, ou usar uma política de despejo para desencadear atualizações de dados na cache.|
| [Colocação em Cache de Conteúdos](cache-aspnet-output-cache-provider.md) | Muitas páginas web são geradas a partir de modelos que usam conteúdo estático, tais como cabeçalhos, rodapés, banners. Estes itens estáticos não devem mudar com frequência. A utilização de uma cache na memória proporciona acesso rápido a conteúdos estáticos em comparação com as datas de backend. Este padrão reduz o tempo de processamento e a carga do servidor, permitindo que os servidores da Web sejam mais responsivos. Pode permitir-lhe reduzir o número de servidores necessários para lidar com cargas. A Azure Cache for Redis fornece o Fornecedor de Cache de Saída Redis para suportar este padrão com ASP.NET.|
| [Colocação em cache da sessão de utilizador](cache-aspnet-session-state-provider.md) | Este padrão é comumente usado com carrinhos de compras e outros dados de histórico de utilizadores que uma aplicação web pode querer associar com cookies do utilizador. Armazenar demasiada informação num cookie pode ter um impacto negativo no desempenho, uma vez que o tamanho deste cresce à medida que é transmitido e validado com cada pedido. Uma solução típica utiliza o cookie como chave para consultar os dados numa base de dados. Usar uma cache na memória, como a Cache Azure para Redis, associar informações a um utilizador é muito mais rápido do que interagir com uma base de dados relacional completa. |
| Colocação em fila de tarefas e mensagens | As aplicações muitas vezes adicionam tarefas a uma fila quando as operações associadas ao pedido demoram tempo a ser executadas. As operações de execução mais longas são processadas em sequência, muitas vezes por outro servidor.  Este método de diferimento do trabalho é denominado colocação em fila de tarefas. A azure Cache para Redis fornece uma fila distribuída para ativar este padrão na sua aplicação.|
| Transações distribuídas | As aplicações às vezes requerem uma série de comandos contra uma loja de dados de backend para executar como uma única operação atómica. Todos os comandos têm de ser realizados com êxito, caso contrário, devem ser todos revertidos para o estado inicial. A Azure Cache for Redis suporta a execução de um lote de comandos como uma única [transação](https://redis.io/topics/transactions). |

## <a name="azure-cache-for-redis-offerings"></a>Cache Azure para ofertas de Redis

Azure Cache for Redis está disponível nos seguintes níveis:

| Escalão de serviço | Descrição |
|---|---|
Básico | Uma cache de nó único. Este nível suporta vários tamanhos de memória (250 MB - 53 GB)e é ideal para trabalhos de desenvolvimento/teste e cargas de trabalho não críticas. O escalão Básico não tem nenhum contrato de nível de serviço (SLA) |
| Standard | Uma cache replicada num nó de dois nós, configuração primária/réplica, gerida por Azure com um SLA de alta disponibilidade (99,9%) |
| Premium | O nível Premium é o nível pronto para a Enterprise. As Caches de escalão Premium suportam mais funcionalidades e têm um maior débito com menores latências. As Caches no escalão Premium são implementadas em hardware mais poderoso e fornecem um melhor desempenho comparativamente aos Escalões Básico ou Standard. Esta vantagem significa que a produção para uma cache do mesmo tamanho será maior em Premium em comparação com o nível Standard. |

> [!TIP]
> Para obter mais informações sobre tamanho, produção e largura de banda com caches premium, consulte [Azure Cache para Redis FAQ](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).
>

Pode escalar o seu cache até um nível mais alto depois de ter sido criado. A diminuição para um escalão inferior não é suportada. Para obter instruções de escalonamento passo a passo, consulte [Como escalar a cache Azure para redis](cache-how-to-scale.md) e como [automatizar uma operação de escala](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparison"></a>Comparação de funcionalidades

A [página Azure Cache para preços redis](https://azure.microsoft.com/pricing/details/cache/) fornece uma comparação detalhada de cada nível. A tabela seguinte ajuda a descrever algumas das funcionalidades suportadas pelo escalão:

| Descrição da Funcionalidade | Premium | Standard | Básico |
| ------------------- | :-----: | :------: | :---: |
| [Acordo de Nível de Serviço (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Persistência de dados de Redis](cache-how-to-premium-persistence.md) |✔|-|-|
| [Cluster de Redis](cache-how-to-premium-clustering.md) |✔|-|-|
| [Segurança através de regras da Firewall](cache-configure.md#firewall) |✔|✔|✔|
| Encriptação de dados em circulação |✔|✔|✔|
| [Segurança e isolamento otimizados com VNet](cache-how-to-premium-vnet.md) |✔|-|-|
| [Importação/Exportação](cache-how-to-import-export-data.md) |✔|-|-|
| [Atualizações agendadas](cache-administration.md#schedule-updates) |✔|✔|✔|
| [Georreplicação](cache-how-to-geo-replication.md) |✔|-|-|
| [Reiniciar](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>Passos seguintes

* [ASP.NET Web App Quickstart](cache-web-app-howto.md) Crie uma simples aplicação web ASP.NET que utilize um Cache Azure para Redis.
* [.NET Quickstart](cache-dotnet-how-to-use-azure-redis-cache.md) Crie uma aplicação .NET que utilize uma Cache Azure para Redis.
* [.NET Core Quickstart](cache-dotnet-core-quickstart.md) Crie uma aplicação .NET Core que utilize uma Cache Azure para Redis.
* [Node.js Quickstart](cache-nodejs-get-started.md) Crie uma aplicação de Node.js simples que utilize um Cache Azure para Redis.
* [Java Quickstart](cache-java-get-started.md) Crie uma aplicação Java simples que usa um Cache Azure para Redis.
* [Python Quickstart](cache-python-get-started.md) Crie uma aplicação Python que utilize um Cache Azure para Redis.
