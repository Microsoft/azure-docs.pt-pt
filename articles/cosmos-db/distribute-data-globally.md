---
title: Distribuir dados globalmente com o Azure Cosmos DB
description: Saiba mais sobre a replicação geográfica em escala de planeta, o multimestre, o failover e a recuperação de dados por meio de bancos de dados globais do Azure Cosmos DB, um serviço de banco de dados multimodelo distribuído globalmente.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 4f17fc7df5aef449c3b0f6dd8d02ae58df959070
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384886"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>Distribuição de dados globais com Azure Cosmos DB-visão geral

Os aplicativos de hoje precisam ser altamente responsivos e sempre online. Para obter baixa latência e alta disponibilidade, as instâncias desses aplicativos precisam ser implantadas em data centers próximos aos seus usuários. Esses aplicativos normalmente são implantados em vários datacenters e são chamados globalmente distribuídos. Os aplicativos distribuídos globalmente precisam de um banco de dados distribuído globalmente, que pode replicar de forma transparente as data em qualquer lugar do mundo para permitir que os aplicativos operem em uma cópia dos dados que estão próximos de seus usuários. 

Azure Cosmos DB é um serviço de banco de dados distribuído globalmente que é projetado para fornecer baixa latência, escalabilidade elástica de taxa de transferência, semântica bem definida para consistência de dados e alta disponibilidade. Resumindo, se seu aplicativo precisar de tempo de resposta rápido garantido em qualquer lugar do mundo, se for necessário estar sempre online e precisar de escalabilidade ilimitada e elástica de taxa de transferência e armazenamento, você deverá criar seu aplicativo em Azure Cosmos DB.

Você pode configurar seus bancos de dados para serem distribuídos globalmente e disponíveis em qualquer uma das regiões do Azure. Para diminuir a latência, coloque os dados próximos de onde os usuários estão. A escolha das regiões necessárias depende do alcance global do seu aplicativo e do local em que os usuários estão localizados. Cosmos DB replica os dados de forma transparente para todas as regiões associadas à sua conta do cosmos. Ele fornece uma única imagem de sistema de seu banco de dados Cosmos do Azure distribuído globalmente e contêineres que seu aplicativo pode ler e gravar localmente. 

Com Azure Cosmos DB, você pode adicionar ou remover as regiões associadas à sua conta a qualquer momento. A sua aplicação não precisa de ser colocada em pausa ou reimplementada para adicionar ou remover uma região. Ele continua sendo altamente disponível o tempo todo devido aos recursos de hospedagem múltipla que o serviço fornece nativamente.

![Topologia de implantação altamente disponível](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>Principais benefícios da distribuição global

**Crie aplicativos globais ativos-ativos.** Com seu próprio protocolo de replicação de vários mestres, cada região dá suporte a gravações e leituras. A funcionalidade de vários mestres também permite:

- Gravação e escalabilidade de leitura elástica ilimitada. 
- 99,999% de disponibilidade de leitura e gravação em todo o mundo.
- Leituras e gravações Garantidas servidas em menos de 10 milissegundos no 99 º percentil.

Usando o Azure Cosmos DB APIs de hospedagem múltipla, seu aplicativo está ciente da região mais próxima e pode enviar solicitações para essa região. A região mais próxima é identificada sem nenhuma alteração de configuração. À medida que você adiciona e remove regiões de e para sua conta do Azure Cosmos, seu aplicativo não precisa ser reimplantado ou pausado, ele continua altamente disponível em todos os momentos.

**Crie aplicativos altamente responsivos.** Seu aplicativo pode executar leituras e gravações quase em tempo real em todas as regiões escolhidas para o banco de dados. O Azure Cosmos DB manipula internamente a replicação de dados entre regiões com garantias de nível de consistência do nível que você selecionou.

**Crie aplicativos altamente disponíveis.** A execução de um banco de dados em várias regiões em todo o mundo aumenta a disponibilidade de um banco de dados. Se uma região não estiver disponível, outras regiões tratarão automaticamente as solicitações de aplicativos. O Azure Cosmos DB oferece 99,999% de disponibilidade de leitura e gravação para bancos de dados de várias regiões.

**Mantenha a continuidade dos negócios durante interrupções regionais.** O Azure Cosmos DB dá suporte ao [failover automático](how-to-manage-database-account.md#automatic-failover) durante uma interrupção regional. Durante uma interrupção regional, Azure Cosmos DB continua a manter seus SLAs de latência, disponibilidade, consistência e taxa de transferência. Para ajudar a garantir que todo o seu aplicativo esteja altamente disponível, o Cosmos DB oferece uma API de failover manual para simular uma interrupção regional. Usando essa API, você pode realizar análises regulares de continuidade de negócios.

**Dimensionar a taxa de transferência de leitura e gravação globalmente.** Você pode habilitar todas as regiões para serem graváveis e dimensionar de forma elástica as leituras e gravações em todo o mundo. A taxa de transferência que seu aplicativo configura em um banco de dados Cosmos do Azure ou um contêiner é garantida para ser entregue em todas as regiões associadas à sua conta do Azure Cosmos. A taxa de transferência provisionada é garantida por [SLAs com suporte financeiro](https://aka.ms/acdbsla).

**Escolha entre vários modelos de consistência bem definidos.** O protocolo de replicação Azure Cosmos DB oferece cinco modelos de consistência bem definidos, práticos e intuitivos. Cada modelo tem uma compensação entre consistência e desempenho. Use esses modelos de consistência para criar aplicativos distribuídos globalmente com facilidade.

## <a id="Next Steps"></a>Passos seguintes

Leia mais sobre a distribuição global nos seguintes artigos:

* [Distribuição global-nos bastidores](global-dist-under-the-hood.md)
* [Como configurar vários mestres em seus aplicativos](how-to-multi-master.md)
* [Configurar clientes para hospedagem múltipla](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Adicionar ou remover regiões de sua conta de Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Criar uma política de resolução de conflito personalizada para contas da API do SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Modelos de consistência programáveis no Cosmos DB](consistency-levels.md)
* [Escolha o nível de consistência certo para a sua aplicação](consistency-levels-choosing.md)
* [Níveis de consistência entre as APIs do Azure Cosmos DB](consistency-levels-across-apis.md)
* [Desvantagens de desempenho e disponibilidade para vários níveis de consistência](consistency-levels-tradeoffs.md)
* [Como implementar a sincronização personalizada para otimizar para maior disponibilidade e desempenho](how-to-custom-synchronization.md)
