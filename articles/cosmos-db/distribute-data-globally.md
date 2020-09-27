---
title: Distribuir dados globalmente com a Azure Cosmos DB
description: Saiba mais sobre a geo-replicação à escala planetária, multi-mestre, failover e recuperação de dados usando bases de dados globais da Azure Cosmos DB, um serviço de base de dados multi-modelo distribuído globalmente.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: abd18132c7489153566042b0b7e3557662864f30
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2020
ms.locfileid: "91399470"
---
# <a name="distribute-your-data-globally-with-azure-cosmos-db"></a>Distribuir dados globalmente com o Azure Cosmos DB

As aplicações de hoje são necessárias para serem altamente responsivas e sempre online. Para alcançar baixa latência e elevada disponibilidade, é necessário implementar casos destas aplicações em centros de dados próximos dos seus utilizadores. Estas aplicações são normalmente implantadas em vários datacenters e são chamadas distribuídas globalmente. As aplicações distribuídas globalmente precisam de uma base de dados distribuída globalmente que possa replicar de forma transparente os dados em qualquer parte do mundo para permitir que as aplicações operem numa cópia dos dados próxima dos utilizadores. 

Azure Cosmos DB é um serviço de base de dados distribuído globalmente que é projetado para fornecer baixa latência, escalabilidade elástica de produção, semântica bem definida para consistência de dados, e alta disponibilidade. Em resumo, se a sua aplicação necessitar de tempo de resposta rápida garantido em qualquer parte do mundo, se for necessário estar sempre on-line, e precisar de uma escalabilidade ilimitada e elástica de produção e armazenamento, deve construir a sua aplicação no Azure Cosmos DB.

Pode configurar as suas bases de dados para serem distribuídas globalmente e disponíveis em qualquer uma das regiões de Azure. Para diminuir a latência, coloque os dados perto de onde os seus utilizadores estão. A escolha das regiões requeridas depende do alcance global da sua aplicação e da posição dos seus utilizadores. Cosmos DB replica transparentemente os dados para todas as regiões associadas à sua conta Cosmos. Fornece uma única imagem do sistema da sua base de dados E recipientes Azure Cosmos distribuídos globalmente e contentores que a sua aplicação pode ler e escrever localmente. 

Com a Azure Cosmos DB, pode adicionar ou remover as regiões associadas à sua conta a qualquer momento. A sua aplicação não precisa de ser pausada ou redistribuída para adicionar ou remover uma região. Continua a estar altamente disponível a toda a hora devido às capacidades multi-homing que o serviço fornece de forma nativa.

:::image type="content" source="./media/distribute-data-globally/deployment-topology.png" alt-text="Topologia de implantação altamente disponível" border="false":::

## <a name="key-benefits-of-global-distribution"></a>Principais benefícios da distribuição global

**Construa aplicativos ativos globais.** Com o seu novo protocolo de replicação multi-mestre, todas as regiões apoiam tanto os escritos como os leituras. A capacidade multi-master também permite:

- Escrita elástica ilimitada e escalabilidade de leitura. 
- 99,999% lê e escreve disponibilidade em todo o mundo.
- Leituras e escritos garantidos servidos em menos de 10 milissegundos no percentil 99.

Ao utilizar as APIs multi-homing da Azure Cosmos, a sua aplicação está ciente da região mais próxima e pode enviar pedidos para essa região. A região mais próxima é identificada sem alterações de configuração. À medida que adiciona e remove regiões de e para a sua conta Azure Cosmos, a sua aplicação não precisa de ser redistribuída ou pausada, continua a estar altamente disponível em todos os momentos.

**Construa aplicativos altamente responsivos.** A sua aplicação pode realizar leituras e escritos em tempo real perto de todas as regiões que escolheu para a sua base de dados. A Azure Cosmos DB trata internamente a replicação de dados entre regiões com garantias de nível de consistência do nível selecionado.

**Construa aplicativos altamente disponíveis.** A execução de uma base de dados em várias regiões em todo o mundo aumenta a disponibilidade de uma base de dados. Se uma região não estiver disponível, outras regiões manuseiam automaticamente os pedidos de candidatura. A Azure Cosmos DB oferece 99,999% de leitura e escrita de disponibilidade para bases de dados multi-regiões.

**Manter a continuidade do negócio durante as paragens regionais.** A Azure Cosmos DB suporta [falha automática](how-to-manage-database-account.md#automatic-failover) durante uma paragem regional. Durante uma paragem regional, a Azure Cosmos DB continua a manter a sua latência, disponibilidade, consistência e SLAs de produção. Para ajudar a garantir que toda a sua aplicação está altamente disponível, a Cosmos DB oferece um manual de falha da API para simular uma paragem regional. Ao utilizar esta API, pode realizar exercícios regulares de continuidade de negócios.

**Leitura em escala e produção global.** Você pode permitir que todas as regiões sejam escritas e escritas em toda a escala em todo o mundo. A produção que a sua aplicação configura numa base de dados Azure Cosmos ou num contentor é garantidamente entregue em todas as regiões associadas à sua conta Azure Cosmos. A produção prevista é garantida por [SLAs financeiramente apoiadas.](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)

**Escolha entre vários modelos de consistência bem definidos.** O protocolo de replicação DB Azure Cosmos oferece cinco modelos de consistência bem definidos, práticos e intuitivos. Cada modelo tem uma troca entre consistência e desempenho. Use estes modelos de consistência para construir aplicações distribuídas globalmente com facilidade.

## <a name="next-steps"></a><a id="Next Steps"></a>Passos seguintes

Leia mais sobre distribuição global nos seguintes artigos:

* [Distribuição global - em segundo plano](global-dist-under-the-hood.md)
* [Como configurar multi-mestre nas suas aplicações](how-to-multi-master.md)
* [Configurar clientes para multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Adicione ou remova regiões da sua conta DB Azure Cosmos](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Criar uma política de resolução de conflitos personalizada para contas API SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Modelos de consistência programável em Cosmos DB](consistency-levels.md)
* [Escolha o nível de consistência certo para a sua aplicação](consistency-levels-choosing.md)
* [Níveis de consistência em Azure Cosmos DB APIs](consistency-levels-across-apis.md)
* [Disponibilidade e compensações de desempenho para vários níveis de consistência](consistency-levels-tradeoffs.md)

