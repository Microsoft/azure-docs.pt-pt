---
title: Opções de migração cosmos DB
description: Este doc descreve as várias opções para migrar os seus dados no local ou na nuvem para o Azure Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 281cdea42975172b7e2366ab5d3e1606f61d5025
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020545"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Opções para migrar os seus dados no local ou na nuvem para O Azure Cosmos DB

Pode carregar dados de várias fontes de dados para o Azure Cosmos DB. Além disso, uma vez que o Azure Cosmos DB suporta várias APIs, os alvos podem ser qualquer uma das APIs existentes. De forma a apoiar os caminhos de migração das várias fontes para as diferentes APIs DB do Azure Cosmos, existem múltiplas soluções que fornecem um manuseamento especializado para cada caminho de migração. Este documento lista as soluções disponíveis e descreve as suas vantagens e limitações.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Fatores que afetam a escolha da ferramenta de migração

Os seguintes fatores determinam a escolha da ferramenta de migração:
* **Migração online vs offline**: Muitas ferramentas de migração fornecem um caminho para fazer uma migração única. Isto significa que as aplicações que acedem à base de dados podem experimentar um período de inatividade. Algumas soluções de migração fornecem uma forma de fazer uma migração viva onde existe um gasoduto de replicação criado entre a fonte e o alvo.

* **Fonte de dados**: Os dados existentes podem estar em várias fontes de dados como o Oracle DB2, Datastax Cassanda, Azure SQL Database, PostgreSQL, etc. Os dados também podem estar numa conta Azure Cosmos DB existente e a intenção da migração pode ser alterar o modelo de dados ou repartipartição dos dados num recipiente com uma chave de partição diferente.

* **Azure Cosmos DB API**: Para a API SQL em Azure Cosmos DB, existem uma variedade de ferramentas desenvolvidas pela equipa Azure Cosmos DB que ajudam nos diferentes cenários de migração. Todas as outras APIs têm o seu próprio conjunto especializado de ferramentas desenvolvidas e mantidas pela comunidade. Uma vez que a Azure Cosmos DB suporta estas APIs a nível de protocolo de arame, estas ferramentas devem funcionar como está enquanto migram dados para o Azure Cosmos DB também. No entanto, podem necessitar de manuseamento personalizado para aceleradores, uma vez que este conceito é específico do Azure Cosmos DB.

* **Tamanho dos dados**: A maioria das ferramentas de migração funciona muito bem para conjuntos de dados mais pequenos. Quando o conjunto de dados excede algumas centenas de gigabytes, as escolhas das ferramentas de migração são limitadas. 

* **Duração prevista da migração**: As migrações podem ser configuradas para ocorrer a um ritmo lento e incremental que consuma menos de entrada ou que possa consumir toda a entrada disponibilizada no recipiente Azure Cosmos DB e completar a migração em menos tempo.

