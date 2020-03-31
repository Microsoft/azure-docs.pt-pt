---
title: Trabalhando com a Azure Cosmos DB Cassandra API da Spark
description: Este artigo é a página principal para a integração da Cosmos DB Cassandra API da Spark.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/01/2019
ms.openlocfilehash: cb34ea44c069f067d13a6480531a94a1a515f380
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70241249"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Connect to Azure Cosmos DB Cassandra API from Spark (Ligar à API para Cassandra do Azure Cosmos DB a partir do Spark)

Este artigo é um entre uma série de artigos sobre a integração da API da Azure Cosmos DB Cassandra da Spark. Os artigos abrangem a conectividade, as operações de linguagem de definição de dados (DDL), operações básicas de linguagem de manipulação de dados (DML) e a integração avançada da API da Azure Cosmos DB Cassandra da Spark. 

## <a name="prerequisites"></a>Pré-requisitos
* [Provisão de uma conta API Da API da Azure Cosmos DB Cassandra.](create-cassandra-dotnet.md#create-a-database-account)

* Providenciar a sua escolha de spark environment [[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) | [Azure HDInsight-Spark](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) ] Outros].

## <a name="dependencies-for-connectivity"></a>Dependências para a conectividade
* **Conector de faísca saqueador para Cassandra:** O conector de faíscas é utilizado para ligar à API Da API da Azure Cosmos DB Cassandra.  Identifique e utilize a versão do conector localizado na central de [Maven]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) que é compatível com as versões Spark e Scala do seu ambiente Spark.

* Biblioteca de **ajudantes Azure Cosmos DB para Cassandra API:** Além do conector Spark, você precisa de outra biblioteca chamada [azure-cosmos-cassandra-spark-helper]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) de Azure Cosmos DB. Esta biblioteca contém fábrica de ligação personalizada e aulas políticas de retry.

  A política de retry em Azure Cosmos DB está configurada para lidar com exceções ao código de estado HTTP 429("Taxa de Pedido Grande"). A API da Azure Cosmos DB Cassandra traduz estas exceções em erros sobrecarregados no protocolo nativo de Cassandra, e você pode voltar a tentar com back-offs. Como o Azure Cosmos DB utiliza o modelo de entrada provisionado, as exceções de limitação da taxa de pedidos ocorrem quando as taxas de entrada/saída aumentam. A política de retry protege os seus trabalhos de faísca contra picos de dados que excedem momentaneamente a entrada atribuída para o seu contentor.

  > [!NOTE] 
  > A política de retry pode proteger os seus trabalhos de faísca apenas contra picos momentâneos. Se não tiver configurado rUs suficientes necessários para executar a sua carga de trabalho, então a política de retry não é aplicável e a classe de política de retry relança a exceção.

* **Detalhes da ligação à conta Azure Cosmos DB:** O nome da conta Azure Cassandra API, o ponto final da conta e a chave.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Parâmetros de configuração de configuração do conector de faísca

A tabela seguinte lista os parâmetros de configuração de entrada específicos da Azure Cosmos DB Cassandra API fornecidos pelo conector. Para obter uma lista detalhada de todos os parâmetros de configuração, consulte a página de referência de [configuração](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md) do repositório Spark Cassandra Connector GitHub.

