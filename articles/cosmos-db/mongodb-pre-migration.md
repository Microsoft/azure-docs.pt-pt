---
title: Etapas de pré-migração para a migração de dados para a API do Azure Cosmos DB para MongoDB
description: Este doc fornece uma visão geral dos pré-requisitos para uma migração de dados de MongoDB para Cosmos DB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: lbosq
ms.openlocfilehash: 73ac1a6ffd5fc2b2d52f169e1e0332044638f9f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75942085"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Etapas de pré-migração para migrações de dados de MongoDB para API da Azure Cosmos DB para MongoDB

Antes de migrar os seus dados do MongoDB (no local ou na nuvem) para a API da Azure Cosmos DB para o MongoDB, deve:

1. [Leia as principais considerações sobre a utilização da API da Azure Cosmos DB para o MongoDB](#considerations)
2. [Escolha uma opção para migrar os seus dados](#options)
3. [Calcule o resultado necessário para as suas cargas de trabalho](#estimate-throughput)
4. [Escolha uma chave de partição ideal para os seus dados](#partitioning)
5. [Compreenda a política de indexação que pode definir nos seus dados](#indexing)

Se já preencheu os pré-requisitos acima referidos para a migração, pode migrar dados do [MongoDB para a API da Azure Cosmos DB para mongoDB utilizando o Serviço](../dms/tutorial-mongodb-cosmos-db.md)de Migração de Bases de Dados Azure. Além disso, se não criou uma conta, pode navegar em qualquer um dos [Quickstarts](create-mongodb-dotnet.md) que mostrem os passos para criar uma conta.

## <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a><a id="considerations"></a>Considerações ao utilizar a API da Azure Cosmos DB para o MongoDB

Seguem-se características específicas sobre a API da Azure Cosmos DB para o MongoDB:

- **Modelo de capacidade**: A capacidade da base de dados no Azure Cosmos DB baseia-se num modelo baseado em despesas. Este modelo baseia-se em [Unidades de Pedido por segundo,](request-units.md)que é uma unidade que representa o número de operações de base de dados que podem ser executadas contra uma recolha por segundo. Esta capacidade pode ser atribuída a nível de base de [dados ou de recolha,](set-throughput.md)podendo ser aprovisionada num modelo de atribuição, ou utilizando o modelo [AutoPilot](provision-throughput-autopilot.md).

- **Unidades de Pedido**: Cada operação de base de dados tem um custo associado das Unidades de Pedido (RUs) em Azure Cosmos DB. Quando executado, este é subtraído do nível de unidades de pedido disponíveis em um segundo. Se um pedido requer mais RUs do que os RU/s atualmente atribuídos, existem duas opções para resolver o problema - aumentar a quantidade de RUs, ou esperar até que o segundo seguinte comece e, em seguida, rejulgar a operação.

- **Capacidade elástica**: A capacidade de uma determinada recolha ou base de dados pode mudar a qualquer momento. Isto permite que a base de dados se adapte elástico aos requisitos de entrada da sua carga de trabalho.

- **Sharding automático**: O Azure Cosmos DB fornece um sistema de partição automático que requer apenas um fragmento (ou uma chave de partição). O [mecanismo automático de partição](partition-data.md) é partilhado em todas as APIs DB do Azure Cosmos e permite dados sem emenda e ao longo da escala através da distribuição horizontal.

## <a name="migration-options-for-azure-cosmos-dbs-api-for-mongodb"></a><a id="options"></a>Opções de migração para API do Azure Cosmos DB para MongoDB

O Serviço de Migração de Bases de Dados Azure para a [API da Azure Cosmos DB para o MongoDB](../dms/tutorial-mongodb-cosmos-db.md) fornece um mecanismo que simplifica a migração de dados, fornecendo uma plataforma de hospedagem totalmente gerida, opções de monitorização da migração e manipulação automática de estrangulamento. A lista completa de opções é a seguinte:

|**Tipo de migração**|**Solução**|**Considerações**|
|---------|---------|---------|
|Offline|[Ferramenta de migração de dados](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Fácil de configurar e suporta várias fontes <br/>&bull;Não é adequado para grandes conjuntos de dados.|
|Offline|[Fábrica de Dados Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Fácil de configurar e suporta várias fontes <br/>&bull;Faz uso da biblioteca de executora a granel Azure Cosmos DB <br/>&bull;Adequado para grandes conjuntos de dados <br/>&bull;A falta de controlo significa que qualquer problema durante o curso da migração exigiria um reinício de todo o processo de migração<br/>&bull;A falta de uma fila de cartas mortas significaria que alguns ficheiros erróneos poderiam parar todo o processo de migração. <br/>&bull;Precisa de código personalizado para aumentar a produção de leitura para determinadas fontes de dados|
|Offline|[Ferramentas Mongo existentes (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;Fácil de configurar e integrar <br/>&bull;Precisa de manuseamento personalizado para aceleradores|
|Online|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull;Serviço de migração totalmente gerido.<br/>&bull;Fornece soluções de hospedagem e monitorização para a tarefa de migração. <br/>&bull;Adequado para grandes conjuntos de dados e cuida da replicação de alterações ao vivo <br/>&bull;Trabalha apenas com outras fontes do MongoDB|


## <a name="estimate-the-throughput-need-for-your-workloads"></a><a id="estimate-throughput"></a>Calcule a necessidade de entrada para as suas cargas de trabalho

Em Azure Cosmos DB, a entrada é aprovisionada com antecedência e é medida em Unidades de Pedido (RU's) por segundo. Ao contrário de VMs ou servidores no local, as RUs são fáceis de escalar para cima e para baixo a qualquer momento. Pode alterar instantaneamente o número de RUs provisionados. Para mais informações, consulte [Unidades de Pedido em Azure Cosmos DB](request-units.md).

Pode utilizar a Calculadora de [Capacidade DB do Azure Cosmos](https://cosmos.azure.com/capacitycalculator/) para determinar a quantidade de Unidades de Pedido com base na configuração da sua conta de base de dados, quantidade de dados, tamanho do documento e leituras e escritos por segundo.

Seguem-se factores-chave que afetam o número de RUs necessários:
- **Tamanho**do documento : À medida que o tamanho de um item/documento aumenta, o número de RUs consumidos para ler ou escrever o item/documento também aumenta.

- **Contagem de propriedades documentais**:O número de RUs consumidos para criar ou atualizar um documento está relacionado com o número, complexidade e comprimento das suas propriedades. Pode reduzir o consumo unitário de pedidos para operações de escrita [limitando o número de propriedades indexadas](mongodb-indexing.md).

- **Padrões**de consulta : A complexidade de uma consulta afeta quantas unidades de pedido são consumidas pela consulta. 

A melhor maneira de compreender o custo das consultas é utilizar dados de amostras em Azure Cosmos DB, [e executar consultas de amostra da MongoDB Shell](connect-mongodb-account.md) usando o `getLastRequestStastistics` comando para obter a taxa de pedido, que irá obter o número de RUs consumidos:

`db.runCommand({getLastRequestStatistics: 1})`

Este comando irá forereum documento JSON semelhante ao seguinte:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Também pode utilizar [as definições](cosmosdb-monitor-resource-logs.md) de diagnóstico para entender a frequência e os padrões das consultas executadas contra o Azure Cosmos DB. Os resultados dos registos de diagnóstico podem ser enviados para uma conta de armazenamento, uma instância eventHub ou [Azure Log Analytics.](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)  

## <a name="choose-your-partition-key"></a><a id="partitioning"></a>Escolha a sua chave de partição
A partilha, também conhecida como Sharding, é um ponto chave de consideração antes de migrar dados. A Azure Cosmos DB utiliza divisórias totalmente geridas para aumentar a capacidade numa base de dados para satisfazer os requisitos de armazenamento e de entrada. Esta funcionalidade não necessita do alojamento ou configuração dos servidores de encaminhamento.   

Da mesma forma, a capacidade de partição adiciona automaticamente capacidade e reequilibra os dados em conformidade. Para mais detalhes e recomendações sobre a escolha da chave de partição certa para os seus dados, consulte o [artigo Chave de Escolha](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). 

## <a name="index-your-data"></a><a id="indexing"></a>Indexar os seus dados
Por padrão, o Azure Cosmos DB fornece indexação automática em todos os dados inseridos. As capacidades de indexação fornecidas pela Azure Cosmos DB incluem a adição de índices compósitos, índices únicos e índices de tempo-a-vivo (TTL). A interface de gestão de `createIndex()` índices está mapeada para o comando. Saiba mais na [Indexing in Azure Cosmos DB's API para MongoDB](mongodb-indexing.md).

O Serviço de Migração de Bases de [Dados Azure](../dms/tutorial-mongodb-cosmos-db.md) migra automaticamente coleções MongoDB com índices únicos. No entanto, os índices únicos devem ser criados antes da migração. A Azure Cosmos DB não suporta a criação de índices únicos, quando já existem dados nas suas coleções. Para mais informações, consulte [as chaves Unique em Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Passos seguintes
* [Emigra os seus dados mongoDB para cosmos DB utilizando o Serviço de Migração de Bases de Dados.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Provisão para recipientes e bases de dados Da Azure Cosmos](set-throughput.md)
* [Criação de partições no Azure Cosmos DB](partition-data.md)
* [Distribuição Global em Azure Cosmos DB](distribute-data-globally.md)
* [Indexação no Azure Cosmos DB](index-overview.md)
* [Request Units in Azure Cosmos DB](request-units.md) (Unidades de Pedido no Azure Cosmos DB)
