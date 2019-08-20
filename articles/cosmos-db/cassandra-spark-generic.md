---
title: Trabalhando com Azure Cosmos DB API do Cassandra do Spark
description: Este artigo é a página principal para Cosmos DB API do Cassandra integração do Spark.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: cc28cf590a1fd2c3fdfe8651f136526188801c04
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615631"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Conectar-se ao Azure Cosmos DB API do Cassandra do Spark

Este artigo é um entre uma série de artigos sobre a integração do Azure Cosmos DB API do Cassandra do Spark. Os artigos abordam conectividade, operações DDL (linguagem de definição de dados), operações DML (linguagem de manipulação de dados) básica e integração avançada de Azure Cosmos DB API do Cassandra do Spark. 

## <a name="prerequisites"></a>Pré-requisitos
* [Provisionar uma conta de API do Cassandra Azure Cosmos DB.](create-cassandra-dotnet.md#create-a-database-account)

* Provisione sua escolha do ambiente do Spark [[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) | [Azure HDInsight-Spark](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) | Outros].

## <a name="dependencies-for-connectivity"></a>Dependências para conectividade
* **Conector do Spark para Cassandra:** O conector do Spark é usado para se conectar ao Azure Cosmos DB API do Cassandra.  Identifique e use a versão do conector localizada no [Maven central]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) que é compatível com as versões Spark e escalares do seu ambiente Spark.

* **Azure Cosmos DB biblioteca auxiliar para API do Cassandra:** Além do conector do Spark, você precisa de outra biblioteca chamada [Azure-Cosmos-Cassandra-Spark-Helper]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) da Azure Cosmos DB. Esta biblioteca contém a fábrica de conexões personalizada e as classes de política de repetição.

  A política de repetição no Azure Cosmos DB é configurada para tratar exceções de código de status HTTP 429 ("taxa de solicitação grande"). O Azure Cosmos DB API do Cassandra converte essas exceções em erros sobrecarregados no protocolo nativo Cassandra, e você pode tentar novamente com as desvantagens. Como Azure Cosmos DB usa o modelo de taxa de transferência provisionado, as exceções de limitação de taxa de solicitação ocorrem quando as taxas de entrada/saída aumentam. A política de repetição protege seus trabalhos do Spark contra picos de dados que excedem momentaneamente a taxa de transferência alocada para sua coleção.

  > [!NOTE] 
  > A política de repetição pode proteger seus trabalhos do Spark apenas contra picos momentâneos. Se você não tiver configurado o RUs suficiente necessário para executar a carga de trabalho, a política de repetição não será aplicável e a classe de política de repetição relançará a exceção.

* **Detalhes da conexão da conta de Azure Cosmos DB:** O nome da conta do API do Cassandra do Azure, o ponto de extremidade da conta e a chave.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Parâmetros de configuração de taxa de transferência do conector Spark

A tabela a seguir lista Azure Cosmos DB parâmetros de configuração de taxa de transferência específicos do API do Cassandra fornecidos pelo conector. Para obter uma lista detalhada de todos os parâmetros de configuração, consulte a página [referência de configuração](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md) do repositório GitHub do conector do Spark Cassandra.

