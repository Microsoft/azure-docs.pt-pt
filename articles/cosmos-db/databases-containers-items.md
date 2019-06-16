---
title: Trabalhar com bancos de dados, contentores e itens no Azure Cosmos DB
description: Este artigo descreve como criar e utilizar bases de dados, contentores e itens no Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 574dd9fd6189b6d0f1e5d455146d6d083ad7ff77
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66389472"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>Trabalhar com bancos de dados, contentores e itens no Azure Cosmos DB

Depois de criar uma [conta do Azure Cosmos DB](account-overview.md) na sua subscrição do Azure, pode gerir dados na sua conta através da criação de bases de dados, contentores e itens. Este artigo descreve cada uma delas. 

A imagem seguinte mostra a hierarquia de entidades diferentes numa conta do Azure Cosmos DB:

![Entidades de conta do Cosmos do Azure](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Bases de dados do Cosmos do Azure

Pode criar uma ou várias bases de dados do Cosmos do Azure na sua conta. Uma base de dados é semelhante a um espaço de nomes. Uma base de dados é a unidade de gestão para um conjunto de contentores do Azure Cosmos. A tabela seguinte mostra como uma base de dados do Cosmos do Azure está mapeada para várias entidades de API específicas:

| Entidades do Cosmos do Azure | SQL API | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- |
|Base de dados do Cosmos do Azure | Base de Dados | Espaço de chaves | Base de Dados | Base de Dados | N/D |

> [!NOTE]
> Com contas da API de tabela, ao criar a primeira tabela, uma base de dados predefinida é automaticamente criado na sua conta do Cosmos do Azure.

### <a name="operations-on-an-azure-cosmos-database"></a>Operações num banco de dados do Cosmos do Azure

Pode interagir com uma base de dados do Cosmos do Azure com APIs do Cosmos do Azure, tal como descrito na tabela a seguir:

| Operação | CLI do Azure | SQL API | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- |
|Enumerar todos os bancos de dados| Sim | Sim | Sim (a base de dados é mapeado para um keyspace) | Sim | ND | ND |
|Base de dados de leitura| Sim | Sim | Sim (a base de dados é mapeado para um keyspace) | Sim | ND | ND |
|Criar nova base de dados| Sim | Sim | Sim (a base de dados é mapeado para um keyspace) | Sim | ND | ND |
|Atualizar base de dados| Sim | Sim | Sim (a base de dados é mapeado para um keyspace) | Sim | ND | ND |


## <a name="azure-cosmos-containers"></a>Contentores do Azure Cosmos

Um contentor do Cosmos do Azure é a unidade de escalabilidade para o débito aprovisionado e armazenamento. Um contentor é particionado horizontalmente e, em seguida, replicado em várias regiões. Os itens que adicionar para o contentor e a taxa de transferência que for aprovisionado no mesmo automaticamente são distribuídos por um conjunto de partições lógicas com base na chave de partição. Para saber mais sobre a criação de partições e chaves de partição, veja [particionar dados](partition-data.md). 

Quando cria um contentor do Cosmos do Azure, configure o débito dos seguintes modos:

* **Modo de débito dedicado**: O débito aprovisionado num contêiner é reservado exclusivamente para esse contentor e, conta com os SLAs. Para obter mais informações, consulte [como aprovisionar o débito num contentor Azure Cosmos](how-to-provision-container-throughput.md).

* **Modo de débito aprovisionado partilhado**: Estes contentores partilham o débito aprovisionado com os outros contentores na mesma base de dados (excluindo os contentores que tenham sido configuradas com um débito aprovisionado dedicado). Em outras palavras, o débito aprovisionado na base de dados é partilhado entre todos os contentores de "taxa de transferência partilhada". Para obter mais informações, consulte [como aprovisionar o débito numa base de dados do Azure Cosmos](how-to-provision-database-throughput.md).

> [!NOTE]
> Pode configurar o débito dedicado e partilhado apenas quando se cria a base de dados e o contentor. Para mudar do modo débito dedicado para o modo de taxa de transferência partilhada (e vice-versa) depois do contentor é criado, deve criar um contentor novo e migrar os dados para o novo contentor. É possível migrar os dados utilizando o funcionalidade do feed de alterações do Azure Cosmos DB.

Um contentor do Cosmos do Azure pode dimensionar de forma elástica, se cria contentores usando modos de débito aprovisionado partilhados ou dedicados.

Um contentor do Cosmos do Azure é um contentor de esquema desconhecido de itens. Itens num contêiner podem ter esquemas arbitrárias. Por exemplo, um item que representa uma pessoa e um item que representa um automóvel podem ser colocados os *mesmo contentor*. Por predefinição, todos os itens que adicionar a um contentor são indexados automaticamente sem a necessidade de índice explícita ou gestão de esquemas. Pode personalizar o comportamento de indexação ao configurar o [política de indexação](index-overview.md) num contentor. 

Pode definir [tempo Live (TTL)](time-to-live.md) nos itens selecionados num contentor do Cosmos do Azure ou para o contentor inteiro com elegância remover esses itens do sistema. O Azure Cosmos DB elimina automaticamente os itens quando expiram. Esta ação garante também que uma consulta executada no contentor não retorna os itens expirados dentro de um limite fixo. Para obter mais informações, consulte [configurar o valor de TTL no seu contentor](how-to-time-to-live.md).

Pode usar [feed de alterações](change-feed.md) para subscrever o registo de operações que é gerenciado para cada partição lógica do seu contentor. Feed de alterações fornece o registo de todas as atualizações realizadas no contentor, juntamente com as imagens dos itens antes e depois. Para obter mais informações, consulte [Crie aplicações reativas com o feed de alterações](serverless-computing-database.md). Também pode configurar o período de retenção para que a alteração de feed ao utilizar o política no contentor do feed de alterações. 

Pode registrar [procedimentos armazenados, disparadores, funções definidas pelo utilizador (UDFs)](stored-procedures-triggers-udfs.md), e [intercalar procedimentos](how-to-manage-conflicts.md) para o contentor do Cosmos do Azure. 

Pode especificar uma [restrição de chave exclusiva](unique-keys.md) no seu contentor do Cosmos do Azure. Ao criar uma política de chaves exclusivas, garantir a exclusividade de um ou mais valores por chave de partição lógica. Se criar um contentor com uma política de chave exclusiva, não existem itens novos ou atualizados com os valores que duplicar os valores especificados pela restrição de chave exclusiva podem ser criados. Para obter mais informações, consulte [restrições de chave exclusivas](unique-keys.md).

Um contentor do Cosmos do Azure é especializado em entidades de API específicas, conforme mostrado na tabela a seguir:

| Entidades do Cosmos do Azure | SQL API | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- |
|Contentor do Cosmos do Azure | Coleção | Tabela | Coleção | Graph | Tabela |

### <a name="properties-of-an-azure-cosmos-container"></a>Propriedades de um contentor do Cosmos do Azure

Um contentor do Cosmos do Azure tem um conjunto de propriedades definidas pelo sistema. Dependendo de qual API utiliza, algumas propriedades poderão não ser expostas diretamente. A tabela seguinte descreve a lista de propriedades definidas pelo sistema:

| Propriedade definidos pelo sistema | Gerados pelo sistema ou configurável pelo utilizador | Objetivo | SQL API | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_ID | Gerados pelo sistema | Identificador exclusivo do contentor | Sim | Não | Não | Não | Não |
|\_etag | Gerados pelo sistema | Etiqueta de entidade utilizada para controlo de simultaneidade otimista | Sim | Não | Não | Não | Não |
|\_ts | Gerados pelo sistema | Última timestamp atualizado do contentor | Sim | Não | Não | Não | Não |
|\_gestão personalizada | Gerados pelo sistema | Endereçável URI do contentor | Sim | Não | Não | Não | Não |
|id | Configurável pelo utilizador | Nome exclusivo definido pelo utilizador do contentor | Sim | Sim | Sim | Sim | Sim |
|indexingPolicy | Configurável pelo utilizador | Fornece a capacidade de alterar o caminho de índice, o tipo de índice e o modo de índice | Sim | Não | Não | Não | Sim |
|timeToLive | Configurável pelo utilizador | Fornece a capacidade de excluir itens automaticamente a partir de um contentor após um período de tempo. Para obter detalhes, consulte [TTL](time-to-live.md). | Sim | Não | Não | Não | Sim |
|changeFeedPolicy | Configurável pelo utilizador | Utilizado para ler as alterações efetuadas aos itens num contentor. Para obter detalhes, consulte [feed de alterações](change-feed.md). | Sim | Não | Não | Não | Sim |
|uniqueKeyPolicy | Configurável pelo utilizador | Utilizado para garantir a exclusividade de um ou mais valores numa partição lógica. Para obter mais informações, consulte [restrições de chave exclusivas](unique-keys.md). | Sim | Não | Não | Não | Sim |

### <a name="operations-on-an-azure-cosmos-container"></a>Operações num contentor do Cosmos do Azure

Um contentor do Cosmos do Azure suporta as seguintes operações quando usar qualquer uma das APIs do Cosmos do Azure:

| Operação | CLI do Azure | SQL API | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- |
| Enumerar contentores numa base de dados | Sim | Sim | Sim | Sim | ND | ND |
| Ler um contentor | Sim | Sim | Sim | Sim | ND | N/D |
| Criar um novo contentor | Sim | Sim | Sim | Sim | ND | N/D |
| Atualizar um contentor | Sim | Sim | Sim | Sim | ND | N/D |
| Eliminar um contentor | Sim | Sim | Sim | Sim | ND | ND |

## <a name="azure-cosmos-items"></a>Itens do Cosmos do Azure

Dependendo de qual API utiliza, um item do Cosmos do Azure pode representar a um documento numa coleção, uma linha numa tabela, ou um nó ou extremidade num gráfico. A tabela seguinte mostra o mapeamento de entidades de API específica para um item do Cosmos do Azure:

| Entidade de cosmos | SQL API | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- |
|Item do Cosmos do Azure | Documento | Linha | Documento | Nó ou de extremidade | Item |

### <a name="properties-of-an-item"></a>Propriedades de um item

Todos os itens do Cosmos do Azure tem as seguintes propriedades definidas pelo sistema. Dependendo de qual API que utilizar, alguns deles podem não ser exposto diretamente.

| Propriedade definidos pelo sistema | Gerados pelo sistema ou configurável pelo utilizador| Objetivo | SQL API | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_ID | Gerados pelo sistema | Identificador exclusivo do item | Sim | Não | Não | Não | Não |
|\_etag | Gerados pelo sistema | Etiqueta de entidade utilizada para controlo de simultaneidade otimista | Sim | Não | Não | Não | Não |
|\_ts | Gerados pelo sistema | TimeStamp da última atualização do item | Sim | Não | Não | Não | Não |
|\_gestão personalizada | Gerados pelo sistema | URI endereçável do item | Sim | Não | Não | Não | Não |
|ID | Qualquer um dos | Definido pelo utilizador nome exclusivo numa partição lógica. Se o utilizador não especifica o ID, o sistema gera automaticamente um. | Sim | Sim | Sim | Sim | Sim |
|Arbitrárias propriedades definidas pelo utilizador | Definidas pelo utilizador | Propriedades definidas pelo utilizador representadas na representação de API nativa (incluindo o Editor de JSON, BSON e CQL) | Sim | Sim | Sim | Sim | Sim |

### <a name="operations-on-items"></a>Operações nos itens

Itens do Cosmos do Azure suportam as seguintes operações. Pode utilizar qualquer uma das APIs de Cosmos do Azure para realizar as operações.

| Operação | CLI do Azure | SQL API | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- |
| Inserir, substituir, eliminar, Upsert, leitura | Não | Sim | Sim | Sim | Sim | Sim |

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre estas tarefas e conceitos:

* [Débito de aprovisionar uma base de dados do Cosmos do Azure](how-to-provision-database-throughput.md)
* [Débito de aprovisionar num contentor do Cosmos do Azure](how-to-provision-container-throughput.md)
* [Trabalhar com partições lógicas](partition-data.md)
* [Configurar o TTL num contentor do Cosmos do Azure](how-to-time-to-live.md)
* [Criar aplicações reativas com o feed de alterações](change-feed.md)
* [Configurar uma restrição de chave exclusiva no seu contentor do Cosmos do Azure](unique-keys.md)
