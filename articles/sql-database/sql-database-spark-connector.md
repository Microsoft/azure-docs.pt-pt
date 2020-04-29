---
title: Conector de faísca com base de dados Azure SQL e Servidor SQL
description: Saiba como utilizar o Conector spark para base de dados SQL Azure e servidor SQL
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: denzilribeiro
ms.author: denzilr
ms.reviewer: carlrab
ms.date: 09/25/2018
ms.openlocfilehash: b22ec475c0281a54d65921bc450b35723aa23219
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77471657"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Acelere a análise de big data em tempo real com conector Spark para Base de Dados Azure SQL e Servidor SQL

O conector Spark para a Base de Dados Azure SQL e o Servidor SQL permite que as bases de dados SQL, incluindo a Base de Dados Azure SQL e o Servidor SQL, atuem como fonte de dados de entrada ou sumidouro de dados de saída para trabalhos spark. Permite-lhe utilizar dados transacionais em tempo real na análise de big data e persistir resultados para consultas adhoc ou relatórios. Em comparação com o conector JDBC incorporado, este conector fornece a capacidade de inserir dados a granel nas bases de dados SQL. Pode superar a inserção de linha por linha com um desempenho 10x a 20x mais rápido. O conector Spark para base de dados Azure SQL e SQL Server também suporta a autenticação AAD. Permite-lhe ligar-se de forma segura à sua base de dados Azure SQL a partir de Bricks Azure, utilizando a sua conta AAD. Fornece interfaces semelhantes com o conector JDBC incorporado. É fácil migrar os seus empregos de Faísca existentes para usar este novo conector.

## <a name="download"></a>Transferência
Para começar, descarregue o conector Spark para SQL DB do [repositório de faíscas azure-sqldb](https://github.com/Azure/azure-sqldb-spark) no GitHub.

## <a name="official-supported-versions"></a>Versões Oficiais Suportadas

| Componente                            | Versão                  |
| :----------------------------------- | :----------------------- |
| Apache Spark                         | 2.0.2 ou mais tarde           |
| Scala                                | 2.10 ou mais tarde            |
| Controlador Microsoft JDBC para SQL Server | 6.2 ou mais tarde             |
| Microsoft SQL Server                 | SQL Server 2008 ou posterior |
| Base de Dados SQL do Azure                   | Suportado                |

O conector Spark para a Base de Dados Azure SQL e o Servidor SQL utiliza o Controlador Microsoft JDBC para o SQL Server para mover dados entre nós de trabalhador estojo e bases de dados SQL:
 
O fluxo de dados é o seguinte:
1. O nó principal spark conecta-se ao SQL Server ou à Base de Dados Azure SQL e carrega dados de uma tabela específica ou usando uma consulta SQL específica
2. O nó mestre Spark distribui dados aos nódosos dos trabalhadores para transformação. 
3. O nó do Trabalhador liga-se ao SQL Server ou à Base de Dados Azure SQL e escreve dados à base de dados. O utilizador pode optar por utilizar a inserção remada ou a inserção a granel.

O diagrama que se segue ilustra o fluxo de dados.

   ![arquitetura](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>Construa o conector SQL DB
Atualmente, o projeto de conector utiliza maven. Para construir o conector sem dependências, pode correr:

- pacote limpo mvn
- Descarregue as versões mais recentes do JAR a partir da pasta de lançamento
- Inclua o SQL DB Spark JAR

## <a name="connect-spark-to-sql-db-using-the-connector"></a>Ligar a Faísca ao SQL DB utilizando o conector
Pode ligar-se à Base de Dados Azure SQL ou ao Servidor SQL a partir de trabalhos spark, ler ou escrever dados. Também pode executar uma consulta DML ou DDL numa base de dados Azure SQL ou na base de dados do SQL Server.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Leia os dados da Base de Dados Azure SQL ou do Servidor SQL

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients",
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Dados de leitura da Base de Dados Azure SQL ou do Servidor SQL com consulta SQL especificada
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "queryCustom"  -> "SELECT TOP 100 * FROM dbo.Clients WHERE PostalCode = 98074" //Sql query
  "user"         -> "username",
  "password"     -> "*********",
))

//Read all data in table dbo.Clients
val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="write-data-to-azure-sql-database-or-sql-server"></a>Escreva dados para a Base de Dados Azure SQL ou para o Servidor SQL
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
 
// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients",
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>Executar consulta DML ou DDL em Base de Dados SQL Azure ou Servidor SQL
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City = 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.sqlDBQuery(config)
```

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>Ligar a Spark à Base de Dados SQL Azure utilizando a autenticação AAD
Pode ligar-se à Base de Dados Azure SQL utilizando a autenticação do Diretório Ativo Azure (AAD). Utilize a autenticação AAD para gerir centralmente as identidades dos utilizadores de bases de dados e como alternativa à autenticação do Servidor SQL.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Ligação utilizando o modo de autenticação ActiveDirectoryPassword
#### <a name="setup-requirement"></a>Requisito de configuração
Se estiver a utilizar o modo de autenticação ActiveDirectoryPassword, precisa de descarregar [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) e das suas dependências, e incluí-los no caminho de construção Java.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="connecting-using-access-token"></a>Ligação usando Access Token
#### <a name="setup-requirement"></a>Requisito de configuração
Se estiver a utilizar o modo de autenticação baseado em token de acesso, precisa de descarregar [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) e suas dependências, e incluí-los no caminho de construção java.

Consulte [a autenticação de diretório ativo Azure para autenticação com base de dados SQL](sql-database-aad-authentication.md) para aprender a aceder à sua base de dados Azure SQL.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Escreva dados para base de dados Azure SQL ou Servidor SQL usando a introdução a granel
O conector jdbc tradicional escreve dados na base de dados Azure SQL ou no SQL Server utilizando a inserção row-by-row. Pode utilizar o conector Spark to SQL DB para escrever dados na base de dados SQL utilizando a inserção a granel. Melhora significativamente o desempenho da escrita ao carregar grandes conjuntos de dados ou carregar dados em tabelas onde é utilizado um índice de loja de colunas.

```scala
import com.microsoft.azure.sqldb.spark.bulkcopy.BulkCopyMetadata
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

/** 
  Add column Metadata.
  If not specified, metadata is automatically added
  from the destination table, which may suffer performance.
*/
var bulkCopyMetadata = new BulkCopyMetadata
bulkCopyMetadata.addColumnMetadata(1, "Title", java.sql.Types.NVARCHAR, 128, 0)
bulkCopyMetadata.addColumnMetadata(2, "FirstName", java.sql.Types.NVARCHAR, 50, 0)
bulkCopyMetadata.addColumnMetadata(3, "LastName", java.sql.Types.NVARCHAR, 50, 0)

val bulkCopyConfig = Config(Map(
  "url"               -> "mysqlserver.database.windows.net",
  "databaseName"      -> "MyDatabase",
  "user"              -> "username",
  "password"          -> "*********",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>Passos seguintes
Se ainda não o fez, descarregue o conector Spark para a Base de Dados Azure SQL e o SQL Server do [repositório GitHub de faísca sqldb-spark e](https://github.com/Azure/azure-sqldb-spark) explore os recursos adicionais no repo:

- [Amostra de cadernos de databricks Azure](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Scripts de amostra (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Também pode querer rever o [Apache Spark SQL, DataFrames e Datasets Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html) e a [documentação de Tijolos](https://docs.microsoft.com/azure/azure-databricks/)de Dados Azure .

