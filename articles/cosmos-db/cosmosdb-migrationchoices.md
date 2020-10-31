---
title: Opções de migração cosmos DB
description: Este doc descreve as várias opções para migrar os seus dados no local ou na nuvem para Azure Cosmos DB
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 8721c0eb728f568521e86baecb658dc9c869a7f6
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097588"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>Opções para migrar os seus dados no local ou na nuvem para Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Pode carregar dados de várias fontes de dados para a Azure Cosmos DB. Uma vez que a Azure Cosmos DB suporta várias APIs, os alvos podem ser qualquer uma das APIs existentes. Seguem-se alguns cenários em que migra dados para a Azure Cosmos DB:

* Mover dados de um contentor Azure Cosmos para outro recipiente na mesma base de dados ou em bases de dados diferentes.
* Deslocar dados entre recipientes dedicados a recipientes de base de dados partilhados.
* Mover dados de uma conta Azure Cosmos localizada na região1 para outra conta Azure Cosmos na mesma ou numa região diferente.
* Mover dados de uma fonte como o armazenamento de blob Azure, um ficheiro JSON, base de dados Oracle, Couchbase, DynamoDB para Azure Cosmos DB.

De forma a apoiar caminhos de migração das várias fontes para as diferentes APIs Azure Cosmos, existem múltiplas soluções que fornecem manuseamento especializado para cada caminho de migração. Este documento lista as soluções disponíveis e descreve as suas vantagens e limitações.

## <a name="factors-affecting-the-choice-of-migration-tool"></a>Fatores que afetam a escolha da ferramenta de migração

Os seguintes fatores determinam a escolha da ferramenta de migração:

* **Migração online vs offline** : Muitas ferramentas de migração fornecem um caminho para fazer uma migração única apenas. Isto significa que as aplicações que acedem à base de dados podem experimentar um período de inatividade. Algumas soluções de migração fornecem uma forma de fazer uma migração viva onde existe um gasoduto de replicação criado entre a fonte e o alvo.

* **Fonte de dados** : Os dados existentes podem estar em várias fontes de dados como o Oracle DB2, Datastax Cassanda, Azure SQL Database, PostgreSQL, etc. Os dados também podem estar numa conta DB Azure Cosmos existente e a intenção de migração pode ser alterar o modelo de dados ou repartir os dados num recipiente com uma chave de partição diferente.

* **Azure Cosmos DB API** : Para a API SQL em Azure Cosmos DB, existem uma variedade de ferramentas desenvolvidas pela equipa DB da Azure Cosmos que ajudam nos diferentes cenários de migração. Todas as outras APIs têm o seu próprio conjunto de ferramentas especializadas desenvolvidas e mantidas pela comunidade. Uma vez que a Azure Cosmos DB suporta estas APIs a nível de protocolo de fio, estas ferramentas devem funcionar como-está enquanto migram dados para Azure Cosmos DB também. No entanto, podem necessitar de manuseamento personalizado para aceleradores, uma vez que este conceito é específico da Azure Cosmos DB.

* **Tamanho dos dados** : A maioria das ferramentas de migração funciona muito bem para conjuntos de dados mais pequenos. Quando o conjunto de dados excede algumas centenas de gigabytes, as escolhas das ferramentas de migração são limitadas. 

* **Duração prevista da migração** : As migrações podem ser configuradas para ocorrer a um ritmo lento e incremental que consuma menos produção ou possa consumir toda a produção prevista no contentor DB Azure Cosmos e completar a migração em menos tempo.

## <a name="azure-cosmos-db-sql-api"></a>API SQL do Azure Cosmos DB

