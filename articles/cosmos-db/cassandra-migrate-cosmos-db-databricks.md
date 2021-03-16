---
title: Migrar dados de Apache Cassandra para Azure Cosmos DB Cassandra API usando Databricks (Faísca)
description: Saiba como migrar dados da base de dados Apache Cassandra para Azure Cosmos DB Cassandra API usando Azure Databricks e Spark.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 03/10/2021
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: caf9cbb0ca017ee00c5061d94e0d37703194943d
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573383"
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

Pode seguir as instruções para [providenciar um cluster Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Recomendamos a seleção da versão de execução 7.5 de Databricks, que suporta a Spark 3.0:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Databricks tempo de execução":::


## <a name="add-dependencies"></a>Adicionar dependências

Você precisará adicionar a biblioteca de conector Apache Spark Cassandra ao seu cluster, a fim de se conectar com pontos finais nativos e cosmos DB Cassandra. No seu cluster selecione bibliotecas -> instalar maven novo ->. adicionar `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` em coordenadas maven:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Pacotes de pesquisa databricks":::

Selecione a instalação e certifique-se de que reinicia o cluster quando a instalação estiver concluída. 

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
  .mode(SaveMode.Append)
  .save
```

> [!NOTE]
> Os valores `spark.cassandra.output.batch.size.rows` para `spark.cassandra.output.concurrent.writes` e, bem como o número de trabalhadores no seu cluster Spark, são configurações importantes para sintonizar de modo a evitar [a limitação de tarifas,](/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/)o que acontece quando os pedidos à Azure Cosmos DB excedem o rendimento/(unidades[de pedido).](./request-units.md) Poderá ser necessário ajustar estas definições dependendo do número de executores no cluster Spark, e potencialmente o tamanho (e, portanto, o custo RU) de cada registo ser escrito nas tabelas-alvo.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="rate-limiting-429-error"></a>Limitação de taxa (erro de 429)
Pode ver um código de erro de 429 ou `request rate is large` texto de erro, apesar de reduzir as definições acima para os seus valores mínimos. Seguem-se alguns desses cenários:

- **O rendimento atribuído à tabela é inferior a 6000 [unidades de pedido.](./request-units.md)** Mesmo em configurações mínimas, a Spark poderá executar escritas a uma taxa de cerca de 6000 unidades de pedido ou mais. Se disponibilizou uma tabela num espaço-chave com produção partilhada, é possível que esta tabela tenha menos de 6000 RUs disponíveis em tempo de execução. Certifique-se de que a tabela para a que está a migrar tem pelo menos 6000 RUs disponíveis para ele durante a execução da migração, e se necessário alocar unidades de pedido dedicadas a essa tabela. 
- **Distorcer dados excessivos com grande volume de dados**. Se tiver uma grande quantidade de dados (isto é linhas de tabela) para migrar para uma dada tabela, mas tiver um desvio significativo nos dados (ou seja, um grande número de registos a serem escritos para o mesmo valor de chave de partição), então poderá ainda experimentar a limitação da taxa mesmo que tenha uma grande quantidade de unidades de pedido a serem [apresentadas](./request-units.md) na sua tabela. Isto porque as unidades de pedido são divididas igualmente entre divisórias físicas, e a distorção de dados pesados pode resultar num estrangulamento de pedidos a uma única divisória, causando a limitação da taxa. Neste cenário, é aconselhável reduzir para as definições mínimas de produção em Spark para evitar a limitação de taxas e forçar a migração a funcionar lentamente. Este cenário pode ser mais comum quando migrar tabelas de referência ou de controlo, onde o acesso é menos frequente, mas o enviesamento pode ser elevado. No entanto, se houver uma distorção significativa em qualquer outro tipo de tabela, também pode ser aconselhável rever o seu modelo de dados para evitar problemas de partição quentes para a sua carga de trabalho durante operações de estado estável. 



## <a name="next-steps"></a>Passos seguintes

* [Aprovisionar o débito em contentores e bases de dados](set-throughput.md) 
* [Principais práticas de partição](partitioning-overview.md#choose-partitionkey)
* [Estimativa RU/s usando os artigos de planificador de capacidadeS do Azure Cosmos DB](estimate-ru-with-capacity-planner.md)
* [Escala elástica em Azure Cosmos DB Cassandra API](manage-scale-cassandra.md)
