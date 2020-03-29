---
title: Distribua dados globalmente com a Azure Cosmos DB
description: Conheça a geo-replicação em escala planetária, multi-master, failover e recuperação de dados usando bases de dados globais da Azure Cosmos DB, um serviço de base de dados multimodelo distribuído globalmente.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 84cd201cd758293082a61a87528332c7d8c58811
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264310"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>Distribuição de dados globais com o Azure Cosmos DB – descrição geral

As aplicações de hoje são necessárias para serem altamente responsivas e sempre online. Para alcançar baixa latência e elevada disponibilidade, as ocorrências destas aplicações precisam de ser implementadas em datacenters próximos dos seus utilizadores. Estas aplicações são normalmente implantadas em vários datacenters e são chamadas de distribuídas globalmente. As aplicações distribuídas globalmente precisam de uma base de dados distribuída globalmente que possa replicar de forma transparente os dados em qualquer parte do mundo para permitir que as aplicações operem numa cópia dos dados próxima dos utilizadores. 

Azure Cosmos DB é um serviço de base de dados distribuído globalmente que é projetado para fornecer baixa latência, escalabilidade elástica de produção, semântica bem definida para consistência de dados, e alta disponibilidade. Em suma, se a sua aplicação necessitar de tempo de resposta rápido garantido em qualquer parte do mundo, se for necessário estar sempre online, e precisar de escalabilidade ilimitada e elástica de entrada e armazenamento, deverá construir a sua aplicação no Azure Cosmos DB.

Pode configurar as suas bases de dados para serem distribuídas globalmente e disponíveis em qualquer uma das regiões do Azure. Para baixar a latência, coloque os dados perto de onde os seus utilizadores se encontram. A escolha das regiões necessárias depende do alcance global da sua aplicação e do local onde os seus utilizadores estão localizados. Cosmos DB replica transparentemente os dados a todas as regiões associadas à sua conta Cosmos. Fornece uma única imagem do sistema da sua base de dados azure cosmos distribuída globalmente e contentores que a sua aplicação pode ler e escrever localmente. 

Com a Azure Cosmos DB, pode adicionar ou remover as regiões associadas à sua conta a qualquer momento. A sua aplicação não necessita de ser interrompida ou reimplantada para adicionar ou remover uma região. Continua a estar sempre altamente disponível devido às capacidades multi-homing que o serviço fornece de forma nativa.

![Topologia de implantação altamente disponível](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>Principais benefícios da distribuição global

**Construir aplicativos ativos ativos globais.** Com o seu novo protocolo de replicação multi-master, cada região apoia tanto escritos como leituras. A capacidade multi-master também permite:

- Escrever elástico ilimitado e ler escalabilidade. 
- 99,999% lê e escreve disponibilidade em todo o mundo.
- Leituras garantidas e escritos servidos em menos de 10 milissegundos no percentil 99.

Ao utilizar as APIs multi-homing do Azure Cosmos DB, a sua aplicação está ciente da região mais próxima e pode enviar pedidos para aquela região. A região mais próxima é identificada sem alterações de configuração. À medida que adiciona e remove regiões de e para a sua conta Azure Cosmos, a sua aplicação não precisa de ser reimplantada ou pausada, continua a estar sempre altamente disponível.

**Construir aplicativos altamente responsivos.** A sua aplicação pode realizar leituras e escritos em tempo real contra todas as regiões que escolheu para a sua base de dados. A Azure Cosmos DB lida internamente com a replicação de dados entre regiões com garantias de nível de consistência do nível que selecionou.

**Construa aplicações altamente disponíveis.** A gestão de uma base de dados em várias regiões do mundo aumenta a disponibilidade de uma base de dados. Se uma região não estiver disponível, outras regiões lidam automaticamente com pedidos de pedido. A Azure Cosmos DB oferece 99,999% de disponibilidade de leitura e escrita para bases de dados multi-regiões.

**Mantenha a continuidade do negócio durante as interrupções regionais.** A Azure Cosmos DB suporta [a falha automática](how-to-manage-database-account.md#automatic-failover) durante uma paragem regional. Durante uma paragem regional, a Azure Cosmos DB continua a manter a sua latência, disponibilidade, consistência e slas de entrada. Para ajudar a garantir que toda a sua aplicação está altamente disponível, a Cosmos DB oferece uma falha manual da API para simular uma paragem regional. Ao utilizar esta API, pode realizar exercícios regulares de continuidade de negócios.

**Escalar ler e escrever produção globalmente.** Você pode permitir que cada região seja escrita e elástico em todo o mundo. O resultado que a sua aplicação configura numa base de dados Azure Cosmos ou num contentor é garantido que será entregue em todas as regiões associadas à sua conta Azure Cosmos. A produção prevista é garantida por [SLAs apoiados financeiramente.](https://aka.ms/acdbsla)

**Escolha entre vários modelos de consistência bem definidos.** O protocolo de replicação do Azure Cosmos DB oferece cinco modelos de consistência bem definidos, práticos e intuitivos. Cada modelo tem uma compensação entre consistência e desempenho. Use estes modelos de consistência para construir aplicações distribuídas globalmente com facilidade.

## <a name="next-steps"></a><a id="Next Steps"></a>Passos seguintes

Leia mais sobre a distribuição global nos seguintes artigos:

* [Distribuição global - em segundo plano](global-dist-under-the-hood.md)
* [Como configurar o multi-master nas suas aplicações](how-to-multi-master.md)
* [Configure clientes para multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Adicione ou remova regiões da sua conta Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Criar uma política personalizada de resolução de conflitos para contas SQL API](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Modelos de consistência programável no Cosmos DB](consistency-levels.md)
* [Escolha o nível de consistência certo para a sua aplicação](consistency-levels-choosing.md)
* [Níveis de consistência em IA DB](consistency-levels-across-apis.md)
* [Trocas de disponibilidade e desempenho para vários níveis de consistência](consistency-levels-tradeoffs.md)