|Tipo de migração|Solução|Fontes apoiadas|Alvos apoiados|Considerações|
|---------|---------|---------|---------|---------|
|Offline|[Ferramenta de Migração de Dados](import-data.md)| &bull;Ficheiros JSON/CSV<br/>&bull;API SQL do Azure Cosmos DB<br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Armazenamento de Tabelas<br/>&bull;AWS DynamoDB<br/>&bull;Armazenamento de Blobs do Azure|&bull;API SQL do Azure Cosmos DB<br/>&bull;Azure Cosmos DB Tabelas API<br/>&bull;Ficheiros JSON |&bull; Fácil de configurar e suporta múltiplas fontes. <br/>&bull; Não é adequado para grandes conjuntos de dados.|
|Offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)| &bull;Ficheiros JSON/CSV<br/>&bull;API SQL do Azure Cosmos DB<br/>&bull;API do Azure Cosmos DB para MongoDB<br/>&bull;MongoDB <br/>&bull;SQL Server<br/>&bull;Armazenamento de Tabelas<br/>&bull;Armazenamento de Blobs do Azure <br/> <br/>Consulte o artigo [da Azure Data Factory](../data-factory/connector-overview.md) para outras fontes apoiadas.|&bull;API SQL do Azure Cosmos DB<br/>&bull;API do Azure Cosmos DB para MongoDB<br/>&bull;Ficheiros JSON <br/><br/> Consulte o artigo da [Azure Data Factory](../data-factory/connector-overview.md) para outros alvos suportados. |&bull; Fácil de configurar e suporta múltiplas fontes.<br/>&bull; Faz uso da biblioteca de executor a granel Azure Cosmos. <br/>&bull; Adequado para grandes conjuntos de dados. <br/>&bull; Falta de controlo - Significa que, se ocorrer um problema durante a migração, é necessário reiniciar todo o processo de migração.<br/>&bull; Falta de uma fila de cartas mortas - Significa que alguns ficheiros erróneos podem parar todo o processo de migração.|
|Offline|[Conector Azure Cosmos DB Spark](spark-connector.md)|Azure Cosmos DB SQL API. <br/><br/>Pode utilizar outras fontes com conectores adicionais do ecossistema Spark.| Azure Cosmos DB SQL API. <br/><br/>Pode utilizar outros alvos com conectores adicionais do ecossistema Spark.| &bull; Faz uso da biblioteca de executor a granel Azure Cosmos. <br/>&bull; Adequado para grandes conjuntos de dados. <br/>&bull; Precisa de uma configuração personalizada da Spark. <br/>&bull; A faísca é sensível às inconsistências do esquema e isso pode ser um problema durante a migração. |
|Offline|[Ferramenta personalizada com biblioteca de executor a granel Cosmos DB](migrate-cosmosdb-data.md)| A fonte depende do seu código personalizado | API SQL do Azure Cosmos DB| &bull; Fornece capacidades de checkpoint, letras mortas, o que aumenta a resiliência da migração. <br/>&bull; Adequado para conjuntos de dados muito grandes (10 TB+).  <br/>&bull; Requer a configuração personalizada desta ferramenta funcionando como um Serviço de Aplicações. |
|Online|[Funções Cosmos DB + ChangeFeed API](change-feed-functions.md)| API SQL do Azure Cosmos DB | API SQL do Azure Cosmos DB| &bull; Fácil de montar. <br/>&bull; Funciona apenas se a fonte for um contentor Azure Cosmos DB. <br/>&bull; Não é adequado para grandes conjuntos de dados. <br/>&bull; Não captura as eliminações do recipiente de origem. |
|Online|[Serviço de Migração Personalizada usando ChangeFeed](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)| API SQL do Azure Cosmos DB | API SQL do Azure Cosmos DB| &bull; Fornece rastreio de progresso. <br/>&bull; Funciona apenas se a fonte for um contentor Azure Cosmos DB. <br/>&bull; Funciona para conjuntos de dados maiores também.<br/>&bull; Requer que o utilizador crie um Serviço de Aplicações para hospedar o processador Change feed. <br/>&bull; Não captura as eliminações do recipiente de origem.|
|Online|[Striim](cosmosdb-sql-api-migrate-data-striim.md)| &bull;Oracle <br/>&bull;Apache Cassandra<br/><br/> Consulte o [site da Striim](https://www.striim.com/sources-and-targets/) para outras fontes apoiadas. |&bull;API SQL do Azure Cosmos DB <br/>&bull; Azure Cosmos DB Cassandra API<br/><br/> Consulte o [site Striim](https://www.striim.com/sources-and-targets/) para outros alvos suportados. | &bull; Funciona com uma grande variedade de fontes como Oracle, DB2, SQL Server.<br/>&bull; Fácil de construir gasodutos ETL e fornece um dashboard para monitorização. <br/>&bull; Suporta conjuntos de dados maiores. <br/>&bull; Uma vez que esta é uma ferramenta de terceiros, precisa de ser comprada no mercado e instalada no ambiente do utilizador.|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo API

|Tipo de migração|Solução|Fontes apoiadas|Alvos apoiados|Considerações|
|---------|---------|---------|---------|---------|
|Online|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB|API do Azure Cosmos DB para MongoDB |&bull; Faz uso da biblioteca de executor a granel Azure Cosmos. <br/>&bull; Adequado para grandes conjuntos de dados e cuida de replicar alterações ao vivo. <br/>&bull; Funciona apenas com outras fontes do MongoDB.|
|Offline|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)| MongoDB| API do Azure Cosmos DB para MongoDB| &bull; Faz uso da biblioteca de executor a granel Azure Cosmos. <br/>&bull; Adequado para grandes conjuntos de dados e cuida de replicar alterações ao vivo. <br/>&bull; Funciona apenas com outras fontes do MongoDB.|
|Offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)| &bull;Ficheiros JSON/CSV<br/>&bull;API SQL do Azure Cosmos DB<br/>&bull;API do Azure Cosmos DB para MongoDB <br/>&bull;MongoDB<br/>&bull;SQL Server<br/>&bull;Armazenamento de Tabelas<br/>&bull;Armazenamento de Blobs do Azure <br/><br/> Consulte o artigo [da Azure Data Factory](../data-factory/connector-overview.md) para outras fontes apoiadas. | &bull;API SQL do Azure Cosmos DB<br/>&bull;API do Azure Cosmos DB para MongoDB <br/>&bull; Ficheiros JSON <br/><br/> Consulte o artigo da [Azure Data Factory](../data-factory/connector-overview.md) para outros alvos suportados.| &bull; Fácil de configurar e suporta múltiplas fontes. <br/>&bull; Faz uso da biblioteca de executor a granel Azure Cosmos. <br/>&bull; Adequado para grandes conjuntos de dados. <br/>&bull; A falta de controlo significa que qualquer questão durante o processo de migração exigiria um reinício de todo o processo de migração.<br/>&bull; A falta de uma fila de cartas mortas significaria que alguns ficheiros erróneos poderiam parar todo o processo de migração. <br/>&bull; Precisa de código personalizado para aumentar a produção de leitura para determinadas fontes de dados.|
|Offline|[Ferramentas mongo existentes (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|MongoDB | API do Azure Cosmos DB para MongoDB| &bull; Fácil de configurar e integração. <br/>&bull; Precisa de manuseamento personalizado para aceleradores.|

## <a name="azure-cosmos-db-cassandra-api"></a>API para Cassandra do Azure Cosmos DB

|Tipo de migração|Solução|Fontes apoiadas|Alvos apoiados|Considerações|
|---------|---------|---------|---------|---------|
|Offline|[cqlsh comando COPY](cassandra-import-data.md#migrate-data-using-cqlsh-copy-command)|Ficheiros CSV | API para Cassandra do Azure Cosmos DB| &bull; Fácil de montar. <br/>&bull; Não é adequado para grandes conjuntos de dados. <br/>&bull; Só funciona quando a fonte é uma mesa cassandra.|
|Offline|[Tabela de cópia com Faísca](cassandra-import-data.md#migrate-data-using-spark) | &bull;Apache Cassandra<br/>&bull;API para Cassandra do Azure Cosmos DB| API para Cassandra do Azure Cosmos DB | &bull; Pode fazer uso das capacidades de Faísca para paralelizar a transformação e a ingestão. <br/>&bull; Necessita de configuração com uma política de reagem personalizada para manusear aceleradores.|
|Online|[Striim (da Oráculo DB/Apache Cassandra)](cosmosdb-cassandra-api-migrate-data-striim.md)| &bull;Oracle<br/>&bull;Apache Cassandra<br/><br/> Consulte o [site da Striim](https://www.striim.com/sources-and-targets/) para outras fontes apoiadas.|&bull;API SQL do Azure Cosmos DB<br/>&bull;API para Cassandra do Azure Cosmos DB <br/><br/> Consulte o [site Striim](https://www.striim.com/sources-and-targets/) para outros alvos suportados.| &bull; Funciona com uma grande variedade de fontes como Oracle, DB2, SQL Server. <br/>&bull; Fácil de construir gasodutos ETL e fornece um dashboard para monitorização. <br/>&bull; Suporta conjuntos de dados maiores. <br/>&bull; Uma vez que esta é uma ferramenta de terceiros, precisa de ser comprada no mercado e instalada no ambiente do utilizador.|
|Online|[Blitzz (da Oráculo DB/Apache Cassandra)](oracle-migrate-cosmos-db-blitzz.md)|&bull;Oracle<br/>&bull;Apache Cassandra<br/><br/>Consulte o [site da Blitzz](https://www.blitzz.io/) para outras fontes apoiadas. |Azure Cosmos DB Cassandra API. <br/><br/>Consulte o [site blitzz](https://www.blitzz.io/) para outros alvos suportados. | &bull; Suporta conjuntos de dados maiores. <br/>&bull; Uma vez que esta é uma ferramenta de terceiros, precisa de ser comprada no mercado e instalada no ambiente do utilizador.|

## <a name="other-apis"></a>Outras APIs

Para as APIs que não a API SQL, a Mongo API e a API Cassandra, existem várias ferramentas apoiadas por cada um dos ecossistemas existentes da API. 

**API de Tabela** 

* [Ferramenta de Migração de Dados](table-import.md#data-migration-tool)
* [AzCopy](table-import.md#migrate-data-by-using-azcopy)

**API do Gremlin**

* [Biblioteca de executor a granel de gráfico](bulk-executor-graph-dotnet.md)
* [Faísca Gremlin](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>Passos seguintes

* Saiba mais experimentando as aplicações de amostra que consomem a biblioteca de executor a granel em [.NET](bulk-executor-dot-net.md) e [Java](bulk-executor-java.md). 
* A biblioteca de executor a granel está integrada no conector Cosmos DB Spark, para saber mais, ver artigo [do conector Azure Cosmos DB Spark.](spark-connector.md)  
* Contacte a equipa de produtos da Azure Cosmos DB abrindo um bilhete de apoio ao abrigo do tipo de problema "General Advisory" e do subtipo de problemas de migração "Grandes (TB+)" para ajuda adicional com migrações em larga escala.
