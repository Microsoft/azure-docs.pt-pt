---
title: O que é a Cache de Redis do Azure? | Microsoft Docs
description: Saiba o que é a Cache de Redis do Azure e como são utilizadas frequentemente.
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: overview
ms.date: 03/26/2018
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 9d789572abf0545eb51b357da091e5a1d712eab2
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433812"
---
# <a name="what-is-azure-cache-for-redis"></a>O que é a Cache de Redis do Azure

A Cache de Redis do Azure baseia-se o software popular [Redis](https://redis.io/). Normalmente, é utilizada como uma cache para melhorar o desempenho e a escalabilidade dos sistemas que dependem bastante de arquivos de dados de back-end. O desempenho é melhorado ao copiar temporariamente os dados acedidos com mais frequência para o armazenamento rápido localizado próximo da aplicação. Com o [a Cache de Redis do Azure](https://redis.io/), este armazenamento rápido é localizada dentro da memória com a Cache de Redis do Azure, em vez de que está a ser carregado do disco por um banco de dados.

A Cache de Redis do Azure também pode ser utilizada como um arquivo de estrutura de dados na memória, distribuída da base de dados não relacionais e Mediador de mensagens. O desempenho da aplicação é melhorado ao tirar partido do desempenho da latência baixa e do alto débito do motor de Redis.

A Cache do Azure para Redis fornece acesso a uma Cache de Azure segura, dedicada para Redis, gerida pela Microsoft, acessível para qualquer aplicação dentro ou fora do Azure e alojada no Azure.

## <a name="why-use-azure-cache-for-redis"></a>Porquê utilizar a Cache do Azure para Redis

Há muitos padrões comuns em que a Cache de Redis do Azure é utilizada para suportar a arquitetura de aplicativos ou para melhorar o desempenho do aplicativo. Alguns dos mais comuns incluem o seguinte:

| Padrão      | Descrição                                        |
| ------------ | -------------------------------------------------- |
| [Cache-Aside](cache-web-app-cache-aside-leaderboard.md) | Uma vez que uma base de dados pode ser grande, o carregamento de uma base de dados completa numa cache não é uma abordagem recomendada. É comum utilizar o padrão [cache-aside](https://docs.microsoft.com/azure/architecture/patterns/cache-aside) para carregar os itens de dados para a cache apenas quando necessários. Quando o sistema realiza alterações aos dados de back-end, este também pode a qualquer momento atualizar a cache, que é distribuída com outros clientes. Além disso, o sistema pode definir uma validade para os itens de dados ou utilizar uma política de expulsão para fazer com que as atualizações de dados sejam recarregadas para a cache.|
| [Colocação em Cache de Conteúdos](cache-aspnet-output-cache-provider.md) | A maioria das páginas Web é gerada a partir de modelos com cabeçalhos, rodapés, barras de ferramentas, menus, etc. Não são alteradas frequentemente nem devem ser geradas dinamicamente. Utilizar uma cache de memória, como o Cache do Azure para Redis, vai dar acesso rápido a seus servidores web para este tipo de conteúdo estático em comparação com os arquivos de dados de back-end. Este padrão reduz o tempo de processamento e a carga do servidor que seria precisa para gerar conteúdos dinamicamente. Tal permite que os servidores Web sejam mais reativos e pode permitir reduzir o número de servidores necessários para processar as cargas. A Cache de Redis do Azure fornece o fornecedor para Redis Cache de saída para ajudar a suportar esse padrão com o ASP.NET.|
| [Colocação em cache da sessão de utilizador](cache-aspnet-session-state-provider.md) | Este padrão é normalmente utilizado com carrinhos de compras e outras informações do tipo de histórico do utilizador que uma aplicação Web possa querer associar aos cookies de utilizador. Armazenar demasiada informação num cookie pode ter um impacto negativo no desempenho, uma vez que o tamanho deste cresce à medida que é transmitido e validado com cada pedido. Uma solução típica passa por utilizar o cookie como uma chave para consultar dados numa base de dados de back-end. Utilizar uma cache de memória, como o Azure Cache de Redis, para associar informações um utilizador é muito mais rápida que a interação com um banco de dados relacional completo. |
| Colocação em fila de tarefas e mensagens | Quando as aplicações recebem pedidos, muitas vezes, as operações associadas ao pedido demoram mais tempo a executar. Uma prática comum é o diferimento de operações mais longas em execução ao adicionar as mesmas numa fila, sendo estas processadas mais tarde e possivelmente por outro servidor. Este método de diferimento do trabalho é denominado colocação em fila de tarefas. Existem muitos componentes de software concebidos para suportar as filas de tarefas. A Cache do Azure para o Redis é também atende a este propósito bem como uma fila distribuída.|
| Transações distribuídas | Um requisito comum das aplicações é a sua capacidade para executar uma série de comandos num arquivo de dados de back-end como uma única operação (atómica). Todos os comandos têm de ser realizados com êxito, caso contrário, devem ser todos revertidos para o estado inicial. A Cache de Redis do Azure suporta a execução de um lote de comandos como uma única operação sob a forma de [transações](https://redis.io/topics/transactions). |

## <a name="azure-cache-for-redis-offerings"></a>Cache do Azure para ofertas de Redis

A Cache de Redis do Azure está disponível nos seguintes escalões:

| Escalão | Descrição |
|---|---|
Básica | Uma cache de nó único. Este escalão suporta vários tamanhos de memória (250 MB a 53 GB). É um escalão ideal para desenvolvimento/teste e cargas de trabalho não críticas. O escalão Básico não tem nenhum contrato de nível de serviço (SLA) |
| Standard | Uma cache replicada numa configuração primária/secundária de dois nós gerida pela Microsoft, com um SLA de elevada disponibilidade (99,9%) |
| Premium | O escalão Premium é um escalão preparado para utilização empresarial. As Caches de escalão Premium suportam mais funcionalidades e têm um maior débito com menores latências. As Caches no escalão Premium são implementadas em hardware mais poderoso e fornecem um melhor desempenho comparativamente aos Escalões Básico ou Standard. Esta vantagem significa que o débito de uma cache do mesmo tamanho será superior no escalão Premium do que no Standard |

> [!TIP]
> Para obter mais informações sobre o tamanho, débito e largura de banda com premium caches, consulte [do Azure na Cache de Redis FAQ](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).
>

Pode dimensionar a sua cache para um escalão superior depois de esta ter sido criada. A diminuição para um escalão inferior não é suportada. Para obter instruções passo a passo do dimensionamento, veja [como dimensionar a cache do Azure para Redis](cache-how-to-scale.md) e [como automatizar uma operação de dimensionamento](cache-how-to-scale.md#how-to-automate-a-scaling-operation).

### <a name="feature-comparison"></a>Comparação de Funcionalidades

O [do Azure na Cache de Redis preços](https://azure.microsoft.com/pricing/details/cache/) página fornece uma comparação detalhada de cada escalão. A tabela seguinte ajuda a descrever algumas das funcionalidades suportadas pelo escalão:

| Descrição da Funcionalidade | Premium | Standard | Básica |
| ------------------- | :-----: | :------: | :---: |
| [Contrato de Nível de Serviço (SLA)](https://azure.microsoft.com/support/legal/sla/cache/v1_0/) |✔|✔|-|
| [Persistência de dados de Redis](cache-how-to-premium-persistence.md) |✔|-|-|
| [Cluster de Redis](cache-how-to-premium-clustering.md) |✔|-|-|
| [Segurança através de regras da Firewall](cache-configure.md#firewall) |✔|✔|✔|
| [Segurança e isolamento otimizados com VNet](cache-how-to-premium-vnet.md) |✔|-|-|
| [Importação/Exportação](cache-how-to-import-export-data.md) |✔|-|-|
| [Atualizações agendadas](cache-administration.md#schedule-updates) |✔|-|-|
| [Georreplicação](cache-how-to-geo-replication.md) |✔|-|-|
| [Reiniciar](cache-administration.md#reboot) |✔|✔|✔|

## <a name="next-steps"></a>Passos Seguintes

* [Início rápido para aplicações Web ASP.NET](cache-web-app-howto.md) criar uma aplicação de web ASP.NET simple que utiliza uma Cache do Azure para Redis.
* [Guia de introdução do .NET](cache-dotnet-how-to-use-azure-redis-cache.md) criar uma aplicação .NET que utiliza uma Cache do Azure para Redis.
* [Guia de introdução do .NET core](cache-dotnet-core-quickstart.md) criar uma aplicação .NET Core que utiliza uma Cache do Azure para Redis.
* [Guia de introdução do node. js](cache-nodejs-get-started.md) criar uma aplicação node. js simple que utiliza uma Cache do Azure para Redis.
* [Início rápido de Java](cache-java-get-started.md) criar uma aplicação Java simple que utiliza uma Cache do Azure para Redis.
* [Guia de introdução do Python](cache-python-get-started.md) criar uma aplicação de Python que utiliza uma Cache do Azure para Redis.
