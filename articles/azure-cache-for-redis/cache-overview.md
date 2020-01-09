---
title: O que é a Cache do Azure para Redis?
description: Saiba o que é o cache do Azure para Redis e usos comuns, incluindo armazenamento em cache, cache de conteúdo, cache de sessão de usuário, serviço de enfileiramento de trabalhos e mensagens e transações distribuídas.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: overview
ms.custom: mvc
ms.date: 03/26/2018
ms.openlocfilehash: 5224be999ff8ff52c2f52568a504095dc5962398
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433424"
---
# <a name="azure-cache-for-redis-description"></a>Descrição do cache do Azure para Redis

O cache do Azure para Redis é baseado no popular software [Redis](https://redis.io/). Normalmente, é utilizada como uma cache para melhorar o desempenho e a escalabilidade dos sistemas que dependem bastante de arquivos de dados de back-end. O desempenho é melhorado ao copiar temporariamente os dados acedidos com mais frequência para o armazenamento rápido localizado próximo da aplicação. Com o [cache do Azure para Redis](https://redis.io/), esse armazenamento rápido está localizado na memória com o cache do Azure para Redis em vez de ser carregado do disco por um banco de dados.

O cache do Azure para Redis também pode ser usado como um armazenamento de estrutura de dados na memória, um banco de dado não relacional distribuído e um agente de mensagem. O desempenho da aplicação é melhorado ao tirar partido do desempenho da latência baixa e do alto débito do motor de Redis.

O cache do Azure para Redis fornece acesso a um cache Redis seguro e dedicado. O cache do Azure para Redis é gerenciado pela Microsoft, hospedado no Azure e acessível a qualquer aplicativo dentro ou fora do Azure.

## <a name="using-azure-cache-for-redis"></a>Usando o cache do Azure para Redis

Há muitos padrões comuns em que o cache do Azure para Redis é usado para dar suporte à arquitetura do aplicativo ou melhorar o desempenho do aplicativo. Alguns dos mais comuns incluem o seguinte:

| Padrão      | Descrição                                        |
| ------------ | -------------------------------------------------- |
| [Cache-Aside](cache-web-app-cache-aside-leaderboard.md) | Uma vez que uma base de dados pode ser grande, o carregamento de uma base de dados completa numa cache não é uma abordagem recomendada. É comum utilizar o padrão [cache-aside](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) para carregar os itens de dados para a cache apenas quando necessários. Quando o sistema realiza alterações aos dados de back-end, este também pode a qualquer momento atualizar a cache, que é distribuída com outros clientes. Além disso, o sistema pode definir uma validade para os itens de dados ou utilizar uma política de expulsão para fazer com que as atualizações de dados sejam recarregadas para a cache.|
| [Colocação em Cache de Conteúdos](cache-aspnet-output-cache-provider.md) | A maioria das páginas da Web é gerada a partir de modelos com cabeçalhos, rodapés, barras de ferramentas, menus, etc. Elas não são realmente alteradas com frequência e não devem ser geradas dinamicamente. Usar um cache na memória, como o cache do Azure para Redis, dará aos seus servidores Web acesso rápido a esse tipo de conteúdo estático em comparação com os armazenamentos de back-end. Este padrão reduz o tempo de processamento e a carga do servidor que seria precisa para gerar conteúdos dinamicamente. Tal permite que os servidores Web sejam mais reativos e pode permitir reduzir o número de servidores necessários para processar as cargas. O cache do Azure para Redis fornece o provedor de cache de saída Redis para ajudar a dar suporte a esse padrão com ASP.NET.|
| [Colocação em cache da sessão de utilizador](cache-aspnet-session-state-provider.md) | Este padrão é normalmente utilizado com carrinhos de compras e outras informações do tipo de histórico do utilizador que uma aplicação Web possa querer associar aos cookies de utilizador. Armazenar demasiada informação num cookie pode ter um impacto negativo no desempenho, uma vez que o tamanho deste cresce à medida que é transmitido e validado com cada pedido. Uma solução típica passa por utilizar o cookie como uma chave para consultar dados numa base de dados de back-end. O uso de um cache na memória, como o cache do Azure para Redis, para associar informações a um usuário é muito mais rápido do que interagir com um banco de dados relacional completo. |
| Colocação em fila de tarefas e mensagens | Quando as aplicações recebem pedidos, muitas vezes, as operações associadas ao pedido demoram mais tempo a executar. Uma prática comum é o diferimento de operações mais longas em execução ao adicionar as mesmas numa fila, sendo estas processadas mais tarde e possivelmente por outro servidor. Este método de diferimento do trabalho é denominado colocação em fila de tarefas. Existem muitos componentes de software concebidos para suportar as filas de tarefas. O cache do Azure para Redis também atende a essa finalidade como uma fila distribuída.|
| Transações distribuídas | Um requisito comum das aplicações é a sua capacidade para executar uma série de comandos num arquivo de dados de back-end como uma única operação (atómica). Todos os comandos têm de ser realizados com êxito, caso contrário, devem ser todos revertidos para o estado inicial. O cache do Azure para Redis dá suporte à execução de um lote de comandos como uma única operação na forma de [Transações](https://redis.io/topics/transactions). |

## <a name="azure-cache-for-redis-offerings"></a>Cache do Azure para ofertas do Redis

O cache do Azure para Redis está disponível nas seguintes camadas:

| Escalão | Descrição |
|---|---|
Basic | Uma cache de nó único. Este escalão suporta vários tamanhos de memória (250 MB a 53 GB). É um escalão ideal para desenvolvimento/teste e cargas de trabalho não críticas. O escalão Básico não tem nenhum contrato de nível de serviço (SLA) |
| Padrão | Uma cache replicada numa configuração primária/secundária de dois nós gerida pela Microsoft, com um SLA de elevada disponibilidade (99,9%) |
| Premium | A camada Premium é a camada pronta para a empresa. As Caches de escalão Premium suportam mais funcionalidades e têm um maior débito com menores latências. As Caches no escalão Premium são implementadas em hardware mais poderoso e fornecem um melhor desempenho comparativamente aos Escalões Básico ou Standard. Essa vantagem significa que a taxa de transferência para um cache do mesmo tamanho será maior em relação ao Premium em comparação com a camada Standard. |

> [!TIP]
> Para obter mais informações sobre tamanho, taxa de transferência e largura de banda com caches Premium, consulte [perguntas frequentes sobre o cache do Azure para Redis](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).
>

Pode dimensionar a sua cache para um escalão superior depois de esta ter sido criada. A diminuição para um escalão inferior não é suportada. Para obter instruções de dimensionamento passo a passo, consulte [como dimensionar o cache do Azure para Redis](cache-how-to-scale.md) e [como automatizar uma operação de dimensionamento](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparison"></a>Comparação de funcionalidades

A página de [preços do cache do Azure para Redis](https://azure.microsoft.com/pricing/details/cache/) fornece uma comparação detalhada de cada camada. A tabela seguinte ajuda a descrever algumas das funcionalidades suportadas pelo escalão:

| Descrição da Funcionalidade | Premium | Padrão | Basic |
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

## <a name="next-steps"></a>Passos seguintes

* Guia de [início rápido do aplicativo Web ASP.net](cache-web-app-howto.md) Crie um aplicativo Web ASP.NET simples que usa um cache do Azure para Redis.
* [Início rápido do .net](cache-dotnet-how-to-use-azure-redis-cache.md) Crie um aplicativo .NET que usa um cache do Azure para Redis.
* [Início rápido do .NET Core](cache-dotnet-core-quickstart.md) Crie um aplicativo .NET Core que usa um cache do Azure para Redis.
* [Início rápido do node. js](cache-nodejs-get-started.md) Crie um aplicativo node. js simples que usa um cache do Azure para Redis.
* [Início rápido do Java](cache-java-get-started.md) Crie um aplicativo Java simples que usa um cache do Azure para Redis.
* [Início rápido do Python](cache-python-get-started.md) Crie um aplicativo Python que usa um cache do Azure para Redis.
