---
title: Migrar dados de Apache Cassandra para a Azure Cosmos DB Cassandra API usando Databricks (Faísca)
description: Saiba como migrar dados de uma base de dados Apache Cassandra para a API AZURE Cosmos DB Cassandra, utilizando Azure Databricks e Spark.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 03/10/2021
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: caedefbf3887205b68bcd5de5e7cd5f1f7d7f53c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801014"
---
# <a name="migrate-data-from-cassandra-to-an-azure-cosmos-db-cassandra-api-account-by-using-azure-databricks"></a>Migrar dados de Cassandra para uma conta Azure Cosmos DB Cassandra API usando Azure Databricks
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Cassandra API em Azure Cosmos DB tornou-se uma ótima escolha para cargas de trabalho empresariais em execução em Apache Cassandra por várias razões:

* **Sem despesas gerais de gestão e monitorização:** Elimina a sobrecarga de gestão e monitorização de configurações através dos ficheiros OS, JVM e YAML e suas interações.

* **Economia de custos significativa:** Você pode economizar custos com o Azure Cosmos DB, que inclui o custo de VMs, largura de banda e quaisquer licenças aplicáveis. Não é preciso gerir centros de dados, servidores, armazenamento SSD, networking e custos de eletricidade.

* **Capacidade de utilizar código e ferramentas existentes:** O Azure Cosmos DB fornece compatibilidade ao nível do protocolo de fio com os SDKs e ferramentas existentes da Cassandra. Esta compatibilidade garante que pode utilizar a sua base de código existente com a API API AZURE Cosmos DB Cassandra com alterações triviais.

Há muitas formas de migrar cargas de trabalho de base de dados de uma plataforma para outra. [Azure Databricks](https://azure.microsoft.com/services/databricks/) é uma plataforma como um serviço (PaaS) que oferece para [a Apache Spark](https://spark.apache.org/) que oferece uma maneira de realizar migrações offline em larga escala. Este artigo descreve os passos necessários para migrar dados de espaços e tabelas nativas apache Cassandra para o Azure Cosmos DB Cassandra API usando Azure Databricks.

## <a name="prerequisites"></a>Pré-requisitos

* [Provisão de uma conta Azure Cosmos DB Cassandra API](create-cassandra-dotnet.md#create-a-database-account).

* [Reveja os fundamentos da ligação a uma API API AZURE Cosmos DB Cassandra](cassandra-spark-generic.md).

* Reveja as [funcionalidades suportadas na Azure Cosmos DB Cassandra API](cassandra-support.md) para garantir a compatibilidade.

* Certifique-se de que já criou espaços e tabelas vazias na conta Azure Cosmos DB Cassandra API.

* [Utilize cqlsh ou concha alojada para validação](cassandra-support.md#hosted-cql-shell-preview).

## <a name="provision-an-azure-databricks-cluster"></a>Provisão de um cluster Azure Databricks

Pode seguir as instruções para [o fornecimento de um cluster Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Recomendamos a seleção da versão de execução 7.5 de Databricks, que suporta o Spark 3.0.

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Screenshot que mostra encontrar a versão de tempo de execução databricks.":::

## <a name="add-dependencies"></a>Adicionar dependências

Você precisa adicionar a biblioteca Apache Spark Cassandra Connector ao seu cluster para se conectar com pontos finais nativos e Azure Cosmos DB Cassandra. No seu cluster, selecione **Bibliotecas**  >  **Instale New**  >  **Maven** e adicione `com.datastax.spark:spark-cassandra-connector-assembly_2.12:3.0.0` as coordenadas Maven.

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Screenshot que mostra a procura de pacotes Maven em Databricks.":::

Selecione **Instalar** e, em seguida, reiniciar o cluster quando a instalação estiver concluída.

> [!NOTE]
> Certifique-se de que reinicia o cluster Databricks depois de instalada a biblioteca Cassandra Connector.

## <a name="create-scala-notebook-for-migration"></a>Criar Caderno Scala para migração

Crie um Caderno Scala em Databricks. Substitua as configurações de Cassandra de origem e alvo pelas credenciais correspondentes e os espaços e tabelas de chaves de origem e alvo. Em seguida, executar o seguinte código:

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
> Os `spark.cassandra.output.batch.size.rows` `spark.cassandra.output.concurrent.writes` valores e valores e o número de trabalhadores no seu cluster Spark são configurações importantes para sintonizar de modo a evitar a [limitação das taxas](/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/). A limitação da taxa ocorre quando os pedidos à Azure Cosmos DB excedem [as unidades](./request-units.md) de produção ou pedido (RUs) previstas. Poderá ser necessário ajustar estas definições, dependendo do número de executores no cluster Spark e potencialmente o tamanho (e, portanto, o custo RU) de cada registo ser escrito nas tabelas-alvo.

## <a name="troubleshoot"></a>Resolução de problemas

### <a name="rate-limiting-429-error"></a>Limitação de taxa (erro de 429)

Pode ver um código de erro 429 ou um texto de erro "taxa de pedido é grande", mesmo que reduza as definições para os seus valores mínimos. Os seguintes cenários podem causar limitação de taxas:

* **O rendimento atribuído à mesa é inferior a 6.000 [unidades de pedido.](./request-units.md)** Mesmo em configurações mínimas, a Spark pode escrever a uma taxa de cerca de 6.000 unidades de pedido ou mais. Se você fornece uma mesa em um espaço chave com produção compartilhada, é possível que esta tabela tenha menos de 6.000 RUs disponíveis em tempo de execução.

    Certifique-se de que a tabela para a que está a migrar tem pelo menos 6.000 RUs disponíveis quando executar a migração. Se necessário, aloque as unidades de pedido dedicadas a essa tabela.

* **Distorcer dados excessivos com grande volume de dados**. Se tiver uma grande quantidade de dados para migrar para uma dada tabela, mas tiver um desvio significativo nos dados (isto é, um grande número de registos sendo escritos para o mesmo valor chave de partição), então você ainda pode experimentar a taxa limitando mesmo que você tenha [várias unidades de pedido](./request-units.md) a provisionadas na sua tabela. As unidades de pedido são divididas igualmente entre divisórias físicas, e a distorção de dados pesados pode causar um estrangulamento de pedidos a uma única divisória.

    Neste cenário, reduza para as definições mínimas de produção em Spark e force a migração a funcionar lentamente. Este cenário pode ser mais comum quando se está a migrar tabelas de referência ou de controlo, onde o acesso é menos frequente e o distorção pode ser elevado. No entanto, se houver uma distorção significativa em qualquer outro tipo de tabela, é melhor rever o seu modelo de dados para evitar problemas de partição quentes para a sua carga de trabalho durante operações de estado estável.

## <a name="next-steps"></a>Passos seguintes

* [Aprovisionar o débito em contentores e bases de dados](set-throughput.md)
* [Principais práticas de partição](partitioning-overview.md#choose-partitionkey)
* [Estimativa RU/s usando o planejador de capacidades DB Azure Cosmos](estimate-ru-with-capacity-planner.md)
* [Escala elástica em Azure Cosmos DB Cassandra API](manage-scale-cassandra.md)
