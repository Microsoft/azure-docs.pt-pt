---
title: Etapas de pré-migração para migração de dados para a API do Azure Cosmos DB para MongoDB
description: Este documento fornece uma visão geral dos pré-requisitos para uma migração de dados do MongoDB para Cosmos DB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: roaror
ms.openlocfilehash: 4dc7038d0ff5180f15a43268fd3f3aa0cbb0c7a0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445202"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Etapas de pré-migração para migrações de dados do MongoDB para a API do Azure Cosmos DB para o MongoDB

Antes de migrar seus dados do MongoDB (local ou na nuvem) para a API do Azure Cosmos DB para MongoDB, você deve:

1. [Criar uma conta do Azure Cosmos DB](#create-account)
2. [Estimar a taxa de transferência necessária para suas cargas de trabalho](#estimate-throughput)
3. [Escolha uma chave de partição ideal para seus dados](#partitioning)
4. [Entender a política de indexação que você pode definir em seus dados](#indexing)

Se você já tiver concluído os pré-requisitos acima para a migração, consulte os [dados migrar MongoDB para a API do Azure Cosmos DB para MongoDB](../dms/tutorial-mongodb-cosmos-db.md) para as instruções de migração de dados reais. Caso contrário, este documento fornece instruções para lidar com esses pré-requisitos. 

## <a id="create-account"></a>Criar uma conta de Azure Cosmos DB 

Antes de iniciar a migração, você precisa [criar uma conta do Azure Cosmos usando a API do Azure Cosmos DB para MongoDB](create-mongodb-dotnet.md). 

Na criação da conta, você pode escolher as configurações para [distribuir globalmente](distribute-data-globally.md) seus dados. Você também tem a opção de habilitar gravações de várias regiões (ou configuração de vários mestres), que permite que cada uma de suas regiões seja uma região de gravação e leitura.

![Criação de conta](./media/mongodb-pre-migration/account-creation.png)

## <a id="estimate-throughput"></a>Estimar a necessidade de taxa de transferência para suas cargas de trabalho

Antes de iniciar a migração usando o [serviço de migração de banco de dados (DMS)](../dms/dms-overview.md), você deve estimar a quantidade de taxa de transferência a ser provisionada para seus bancos de dados e coleções do Azure Cosmos.

A taxa de transferência pode ser provisionada em:

- Coleção

- Base de Dados

> [!NOTE]
> Você também pode ter uma combinação dos itens acima, em que algumas coleções em um banco de dados podem ter uma taxa de transferência provisionada dedicada e outras podem compartilhar a taxa de transferência. Para obter detalhes, consulte a página [definir taxa de transferência em um banco de dados e um contêiner](set-throughput.md) .
>

Primeiro, você deve decidir se deseja provisionar a taxa de transferência de nível de banco de dados ou de coleção ou uma combinação de ambos. Em geral, é recomendável configurar uma taxa de transferência dedicada no nível da coleção. O provisionamento de taxa de transferência no nível de banco de dados permite que as coleções em seu banco de dados compartilhem a taxa de transferência provisionada. Com a taxa de transferência compartilhada, no entanto, não há nenhuma garantia para uma taxa de transferência específica em cada coleção individual e você não obtém um desempenho previsível em nenhuma coleção específica.

Se você não tiver certeza quanto à taxa de transferência deve ser dedicada a cada coleção individual, poderá escolher a taxa de transferência no nível do banco de dados. Você pode considerar a taxa de transferência provisionada configurada em seu banco de dados Cosmos do Azure como um equivalente lógico ao da capacidade de computação de uma VM do MongoDB ou de um servidor físico, mas mais econômico com a capacidade de dimensionar de forma elástica. Para obter mais informações, consulte [provisionar taxa de transferência em bancos de dados e contêineres do Azure Cosmos](set-throughput.md).

Se você provisionar a taxa de transferência no nível do banco de dados, todas as coleções criadas nesse banco de dados deverão ser criadas com uma chave de partição/fragmento. Para obter mais informações sobre particionamento, consulte [particionamento e dimensionamento horizontal em Azure Cosmos DB](partition-data.md). Se você não especificar uma chave de partição/fragmento durante a migração, o serviço de migração de banco de dados do Azure preencherá automaticamente o campo de chave de fragmento com um atributo *_id* que é gerado automaticamente para cada documento.

### <a name="optimal-number-of-request-units-rus-to-provision"></a>Número ideal de RUs (unidades de solicitação) a serem provisionadas

Em Azure Cosmos DB, a taxa de transferência é provisionada antecipadamente e é medida em unidades de solicitação (RU) por segundo. Se você tiver cargas de trabalho que executam o MongoDB em uma VM ou local, considere o RU como uma abstração simples para recursos físicos, como para o tamanho de uma VM ou um servidor local e os recursos que eles possuem, por exemplo, memória, CPU, IOPs. 

Diferentemente das VMs ou servidores locais, o RUs é fácil de escalar e reduzir verticalmente a qualquer momento. Você pode alterar o número de RUs provisionadas em segundos e você será cobrado apenas pelo número máximo de RUs que você provisiona para um determinado período de uma hora. Para obter mais informações, consulte [unidades de solicitação no Azure Cosmos DB](request-units.md).

Veja a seguir os principais fatores que afetam o número de RUs necessário:
- **Tamanho do item (ou seja, documento)** : à medida que o tamanho de um item/documento aumenta, o número de RUs consumidas para ler ou gravar o item/documento também aumenta.
- **Contagem de propriedades do item**: assumindo a [indexação padrão](index-overview.md) em todas as propriedades, o número de RUs consumidas para gravar um item aumenta conforme a contagem de propriedades do item aumenta. Você pode reduzir o consumo de unidade de solicitação para operações de gravação [limitando o número de propriedades indexadas](index-policy.md).
- **Operações simultâneas**: as unidades de solicitação consumidas também dependem da frequência com que diferentes operações CRUD (como gravações, leituras, atualizações, exclusões) e consultas mais complexas são executadas. Você pode usar o [mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/) para gerar as necessidades de simultaneidade dos dados atuais do MongoDB.
- **Padrões de consulta**: a complexidade de uma consulta afeta quantas unidades de solicitação são consumidas pela consulta.

Se você exportar arquivos JSON usando o [mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/) e entender quantas gravações, leituras, atualizações e exclusões ocorrem por segundo, você pode usar o [planejador de capacidade Azure Cosmos DB](https://www.documentdb.com/capacityplanner) para estimar o número inicial de RUs a serem provisionadas. O planejador de capacidade não calcula o custo de consultas mais complexas. Portanto, se você tiver consultas complexas em seus dados, o RUs adicional será consumido. A calculadora também pressupõe que todos os campos são indexados e a consistência da sessão é usada. A melhor maneira de entender o custo das consultas é migrar seus dados (ou dados de exemplo) para Azure Cosmos DB, [conectar-se ao ponto de extremidade do cosmos DB](connect-mongodb-account.md) e executar uma consulta de exemplo do shell do MongoDB usando o comando `getLastRequestStastistics` para obter o encargo da solicitação, o que produzirá o número de RUs consumidas:

`db.runCommand({getLastRequestStatistics: 1})`

Esse comando produzirá um documento JSON semelhante ao seguinte:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Depois de entender o número de RUs consumidas por uma consulta e as necessidades de simultaneidade para essa consulta, você pode ajustar o número de RUs provisionadas. A otimização de RUs não é um evento único – você deve otimizar ou escalar verticalmente o RUs provisionado, dependendo se você não está esperando um tráfego pesado, em vez de uma carga de trabalho pesada ou de importar dados.

## <a id="partitioning"></a>Escolha sua chave de partição
O particionamento é um ponto-chave de consideração antes de migrar para um banco de dados distribuído globalmente, como Azure Cosmos DB. O Azure Cosmos DB usa o particionamento para dimensionar contêineres individuais em um banco de dados para atender às necessidades de escalabilidade e desempenho do seu aplicativo. No particionamento, os itens em um contêiner são divididos em subconjuntos distintos chamados partições lógicas. Para obter detalhes e recomendações sobre como escolher a chave de partição correta para seus dados, consulte a [seção escolhendo uma chave de partição](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a id="indexing"></a>Indexar seus dados
Por padrão, Azure Cosmos DB indexa todos os campos de dados na ingestão. Você pode modificar a [política de indexação](index-policy.md) em Azure Cosmos dB a qualquer momento. Na verdade, geralmente é recomendável desativar a indexação ao migrar dados e, em seguida, ativá-la novamente quando os dados já estiverem em Cosmos DB. Para obter mais detalhes sobre a indexação, você pode ler mais sobre isso na seção [indexação no Azure Cosmos DB](index-overview.md) . 

O [serviço de migração de banco de dados do Azure](../dms/tutorial-mongodb-cosmos-db.md) migra coleções do MongoDB automaticamente com índices exclusivos. No entanto, os índices exclusivos devem ser criados antes da migração. Azure Cosmos DB não oferece suporte à criação de índices exclusivos, quando já existem dados em suas coleções. Para obter mais informações, consulte [chaves exclusivas em Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Passos seguintes
* [Migre seus dados do MongoDB para Cosmos DB usando o serviço de migração de banco de dado.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Provisionar a taxa de transferência em bancos de dados e contêineres Cosmos do Azure](set-throughput.md)
* [Criação de partições no Azure Cosmos DB](partition-data.md)
* [Distribuição global no Azure Cosmos DB](distribute-data-globally.md)
* [Indexação no Azure Cosmos DB](index-overview.md)
* [Request Units in Azure Cosmos DB](request-units.md) (Unidades de Pedido no Azure Cosmos DB)
