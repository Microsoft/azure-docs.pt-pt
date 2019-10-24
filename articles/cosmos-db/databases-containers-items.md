---
title: Trabalhar com bancos de dados, contêineres e itens no Azure Cosmos DB
description: Este artigo descreve como criar e usar bancos de dados, contêineres e itens no Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.reviewer: sngun
ms.openlocfilehash: 0823b4b7e19bec9d8efc83dbb3996076425cb4f1
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756890"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>Trabalhar com bancos de dados, contêineres e itens no Azure Cosmos DB

Depois de criar uma [conta de Azure Cosmos DB](account-overview.md) em sua assinatura do Azure, você pode gerenciar os dados em sua conta Criando bancos de dados, contêineres e itens. Este artigo descreve cada uma dessas entidades. 

A imagem a seguir mostra a hierarquia de entidades diferentes em uma conta de Azure Cosmos DB:

![Entidades de conta do Azure Cosmos](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Bancos de dados Cosmos do Azure

Você pode criar um ou vários bancos de dados do Azure Cosmos em sua conta. Um banco de dados é análogo a um namespace. Um banco de dados é a unidade de gerenciamento para um conjunto de contêineres de Cosmos do Azure. A tabela a seguir mostra como um banco de dados Cosmos do Azure é mapeado para várias entidades específicas de API:

| Entidade Cosmos do Azure | SQL API | API para Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- |
|Banco de dados Cosmos do Azure | Base de Dados | keyspace | Base de Dados | Base de Dados | N/D |

> [!NOTE]
> Com contas de API de Tabela, quando você cria sua primeira tabela, um banco de dados padrão é criado automaticamente em sua conta do Azure Cosmos.

### <a name="operations-on-an-azure-cosmos-database"></a>Operações em um banco de dados Cosmos do Azure

Você pode interagir com um banco de dados Cosmos do Azure com as APIs Cosmos do Azure, conforme descrito na tabela a seguir:

| Operação | CLI do Azure | SQL API | API para Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- |
|Enumerar todos os bancos de dados| Sim | Sim | Sim (o banco de dados é mapeado para um keyspace) | Sim | N/D | N/D |
|Ler banco de dados| Sim | Sim | Sim (o banco de dados é mapeado para um keyspace) | Sim | N/D | N/D |
|Criar novo banco de dados| Sim | Sim | Sim (o banco de dados é mapeado para um keyspace) | Sim | N/D | N/D |
|Atualizar banco de dados| Sim | Sim | Sim (o banco de dados é mapeado para um keyspace) | Sim | N/D | N/D |


## <a name="azure-cosmos-containers"></a>Contêineres de Cosmos do Azure

Um contêiner Cosmos do Azure é a unidade de escalabilidade para taxa de transferência e armazenamento provisionados. Um contêiner é particionado horizontalmente e, em seguida, replicado em várias regiões. Os itens que você adiciona ao contêiner e a taxa de transferência que você provisiona sobre ele são automaticamente distribuídos em um conjunto de partições lógicas com base na chave de partição. Para saber mais sobre particionamento e chaves de partição, consulte [Particionar dados](partition-data.md). 

Ao criar um contêiner Cosmos do Azure, você configura a taxa de transferência em um dos seguintes modos:

* **Modo de taxa de transferência provisionado dedicado**: a taxa de transferência provisionada em um contêiner é exclusivamente reservada para esse contêiner e é apoiada pelos SLAs. Para saber mais, confira [como provisionar a taxa de transferência em um contêiner Cosmos do Azure](how-to-provision-container-throughput.md).

* **Modo de produtividade provisionada compartilhada**: esses contêineres compartilham a taxa de transferência provisionada com os outros contêineres no mesmo banco de dados (excluindo contêineres que foram configurados com taxa de transferência provisionada dedicada). Em outras palavras, a taxa de transferência provisionada no banco de dados é compartilhada entre todos os contêineres de "produtividade compartilhada". Para saber mais, confira [como provisionar a taxa de transferência em um banco de dados Cosmos do Azure](how-to-provision-database-throughput.md).

> [!NOTE]
> Você pode configurar a taxa de transferência compartilhada e dedicada somente ao criar o banco de dados e o contêiner. Para alternar do modo de taxa de transferência dedicada para o modo de taxa de transferência compartilhada (e vice-versa) depois que o contêiner for criado, você precisará criar um novo contêiner e migrar os dados para o novo contêiner. Você pode migrar os dados usando o Azure Cosmos DB recurso de feed de alterações.

Um contêiner Cosmos do Azure pode ser dimensionado de forma elástica, independentemente de você criar contêineres usando modos de taxa de transferência provisionada ou compartilhadas.

Um contêiner Cosmos do Azure é um contêiner de itens independente de esquema. Os itens em um contêiner podem ter esquemas arbitrários. Por exemplo, um item que representa uma pessoa e um item que representa um automóvel pode ser colocado no *mesmo contêiner*. Por padrão, todos os itens que você adiciona a um contêiner são indexados automaticamente sem a necessidade de um índice explícito ou gerenciamento de esquema. Você pode personalizar o comportamento de indexação Configurando a [política de indexação](index-overview.md) em um contêiner. 

Você pode definir [TTL (vida útil)](time-to-live.md) em itens selecionados em um contêiner Cosmos do Azure ou para todo o contêiner para limpar normalmente esses itens do sistema. Azure Cosmos DB exclui automaticamente os itens quando eles expiram. Ele também garante que uma consulta executada no contêiner não retorne os itens expirados em um limite fixo. Para saber mais, consulte [Configurar TTL em seu contêiner](how-to-time-to-live.md).

Você pode usar o [feed de alterações](change-feed.md) para assinar o log de operações que é gerenciado para cada partição lógica do seu contêiner. O feed de alterações fornece o log de todas as atualizações executadas no contêiner, juntamente com as imagens anteriores e posteriores dos itens. Para obter mais informações, consulte [criar aplicativos reativos usando o feed de alterações](serverless-computing-database.md). Você também pode configurar a duração da retenção para o feed de alterações usando a política do feed de alterações no contêiner. 

Você pode registrar [procedimentos armazenados, gatilhos, UDFs (funções definidas pelo usuário)](stored-procedures-triggers-udfs.md)e [procedimentos de mesclagem](how-to-manage-conflicts.md) para seu contêiner Cosmos do Azure. 

Você pode especificar uma [restrição de chave exclusiva](unique-keys.md) em seu contêiner Cosmos do Azure. Ao criar uma política de chave exclusiva, você garante a exclusividade de um ou mais valores por chave de partição lógica. Se você criar um contêiner usando uma política de chave exclusiva, nenhum item novo ou atualizado com valores que duplicar os valores especificados pela restrição UNIQUE KEY poderá ser criado. Para saber mais, consulte [restrições de chave exclusivas](unique-keys.md).

Um contêiner Cosmos do Azure é especializado em entidades específicas de API, conforme mostrado na tabela a seguir:

| Entidade Cosmos do Azure | SQL API | API para Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- |
|Contêiner Cosmos do Azure | Contentor | Tabelas | Coleção | Graph | Tabelas |

### <a name="properties-of-an-azure-cosmos-container"></a>Propriedades de um contêiner Cosmos do Azure

Um contêiner Cosmos do Azure tem um conjunto de propriedades definidas pelo sistema. Dependendo de qual API você usa, algumas propriedades podem não ser diretamente expostas. A tabela a seguir descreve a lista de propriedades definidas pelo sistema:

| Propriedade definida pelo sistema | Gerado pelo sistema ou configurável pelo usuário | Finalidade | SQL API | API para Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_id | Gerado pelo sistema | Identificador exclusivo do contêiner | Sim | Não | Não | Não | Não |
|\_etag | Gerado pelo sistema | Marca de entidade usada para controle de simultaneidade otimista | Sim | Não | Não | Não | Não |
|\_ts | Gerado pelo sistema | Último carimbo de data/hora atualizado do contêiner | Sim | Não | Não | Não | Não |
|\_self | Gerado pelo sistema | URI endereçável do contêiner | Sim | Não | Não | Não | Não |
|ID | Configurável pelo usuário | Nome exclusivo definido pelo usuário do contêiner | Sim | Sim | Sim | Sim | Sim |
|indexingPolicy | Configurável pelo usuário | Fornece a capacidade de alterar o caminho do índice, o tipo de índice e o modo de índice | Sim | Não | Não | Não | Sim |
|TimeToLive | Configurável pelo usuário | Fornece a capacidade de excluir itens automaticamente de um contêiner após um período de tempo definido. Para obter detalhes, consulte [vida útil](time-to-live.md). | Sim | Não | Não | Não | Sim |
|changeFeedPolicy | Configurável pelo usuário | Usado para ler alterações feitas em itens em um contêiner. Para obter detalhes, consulte o [feed de alterações](change-feed.md). | Sim | Não | Não | Não | Sim |
|uniqueKeyPolicy | Configurável pelo usuário | Usado para garantir a exclusividade de um ou mais valores em uma partição lógica. Para obter mais informações, consulte [Unique Key Constraints](unique-keys.md). | Sim | Não | Não | Não | Sim |

### <a name="operations-on-an-azure-cosmos-container"></a>Operações em um contêiner Cosmos do Azure

Um contêiner Cosmos do Azure dá suporte às seguintes operações quando você usa qualquer uma das APIs Cosmos do Azure:

| Operação | CLI do Azure | SQL API | API para Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- |
| Enumerar contêineres em um banco de dados | Sim | Sim | Sim | Sim | N/D | N/D |
| Ler um contêiner | Sim | Sim | Sim | Sim | N/D | N/D |
| Criar um novo contêiner | Sim | Sim | Sim | Sim | N/D | N/D |
| Atualizar um contêiner | Sim | Sim | Sim | Sim | N/D | N/D |
| Eliminar um contentor | Sim | Sim | Sim | Sim | N/D | N/D |

## <a name="azure-cosmos-items"></a>Itens Cosmos do Azure

Dependendo de qual API você usa, um item Cosmos do Azure pode representar um documento em uma coleção, uma linha em uma tabela ou um nó ou borda em um grafo. A tabela a seguir mostra o mapeamento de entidades específicas de API para um item Cosmos do Azure:

| Entidade Cosmos | SQL API | API para Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- |
|Item Cosmos do Azure | Documento | fila | Documento | Nó ou borda | Item |

### <a name="properties-of-an-item"></a>Propriedades de um item

Cada item Cosmos do Azure tem as seguintes propriedades definidas pelo sistema. Dependendo de qual API você usa, alguns deles podem não ser expostos diretamente.

| Propriedade definida pelo sistema | Gerado pelo sistema ou configurável pelo usuário| Finalidade | SQL API | API para Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_id | Gerado pelo sistema | Identificador exclusivo do item | Sim | Não | Não | Não | Não |
|\_etag | Gerado pelo sistema | Marca de entidade usada para controle de simultaneidade otimista | Sim | Não | Não | Não | Não |
|\_ts | Gerado pelo sistema | Carimbo de data/hora da última atualização do item | Sim | Não | Não | Não | Não |
|\_self | Gerado pelo sistema | URI endereçável do item | Sim | Não | Não | Não | Não |
|ID | O | Nome exclusivo definido pelo usuário em uma partição lógica. Se o usuário não especificar a ID, o sistema gerará automaticamente uma. | Sim | Sim | Sim | Sim | Sim |
|Propriedades arbitrárias definidas pelo usuário | Definidas pelo utilizador | Propriedades definidas pelo usuário representadas na representação nativa da API (incluindo JSON, BSON e CQL) | Sim | Sim | Sim | Sim | Sim |

> [!NOTE]
> A exclusividade da propriedade `id` é imposta somente dentro de cada partição lógica. Vários documentos podem ter a mesma propriedade de `id` com valores de chave de partição diferentes.

### <a name="operations-on-items"></a>Operações em itens

Os itens Cosmos do Azure dão suporte às seguintes operações. Você pode usar qualquer uma das APIs do Azure Cosmos para executar as operações.

| Operação | CLI do Azure | SQL API | API para Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- |
| Inserir, substituir, excluir, Upsert, ler | Não | Sim | Sim | Sim | Sim | Sim |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre estas tarefas e conceitos:

* [Provisionar taxa de transferência em um banco de dados Cosmos do Azure](how-to-provision-database-throughput.md)
* [Provisionar a taxa de transferência em um contêiner Cosmos do Azure](how-to-provision-container-throughput.md)
* [Trabalhar com partições lógicas](partition-data.md)
* [Configurar TTL em um contêiner Cosmos do Azure](how-to-time-to-live.md)
* [Criar aplicativos reativos usando o feed de alterações](change-feed.md)
* [Configurar uma restrição de chave exclusiva em seu contêiner Cosmos do Azure](unique-keys.md)