## <a name="azure-cosmos-db-sql-api"></a>API SQL do Azure Cosmos DB
|**Tipo de migração**|**Solução**|**Considerações**|
|---------|---------|---------|
|Offline|[Ferramenta de migração de dados](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Fácil de configurar e suporta várias fontes <br/>&bull;Não é adequado para grandes conjuntos de dados|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Fácil de configurar e suporta várias fontes <br/>&bull;Faz uso da biblioteca de executora a granel Azure Cosmos DB <br/>&bull;Adequado para grandes conjuntos de dados <br/>&bull;Falta de controlo - Significa que se um problema ocorrer durante a migração, você precisa reiniciar todo o processo de migração<br/>&bull;Falta de uma fila de cartas mortas - Significa que alguns ficheiros erróneos podem parar todo o processo de migração.|
|Offline|[Conector Azure Cosmos DB Spark](https://docs.microsoft.com/azure/cosmos-db/spark-connector)|&bull;Faz uso da biblioteca de executora a granel Azure Cosmos DB <br/>&bull;Adequado para grandes conjuntos de dados <br/>&bull;Precisa de uma configuração personalizada de Faísca <br/>&bull;A faísca é sensível às inconsistências do esquema e isso pode ser um problema durante a migração |
|Offline|[Ferramenta personalizada com biblioteca de executor a granel Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/migrate-cosmosdb-data)|&bull;Fornece capacidades de checkpointing, letras mortas que aumentam a resiliência da migração <br/>&bull;Adequado para conjuntos de dados muito grandes (10 TB+)  <br/>&bull;Requer configuração personalizada desta ferramenta funcionando como um Serviço de Aplicações |
|Online|[Funções Cosmos DB + ChangeFeed API](https://docs.microsoft.com/azure/cosmos-db/change-feed-functions)|&bull;Fácil de configurar <br/>&bull;Só funciona se a fonte for um recipiente Azure Cosmos DB <br/>&bull;Não é adequado para grandes conjuntos de dados <br/>&bull;Não captura apaga do recipiente de origem |
|Online|[Serviço de migração personalizado usando changefeed](https://github.com/nomiero/CosmosDBLiveETLSample)|&bull;Fornece rastreio de progresso <br/>&bull;Só funciona se a fonte for um recipiente Azure Cosmos DB <br/>&bull;Também funciona para conjuntos de dados maiores <br/>&bull;Requer que o utilizador cria um Serviço de Aplicações para hospedar o processador de feed Change <br/>&bull;Não captura apaga do recipiente de origem|
|Online|[Rio Striim](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-sql-api-migrate-data-striim)|&bull;Funciona com uma grande variedade de fontes como Oracle, DB2, SQL Server <br/>&bull;Fácil de construir oleodutos ETL e fornece um dashboard para monitorização <br/>&bull;Suporta conjuntos de dados maiores <br/>&bull;Uma vez que esta é uma ferramenta de terceiros, precisa de ser comprada no mercado e instalada no ambiente do utilizador|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo API
|**Tipo de migração**|**Solução**|**Considerações**|
|---------|---------|---------|
|Offline|[Ferramenta de migração de dados](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull;Fácil de configurar e suporta várias fontes <br/>&bull;Não é adequado para grandes conjuntos de dados|
|Offline|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull;Fácil de configurar e suporta várias fontes <br/>&bull;Faz uso da biblioteca de executora a granel Azure Cosmos DB <br/>&bull;Adequado para grandes conjuntos de dados <br/>&bull;A falta de controlo significa que qualquer problema durante o curso da migração exigiria um reinício de todo o processo de migração<br/>&bull;A falta de uma fila de cartas mortas significaria que alguns ficheiros erróneos poderiam parar todo o processo de migração. <br/>&bull;Precisa de código personalizado para aumentar a produção de leitura para determinadas fontes de dados|
|Offline|[Ferramentas Mongo existentes (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull;Fácil de configurar e integrar <br/>&bull;Precisa de manuseamento personalizado para aceleradores|
|Online|[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/tutorial-mongodb-cosmos-db-online)|&bull;Faz uso da biblioteca de executora a granel Azure Cosmos DB <br/>&bull;Adequado para grandes conjuntos de dados e cuida da replicação de alterações ao vivo <br/>&bull;Trabalha apenas com outras fontes do MongoDB|

## <a name="azure-cosmos-db-cassandra-api"></a>API para Cassandra do Azure Cosmos DB
|**Tipo de migração**|**Solução**|**Considerações**|
|---------|---------|---------|
|Offline|[comando cqlsh COPY](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-cqlsh-copy-command)|&bull;Fácil de configurar <br/>&bull;Não é adequado para grandes conjuntos de dados <br/>&bull;Só funciona quando a fonte é uma mesa de Cassandra.|
|Offline|[Mesa de cópia com faísca](https://docs.microsoft.com/azure/cosmos-db/cassandra-import-data#migrate-data-using-spark) |&bull;Pode fazer uso das capacidades de Faísca para paralelo a transformação e ingestão <br/>&bull;Precisa de configuração com uma política de retry personalizada para lidar com aceleradores|
|Online|[Striim (da Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-cassandra-api-migrate-data-striim)|&bull;Funciona com uma grande variedade de fontes como Oracle, DB2, SQL Server <br/>&bull;Fácil de construir oleodutos ETL e fornece um dashboard para monitorização <br/>&bull;Suporta conjuntos de dados maiores <br/>&bull;Uma vez que esta é uma ferramenta de terceiros, precisa de ser comprada no mercado e instalada no ambiente do utilizador|
|Online|[Blitzz (da Oracle DB/Apache Cassandra)](https://docs.microsoft.com/azure/cosmos-db/oracle-migrate-cosmos-db-blitzz)|<br/>&bull;Suporta conjuntos de dados maiores <br/>&bull;Uma vez que esta é uma ferramenta de terceiros, precisa de ser comprada no mercado e instalada no ambiente do utilizador|

## <a name="other-apis"></a>Outras APIs
Para além da API SQL, da API do Mongo e da API Cassandra, existem várias ferramentas apoiadas por cada um dos ecossistemas existentes da API. 

**API de Tabela** 
* [Ferramenta de migração de dados](https://docs.microsoft.com/azure/cosmos-db/table-import#data-migration-tool)
* [AzCopy](https://docs.microsoft.com/azure/cosmos-db/table-import#migrate-data-by-using-azcopy)

**API do Gremlin**
* [Biblioteca de executor a granel de gráfico](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-graph-dotnet)
* [Faísca de Gremlin](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Próximos passos

* Saiba mais experimentando as aplicações de amostra que consomem a biblioteca de executora a granel em [.NET](bulk-executor-dot-net.md) e [Java](bulk-executor-java.md). 
* A biblioteca de executora a granel está integrada no conector Cosmos DB Spark, para saber mais, ver artigo de [conector Azure Cosmos DB Spark.](spark-connector.md)  
* Contacte a equipa de produtos Azure Cosmos DB abrindo um bilhete de apoio ao abrigo do tipo de problema "General Advisory" e do subtipo de problemas de migração "Grandes (TB+) para obter ajuda adicional com migrações em larga escala.