| **Nome da propriedade** | **Default value** (Valor predefinido) | **Descrição** |
|---------|---------|---------|
| spark.cassandra.output.batch.size.rows |  1 |Número de linhas por lote único. Defina esse parâmetro como 1. Esse parâmetro é usado para obter uma taxa de transferência mais alta para cargas de trabalho pesadas. |
| spark.cassandra.connection.connections_per_executor_max  | Nenhum | Número máximo de conexões por nó por executor. 10 * n é equivalente a 10 conexões por nó em um cluster Cassandra de n nós. Portanto, se você precisar de 5 conexões por nó por executor para um cluster Cassandra de 5 nós, deverá definir essa configuração como 25. Modifique esse valor com base no grau de paralelismo ou no número de executores para os quais seus trabalhos do Spark estão configurados.   |
| spark.cassandra.output.concurrent.writes  |  100 | Define o número de gravações paralelas que podem ocorrer por executor. Como você definiu "Batch. tamanho. Rows" como 1, certifique-se de escalar verticalmente esse valor de acordo. Modifique esse valor com base no grau de paralelismo ou na taxa de transferência que você deseja obter para sua carga de trabalho. |
| spark.cassandra.concurrent.reads |  512 | Define o número de leituras paralelas que podem ocorrer por executor. Modifique esse valor com base no grau de paralelismo ou na taxa de transferência que você deseja obter para sua carga de trabalho  |
| spark.cassandra.output.throughput_mb_per_sec  | Nenhum | Define a taxa de transferência de gravação total por executor. Esse parâmetro pode ser usado como um limite superior para a taxa de transferência do trabalho do Spark e baseá-lo na taxa de transferência provisionada do seu contêiner Cosmos.   |
| spark.cassandra.input.reads_per_sec| Nenhum   | Define a taxa de transferência de leitura total por executor. Esse parâmetro pode ser usado como um limite superior para a taxa de transferência do trabalho do Spark e baseá-lo na taxa de transferência provisionada do seu contêiner Cosmos.  |
| spark.cassandra.output.batch.grouping.buffer.size |  1000  | Define o número de lotes por tarefa única do Spark que pode ser armazenado na memória antes de enviar para API do Cassandra |
| spark.cassandra.connection.keep_alive_ms | 60000 | Define o período de tempo até o qual as conexões não utilizadas estão disponíveis. | 

Ajuste a taxa de transferência e o grau de paralelismo desses parâmetros com base na carga de trabalho esperada para seus trabalhos do Spark e a taxa de transferência que você provisionou para sua conta de Cosmos DB.

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Conectando-se ao Azure Cosmos DB API do Cassandra do Spark

### <a name="cqlsh"></a>cqlsh
Os comandos a seguir detalham como se conectar ao Azure CosmosDB API do Cassandra do cqlsh.  Isso é útil para validação à medida que você executa os exemplos no Spark.<br>
**Do Linux/Unix/Mac:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1.  Azure Databricks
O artigo a seguir aborda Azure Databricks provisionamento de cluster, configuração de cluster para conexão com Azure Cosmos DB API do Cassandra e vários blocos de anotações de exemplo que abrangem operações DDL, operações DML e muito mais.<BR>
[Trabalhar com Azure Cosmos DB API do Cassandra do Azure databricks](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2.  Azure HDInsight-Spark
O artigo a seguir aborda o serviço HDinsight-Spark, provisionamento, configuração de cluster para se conectar a Azure Cosmos DB API do Cassandra e vários blocos de anotações de exemplo que abrangem operações DDL, operações DML e muito mais.<BR>
[Trabalhar com API do Cassandra de Azure Cosmos DB do Azure HDInsight-Spark](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3.  Ambiente do Spark em geral
Embora as seções acima fossem específicas para os serviços de PaaS baseados no Azure Spark, esta seção aborda qualquer ambiente Spark geral.  As dependências de conector, importações e configuração de sessão do Spark são detalhadas abaixo. A seção "próximas etapas" aborda exemplos de código para operações DDL, operações DML e muito mais.  

#### <a name="connector-dependencies"></a>Dependências do conector:

1. Adicionar as coordenadas do Maven para obter o [conector do Cassandra para Spark](cassandra-spark-generic.md#dependencies-for-connectivity)
2. Adicione as coordenadas do Maven para a [biblioteca auxiliar de Azure Cosmos DB](cassandra-spark-generic.md#dependencies-for-connectivity) para API do Cassandra

#### <a name="imports"></a>Importar

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Configuração da sessão do Spark:

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

Os artigos a seguir demonstram a integração do Spark com o Azure Cosmos DB API do Cassandra. 
 
* [Operações DDL](cassandra-spark-ddl-ops.md)
* [Operações de criação/inserção](cassandra-spark-create-ops.md)
* [Operações de leitura](cassandra-spark-read-ops.md)
* [Operações de Upsert](cassandra-spark-upsert-ops.md)
* [Operações de eliminação](cassandra-spark-delete-ops.md)
* [Operações de agregação](cassandra-spark-aggregation-ops.md)
* [Operações de cópia da tabela](cassandra-spark-table-copy-ops.md)
