---
title: Trabalhe com bases de dados, contentores e itens em Azure Cosmos DB
description: Este artigo descreve como criar e utilizar bases de dados, contentores e itens em Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/24/2020
ms.reviewer: sngun
ms.openlocfilehash: f3906878755b7c7c2e3801da1bfa70a50d73ea16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318794"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>Trabalhe com bases de dados, contentores e itens em Azure Cosmos DB

Depois de criar uma [conta DB Azure Cosmos](account-overview.md) sob a sua assinatura Azure, pode gerir dados na sua conta criando bases de dados, contentores e itens. Este artigo descreve cada uma dessas entidades. 

A imagem a seguir mostra a hierarquia de diferentes entidades numa conta DB da Azure Cosmos:

:::image type="content" source="./media/databases-containers-items/cosmos-entities.png" alt-text="Entidades de conta Azure Cosmos" border="false":::

## <a name="azure-cosmos-databases"></a>Bases de dados do Azure Cosmos

Pode criar uma ou várias bases de dados da Azure Cosmos na sua conta. Uma base de dados é análoga a um espaço de nome. Uma base de dados é a unidade de gestão de um conjunto de contentores Azure Cosmos. A tabela a seguir mostra como uma base de dados Azure Cosmos é mapeada para várias entidades específicas da API:

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

Um recipiente Azure Cosmos é a unidade de escalabilidade tanto para produção e armazenamento a provisionados. Um recipiente é horizontalmente dividido e depois replicado em várias regiões. Os itens que adiciona ao recipiente são automaticamente agrupados em divisórias lógicas, que são distribuídas por divisórias físicas, com base na tecla de partição. A produção num recipiente é distribuída uniformemente pelas divisórias físicas. Para saber mais sobre as chaves de partição e partição, consulte os [dados da partição.](partition-data.md) 

Quando cria um recipiente Azure Cosmos, configura a produção num dos seguintes modos:

* **Modo de produção dedicado**: A produção a prevista num contentor é exclusivamente reservada a esse contentor e é apoiada pelas AEA. Para saber mais, consulte [Como providenciar a produção num contentor Azure Cosmos.](how-to-provision-container-throughput.md)

* **Modo de produção partilhado**: Estes contentores partilham a produção a provisionada com os outros contentores na mesma base de dados (excluindo os contentores que tenham sido configurados com produção dedicada). Por outras palavras, o rendimento previsto na base de dados é partilhado entre todos os contentores de "produção partilhada". Para saber mais, consulte [como obter a produção numa base de dados da Azure Cosmos.](how-to-provision-database-throughput.md)

> [!NOTE]
> Só pode configurar a produção partilhada e dedicada quando estiver a criar a base de dados e o contentor. Para mudar do modo de débito dedicado para o modo de débito partilhado (e vice-versa) após o contentor ser criado, precisará de criar um novo contentor e migrar os dados para esse contentor. Pode migrar os dados utilizando a função de feed de alteração DB do Azure Cosmos.

Um recipiente Azure Cosmos pode escalar elasticamente, quer crie recipientes utilizando modos de produção dedicados ou partilhados.

Um recipiente Azure Cosmos é um recipiente de itens agnósticos. Os artigos num recipiente podem ter esquemas arbitrários. Por exemplo, um item que represente uma pessoa e um item que represente um automóvel pode ser colocado no *mesmo recipiente.* Por predefinição, todos os itens que adiciona a um recipiente são automaticamente indexados sem exigir uma gestão explícita do índice ou do esquema. Pode personalizar o comportamento de indexação configurando a [política de indexação](index-overview.md) num recipiente. 

Pode definir [Time to Live (TTL)](time-to-live.md) em itens selecionados num recipiente Azure Cosmos ou para que todo o recipiente purgue graciosamente esses itens do sistema. A Azure Cosmos DB elimina automaticamente os itens quando expiram. Também garante que uma consulta realizada no recipiente não devolve os itens expirados dentro de um limite fixo. Para saber mais, consulte [o Configure TTL no seu recipiente.](how-to-time-to-live.md)

Pode utilizar [o feed de alteração](change-feed.md) para subscrever o registo de operações que é gerido para cada divisão lógica do seu recipiente. O feed de alteração fornece o registo de todas as atualizações realizadas no recipiente, juntamente com as imagens antes e depois dos itens. Para obter mais informações, consulte [Build reative applications usando change feed](serverless-computing-database.md). Também pode configurar a duração de retenção para a alteração do alimento através da política de alteração do alimento para animais no recipiente.

Pode registar [procedimentos armazenados, gatilhos, funções definidas pelo utilizador (UDFs)](stored-procedures-triggers-udfs.md)e [fundir procedimentos](how-to-manage-conflicts.md) para o seu contentor Azure Cosmos.

