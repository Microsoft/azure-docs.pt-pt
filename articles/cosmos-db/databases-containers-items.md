---
title: Trabalhar com bancos de dados do Azure Cosmos DB, contentores e itens
description: Este artigo descreve como criar e utilizar bases de dados do Azure Cosmos DB, contentores e itens
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: c7288b112ce2fd062a67cb673a1efa6028ec7d57
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57888524"
---
# <a name="work-with-databases-containers-and-items"></a>Trabalhar com bases de dados, contentores e itens

Depois de criar uma [conta do Azure Cosmos DB](account-overview.md) na sua subscrição do Azure, pode gerir dados na sua conta através da criação de bases de dados, contentores e itens. Este artigo descreve cada uma delas: bases de dados, contentores e itens. A imagem seguinte mostra a hierarquia de entidades diferentes numa conta do Cosmos do Azure:

![Entidades de conta do Cosmos do Azure](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Bases de dados do Cosmos do Azure

Pode criar um ou mais bases de dados do Cosmos do Azure na sua conta. Uma base de dados é semelhante a um espaço de nomes, é a unidade de gestão para um conjunto de contentores do Azure Cosmos. A tabela seguinte mostra como uma base de dados do Cosmos do Azure está mapeada para várias entidades de API específicas:

| **Entidades do Cosmos do Azure** | **API DE SQL** | **API para Cassandra** | **API do Azure Cosmos DB para o MongoDB** | **API do gremlin** | **API de Tabela** |
| --- | --- | --- | --- | --- | --- |
|Base de dados do Cosmos do Azure | Base de Dados | Espaço de chaves | Base de Dados | Base de Dados | ND |

> [!NOTE]
> Com as contas de APIs de tabela, ao criar a primeira tabela uma base de dados predefinida é automaticamente criado na sua conta do Cosmos do Azure.

### <a name="operations-on-an-azure-cosmos-database"></a>Operações num banco de dados do Cosmos do Azure

Pode interagir com uma base de dados do Cosmos do Azure com as APIs do Cosmos do Azure seguintes:

| **Operação** | **CLI do Azure**|**API DE SQL** | **API para Cassandra** | **API do Azure Cosmos DB para o MongoDB** | **API do gremlin** | **API de Tabela** |
| --- | --- | --- | --- | --- | --- | --- |
|Enumerar todos os bancos de dados| Sim | Sim | Sim (a base de dados é mapeado para um keyspace) | Sim | ND | ND |
|Base de dados de leitura| Sim | Sim | Sim (a base de dados é mapeado para um keyspace) | Sim | ND | ND |
|Criar nova base de dados| Sim | Sim | Sim (a base de dados é mapeado para um keyspace) | Sim | ND | ND |
|Atualizar base de dados| Sim | Sim | Sim (a base de dados é mapeado para um keyspace) | Sim | ND | ND |


## <a name="azure-cosmos-containers"></a>Contentores do Azure Cosmos

Um contentor do Cosmos do Azure é a unidade de escalabilidade para o débito aprovisionado e armazenamento de itens. Um contentor é particionado horizontalmente e, em seguida, replicado em várias regiões. Os itens que adicionar para o contentor e a taxa de transferência que for aprovisionado no mesmo automaticamente são distribuídos por um conjunto de partições lógicas com base na chave de partição. Para saber mais sobre a criação de partições e a chave de partição, veja [partições lógicas](partition-data.md) artigo. 

Ao criar um contentor do Cosmos do Azure, configure o débito dos seguintes modos:

* **Débito dedicado** modo: Débito aprovisionado num contêiner é reservado exclusivamente para ele e, conta com os SLAs. Para obter mais informações, consulte [como aprovisionar o débito num contentor Azure Cosmos](how-to-provision-container-throughput.md).

* **Débito aprovisionado partilhado** modo: Estes contentores partilham o débito aprovisionado com outros contentores no mesmo banco de dados (excluindo desses contentores que tenham sido configurados com um débito aprovisionado dedicado). Em outras palavras, o débito aprovisionado na base de dados é partilhado entre todos os contentores "partilhados". Para obter mais informações, consulte [como configurar o débito aprovisionado numa base de dados do Azure Cosmos](how-to-provision-database-throughput.md).

Um contentor do Cosmos do Azure pode dimensionar de forma elástica, se criar contentores com um "partilhado" ou "dedicado" aprovisionado modos de débito.

Um contentor do Cosmos do Azure é um contentor de esquema desconhecido de itens. Itens dentro de um contêiner podem ter esquemas arbitrárias. Por exemplo, um item que representa uma pessoa, um item que representa um automóvel pode ser colocado no mesmo contentor. Por predefinição, todos os itens que adicionar a um contentor automaticamente eram indexados sem necessidade de qualquer índice explícita ou gestão de esquemas. Pode personalizar o comportamento de indexação ao configurar a política de indexação num contentor. 

Pode definir o tempo para Live (TTL) nos itens selecionados dentro de um contentor do Cosmos do Azure ou para o contentor inteiro com elegância remover esses itens fora do sistema. O Azure Cosmos DB irá eliminar automaticamente os itens quando expiram. Esta ação garante também que uma consulta executada no contentor não devolve os itens expirados dentro de um limite fixo. Para obter mais informações, consulte [como configurar o TTL no seu contentor](how-to-time-to-live.md).

Ao utilizar o feed de alterações, pode subscrever o registo de operações que é gerenciado para cada partição lógica do seu contentor. O tipo de Feed de alterações fornece o registo de todas as atualizações realizadas no contêiner juntamente com o antes e as imagens depois dos itens. Ver [como criar aplicações reativas com alterar feed](change-feed.md). Também pode configurar a duração da retenção de feed ao utilizar o política no contentor do feed de alterações de alterações. 

Pode registrar procedimentos armazenados, disparadores, funções definidas pelo utilizador (UDFs) e procedimentos de intercalação com o seu contentor do Cosmos do Azure. 

Pode especificar uma chave exclusiva no seu contentor do Cosmos do Azure. Ao criar uma política de chaves exclusivas, garantir a exclusividade de um ou mais valores por chave de partição lógica. Assim que tiver sido criado um contentor com uma política de chaves exclusivas, ele impede a criação de todos os itens novos ou atualizados com os valores que duplicar os valores especificados pela restrição de chave exclusiva. Para obter mais informações, consulte [restrições de chave exclusivas](unique-keys.md).

Um contentor do Cosmos do Azure é especializado em entidades de API específicas da seguinte forma:

| **Entidades do Cosmos do Azure** | **API DE SQL** | **API para Cassandra** | **API do Azure Cosmos DB para o MongoDB** | **API do gremlin** | **API de Tabela** |
| --- | --- | --- | --- | --- | --- |
|Contentor do Cosmos do Azure | Coleção | Tabela | Coleção | Graph | Tabela |

### <a name="properties-of-an-azure-cosmos-container"></a>Propriedades de um contentor do Cosmos do Azure

Um contentor do Cosmos do Azure tem um conjunto de propriedades do sistema definido. Consoante a opção da API, alguns deles podem não ser exposto diretamente. A tabela seguinte descreve a lista de propriedades do sistema com suporte definido:

| **Propriedade de sistema definido** | **Sistema gerado ou utilizador definível** | **Objetivo** | **API DE SQL** | **API para Cassandra** | **API do Azure Cosmos DB para o MongoDB** | **API do gremlin** | **API de Tabela** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|_rid | Gerada pelo sistema | Identificador exclusivo do contentor | Sim | Não | Não | Não | Não |
|_etag | Gerada pelo sistema | Etiqueta de entidade utilizada para controlo de simultaneidade otimista | Sim | Não | Não | Não | Não |
|_ts | Gerada pelo sistema | Última timestamp atualizado do contentor | Sim | Não | Não | Não | Não |
|_self | Gerada pelo sistema | Endereçável URI do contentor | Sim | Não | Não | Não | Não |
|ID | Utilizador configurável | Nome exclusivo definido pelo utilizador do contentor | Sim | Sim | Sim | Sim | Sim |
|indexingPolicy | Utilizador configurável | Fornece a capacidade de alterar o caminho de índice, sua precisão e o modelo de consistência. | Sim | Não | Não | Não | Sim |
|TimeToLive | Utilizador configurável | Fornece a capacidade de excluir itens automaticamente a partir de um contentor após um determinado período de tempo. Para obter mais detalhes, consulte a [Time To Live](time-to-live.md) artigo. | Sim | Não | Não | Não | Sim |
|changeFeedPolicy | Utilizador configurável | Utilizado para ler as alterações efetuadas aos itens num contentor. Para obter mais detalhes, consulte a [feed de alterações](change-feed.md) artigo. | Sim | Não | Não | Não | Sim |
|uniqueKeyPolicy | Utilizador configurável | Com as chaves exclusivas, garantir a exclusividade de um ou mais valores dentro de uma partição lógica. Para obter mais informações, consulte a [chaves exclusivas](unique-keys.md) artigo. | Sim | Não | Não | Não | Sim |

### <a name="operations-on-an-azure-cosmos-container"></a>Operações num contentor do Cosmos do Azure

Um contentor do Cosmos do Azure suporta as seguintes operações através de qualquer uma das APIs do Cosmos do Azure.

| **Operação** | **CLI do Azure** | **API DE SQL** | **API para Cassandra** | **API do Azure Cosmos DB para o MongoDB** | **API do gremlin** | **API de Tabela** |
| --- | --- | --- | --- | --- | --- | --- |
| Enumerar contentores numa base de dados | Sim* | Sim | Sim | Sim | ND | ND |
| Ler um contentor | Sim | Sim | Sim | Sim | ND | ND |
| Criar novo contentor | Sim | Sim | Sim | Sim | ND | ND |
| Atualização do contentor | Sim | Sim | Sim | Sim | ND | ND |
| Eliminar contentor | Sim | Sim | Sim | Sim | ND | ND |

## <a name="azure-cosmos-items"></a>Itens do Cosmos do Azure

Consoante a opção da API, um item do Cosmos do Azure pode representar qualquer um de um documento numa coleção, uma linha numa tabela ou uma nó de extremidade num gráfico. A tabela seguinte mostra o mapeamento entre entidades de API específica para um item do Cosmos do Azure:

| **Entidade de cosmos** | **API DE SQL** | **API para Cassandra** | **API do Azure Cosmos DB para o MongoDB** | **API do gremlin** | **API de Tabela** |
| --- | --- | --- | --- | --- | --- |
|Item do Cosmos do Azure | Documento | Linha | Documento | Nó ou de extremidade | Item |

### <a name="properties-of-an-item"></a>Propriedades de um item

Todos os itens do Cosmos do Azure tem as seguintes propriedades do sistema definido. Consoante a opção da API, alguns deles podem não ser exposto diretamente.

|**Propriedade de sistema definido** | **Sistema gerado ou utilizador definível**| **Objetivo** | **API DE SQL** | **API para Cassandra** | **API do Azure Cosmos DB para o MongoDB** | **API do gremlin** | **API de Tabela** |
| --- | --- | --- | --- | --- | --- | --- | --- |
|_id | Gerada pelo sistema | Identificador exclusivo do item | Sim | Não | Não | Não | Não |
|_etag | Gerada pelo sistema | Etiqueta de entidade utilizada para controlo de simultaneidade otimista | Sim | Não | Não | Não | Não |
|_ts | Gerada pelo sistema | Última timestamp atualizado do item | Sim | Não | Não | Não | Não |
|_self | Gerada pelo sistema | URI endereçável do item | Sim | Não | Não | Não | Não |
|ID | Qualquer um dos | Definido pelo utilizador nome exclusivo dentro de uma partição lógica. Se o utilizador não especifica o id, o sistema irá gerar automaticamente um. | Sim | Sim | Sim | Sim | Sim |
|Arbitrárias propriedades definidas pelo utilizador | Definidas pelo utilizador | Propriedades definidas pelo utilizador representadas na representação de API nativa (JSON, BSON, CQL, etc.) | Sim | Sim | Sim | Sim | Sim |

### <a name="operations-on-items"></a>Operações nos itens

Item do Cosmos do Azure suporta as seguintes operações que podem ser efetuadas através de qualquer uma das APIs do Cosmos do Azure.

| **Operação** | **CLI do Azure** | **API DE SQL** | **API para Cassandra** | **API do Azure Cosmos DB para o MongoDB** | **API do gremlin** | **API de Tabela** |
| --- | --- | --- | --- | --- | --- | --- |
| Inserir, substituir, eliminar, Upsert, leitura | Não | Sim | Sim | Sim | Sim | Sim |

## <a name="next-steps"></a>Passos Seguintes

Agora, pode avançar para saber como aprovisionar o débito na conta do Cosmos do Azure ou veja outros conceitos:

* [Como configurar o débito aprovisionado numa base de dados do Cosmos do Azure](how-to-provision-database-throughput.md)
* [Como configurar o débito aprovisionado num contentor do Cosmos do Azure](how-to-provision-container-throughput.md)
* [Partições lógicas](partition-data.md)
* [Como configurar o TTL num contentor do Cosmos do Azure](how-to-time-to-live.md)
* [Como criar aplicações reativas com o Feed de alterações](change-feed.md)
* [Como configurar a restrição de chave exclusiva no seu contentor do Cosmos do Azure](unique-keys.md)
