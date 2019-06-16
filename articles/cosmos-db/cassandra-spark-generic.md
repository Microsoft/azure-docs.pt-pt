---
title: Trabalhar com o Azure Cosmos DB Cassandra API do Spark
description: Este artigo é a página principal para a integração do Cassandra API do Cosmos DB do Spark.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 75d2930363b6ad1aeace22d7529df04f31deefe5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60893641"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Ligar ao Azure Cosmos DB Cassandra API a partir do Spark

Este artigo é uma dentre uma série de artigos sobre a integração de API de Cassandra do Azure Cosmos DB do Spark. Os artigos sobre conectividade, operações Language(DDL) de definição de dados, operações básicas de Language(DML) de manipulação de dados e integração avançada de API de Cassandra do Azure Cosmos DB do Spark. 

## <a name="prerequisites"></a>Pré-requisitos
* [Aprovisione uma conta de API de Cassandra do Azure Cosmos DB.](create-cassandra-dotnet.md#create-a-database-account)

* Aprovisionar à sua escolha do ambiente de Spark [[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) | [do Azure HDInsight-Spark](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) | Outros].

## <a name="dependencies-for-connectivity"></a>Dependências de conectividade
* **Conector do Spark para Cassandra:** Conector do Spark é utilizada para ligar à API de Cassandra do Azure Cosmos DB.  Identificar e utilizar a versão do conector localizado em [Maven central]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) que é compatível com as versões do Spark e Scala do seu ambiente do Spark.

* **Biblioteca de programa auxiliar de Cosmos DB do Azure para a API de Cassandra:** Além do conector do Spark, terá de outra biblioteca chamada [azure-cosmos-cassandra-spark-auxiliar]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) do Azure Cosmos DB. Esta biblioteca contém classes da diretiva de conexão personalizada factory e tente novamente.

  A política de repetição no Azure Cosmos DB está configurada para manipular HTTP status código 429 ("grande de taxa de pedido") exceções. A API de Cassandra do Azure Cosmos DB traduz essas exceções em erros sobrecarregados no protocolo nativo Cassandra e, pode voltar a tentar com back e desvantagens. Como o Azure Cosmos DB utiliza o modelo de débito aprovisionado, exceções de limitação de taxa de pedido ocorrerem quando a entrada/saída taxas de aumento. A política de repetição protege as suas tarefas do spark em relação a picos de dados que excedem momentaneamente a taxa de transferência alocada para a sua coleção.

  > [!NOTE] 
  > A política de repetição pode proteger as suas tarefas do spark contra momentânea picos apenas. Se não tiver configurado o suficiente RUs necessários para executar a sua carga de trabalho, em seguida, a política de repetição não é aplicável e a classe de política de repetição gera novamente a exceção.

* **Detalhes de ligação de conta do Azure Cosmos DB:** O nome da conta Cassandra API do Azure, um ponto final da conta e chave.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Parâmetros de configuração do débito de conector do Spark

A tabela seguinte lista os parâmetros de configuração específicas de API de Cassandra do Azure Cosmos DB débito fornecidos pelo conector. Para obter uma lista detalhada de todos os parâmetros de configuração, consulte [referência de configuração](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md) página do repositório do GitHub de conector do Spark Cassandra.

