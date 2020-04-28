---
title: Trabalhe com bases de dados, contentores e itens em Azure Cosmos DB
description: Este artigo descreve como criar e usar bases de dados, contentores e itens em Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/24/2020
ms.reviewer: sngun
ms.openlocfilehash: b3874bbe7a5830b0a80b658ac32952fe8985c1c3
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82161695"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>Trabalhe com bases de dados, contentores e itens em Azure Cosmos DB

Depois de criar uma [conta Azure Cosmos DB](account-overview.md) sob a sua subscrição Azure, pode gerir dados na sua conta criando bases de dados, contentores e itens. Este artigo descreve cada uma dessas entidades. 

A imagem seguinte mostra a hierarquia de diferentes entidades numa conta Azure Cosmos DB:

![Entidades de conta Azure Cosmos](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Bases de dados do Azure Cosmos

Pode criar uma ou várias bases de dados azure cosmos na sua conta. Uma base de dados é análoga a um espaço de nome. Uma base de dados é a unidade de gestão de um conjunto de contentores Azure Cosmos. A tabela que se segue mostra como uma base de dados Azure Cosmos é mapeada para várias entidades específicas da API:

| Entidade Azure Cosmos | SQL API | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- |
|Base de dados Azure Cosmos | Base de Dados | Espaço-chave | Base de Dados | Base de Dados | ND |

> [!NOTE]
> Com as contas API da Tabela, quando cria a sua primeira tabela, uma base de dados padrão é criada automaticamente na sua conta Azure Cosmos.

### <a name="operations-on-an-azure-cosmos-database"></a>Operações numa base de dados Azure Cosmos

Pode interagir com uma base de dados Azure Cosmos com APIs Azure Cosmos conforme descrito na tabela seguinte:

| Operação | CLI do Azure | SQL API | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- |
|Enumerar todas as bases de dados| Sim | Sim | Sim (base de dados está mapeada para um espaço-chave) | Sim | ND | ND |
|Ler base de dados| Sim | Sim | Sim (base de dados está mapeada para um espaço-chave) | Sim | ND | ND |
|Criar nova base de dados| Sim | Sim | Sim (base de dados está mapeada para um espaço-chave) | Sim | ND | ND |
|Base de dados de atualizações| Sim | Sim | Sim (base de dados está mapeada para um espaço-chave) | Sim | ND | ND |


## <a name="azure-cosmos-containers"></a>Contentores do Azure Cosmos

Um recipiente Azure Cosmos é a unidade de escalabilidade tanto para o fornecimento de entrada e armazenamento. Um recipiente é horizontalmente dividido e depois replicado em várias regiões. Os itens que adiciona ao recipiente e a entrada que você disponibiliza nele são distribuídos automaticamente através de um conjunto de divisórias lógicas com base na chave de partição. Para saber mais sobre divisórias e chaves de partição, consulte [dados de partição](partition-data.md). 

Quando cria um recipiente Azure Cosmos, configura a entrada num dos seguintes modos:

* Modo de **produção aprovisionado dedicado**: A entrada disponibilizada num recipiente é exclusivamente reservada para esse contentor e é apoiada pelos SLAs. Para saber mais, veja [como fornecer a entrada num recipiente Azure Cosmos.](how-to-provision-container-throughput.md)

* Modo de **entrada aprovisionado partilhado**: Estes recipientes partilham a entrada aprovisionada com os outros contentores na mesma base de dados (excluindo os contentores que foram configurados com uma entrada disponibilizada dedicada). Por outras palavras, a entrada prevista na base de dados é partilhada entre todos os recipientes de "entrada partilhada". Para saber mais, consulte como fornecer a entrada numa base de [dados Azure Cosmos.](how-to-provision-database-throughput.md)

> [!NOTE]
> Só é possível configurar a entrada partilhada e dedicada apenas quando criar a base de dados e o recipiente. Para mudar do modo de débito dedicado para o modo de débito partilhado (e vice-versa) após o contentor ser criado, precisará de criar um novo contentor e migrar os dados para esse contentor. Pode migrar os dados utilizando a funcionalidade de alimentação de mudança de DB Do Azure Cosmos.

Um recipiente Azure Cosmos pode escalar elástico, quer crie recipientes utilizando modos de entrada de fornecimento dedicados ou partilhados.

Um recipiente Azure Cosmos é um recipiente de itens schema-agnósticos. Itens num recipiente podem ter esquemia arbitrária. Por exemplo, um item que represente uma pessoa e um item que represente um automóvel pode ser colocado no *mesmo recipiente*. Por padrão, todos os itens que adiciona a um recipiente são automaticamente indexados sem necessitar de uma gestão explícita de índice ou esquema. Pode personalizar o comportamento de indexação configurando a política de [indexação](index-overview.md) num recipiente. 

Você pode definir [Time to Live (TTL)](time-to-live.md) em itens selecionados em um recipiente Azure Cosmos ou para que todo o recipiente expurgar graciosamente esses itens do sistema. A Azure Cosmos DB elimina automaticamente os itens quando expiram. Também garante que uma consulta realizada no recipiente não devolve os itens expirados dentro de um limite fixo. Para saber mais, consulte [configure TTL no seu recipiente](how-to-time-to-live.md).

Pode utilizar [o feed](change-feed.md) de alteração para subscrever o registo de operações que é gerido para cada partição lógica do seu recipiente. A alteração do feed fornece o registo de todas as atualizações efetuadas no recipiente, juntamente com as imagens antes e posteriores dos itens. Para mais informações, consulte [Construir aplicações reativas utilizando](serverless-computing-database.md)o feed de mudança . Também pode configurar a duração da retenção para o feed de mudança utilizando a política de alimentação de mudança no recipiente.

Pode registar [procedimentos armazenados, gatilhos, funções definidas pelo utilizador (UDFs)](stored-procedures-triggers-udfs.md)e [fundir procedimentos](how-to-manage-conflicts.md) para o seu recipiente Azure Cosmos.

Pode especificar uma [restrição de chave única](unique-keys.md) no seu recipiente Azure Cosmos. Ao criar uma política-chave única, você garante a singularidade de um ou mais valores por chave de partição lógica. Se criar um recipiente utilizando uma política-chave única, não podem ser criados itens novos ou atualizados com valores que dupliquem os valores especificados pela restrição de chave única. Para saber mais, consulte [constrangimentos de chaves únicos.](unique-keys.md)

Um contentor Azure Cosmos é especializado em entidades específicas da API, como mostra a seguinte tabela:

| Entidade Azure Cosmos | SQL API | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- |
|Recipiente Azure Cosmos | Contentor | Tabela | Coleção | Graph | Tabela |

> [!NOTE]
> Ao criar recipientes, certifique-se de que não cria dois recipientes com o mesmo nome, mas invólucrodiferente. Isto porque algumas partes da plataforma Azure não são sensíveis a casos, o que pode resultar em confusão/colisão de telemetria e ações em contentores com tais nomes.

### <a name="properties-of-an-azure-cosmos-container"></a>Propriedades de um recipiente Azure Cosmos

Um recipiente Azure Cosmos tem um conjunto de propriedades definidas pelo sistema. Dependendo da API que utiliza, algumas propriedades podem não estar diretamente expostas. O quadro seguinte descreve a lista de propriedades definidas pelo sistema:

| Propriedade definida pelo sistema | Gerado pelo sistema ou configurável pelo utilizador | Objetivo | SQL API | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Gerado pelo sistema | Identificador único do recipiente | Sim | Não | Não | Não | Não |
|\_etag | Gerado pelo sistema | Etiqueta de entidade usada para controlo de moeda otimista | Sim | Não | Não | Não | Não |
|\_ts | Gerado pelo sistema | Última marca de tempo atualizada do recipiente | Sim | Não | Não | Não | Não |
|\_auto | Gerado pelo sistema | URI endereçado do recipiente | Sim | Não | Não | Não | Não |
|ID | Configurável pelo utilizador | Nome único definido pelo utilizador do recipiente | Sim | Sim | Sim | Sim | Sim |
|indexaçãoPolítica | Configurável pelo utilizador | Fornece a capacidade de alterar a trajetória do índice, o tipo de índice e o modo de índice | Sim | Não | Não | Não | Sim |
|TimeToLive | Configurável pelo utilizador | Fornece a capacidade de apagar automaticamente os itens de um recipiente após um período de tempo definido. Para mais detalhes, consulte [Time to Live](time-to-live.md). | Sim | Não | Não | Não | Sim |
|changeFeedPolicy | Configurável pelo utilizador | Costumava ler alterações feitas em itens num recipiente. Para mais detalhes, consulte [Alterar feed](change-feed.md). | Sim | Não | Não | Não | Sim |
|uniqueKeyPolicy | Configurável pelo utilizador | Usado para garantir a singularidade de um ou mais valores numa partição lógica. Para mais informações, consulte [constrangimentos de chaves únicos.](unique-keys.md) | Sim | Não | Não | Não | Sim |

### <a name="operations-on-an-azure-cosmos-container"></a>Operações num contentor Azure Cosmos

Um contentor Azure Cosmos suporta as seguintes operações quando utiliza qualquer uma das APIs do Cosmos Do Azure:

| Operação | CLI do Azure | SQL API | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- |
| Enumerar contentores numa base de dados | Sim | Sim | Sim | Sim | ND | ND |
| Leia um recipiente | Sim | Sim | Sim | Sim | ND | ND |
| Criar um novo recipiente | Sim | Sim | Sim | Sim | ND | ND |
| Atualizar um recipiente | Sim | Sim | Sim | Sim | ND | ND |
| Eliminar um contentor | Sim | Sim | Sim | Sim | ND | ND |

## <a name="azure-cosmos-items"></a>Itens Azure Cosmos

Dependendo da API que você usa, um item Azure Cosmos pode representar um documento em uma coleção, uma linha em uma mesa, ou um nó ou borda em um gráfico. A tabela seguinte mostra o mapeamento de entidades específicas da API para um item Azure Cosmos:

| Entidade Cosmos | SQL API | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- |
|Item Azure Cosmos | Documento | Linha | Documento | Nó ou borda | Item |

### <a name="properties-of-an-item"></a>Propriedades de um item

Cada item Azure Cosmos tem as seguintes propriedades definidas pelo sistema. Dependendo da API que usas, algumas delas podem não estar diretamente expostas.

| Propriedade definida pelo sistema | Gerado pelo sistema ou configurável pelo utilizador| Objetivo | SQL API | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Gerado pelo sistema | Identificador único do item | Sim | Não | Não | Não | Não |
|\_etag | Gerado pelo sistema | Etiqueta de entidade usada para controlo de moeda otimista | Sim | Não | Não | Não | Não |
|\_ts | Gerado pelo sistema | Carimbo de tempo da última atualização do item | Sim | Não | Não | Não | Não |
|\_auto | Gerado pelo sistema | URI endereçado do item | Sim | Não | Não | Não | Não |
|ID | Qualquer um | Nome único definido pelo utilizador numa partição lógica. | Sim | Sim | Sim | Sim | Sim |
|Propriedades arbitrárias definidas pelo utilizador | Definidas pelo utilizador | Propriedades definidas pelo utilizador representadas na representação nativa da API (incluindo JSON, BSON e CQL) | Sim | Sim | Sim | Sim | Sim |

> [!NOTE]
> A singularidade `id` da propriedade só é aplicada dentro de cada partição lógica. Vários documentos `id` podem ter a mesma propriedade com diferentes valores-chave de partição.

### <a name="operations-on-items"></a>Operações em itens

Os itens da Azure Cosmos suportam as seguintes operações. Você pode usar qualquer uma das APIs Azure Cosmos para realizar as operações.

| Operação | CLI do Azure | SQL API | API de Cassandra | API do Azure Cosmos DB para MongoDB | API do Gremlin | API de Tabela |
| --- | --- | --- | --- | --- | --- | --- |
| Inserir, Substituir, Excluir, Apagar, Ler | Não | Sim | Sim | Sim | Sim | Sim |

## <a name="next-steps"></a>Passos seguintes

Conheça estas tarefas e conceitos:

* [Provisão para uma base de dados Azure Cosmos](how-to-provision-database-throughput.md)
* [Provisão de entrada num recipiente Azure Cosmos](how-to-provision-container-throughput.md)
* [Trabalhar com divisórias lógicas](partition-data.md)
* [Configure TTL em um recipiente Azure Cosmos](how-to-time-to-live.md)
* [Construir aplicações reativas utilizando o feed de mudança](change-feed.md)
* [Configure uma restrição de chave única no seu recipiente Azure Cosmos](unique-keys.md)
