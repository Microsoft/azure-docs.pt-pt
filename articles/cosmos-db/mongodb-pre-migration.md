---
title: Etapas de pré-migração para a migração de dados para a API da Azure Cosmos DB para a MongoDB
description: Este doc fornece uma visão geral dos pré-requisitos para uma migração de dados de MongoDB para Cosmos DB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 09/01/2020
ms.author: chrande
ms.openlocfilehash: 337341daf0e092def639a4e8f6fc8ee0a9b57c75
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349423"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Etapas de pré-migração para migrações de dados de MongoDB para Azure Cosmos DB's API para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Antes de migrar os seus dados de MongoDB (no local ou na nuvem) para a API da Azure Cosmos DB para a MongoDB, deve:

1. [Leia as principais considerações sobre a utilização da API da Azure Cosmos para a MongoDB](#considerations)
2. [Escolha uma opção para migrar os seus dados](#options)
3. [Estimeça a produção necessária para as suas cargas de trabalho](#estimate-throughput)
4. [Escolha uma chave de partição ideal para os seus dados](#partitioning)
5. [Compreenda a política de indexação que pode definir nos seus dados](#indexing)

Se já completou os pré-requisitos acima referidos para a migração, pode [migrar dados mongoDB para a API da Azure Cosmos DB para a MongoDB utilizando o Serviço de Migração da Base de Dados Azure](../dms/tutorial-mongodb-cosmos-db.md). Além disso, se não criou uma conta, pode navegar em qualquer um dos [Quickstarts](create-mongodb-dotnet.md) que mostram os passos para criar uma conta.

## <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a><a id="considerations"></a>Considerações ao utilizar a API da Azure Cosmos para a MongoDB

Seguem-se características específicas sobre a API da Azure Cosmos DB para a MongoDB:

- **Modelo de capacidade**: A capacidade de base de dados na Azure Cosmos DB baseia-se num modelo baseado em produção. Este modelo baseia-se em [Unidades de Pedido por segundo,](request-units.md)que é uma unidade que representa o número de operações de base de dados que podem ser executadas contra uma recolha por segundo. Esta capacidade pode ser atribuída a [uma base de dados ou a um nível de recolha,](set-throughput.md)podendo ser alocado num modelo de atribuição, ou utilizando o [rendimento de escala automática.](provision-throughput-autoscale.md)

- **Unidades de pedido**: Cada operação de base de dados tem um custo associado de Unidades de Pedido (RUs) em Azure Cosmos DB. Quando executado, este é subtraído do nível de unidades de pedido disponíveis num dado segundo. Se um pedido requer mais RUs do que o RU/s atualmente atribuído, existem duas opções para resolver o problema - aumentar a quantidade de RUs, ou esperar até o segundo seguinte começar e, em seguida, voltar a tentar a operação.

- **Capacidade elástica**: A capacidade de uma determinada recolha ou base de dados pode ser alterada a qualquer momento. Isto permite que a base de dados se adapte elasticamente aos requisitos de produção da sua carga de trabalho.

- **Fragmento automático**: Azure Cosmos DB fornece um sistema de partição automático que requer apenas um fragmento (ou uma chave de partição). O [mecanismo de partição automática](partitioning-overview.md) é partilhado em todas as APIs DB do Azure Cosmos e permite dados sem emenda e ao longo da escala através da distribuição horizontal.

## <a name="migration-options-for-azure-cosmos-dbs-api-for-mongodb"></a><a id="options"></a>Opções de migração para a API da Azure Cosmos DB para o MongoDB

O [Azure Database Migration Service for Azure Cosmos DB's API for MongoDB](../dms/tutorial-mongodb-cosmos-db.md) fornece um mecanismo que simplifica a migração de dados, fornecendo uma plataforma de hospedagem totalmente gerida, opções de monitorização da migração e manuseamento automático de estrangulamento. A lista completa de opções são as seguintes:

|**Tipo de migração**|**Solução**|**Considerações**|
|---------|---------|---------|
|Online|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Faz uso da biblioteca de executor a granel Azure Cosmos DB <br/>&bull; Adequado para grandes conjuntos de dados e cuida de replicar alterações ao vivo <br/>&bull; Funciona apenas com outras fontes mongoDB|
|Offline|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Faz uso da biblioteca de executor a granel Azure Cosmos DB <br/>&bull; Adequado para grandes conjuntos de dados e cuida de replicar alterações ao vivo <br/>&bull; Funciona apenas com outras fontes mongoDB|
|Offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)|&bull; Fácil de configurar e suporta múltiplas fontes <br/>&bull; Faz uso da biblioteca de executor a granel Azure Cosmos DB <br/>&bull; Adequado para grandes conjuntos de dados <br/>&bull; A falta de controlo significa que qualquer problema durante a migração exigiria um reinício de todo o processo de migração<br/>&bull; A falta de uma fila de cartas mortas significaria que alguns ficheiros erróneos poderiam parar todo o processo de migração. <br/>&bull; Precisa de código personalizado para aumentar a produção de leitura para determinadas fontes de dados|
|Offline|[Ferramentas mongo existentes (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; Fácil de configurar e integração <br/>&bull; Precisa de manuseamento personalizado para aceleradores|

## <a name="estimate-the-throughput-need-for-your-workloads"></a><a id="estimate-throughput"></a> Estimeça a necessidade de produção para as suas cargas de trabalho

Em Azure Cosmos DB, a produção é provisida com antecedência e é medida em Unidades de Pedido (RU's) por segundo. Ao contrário de VMs ou servidores no local, as RUs são fáceis de escalar para cima e para baixo a qualquer momento. Pode alterar instantaneamente o número de RUs provisões. Para mais informações, consulte [unidades request in Azure Cosmos DB](request-units.md).

Pode utilizar a Calculadora de Capacidade DB do [Azure Cosmos](https://cosmos.azure.com/capacitycalculator/) para determinar a quantidade de Unidades de Pedido com base na configuração da sua conta de base de dados, quantidade de dados, tamanho do documento e leituras e escritos necessários por segundo.

Seguem-se factores-chave que afetam o número de RUs necessários:
- **Tamanho** do documento : À medida que o tamanho de um item/documento aumenta, o número de RUs consumidos para ler ou escrever o item/documento também aumenta.

- **Contagem de propriedades documentadas**:O número de RUs consumidos para criar ou atualizar um documento está relacionado com o número, complexidade e duração das suas propriedades. Pode reduzir o consumo unitário de pedido para operações de escrita [limitando o número de propriedades indexadas](mongodb-indexing.md).

- **Padrões de consulta**: A complexidade de uma consulta afeta quantas unidades de pedido são consumidas pela consulta. 

A melhor maneira de entender o custo das consultas é usar dados de amostra em Azure Cosmos DB, [e executar consultas de amostra da MongoDB Shell](connect-mongodb-account.md) usando o comando para obter a taxa de `getLastRequestStastistics` pedido, que irá obter o número de RUs consumidos:

`db.runCommand({getLastRequestStatistics: 1})`

Este comando irá desausar um documento JSON semelhante ao seguinte:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

Também pode utilizar [as definições de diagnóstico](cosmosdb-monitor-resource-logs.md) para entender a frequência e os padrões das consultas executadas contra a Azure Cosmos DB. Os resultados dos registos de diagnóstico podem ser enviados para uma conta de armazenamento, uma instância EventHub ou [Azure Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md).  

## <a name="choose-your-partition-key"></a><a id="partitioning"></a>Escolha a sua chave de partição
A partilha, também conhecida como Sharding, é um ponto chave de consideração antes de migrar dados. A Azure Cosmos DB utiliza divisórias totalmente geridas para aumentar a capacidade numa base de dados para satisfazer os requisitos de armazenamento e produção. Esta funcionalidade não necessita do anfitrião ou configuração de servidores de encaminhamento.   

De forma semelhante, a capacidade de partição adiciona automaticamente capacidade e reequilibra os dados em conformidade. Para mais detalhes e recomendações sobre a escolha da chave de partição certa para os seus dados, consulte o [artigo Escolha uma Chave de Partição](partitioning-overview.md#choose-partitionkey). 

## <a name="index-your-data"></a><a id="indexing"></a>Indexar os seus dados

A API do Azure Cosmos DB para a versão 3.6 do servidor MongoDB indexa automaticamente o `_id` campo. Este campo não pode ser abandonado. Impõe automaticamente a singularidade do `_id` campo por chave de fragmentos. Para indexar campos adicionais, aplique os comandos de gestão de índices do MongoDB. Esta política de indexação predefinida difere da API SQL do Azure Cosmos DB, que indexa todos os campos por predefinição.

As capacidades de indexação fornecidas pela Azure Cosmos DB incluem a adição de índices compostos, índices únicos e índices time-to-live (TTL). A interface de gestão de índices está mapeada para o `createIndex()` comando. Saiba mais na [Indexing in Azure Cosmos DB's API para artigo da MongoDB.](mongodb-indexing.md)

[O Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) migra automaticamente as coleções MongoDB com índices únicos. No entanto, os índices únicos devem ser criados antes da migração. A Azure Cosmos DB não suporta a criação de índices únicos, quando já existem dados nas suas coleções. Para mais informações, consulte [as teclas Únicas em Azure Cosmos DB](unique-keys.md).

## <a name="next-steps"></a>Passos seguintes
* [Migrar os seus dados mongoDB para a Cosmos DB utilizando o Serviço de Migração da Base de Dados.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Produção de provisão em contentores e bases de dados da Azure Cosmos](set-throughput.md)
* [Criação de partições no Azure Cosmos DB](partitioning-overview.md)
* [Distribuição Global em Azure Cosmos DB](distribute-data-globally.md)
* [Indexação no Azure Cosmos DB](index-overview.md)
* [Unidades de Pedido no Azure Cosmos DB](request-units.md)