| **Nome da propriedade** | **Default value** (Valor predefinido) | **Descrição** |
|---------|---------|---------|
| spark.cassandra.output.batch.size.rows |  1 |Número de linhas por lote única. Defina este parâmetro como 1. Este parâmetro é utilizado para alcançar um débito mais elevado para cargas de trabalho pesadas. |
| spark.cassandra.connection.connections_per_executor_max  | Nenhuma | Número máximo de ligações por nó por executor. 10 * n é equivalente a 10 ligações por nó num cluster do Cassandra n nós. Assim, se necessitar de 5 ligações por nó por executor para um cluster do Cassandra de 5 nós, em seguida, deve definir esta configuração para 25. Modificar este valor com base no grau de paralelismo ou o número de executores configuradas para as tarefas do spark.   |
| spark.cassandra.output.concurrent.writes  |  100 | Define o número de gravações paralelas que pode ocorrer por executor. Porque definiu "batch.size.rows" como 1, certifique-se aumentar este valor em conformidade. Modificar este valor com base no grau de paralelismo ou o débito que quer atingir a carga de trabalho. |
| spark.cassandra.concurrent.reads |  512 | Define o número de leituras paralelas que pode ocorrer por executor. Modificar este valor com base no grau de paralelismo ou o débito que quer atingir a carga de trabalho  |
| spark.cassandra.output.throughput_mb_per_sec  | Nenhuma | Define o débito total de escrita por executor. Este parâmetro pode ser utilizado como um upper limitar para sua taxa de transferência de tarefa do spark e baseá-la no débito aprovisionado de sua coleção do Cosmos DB.   |
| spark.cassandra.input.reads_per_sec| Nenhuma   | Define o débito de leitura total por executor. Este parâmetro pode ser utilizado como um upper limitar para sua taxa de transferência de tarefa do spark e baseá-la no débito aprovisionado de sua coleção do Cosmos DB.  |
| spark.cassandra.output.batch.grouping.buffer.size |  1000  | Define o número de lotes por tarefa do spark única que podem ser armazenados na memória antes de enviar a API de Cassandra |
| spark.cassandra.connection.keep_alive_ms | 60000 | Define o período de tempo até que as ligações não utilizadas estão disponíveis. | 

Ajuste o débito e o grau de paralelismo desses parâmetros com base na carga de trabalho esperado para as tarefas do spark e o débito que aprovisionou para a sua conta do Cosmos DB.

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Ligar ao Azure Cosmos DB Cassandra API do Spark

### <a name="cqlsh"></a>cqlsh
Os comandos seguintes detalham como ligar à API de Cassandra do Azure cosmos DB a partir de cqlsh.  Isto é útil para validação como executar os exemplos no Spark.<br>
**Do Unix/Linux/Mac:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1.  Azure Databricks
No artigo abaixo abrange o cluster do Azure Databricks, aprovisionamento, configuração de cluster para ligar à API de Cassandra do Azure Cosmos DB e vários blocos de notas de exemplo que abrangem operações DDL, operações DML e muito mais.<BR>
[Trabalhar com a API de Cassandra do Azure Cosmos DB a partir do Azure databricks](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2.  O Azure HDInsight-Spark
No artigo abaixo abrange serviço HDinsight Spark, o aprovisionamento, a configuração de cluster para ligar à API de Cassandra do Azure Cosmos DB e vários blocos de notas de exemplo que abrangem operações DDL, operações DML e muito mais.<BR>
[Trabalhar com o Azure Cosmos DB Cassandra API do Azure HDInsight-Spark](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3.  Ambiente do spark em geral
Embora as seções acima foram específicas aos serviços com base em Spark de Azure PaaS, esta secção abrange qualquer ambiente geral do Spark.  Dependências de conector, imports e configuração de sessão do Spark são detalhados abaixo. A secção "Passos seguintes" inclui exemplos de código para operações DDL, operações DML e muito mais.  

#### <a name="connector-dependencies"></a>Dependências de conector:

1. Adicionar as coordenadas do maven para obter o [conector do Cassandra para o Spark](cassandra-spark-generic.md#dependencies-for-connectivity)
2. Adicionar as coordenadas do maven para o [biblioteca de auxiliar do Azure Cosmos DB](cassandra-spark-generic.md#dependencies-for-connectivity) para a API de Cassandra

#### <a name="imports"></a>Importações:

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Configuração de sessão do Spark:

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

## <a name="next-steps"></a>Passos Seguintes

Os artigos seguintes demonstram a integração do Spark com o Cassandra API do Azure Cosmos DB. 
 
* [Operações DDL](cassandra-spark-ddl-ops.md)
* [Criar/insert operações](cassandra-spark-create-ops.md)
* [operações de leitura](cassandra-spark-read-ops.md)
* [Operações de Upsert](cassandra-spark-upsert-ops.md)
* [Operações de eliminação](cassandra-spark-delete-ops.md)
* [Operações de agregação](cassandra-spark-aggregation-ops.md)
* [Operações de cópia da tabela](cassandra-spark-table-copy-ops.md)
