---
title: Etapas de pré-migração para migração de dados para a API do Azure Cosmos DB para MongoDB
description: Este documento fornece uma visão geral dos pré-requisitos para uma migração de dados do MongoDB para Cosmos DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: lbosq
ms.openlocfilehash: ef3d56b4ec7e4dbe5f6f4097fdd5d8d125b074dc
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75932294"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Etapas de pré-migração para migrações de dados do MongoDB para a API do Azure Cosmos DB para o MongoDB

Antes de migrar seus dados do MongoDB (local ou na nuvem) para a API de Azure Cosmos DB para MongoDB, você deve:

1. [Leia as principais considerações sobre como usar a API do Azure Cosmos DB para MongoDB](#considerations)
2. [Escolha uma opção para migrar seus dados](#options)
3. [Estimar a taxa de transferência necessária para suas cargas de trabalho](#estimate-throughput)
4. [Escolha uma chave de partição ideal para seus dados](#partitioning)
5. [Entender a política de indexação que você pode definir em seus dados](#indexing)

Se você já tiver concluído os pré-requisitos acima para a migração, poderá [migrar dados do MongoDB para a API do Azure Cosmos DB para MongoDB usando o serviço de migração de banco de dados do Azure](../dms/tutorial-mongodb-cosmos-db.md). Além disso, se você não tiver criado uma conta, poderá procurar qualquer um dos [guias de início rápido](create-mongodb-dotnet.md).

## <a id="considerations"></a>Principais considerações ao usar a API do Azure Cosmos DB para MongoDB

Veja a seguir características específicas sobre a API do Azure Cosmos DB para MongoDB:
- **Modelo de capacidade**: a capacidade do banco de dados no Azure Cosmos DB é baseada em um modelo baseado em taxa de transferência. Esse modelo é baseado em [unidades de solicitação por segundo](request-units.md), que é uma unidade que representa o número de operações de banco de dados que podem ser executadas em uma coleção por segundo. Essa capacidade pode ser alocada em [um nível de banco de dados ou de coleção](set-throughput.md), e pode ser provisionada em um modelo de alocação ou usando o [modelo de piloto automático](provision-throughput-autopilot.md).
- **Unidades de solicitação**: cada operação de banco de dados tem um custo de RUs (unidades de solicitação) associadas em Azure Cosmos DB. Quando executado, isso é subtraído do nível de unidades de solicitação disponíveis em um determinado segundo. Se uma solicitação exigir mais RUs do que o atualmente alocado, as duas opções estarão aumentando a quantidade de RUs ou aguardando até que o próximo segundo inicie e, em seguida, repita a operação.
- **Capacidade elástica**: a capacidade de uma determinada coleção ou banco de dados pode ser alterada a qualquer momento. Isso permite que o banco de dados se adapte de forma elástica aos requisitos de taxa de transferência de sua carga de trabalho.
- **Fragmentação automática**: o Azure Cosmos DB fornece um sistema de particionamento automático que requer apenas uma chave de fragmento (ou particionamento). O [mecanismo de particionamento automático](partition-data.md) é compartilhado entre todas as APIs de Azure Cosmos DB e permite dados contínuos e ao longo do dimensionamento por meio da distribuição horizontal.

## <a id="options"></a>Opções de migração para a API do Azure Cosmos DB para MongoDB

O serviço de migração de banco de dados do [Azure para a API do Azure Cosmos DB para MongoDB](../dms/tutorial-mongodb-cosmos-db.md) fornece um mecanismo que simplifica a migração do dado fornecendo uma plataforma de hospedagem totalmente gerenciada, opções de monitoramento de migração e manipulação de limitação automática. A lista completa de opções é a seguinte:

|**Tipo de migração**|**Solução**|**Considerações**|
|---------|---------|---------|
|Offline|[Ferramenta de Migração de Dados](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; fácil de configurar e dar suporte a várias fontes <br/>&bull; não é adequado para grandes conjuntos de altos.|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; fácil de configurar e dar suporte a várias fontes <br/>&bull; usa a biblioteca de executores em massa Azure Cosmos DB <br/>&bull; adequada para conjuntos de grandes volumes <br/>&bull; falta de ponto de verificação significa que qualquer problema durante o curso de migração exigiria uma reinicialização do processo de migração inteiro<br/>&bull; falta de uma fila de mensagens mortas significa que alguns arquivos errados poderiam parar todo o processo de migração <br/>&bull; precisa de código personalizado para aumentar a taxa de transferência de leitura para determinadas fontes de dados|
|Offline|[Ferramentas de Mongo existentes (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; fácil de configurar e integrar <br/>&bull; precisa de manipulação personalizada para limitação|
|Online|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; serviço de migração totalmente gerenciado.<br/>o &bull; fornece soluções de hospedagem e monitoramento para a tarefa de migração. <br/>&bull; adequada para conjuntos de grandes volumes e cuida da replicação de alterações dinâmicas <br/>&bull; funciona somente com outras fontes do MongoDB|


## <a id="estimate-throughput"></a>Estimar a necessidade de taxa de transferência para suas cargas de trabalho

Em Azure Cosmos DB, a taxa de transferência é provisionada antecipadamente e é medida em unidades de solicitação (RU) por segundo. Diferentemente das VMs ou servidores locais, o RUs é fácil de escalar e reduzir verticalmente a qualquer momento. Você pode alterar o número de RUs provisionadas instantaneamente. Para obter mais informações, consulte [unidades de solicitação no Azure Cosmos DB](request-units.md).

Você pode usar a [calculadora de capacidade de Azure Cosmos DB](https://cosmos.azure.com/capacitycalculator/) para determinar a quantidade de unidades de solicitação com base em sua configuração de conta de banco de dados, quantidade, tamanho do documento e leituras e gravações necessárias por segundo.

Veja a seguir os principais fatores que afetam o número de RUs necessário:
- **Tamanho do documento**: à medida que o tamanho de um item/documento aumenta, o número de RUs consumidas para ler ou gravar o item/documento também aumenta.
- **Contagem de propriedades do documento**: o número de RUs consumidas para criar ou atualizar um documento está relacionado ao número, à complexidade e ao comprimento de suas propriedades. Você pode reduzir o consumo de unidade de solicitação para operações de gravação [limitando o número de propriedades indexadas](mongodb-indexing.md).
- **Padrões de consulta**: a complexidade de uma consulta afeta quantas unidades de solicitação são consumidas pela consulta. 

A melhor maneira de entender o custo das consultas é usar dados de exemplo em Azure Cosmos DB [e executar consultas de exemplo do shell do MongoDB](connect-mongodb-account.md) usando o comando `getLastRequestStastistics` para obter o encargo da solicitação, o que produzirá o número de RUs consumidas:

`db.runCommand({getLastRequestStatistics: 1})`

Esse comando produzirá um documento JSON semelhante ao seguinte:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Você também pode usar as [configurações de diagnóstico](cosmosdb-monitor-resource-logs.md) para entender a frequência e os padrões das consultas executadas em relação à Azure Cosmos DB. Os resultados dos logs de diagnóstico podem ser enviados para uma conta de armazenamento, uma instância do EventHub ou [log Analytics do Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).  

## <a id="partitioning"></a>Escolha sua chave de partição
O particionamento, também conhecido como fragmentação, é um ponto-chave de consideração antes da migração de dados. O Azure Cosmos DB usa particionamento totalmente gerenciado para aumentar a capacidade em um banco de dados para atender aos requisitos de armazenamento e taxa de transferência. Esse recurso não precisa de hospedagem ou configuração de servidores de roteamento.   

De maneira semelhante, o recurso de particionamento adiciona automaticamente a capacidade e reequilibra os dados de forma adequada. Para obter detalhes e recomendações sobre como escolher a chave de partição correta para seus dados, consulte o [artigo escolhendo uma chave de partição](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a id="indexing"></a>Indexar seus dados
Por padrão, Azure Cosmos DB fornece indexação automática em todos os dados inseridos. Os recursos de indexação fornecidos pelo Azure Cosmos DB incluem a adição de índices compostos, índices exclusivos e índices TTL (vida útil). A interface de gerenciamento de índice é mapeada para o comando `createIndex()`. Saiba mais na [indexação na API do Azure Cosmos DB para MongoDB](mongodb-indexing.md).

O [serviço de migração de banco de dados do Azure](../dms/tutorial-mongodb-cosmos-db.md) migra coleções do MongoDB automaticamente com índices exclusivos. No entanto, os índices exclusivos devem ser criados antes da migração. Azure Cosmos DB não oferece suporte à criação de índices exclusivos, quando já existem dados em suas coleções. Para obter mais informações, consulte [chaves exclusivas em Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Passos seguintes
* [Migre seus dados do MongoDB para Cosmos DB usando o serviço de migração de banco de dado.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Provisionar a taxa de transferência em bancos de dados e contêineres Cosmos do Azure](set-throughput.md)
* [Criação de partições no Azure Cosmos DB](partition-data.md)
* [Distribuição global no Azure Cosmos DB](distribute-data-globally.md)
* [Indexação no Azure Cosmos DB](index-overview.md)
* [Request Units in Azure Cosmos DB](request-units.md) (Unidades de Pedido no Azure Cosmos DB)
