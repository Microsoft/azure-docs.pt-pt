---
title: Modelo de recursos do Azure Cosmos DB
description: Este artigo descreve o modelo de recursos DB da Azure Cosmos que inclui a conta Azure Cosmos, base de dados, contentor e os itens. Também cobre a hierarquia destes elementos numa conta de Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 37f1c9f59b6ffb45e1b874d2a6969bf263d2d5eb
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341370"
---
# <a name="azure-cosmos-db-resource-model"></a>Modelo de recursos do Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB é uma plataforma totalmente gerida como um serviço (PaaS). Para começar a utilizar o Azure Cosmos DB, deverá inicialmente criar uma conta Azure Cosmos na sua subscrição Azure e bases de dados, contentores, itens por baixo. Este artigo descreve o modelo de recursos DB da Azure Cosmos e diferentes entidades na hierarquia do modelo de recursos.

A conta Azure Cosmos é a unidade fundamental de distribuição global e alta disponibilidade. A sua conta Azure Cosmos contém um nome DNS único e pode gerir uma conta utilizando o portal Azure CLI ou utilizando diferentes SDKs específicos da linguagem. Para mais informações, consulte [como gerir a sua conta Azure Cosmos.](how-to-manage-database-account.md) Para distribuir globalmente os seus dados e produção em várias regiões do Azure, pode adicionar e remover as regiões de Azure na sua conta a qualquer momento. Pode configurar a sua conta para ter uma única região ou várias regiões de escrita. Para mais informações, consulte [como adicionar e remover as regiões de Azure na sua conta.](how-to-manage-database-account.md) Pode configurar o nível [de consistência padrão](consistency-levels.md) numa conta.

## <a name="elements-in-an-azure-cosmos-account"></a>Elementos numa conta do Azure Cosmos

O contentor Azure Cosmos é a unidade fundamental de escalabilidade. Você pode praticamente ter uma produção de provisão ilimitada (RU/s) e armazenamento em um recipiente. A Azure Cosmos DB partilha de forma transparente o seu recipiente utilizando a chave de partição lógica que especifica para escalar elasticamente a sua produção e armazenamento a provisionados.

Atualmente, pode criar um máximo de 50 contas Azure Cosmos sob uma subscrição do Azure (este é um limite suave que pode ser aumentado através de pedido de suporte). Uma única conta Azure Cosmos pode gerir virtualmente uma quantidade ilimitada de dados e produção a provisionada. Para gerir os seus dados e o seu rendimento a provisionado, pode criar uma ou mais bases de dados Azure Cosmos na sua conta e dentro dessa base de dados, pode criar um ou mais contentores. A imagem a seguir mostra a hierarquia dos elementos numa conta da Azure Cosmos:

:::image type="content" source="./media/account-databases-containers-items/hierarchy.png" alt-text="Hierarquia de uma conta de Azure Cosmos" border="false":::

Depois de criar uma conta sob a subscrição do Azure, pode gerir os dados na sua conta criando bases de dados, contentores e itens. 

A imagem a seguir mostra a hierarquia de diferentes entidades numa conta DB da Azure Cosmos:

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Entidades de conta Azure Cosmos" border="false":::

## <a name="azure-cosmos-databases"></a>Bases de dados do Azure Cosmos

Pode criar uma ou várias bases de dados da Azure Cosmos na sua conta. Uma base de dados é análoga a um espaço de nome. Uma base de dados é a unidade de gestão de um conjunto de contentores Azure Cosmos. O quadro que se segue mostra como uma base de dados é mapeada para várias entidades específicas da API:

| Entidade Azure Cosmos | API SQL | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- |
|Base de dados Azure Cosmos | Base de Dados | Espaço-chave | Base de Dados | Base de Dados | ND |

> [!NOTE]
> Com as contas API de tabela, quando cria a sua primeira tabela, uma base de dados predefinida é criada automaticamente na sua conta Azure Cosmos.

### <a name="operations-on-an-azure-cosmos-database"></a>Operações numa base de dados da Azure Cosmos

Pode interagir com uma base de dados Azure Cosmos com APIs Azure Cosmos, conforme descrito na tabela seguinte:

| Operação | CLI do Azure | API SQL | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- |
|Enumerar todas as bases de dados| Sim | Sim | Sim (base de dados está mapeada para um espaço-chave) | Sim | ND | ND |
|Ler base de dados| Sim | Sim | Sim (base de dados está mapeada para um espaço-chave) | Sim | ND | ND |
|Criar nova base de dados| Sim | Sim | Sim (base de dados está mapeada para um espaço-chave) | Sim | ND | ND |
|Base de dados de atualização| Sim | Sim | Sim (base de dados está mapeada para um espaço-chave) | Sim | ND | ND |

## <a name="azure-cosmos-containers"></a>Contentores do Azure Cosmos

