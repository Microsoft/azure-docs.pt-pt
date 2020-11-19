---
title: Migrar dados de Apache Cassandra para Azure Cosmos DB Cassandra API usando Databricks (Faísca)
description: Saiba como migrar dados da base de dados Apache Cassandra para Azure Cosmos DB Cassandra API usando Azure Databricks e Spark.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/16/2020
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: 877aa96b189de47d158721df6585cb94ace4a855
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94932917"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-azure-databricks"></a>Migrar dados de Cassandra para Azure Cosmos DB Cassandra Conta API usando Azure Databricks
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Cassandra API em Azure Cosmos DB tornou-se uma ótima escolha para cargas de trabalho empresariais em execução em Apache Cassandra por uma variedade de razões tais como: 

* **Sem despesas gerais de gestão e monitorização:** Elimina a sobrecarga de gestão e monitorização de uma miríade de configurações através de sistemas DE, JVM e yaml e suas interações.

* **Economia de custos significativa:** Você pode economizar custos com Azure Cosmos DB, que inclui o custo de VM's, largura de banda, e quaisquer licenças aplicáveis. Além disso, não é preciso gerir os centros de dados, servidores, armazenamento SSD, networking e custos de eletricidade. 

* **Capacidade de utilizar código e ferramentas existentes:** A Azure Cosmos DB fornece compatibilidade de protocolo de fio com os SDKs e ferramentas existentes da Cassandra. Esta compatibilidade assegura que pode utilizar o seu código base atual com a API para Cassandra do Azure Cosmos DB, com alterações triviais.

Existem várias formas de migrar cargas de trabalho de base de dados de uma plataforma para outra. [A azure Databricks](https://azure.microsoft.com/services/databricks/) é uma plataforma como uma oferta de serviços para [a Apache Spark](https://spark.apache.org/) que oferece uma forma de realizar migrações offline em larga escala. Este artigo descreve os passos necessários para migrar dados de espaços/tabelas nativos de Apache Cassandra para Azure Cosmos DB Cassandra API usando Azure Databricks.

## <a name="prerequisites"></a>Pré-requisitos

* [Provisão de uma conta Azure Cosmos DB Cassandra API](create-cassandra-dotnet.md#create-a-database-account)

* [Reveja os fundamentos da ligação à Azure Cosmos DB Cassandra API](cassandra-spark-generic.md)

* Reveja as [funcionalidades suportadas na Azure Cosmos DB Cassandra API](cassandra-support.md) para garantir a compatibilidade.

* Certifique-se de que já criou teclados vazios e tabelas na sua conta Azure Cosmos DB Cassandra API

* [Use cqlsh ou concha aloiada para validação](cassandra-support.md#hosted-cql-shell-preview)

## <a name="provision-an-azure-databricks-cluster"></a>Provisão de um cluster Azure Databricks

Pode seguir as instruções para [providenciar um cluster Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). No entanto, note que o Apache Spark 3.x não está atualmente suportado para o Conector Apache Cassandra. Você precisará de providenciar um tempo de execução Databricks com uma versão v2.x suportada de Apache Spark. Recomendamos a versão 6.6 do tempo de execução de Databricks:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Databricks tempo de execução":::


## <a name="add-dependencies"></a>Adicionar dependências

Você precisará adicionar a biblioteca de conector Apache Spark Cassandra ao seu cluster, a fim de se conectar com pontos finais nativos e cosmos DB Cassandra. No seu cluster seleciona bibliotecas -> instalar novos pacotes de pesquisa maven-> > de maven-> novos:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Pacotes de pesquisa databricks":::

Digite `Cassandra` a caixa de pesquisa e selecione o mais recente `spark-cassandra-connector` repositório de maven disponível e, em seguida, selecione instalar:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages-2.png" alt-text="Databricks selecionam pacotes":::

> [!NOTE]
> Certifique-se de que reinicia o cluster Databricks depois de instalada a biblioteca Cassandra Connector.

## <a name="create-scala-notebook-for-migration"></a>Criar Caderno Scala para migração

Crie um Bloco de Notas Scala em Databricks com o seguinte código. Substitua as configurações de cassandra de origem e alvo por credenciais correspondentes e os espaços e tabelas de chaves de origem/alvo, em seguida, executar:

```scala
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql._
import org.apache.spark.SparkContext

// source cassandra configs
val nativeCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "false",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val cosmosCassandra = Map( 
    "spark.cassandra.connection.host" -> "<USERNAME>.cassandra.cosmos.azure.com",
    "spark.cassandra.connection.port" -> "10350",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.connection.connections_per_executor_max" -> "10",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//Read from native Cassandra
val DFfromNativeCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(nativeCassandra)
  .load
  
//Write to CosmosCassandra
DFfromNativeCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(cosmosCassandra)
  .save
```

> [!NOTE]
> As `spark.cassandra.output.concurrent.writes` `connections_per_executor_max` configurações e configurações são importantes para evitar a [limitação da taxa,](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/)o que acontece quando os pedidos à Cosmos DB excedem o rendimento previsto[(unidades de pedido).](https://docs.microsoft.com/azure/cosmos-db/request-units) Poderá ser necessário ajustar estas definições dependendo do número de executores no cluster Spark, e potencialmente o tamanho (e, portanto, o custo RU) de cada registo ser escrito nas tabelas-alvo.

## <a name="next-steps"></a>Passos seguintes

* [Aprovisionar o débito em contentores e bases de dados](set-throughput.md) 
* [Principais práticas de partição](partitioning-overview.md#choose-partitionkey)
* [Estimativa RU/s usando os artigos de planificador de capacidadeS do Azure Cosmos DB](estimate-ru-with-capacity-planner.md)
* [Escala elástica em Azure Cosmos DB Cassandra API](manage-scale-cassandra.md)