Pode especificar uma [chave única](unique-keys.md) no seu recipiente Azure Cosmos. Ao criar uma política chave única, você garante a singularidade de um ou mais valores por chave de partição lógica. Se criar um recipiente utilizando uma política chave única, não podem ser criados itens novos ou atualizados com valores que dupliquem os valores especificados pela restrição única da chave. Para saber mais, consulte [os principais constrangimentos exclusivos.](unique-keys.md)

Um contentor Azure Cosmos é especializado em entidades específicas da API, conforme mostrado no quadro seguinte:

| Entidade Azure Cosmos | API SQL | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- |
|Recipiente Azure Cosmos | Contentor | Tabela | Coleção | Graph | Tabela |

> [!NOTE]
> Ao criar recipientes, certifique-se de que não cria dois recipientes com o mesmo nome, mas invólucros diferentes. Isto porque algumas partes da plataforma Azure não são sensíveis a casos, o que pode resultar em confusão/colisão de telemetria e ações em contentores com tais nomes.

### <a name="properties-of-an-azure-cosmos-container"></a>Propriedades de um contentor Azure Cosmos

Um recipiente Azure Cosmos tem um conjunto de propriedades definidas pelo sistema. Dependendo da API que utiliza, algumas propriedades podem não estar diretamente expostas. A tabela a seguir descreve a lista de propriedades definidas pelo sistema:

| Propriedade definida pelo sistema | Gerado pelo sistema ou configurável pelo utilizador | Objetivo | API SQL | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Gerado pelo sistema | Identificador único do recipiente | Sim | Não | Não | Não | Não |
|\_etag | Gerado pelo sistema | Etiqueta de entidade utilizada para controlo otimista da conuscção | Sim | Não | Não | Não | Não |
|\_ts | Gerado pelo sistema | Última hora atualizada do tempo do recipiente | Sim | Não | Não | Não | Não |
|\_self | Gerado pelo sistema | URI endereçado do contentor | Sim | Não | Não | Não | Não |
|ID | Configurável pelo utilizador | Nome único definido pelo utilizador do recipiente | Sim | Sim | Sim | Sim | Sim |
|indexaçãoPolícia | Configurável pelo utilizador | Fornece a capacidade de alterar a trajetória do índice, o tipo de índice e o modo de índice | Sim | Não | Não | Não | Sim |
|TimeToLive | Configurável pelo utilizador | Fornece a capacidade de eliminar automaticamente os itens de um recipiente após um período de tempo definido. Para mais detalhes, consulte [a hora de viver.](time-to-live.md) | Sim | Não | Não | Não | Sim |
|mudarFeedPolicy | Configurável pelo utilizador | Costumava ler alterações feitas em itens num recipiente. Para mais detalhes, consulte [change feed](change-feed.md). | Sim | Não | Não | Não | Sim |
|exclusivoKeyPolicy | Configurável pelo utilizador | Usado para garantir a singularidade de um ou mais valores numa partição lógica. Para obter mais informações, consulte [as principais restrições exclusivas.](unique-keys.md) | Sim | Não | Não | Não | Sim |

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
|\_rid | Gerado pelo sistema | Identificador único do item | Sim | Não | Não | Não | Não |
|\_etag | Gerado pelo sistema | Etiqueta de entidade utilizada para controlo otimista da conuscção | Sim | Não | Não | Não | Não |
|\_ts | Gerado pelo sistema | Timetamp da última atualização do item | Sim | Não | Não | Não | Não |
|\_self | Gerado pelo sistema | URI endereçada do item | Sim | Não | Não | Não | Não |
|ID | Ambas | Nome único definido pelo utilizador numa divisória lógica. | Sim | Sim | Sim | Sim | Sim |
|Propriedades arbitrárias definidas pelo utilizador | Definidas pelo utilizador | Propriedades definidas pelo utilizador representadas na representação nativa da API (incluindo JSON, BSON e CQL) | Sim | Sim | Sim | Sim | Sim |

> [!NOTE]
> A singularidade da `id` propriedade só é aplicada dentro de cada divisória lógica. Vários documentos podem ter a mesma `id` propriedade com diferentes valores-chave de partição.

### <a name="operations-on-items"></a>Operações em itens

Os itens da Azure Cosmos suportam as seguintes operações. Pode utilizar qualquer uma das APIs do Azure Cosmos para realizar as operações.

| Operação | CLI do Azure | API SQL | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- |
| Inserir, Substituir, Eliminar, Aumentar, Ler | Não | Sim | Sim | Sim | Sim | Sim |

## <a name="next-steps"></a>Próximos passos

Conheça estas tarefas e conceitos:

* [Produção de provisão numa base de dados Azure Cosmos](how-to-provision-database-throughput.md)
* [Produção de provisão num contentor Azure Cosmos](how-to-provision-container-throughput.md)
* [Trabalhar com divisórias lógicas](partition-data.md)
* [Configure TTL em um recipiente Azure Cosmos](how-to-time-to-live.md)
* [Construa aplicações reativas utilizando o feed de alteração](change-feed.md)
* [Configure uma restrição chave única no seu contentor Azure Cosmos](unique-keys.md)
