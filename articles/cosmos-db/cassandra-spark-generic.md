---
title: Trabalhar com Azure Cosmos DB Cassandra API da Spark
description: Este artigo é a página principal da integração da API da Cosmos DB Cassandra da Spark.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/01/2019
ms.openlocfilehash: d25e168e342e22af9dc41d31dd7e18530aaa22b8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93090516"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Connect to Azure Cosmos DB Cassandra API from Spark (Ligar à API para Cassandra do Azure Cosmos DB a partir do Spark)
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Este artigo é um entre uma série de artigos sobre a integração da Azure Cosmos DB Cassandra API da Spark. Os artigos abrangem a conectividade, as operações de linguagem de definição de dados (DDL), operações básicas de linguagem de manipulação de dados (DML) e a integração avançada da Azure Cosmos DB Cassandra API da Spark. 

## <a name="prerequisites"></a>Pré-requisitos
* [Fornece uma conta Azure Cosmos DB Cassandra API.](create-cassandra-dotnet.md#create-a-database-account)

* Provisão da sua escolha do ambiente Spark [[Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)  |  [Azure HDInsight-Spark](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) ! Outros].

## <a name="dependencies-for-connectivity"></a>Dependências da conectividade
* **Conector de faísca para Cassandra:** O conector de faíscas é utilizado para ligar à Azure Cosmos DB Cassandra API.  Identifique e utilize a versão do conector localizado na [central de Maven]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) que é compatível com as versões Spark e Scala do seu ambiente Spark.

* **Biblioteca de ajudantes Azure Cosmos DB para Cassandra API:** Além do conector Spark, você precisa de outra biblioteca chamada [azure-cosmos-cassandra-spark-helper]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) de Azure Cosmos DB. Esta biblioteca contém fábrica de conexão personalizada e aulas de política de repetição.

  A política de reação em Azure Cosmos DB está configurada para lidar com exceções http status code 429 ("Taxa de pedido grande"). A Azure Cosmos DB Cassandra API traduz estas exceções em erros sobrecarregados no protocolo nativo de Cassandra, e você pode voltar a tentar com back-offs. Como a Azure Cosmos DB utiliza um modelo de produção previsto, as exceções de taxa de pedido ocorrem quando as taxas de entrada/saída aumentam. A política de represão protege os seus empregos de faísca contra picos de dados que momentaneamente excedem a produção atribuída ao seu contentor.

  > [!NOTE] 
  > A política de recandidão pode proteger os seus empregos de faíscas apenas contra picos momentâneos. Se não tiver configurado rUs suficientes necessários para executar a sua carga de trabalho, então a política de reagem não é aplicável e a classe política de retry reensibra a exceção.

* **Detalhes da ligação da conta Azure Cosmos DB:** O nome da conta Azure Cassandra API, o ponto final da conta e a chave.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Parâmetros de configuração do conector de faísca

A tabela que se segue lista os parâmetros de configuração de produção específicos da API Azure Cosmos DB Cassandra fornecidos pelo conector. Para obter uma lista detalhada de todos os parâmetros de configuração, consulte a página de referência de [configuração](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md) do repositório Do Conector De Faíscas GitHub.

