---
title: Distribuir dados globalmente com a Azure Cosmos DB
description: Saiba mais sobre a geo-replicação à escala planetária, escreve, falha e recuperação de dados usando bases de dados globais da Azure Cosmos DB, um serviço de base de dados multi-modelo distribuído globalmente.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/06/2021
ms.custom: seo-nov-2020
ms.openlocfilehash: 6bdb167990afeb5c1b6c68185f24a8f930287bed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102487904"
---
# <a name="distribute-your-data-globally-with-azure-cosmos-db"></a>Distribuir dados globalmente com o Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

As aplicações atuais têm de ter uma capacidade de resposta elevada e estar sempre online. Para obter baixa latência e elevada disponibilidade, as instâncias destas aplicações têm de ser implementadas em datacenters próximos dos seus utilizadores. Normalmente, estas aplicações são implementadas em vários datacenters e são distribuídas globalmente. As aplicações distribuídas globalmente precisam de uma base de dados distribuída globalmente que possa replicar de forma transparente os dados em qualquer parte do mundo para permitir que as aplicações operem numa cópia dos dados próxima dos utilizadores. 

O Azure Cosmos DB é um sistema de base de dados distribuído globalmente que lhe permite ler e escrever dados das réplicas locais da sua base de dados. O Azure Cosmos DB replica os dados de forma transparente para todas as regiões associadas à sua conta do Cosmos. Azure Cosmos DB é um serviço de base de dados distribuído globalmente que é projetado para fornecer baixa latência, escalabilidade elástica de produção, semântica bem definida para consistência de dados, e alta disponibilidade. Em resumo, se a sua aplicação precisa de um tempo de resposta rápida em qualquer parte do mundo, se for necessário estar sempre on-line, e precisar de uma escalabilidade ilimitada e elástica de produção e armazenamento, deve construir a sua aplicação no Azure Cosmos DB.

Pode configurar as suas bases de dados para serem distribuídas globalmente e disponíveis em qualquer uma das regiões de Azure. Para diminuir a latência, coloque os dados perto de onde os seus utilizadores estão. A escolha das regiões requeridas depende do alcance global da sua aplicação e da posição dos seus utilizadores. Cosmos DB replica transparentemente os dados para todas as regiões associadas à sua conta Cosmos. Fornece uma única imagem do sistema da sua base de dados E recipientes Azure Cosmos distribuídos globalmente e contentores que a sua aplicação pode ler e escrever localmente.

Com a Azure Cosmos DB, pode adicionar ou remover as regiões associadas à sua conta a qualquer momento. A sua aplicação não precisa de ser pausada ou redistribuída para adicionar ou remover uma região.

:::image type="content" source="./media/distribute-data-globally/deployment-topology.png" alt-text="Topologia de implantação altamente disponível" border="false":::

## <a name="key-benefits-of-global-distribution"></a>Principais benefícios da distribuição global

**Construa aplicativos ativos globais.** Com o seu novo protocolo de replicação multi-região, todas as regiões apoiam tanto os escritos como os leituras. A capacidade de escrita multi-região também permite:

- Escrita elástica ilimitada e escalabilidade de leitura.
- 99,999% lê e escreve disponibilidade em todo o mundo.
- Leituras e escritos garantidos servidos em menos de 10 milissegundos no percentil 99.

À medida que adiciona e remove regiões de e para a sua conta Azure Cosmos, a sua aplicação não precisa de ser redistribuída ou pausada, continua a estar altamente disponível em todos os momentos.

**Construa aplicativos altamente responsivos.** A sua aplicação pode realizar leituras e escritos em tempo real perto de todas as regiões que escolheu para a sua base de dados. A Azure Cosmos DB trata internamente a replicação de dados entre regiões com garantias de nível de consistência do nível selecionado.

**Construa aplicativos altamente disponíveis.** A execução de uma base de dados em várias regiões em todo o mundo aumenta a disponibilidade de uma base de dados. Se uma região não estiver disponível, outras regiões manuseiam automaticamente os pedidos de candidatura. A Azure Cosmos DB oferece 99,999% de leitura e escrita de disponibilidade para bases de dados multi-regiões.

**Manter a continuidade do negócio durante as paragens regionais.** A Azure Cosmos DB suporta [falha automática](how-to-manage-database-account.md#automatic-failover) durante uma paragem regional. Durante uma paragem regional, a Azure Cosmos DB continua a manter a sua latência, disponibilidade, consistência e SLAs de produção. Para ajudar a garantir que toda a sua aplicação está altamente disponível, a Cosmos DB oferece um manual de falha da API para simular uma paragem regional. Ao utilizar esta API, pode realizar exercícios regulares de continuidade de negócios.

**Leitura em escala e produção global.** Você pode permitir que todas as regiões sejam escritas e escritas em toda a escala em todo o mundo. A produção que a sua aplicação configura numa base de dados Azure Cosmos ou num contentor é a provisionada em todas as regiões associadas à sua conta Azure Cosmos. A produção prevista é garantida por [SLAs financeiramente apoiadas.](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)

**Escolha entre vários modelos de consistência bem definidos.** O protocolo de replicação DB Azure Cosmos oferece cinco modelos de consistência bem definidos, práticos e intuitivos. Cada modelo tem uma troca entre consistência e desempenho. Use estes modelos de consistência para construir aplicações distribuídas globalmente com facilidade.

## <a name="next-steps"></a><a id="Next Steps"></a>Passos seguintes

Leia mais sobre distribuição global nos seguintes artigos:

* [Distribuição global - em segundo plano](global-dist-under-the-hood.md)
* [Como configurar várias regiões escreve nas suas aplicações](how-to-multi-master.md)
* [Configurar clientes para multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Adicione ou remova regiões da sua conta DB Azure Cosmos](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Criar uma política de resolução de conflitos personalizada para contas API SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Modelos de consistência programável em Cosmos DB](consistency-levels.md)
* [Escolha o nível de consistência certo para a sua aplicação](./consistency-levels.md)
* [Níveis de consistência em Azure Cosmos DB APIs](./consistency-levels.md)
* [Disponibilidade e compensações de desempenho para vários níveis de consistência](./consistency-levels.md)