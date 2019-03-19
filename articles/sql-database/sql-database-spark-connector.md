---
title: Conector com a base de dados SQL do Azure e o SQL Server do spark | Documentos da Microsoft
description: Saiba como utilizar o conector do Spark para a base de dados do Azure SQL e SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: 8e531de34302ef8aee571c960955d33a4832aa11
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58013709"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Acelerar a análise de macrodados em tempo real com o conector do Spark para a base de dados do Azure SQL e SQL Server

O conector do Spark para Azure SQL Database e o SQL Server permite que as bases de dados SQL, incluindo a base de dados do Azure SQL e SQL Server, para atuar como sink de dados de origem ou de saída de dados de entrada para tarefas do Spark. Permite-lhe utilizar dados transacionais em tempo real na análise de macrodados e manter os resultados de consultas ad hoc ou de relatório. Em comparação com o conector JDBC incorporado, este conector proporciona a capacidade de inserir dados em massa em bases de dados SQL. Ele pode superar o desempenho de inserção de linha por linha com x 10 a 20 desempenho mais rápido de x. O conector do Spark para Azure SQL Database e o SQL Server também suporta a autenticação de AAD. Ele permite-lhe ligar a sua base de dados SQL do Azure com segurança do Azure Databricks com a sua conta do AAD. Ele fornece interfaces semelhante com o conector JDBC incorporado. É fácil migrar suas tarefas do Spark existentes para utilizar este conector novo.

## <a name="download"></a>Transferência
Para começar, transfira o Spark para o conector de BD SQL a partir da [repositório azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) no GitHub.

## <a name="official-supported-versions"></a>Versões suportadas do oficial

| Componente                            |Versão                  |
| :----------------------------------- | :---------------------- |
| Apache Spark                         |2.0.2 ou posterior           |
| Scala                                |2.10 ou posterior            |
| Microsoft JDBC Driver para SQL Server |6.2 ou posterior             |
| Microsoft SQL Server                 |SQL Server 2008 ou posterior |
| Base de Dados SQL do Azure                   |Suportadas                |

O conector do Spark para Azure SQL Database e o SQL Server utiliza o controlador Microsoft JDBC para SQL Server para mover dados entre nós de trabalho do Spark e bases de dados SQL:
 
O fluxo de dados é o seguinte:
1. O nó mestre do Spark liga-se ao SQL Server ou base de dados do Azure SQL e carrega dados a partir de uma tabela específica ou usando uma consulta SQL específica
2. O nó mestre do Spark distribui os dados para nós de trabalho de transformação. 
3. Nó de trabalho liga-se ao SQL Server ou base de dados do Azure SQL e escreve dados na base de dados. Utilizador pode optar por utilizar a inserção de linha por linha ou em massa insert.

O diagrama seguinte ilustra o fluxo de dados.

   ![arquitetura](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>Criar o Spark para o conector de BD SQL
Atualmente, o projeto de conector utiliza o maven. Para criar o conector sem dependências, pode executar:

- pacote de limpa arquétipo
- Baixe as versões mais recentes do JAR a partir da pasta de versão
- Incluir o SQL DB Spark JAR

## <a name="connect-spark-to-sql-db-using-the-connector"></a>Ligar o Spark para BD SQL com o conector
Pode ligar à base de dados do Azure SQL ou SQL Server a partir de tarefas do Spark, ler ou escrever dados. Também pode executar uma consulta DML ou DDL numa base de dados SQL do Azure ou a base de dados do SQL Server.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Ler dados da base de dados do Azure SQL ou SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients"
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Leitura de dados da base de dados do Azure SQL ou SQL Server com a consulta especificada do SQL
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
val collection = sqlContext.read.sqlDb(config)
collection.show()
```

### <a name="write-data-to-azure-sql-database-or-sql-server"></a>Escrever dados para a base de dados do Azure SQL ou SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
 
// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients"
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>Executar consultas DML ou DDL na base de dados do Azure SQL ou SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City= 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.SqlDBQuery(config)
```

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>Ligar o Spark para a base de dados do SQL Azure utilizando a autenticação de AAD
Pode ligar à base de dados do SQL do Azure através da autenticação do Azure Active Directory (AAD). Utilize autenticação do AAD para gerir centralmente identidades de utilizadores de base de dados e como uma alternativa à autenticação do SQL Server.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Se ligar com o modo de autenticação ActiveDirectoryPassword
#### <a name="setup-requirement"></a>Requisito de configuração
Se estiver a utilizar o modo de autenticação ActiveDirectoryPassword, precisa baixar [java do azure-activedirectory-library-para](https://github.com/AzureAD/azure-activedirectory-library-for-java) e as respetivas dependências e incluí-los no caminho de compilação de Java.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username ",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

### <a name="connecting-using-access-token"></a>Se ligar com o Token de acesso
#### <a name="setup-requirement"></a>Requisito de configuração
Se estiver a utilizar o modo de autenticação baseada em tokens de acesso, precisa baixar [java do azure-activedirectory-library-para](https://github.com/AzureAD/azure-activedirectory-library-for-java) e as respetivas dependências e incluí-los no caminho de compilação de Java.

Ver [Use Azure autenticação do Active Directory para autenticação com base de dados SQL](sql-database-aad-authentication.md) para saber como obter acesso token para a base de dados SQL do Azure.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token ",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Escrever dados no banco de dados SQL do Azure ou SQL Server com a inserção em massa
O conector do jdbc tradicional escreve dados na base de dados SQL do Azure ou SQL Server com a inserção de linha por linha. Pode utilizar o Spark para o conector de BD SQL para escrever dados para a base de dados SQL com a inserção em massa. Melhora significativamente o desempenho de escrita ao carregar grandes conjuntos de dados ou ao carregar dados para tabelas em que é utilizado um índice columnstore.

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
  "databaseName"      -> "zeqisql",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>Passos Seguintes
Se ainda não o fez, transfira o conector do Spark para a base de dados do Azure SQL e SQL Server a partir [repositório do GitHub do azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) e explore os recursos adicionais no repositório:

-   [Blocos de notas do exemplo do Azure Databricks](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Scripts de exemplo (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Pode também querer rever os [guia de conjuntos de dados, Apache Spark SQL e DataFrames](https://spark.apache.org/docs/latest/sql-programming-guide.html) e o [documentação do Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