| **Nome da propriedade** | **Valor predefinido** | **Descrição** |
|---------|---------|---------|
| spark.cassandra.output.batch.size.rows |  1 |Número de linhas por único lote. Desa um parâmetro para 1. Este parâmetro é usado para obter uma produção mais elevada para cargas de trabalho pesadas. |
| spark.cassandra.connection.connections_per_executor_max  | Nenhum | Número máximo de ligações por nó por executor. 10*n é equivalente a 10 ligações por nó num aglomerado de Cassandra n-node. Assim, se necessitar de 5 ligações por nó por executor para um cluster Cassandra de 5 nós, então deve definir esta configuração para 25. Modifique este valor com base no grau de paralelismo ou no número de executores para os quais os seus trabalhos de faísca estão configurados.   |
| spark.cassandra.output.concurrent.writes  |  100 | Define o número de escritos paralelos que podem ocorrer por executor. Como configura "batch.size.rows" para 1, certifique-se de aumentar este valor em conformidade. Modifique este valor com base no grau de paralelismo ou na produção que pretende alcançar para a sua carga de trabalho. |
| spark.cassandra.concurrent.reads |  512 | Define o número de leituras paralelas que podem ocorrer por executor. Modifique este valor com base no grau de paralelismo ou na produção que pretende alcançar para a sua carga de trabalho  |
| spark.cassandra.output.throughput_mb_per_sec  | Nenhum | Define a produção total de escrita por executor. Este parâmetro pode ser usado como um limite superior para a sua produção de faísca, e baseá-lo na produção prevista do seu recipiente Cosmos.   |
| spark.cassandra.input.reads_per_sec| Nenhum   | Define a produção total de leitura por executor. Este parâmetro pode ser usado como um limite superior para a sua produção de faísca, e baseá-lo na produção prevista do seu recipiente Cosmos.  |
| spark.cassandra.output.batch.grouping.buffer.size |  1000  | Define o número de lotes por única tarefa de faísca que pode ser armazenado na memória antes de enviar para Cassandra API |
| spark.cassandra.connection.keep_alive_ms | 60000 | Define o período de tempo até ao qual estão disponíveis ligações não disponíveis. | 

Ajuste a produção e o grau de paralelismo destes parâmetros com base na carga de trabalho que espera para os seus trabalhos de faísca, e na produção que advogaste para a sua conta Cosmos DB.

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Ligação à Azure Cosmos DB Cassandra API da Spark

### <a name="cqlsh"></a>cqlsh
Os seguintes comandos detalham como ligar-se à Azure CosmosDB Cassandra API do cqlsh.  Isto é útil para validação enquanto analisa as amostras em Spark.<br>
**De Linux/Unix/Mac:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1. Azure Databricks
O artigo abaixo abrange o fornecimento de clusters Azure Databricks, configuração de cluster para ligação à Azure Cosmos DB Cassandra API, e vários cadernos de amostras que cobrem operações DDL, operações DML e muito mais.<BR>
[Trabalhar com a Azure Cosmos DB Cassandra API a partir de databricks da Azure](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2. Azure HDInsight-Spark
O artigo abaixo abrange HDinsight-Spark serviço, fornecimento, configuração de cluster para ligação à Azure Cosmos DB Cassandra API, e vários cadernos de amostras que cobrem operações DDL, operações DML e muito mais.<BR>
[Trabalhar com Azure Cosmos DB Cassandra API da Azure HDInsight-Spark](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3. Ambiente de faíscas em geral
Embora as secções acima fossem específicas dos serviços PaaS baseados em Azure Spark, esta secção abrange qualquer ambiente geral de Faísca.  As dependências do conector, as importações e a configuração da sessão de faísca são detalhadas abaixo. A secção "Próximos passos" abrange amostras de código para operações DDL, operações DML e muito mais.  

#### <a name="connector-dependencies"></a>Dependências do conector:

1. Adicione as coordenadas maven para obter o [conector Cassandra para faísca](cassandra-spark-generic.md#dependencies-for-connectivity)
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

#### <a name="spark-session-configuration"></a>Configuração da sessão de faíscas:

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

Os seguintes artigos demonstram a integração da Spark com a Azure Cosmos DB Cassandra API. 
 
* [Operações DDL](cassandra-spark-ddl-ops.md)
* [Criar/inserir operações](cassandra-spark-create-ops.md)
* [Operações de leitura](cassandra-spark-read-ops.md)
* [Operações de upsert](cassandra-spark-upsert-ops.md)
* [Operações de eliminação](cassandra-spark-delete-ops.md)
* [Operações de agregação](cassandra-spark-aggregation-ops.md)
* [Operações de cópia de mesa](cassandra-spark-table-copy-ops.md)