Um recipiente Azure Cosmos é a unidade de escalabilidade tanto para produção e armazenamento a provisionados. Um recipiente é horizontalmente dividido e depois replicado em várias regiões. Os itens que adiciona ao recipiente são automaticamente agrupados em divisórias lógicas, que são distribuídas por divisórias físicas, com base na tecla de partição. A produção num recipiente é distribuída uniformemente pelas divisórias físicas. Para saber mais sobre as chaves de partição e partição, consulte os [dados da partição.](partitioning-overview.md) 

Quando cria um recipiente, configura a produção num dos seguintes modos:

* **Modo de produção dedicado** : A produção a prevista num contentor é exclusivamente reservada a esse contentor e é apoiada pelas AEA. Para saber mais, consulte [Como providenciar a produção num recipiente.](how-to-provision-container-throughput.md)

* **Modo de produção partilhado** : Estes contentores partilham a produção a provisionada com os outros contentores na mesma base de dados (excluindo os contentores que tenham sido configurados com produção dedicada). Por outras palavras, o rendimento previsto na base de dados é partilhado entre todos os contentores de "produção partilhada". Para saber mais, consulte [como obter o rendimento numa base de dados.](how-to-provision-database-throughput.md)

> [!NOTE]
> Só pode configurar a produção partilhada e dedicada quando estiver a criar a base de dados e o contentor. Para mudar do modo de débito dedicado para o modo de débito partilhado (e vice-versa) após o contentor ser criado, precisará de criar um novo contentor e migrar os dados para esse contentor. Pode migrar os dados utilizando a função de feed de alteração DB do Azure Cosmos.

Um recipiente Azure Cosmos pode escalar elasticamente, quer crie recipientes utilizando modos de produção dedicados ou partilhados.

Um recipiente é um recipiente de itens esquema-agnósticos. Os artigos num recipiente podem ter esquemas arbitrários. Por exemplo, um item que represente uma pessoa e um item que represente um automóvel pode ser colocado no *mesmo recipiente.* Por predefinição, todos os itens que adiciona a um recipiente são automaticamente indexados sem exigir uma gestão explícita do índice ou do esquema. Pode personalizar o comportamento de indexação configurando a [política de indexação](index-overview.md) num recipiente. 

Pode definir [Tempo para Viver (TTL)](time-to-live.md) em itens selecionados num recipiente ou para que todo o recipiente purgue graciosamente esses itens do sistema. A Azure Cosmos DB elimina automaticamente os itens quando expiram. Também garante que uma consulta realizada no recipiente não devolve os itens expirados dentro de um limite fixo. Para saber mais, consulte [o Configure TTL no seu recipiente.](how-to-time-to-live.md)

Pode utilizar [o feed de alteração](change-feed.md) para subscrever o registo de operações que é gerido para cada divisão lógica do seu recipiente. O feed de alteração fornece o registo de todas as atualizações realizadas no recipiente, juntamente com as imagens antes e depois dos itens. Para obter mais informações, consulte [Build reative applications usando change feed](serverless-computing-database.md). Também pode configurar a duração de retenção para a alteração do alimento através da política de alteração do alimento para animais no recipiente.

Pode registar [procedimentos armazenados, gatilhos, funções definidas pelo utilizador (UDFs)](stored-procedures-triggers-udfs.md)e [fundir procedimentos](how-to-manage-conflicts.md) para o seu recipiente.

Pode especificar uma [chave única](unique-keys.md) no seu recipiente Azure Cosmos. Ao criar uma política chave única, você garante a singularidade de um ou mais valores por chave de partição lógica. Se criar um recipiente utilizando uma política chave única, não podem ser criados itens novos ou atualizados com valores que dupliquem os valores especificados pela restrição única da chave. Para saber mais, consulte [os principais constrangimentos exclusivos.](unique-keys.md)

Um contentor é especializado em entidades específicas da API, conforme indicado no quadro seguinte:

| Entidade Azure Cosmos | API SQL | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- |
|Recipiente Azure Cosmos | Contentor | Tabela | Coleção | Graph | Tabela |

> [!NOTE]
> Ao criar recipientes, certifique-se de que não cria dois recipientes com o mesmo nome, mas invólucros diferentes. Isto porque algumas partes da plataforma Azure não são sensíveis a casos, o que pode resultar em confusão/colisão de telemetria e ações em contentores com tais nomes.

### <a name="properties-of-an-azure-cosmos-container"></a>Propriedades de um contentor Azure Cosmos

Um recipiente Azure Cosmos tem um conjunto de propriedades definidas pelo sistema. Dependendo da API que utiliza, algumas propriedades podem não estar diretamente expostas. A tabela a seguir descreve a lista de propriedades definidas pelo sistema:

| Propriedade definida pelo sistema | Gerado pelo sistema ou configurável pelo utilizador | Objetivo | API SQL | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Gerado pelo sistema | Identificador único do recipiente | Sim | No | No | No | No |
|\_etag | Gerado pelo sistema | Etiqueta de entidade utilizada para controlo otimista da conuscção | Sim | No | No | No | No |
|\_ts | Gerado pelo sistema | Última hora atualizada do tempo do recipiente | Sim | No | No | No | No |
|\_self | Gerado pelo sistema | URI endereçado do contentor | Sim | No | No | No | No |
|ID | Configurável pelo utilizador | Nome único definido pelo utilizador do recipiente | Sim | Sim | Sim | Sim | Sim |
|indexaçãoPolícia | Configurável pelo utilizador | Fornece a capacidade de alterar a trajetória do índice, o tipo de índice e o modo de índice | Sim | No | No | No | Sim |
|TimeToLive | Configurável pelo utilizador | Fornece a capacidade de eliminar automaticamente os itens de um recipiente após um período de tempo definido. Para mais detalhes, consulte [a hora de viver.](time-to-live.md) | Sim | No | No | No | Sim |
|mudarFeedPolicy | Configurável pelo utilizador | Costumava ler alterações feitas em itens num recipiente. Para mais detalhes, consulte [change feed](change-feed.md). | Sim | No | No | No | Sim |
|exclusivoKeyPolicy | Configurável pelo utilizador | Usado para garantir a singularidade de um ou mais valores numa partição lógica. Para obter mais informações, consulte [as principais restrições exclusivas.](unique-keys.md) | Sim | No | No | No | Sim |

### <a name="operations-on-an-azure-cosmos-container"></a>Operações num contentor Azure Cosmos

Um contentor Azure Cosmos suporta as seguintes operações quando utiliza qualquer uma das APIs do Cosmos Azure:

| Operação | CLI do Azure | API SQL | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- |
| Enumerar contentores numa base de dados | Sim | Sim | Sim | Sim | ND | ND |
| Leia um recipiente | Sim | Sim | Sim | Sim | ND | ND |
| Criar um novo recipiente | Sim | Sim | Sim | Sim | ND | ND |
| Atualizar um recipiente | Sim | Sim | Sim | Sim | ND | ND |
| Eliminar um contentor | Sim | Sim | Sim | Sim | ND | ND |

## <a name="azure-cosmos-items"></a>Itens Azure Cosmos

Dependendo da API que utiliza, um item Azure Cosmos pode representar um documento numa coleção, uma linha numa mesa ou um nó ou borda num gráfico. A tabela a seguir mostra o mapeamento de entidades específicas da API para um item Azure Cosmos:

| Entidade cosmos | API SQL | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- |
|Item Azure Cosmos | Item | Linha | Documento | Nó ou borda | Item |

### <a name="properties-of-an-item"></a>Propriedades de um item

Cada item Azure Cosmos tem as seguintes propriedades definidas pelo sistema. Dependendo da API que usa, alguns deles podem não estar diretamente expostos.

| Propriedade definida pelo sistema | Gerado pelo sistema ou configurável pelo utilizador| Objetivo | API SQL | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Gerado pelo sistema | Identificador único do item | Sim | No | No | No | No |
|\_etag | Gerado pelo sistema | Etiqueta de entidade utilizada para controlo otimista da conuscção | Sim | No | No | No | No |
|\_ts | Gerado pelo sistema | Timetamp da última atualização do item | Sim | No | No | No | No |
|\_self | Gerado pelo sistema | URI endereçada do item | Sim | No | No | No | No |
|ID | Ambas | Nome único definido pelo utilizador numa divisória lógica. | Sim | Sim | Sim | Sim | Sim |
|Propriedades arbitrárias definidas pelo utilizador | Definidas pelo utilizador | Propriedades definidas pelo utilizador representadas na representação nativa da API (incluindo JSON, BSON e CQL) | Sim | Sim | Sim | Sim | Sim |

> [!NOTE]
> A singularidade da `id` propriedade só é aplicada dentro de cada divisória lógica. Vários documentos podem ter a mesma `id` propriedade com diferentes valores-chave de partição.

### <a name="operations-on-items"></a>Operações em itens

Os itens da Azure Cosmos suportam as seguintes operações. Pode utilizar qualquer uma das APIs do Azure Cosmos para realizar as operações.

| Operação | CLI do Azure | API SQL | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- |
| Inserir, Substituir, Eliminar, Aumentar, Ler | No | Sim | Sim | Sim | Sim | Sim |

## <a name="next-steps"></a>Próximos passos

Saiba como gerir a sua conta Azure Cosmos e outros conceitos:

* [Como gerir a sua conta Azure Cosmos](how-to-manage-database-account.md)
* [Distribuição global](distribute-data-globally.md)
* [Níveis de consistência](consistency-levels.md)
* [Ponto final de serviço VNET para a sua conta Azure Cosmos](how-to-configure-vnet-service-endpoint.md)
* [FIREWALL para a sua conta Azure Cosmos](how-to-configure-firewall.md)
* [Como adicionar e remover regiões do Azure na sua conta Azure Cosmos](how-to-manage-database-account.md)
* [Azure Cosmos DB SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