| **Nome da Propriedade** | **Default value** (Valor predefinido) | **Descrição** |
|---------|---------|---------|
| spark.cassandra.output.batch.size.rows |  1 |Número de linhas por lote único. Coloque este parâmetro para 1. Este parâmetro é utilizado para obter uma maior entrada para cargas de trabalho pesadas. |
| spark.cassandra.connection.connections_per_executor_max  | Nenhuma | Número máximo de ligações por nó por executor. 10*n é equivalente a 10 ligações por nó num aglomerado de N-nó Cassandra. Então, se você precisa de 5 ligações por nó por executor para um cluster cassandra de 5 nós, então você deve definir esta configuração para 25. Modifique este valor com base no grau de paralelismo ou no número de executores para os quais os seus trabalhos de faísca estão configurados.   |
| spark.cassandra.output.concurrent.writes  |  100 | Define o número de escritos paralelos que podem ocorrer por executor. Porque configurar "batch.size.rows" para 1, certifique-se de aumentar este valor em conformidade. Modifique este valor com base no grau de paralelismo ou na entrada que pretende obter para a sua carga de trabalho. |
| spark.cassandra.concurrent.reads |  512 | Define o número de leituras paralelas que podem ocorrer por executor. Modifique este valor com base no grau de paralelismo ou na entrada que pretende alcançar para a sua carga de trabalho  |
| spark.cassandra.output.throughput_mb_per_sec  | Nenhuma | Define a produção total de escrita por executor. Este parâmetro pode ser usado como um limite superior para a sua entrada de trabalho de faísca, e baseá-lo na entrada do seu recipiente Cosmos.   |
| spark.cassandra.input.reads_per_sec| Nenhuma   | Define a produção total de leitura por executor. Este parâmetro pode ser usado como um limite superior para a sua entrada de trabalho de faísca, e baseá-lo na entrada do seu recipiente Cosmos.  |
| spark.cassandra.output.batch.grouping.buffer.size.size |  1000  | Define o número de lotes por tarefa de faísca única que pode ser armazenado na memória antes de enviar para a Cassandra API |
| spark.cassandra.connection.keep_alive_ms | 60000 | Define o período de tempo até ao qual existem ligações não utilizadas. | 

Ajuste a entrada e o grau de paralelismo destes parâmetros com base na carga de trabalho que espera para os seus trabalhos de faísca, e na entrada que forprovisionou para a sua conta Cosmos DB.

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Ligação ao Azure Cosmos DB Cassandra API de Spark

### <a name="cqlsh"></a>cqlsh
Os seguintes comandos detalham como ligar-se à API de Cassandra Azure CosmosDB a partir do Cqlsh.  Isto é útil para validação à medida que corre através das amostras em Spark.<br>
**De Linux/Unix/Mac:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1. Tijolos de dados azure
O artigo abaixo abrange o fornecimento de cluster Salões De Dados Azure, configuração de cluster para ligação à API Db Cassandra Da Azure Cosmos, e vários cadernos de amostras que cobrem operações dDL, operações DML e muito mais.<BR>
[Trabalho com a Azure Cosmos DB Cassandra API da Azure databricks](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2. Azure HDInsight-Spark
O artigo abaixo abrange o serviço HDinsight-Spark, o provisionamento, a configuração do cluster para a ligação à API Db Cassandra Da Azure Cosmos, e vários cadernos de amostras que cobrem operações dDL, operações DML e muito mais.<BR>
[Trabalho com API Db Cassandra Da Azure Cosmos da Azure HDInsight-Spark](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3. Ambiente de faíscas em geral
Embora as secções acima fossem específicas dos serviços PaaS baseados em Azure Spark, esta secção abrange qualquer ambiente geral de Spark.  As dependências do conector, as importações e a configuração da sessão spark são detalhadas abaixo. A secção "Passos Seguintes" cobre amostras de código para operações dDL, operações DML e muito mais.  

#### <a name="connector-dependencies"></a>Dependências do conector:

1. Adicione as coordenadas maven para obter o [conector Cassandra para Spark](cassandra-spark-generic.md#dependencies-for-connectivity)
2. Adicione as coordenadas maven para a [biblioteca de ajudantes Azure Cosmos DB](cassandra-spark-generic.md#dependencies-for-connectivity) para Cassandra API

#### <a name="imports"></a>Importações:

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Configuração da sessão de faísca:

```scala
//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related. You can adjust the values as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="next-steps"></a>Passos seguintes

Os seguintes artigos demonstram a integração de Spark com a Azure Cosmos DB Cassandra API. 
 
* [Operações DDL](cassandra-spark-ddl-ops.md)
* [Criar/inserir operações](cassandra-spark-create-ops.md)
* [Ler operações](cassandra-spark-read-ops.md)
* [Operações upsert](cassandra-spark-upsert-ops.md)
* [Eliminar operações](cassandra-spark-delete-ops.md)
* [Operações de agregação](cassandra-spark-aggregation-ops.md)
* [Operações de cópia de tabela](cassandra-spark-table-copy-ops.md)
