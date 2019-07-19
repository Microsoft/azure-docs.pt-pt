---
title: Conector do Spark com Banco de dados SQL do Azure e SQL Server | Microsoft Docs
description: Saiba como usar o conector do Spark para o banco de dados SQL do Azure e SQL Server
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
ms.openlocfilehash: 192387958785e4032a1cb549d0ba071fa406a60e
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68228228"
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Acelere a análise de Big Data em tempo real com o conector do Spark para o banco de dados SQL do Azure e SQL Server

O conector do Spark para o banco de dados SQL do Azure e o SQL Server habilita bancos de dados SQL, incluindo o banco de dados SQL do Azure e o SQL Server, para atuar como fonte de dado de entrada ou coletor de dado de saída para trabalhos Spark. Ele permite que você utilize dados transacionais em tempo real na análise de Big Data e persiste resultados para consultas ou relatórios adhoc. Em comparação com o conector JDBC interno, esse conector fornece a capacidade de inserir dados em massa em bancos de dado SQL. Ele pode superar o desempenho de linha por linha com 10 vezes para 20xr mais rápido. O conector do Spark para o banco de dados SQL do Azure e o SQL Server também oferece suporte à autenticação do AAD. Ele permite que você se conecte com segurança ao seu banco de dados SQL do Azure de Azure Databricks usando sua conta do AAD. Ele fornece interfaces semelhantes com o conector JDBC interno. É fácil migrar seus trabalhos do Spark existentes para usar esse novo conector.

## <a name="download"></a>Transferência
Para começar, baixe o conector do Spark para o BD SQL do [repositório Azure-SQLDB-Spark](https://github.com/Azure/azure-sqldb-spark) no github.

## <a name="official-supported-versions"></a>Versões oficiais com suporte

| Componente                            |Versão                  |
| :----------------------------------- | :---------------------- |
| Apache Spark                         |2.0.2 ou posterior           |
| Scala                                |2,10 ou posterior            |
| Microsoft JDBC Driver para SQL Server |6,2 ou posterior             |
| Microsoft SQL Server                 |SQL Server 2008 ou posterior |
| Base de Dados SQL do Azure                   |Suportadas                |

O conector do Spark para o banco de dados SQL do Azure e o SQL Server utiliza o Microsoft JDBC Driver para SQL Server para mover dados entre nós de trabalho do Spark e bancos de dados SQL:
 
A Dataflow é a seguinte:
1. O nó mestre do Spark conecta-se ao SQL Server ou ao banco de dados SQL do Azure e carrega um dado de uma tabela específica ou usando uma consulta SQL específica
2. O nó mestre do Spark distribui dados para nós de trabalho para transformação. 
3. O nó de trabalho conecta-se ao SQL Server ou ao banco de dados SQL do Azure e grava os dados no banco de dado. O usuário pode optar por usar inserção de linha por linha ou inserção em massa.

O diagrama a seguir ilustra o fluxo de dados.

   ![arquitetura](./media/sql-database-spark-connector/architecture.png)

### <a name="build-the-spark-to-sql-db-connector"></a>Criar o conector do Spark ao BD SQL
Atualmente, o projeto do conector usa o Maven. Para criar o conector sem dependências, você pode executar:

- MVN limpar pacote
- Baixar as versões mais recentes do JAR da pasta de liberação
- Incluir o SQL DB Spark JAR

## <a name="connect-spark-to-sql-db-using-the-connector"></a>Conectar o Spark ao BD SQL usando o conector
Você pode se conectar ao banco de dados SQL do Azure ou SQL Server a partir de trabalhos do Spark, ler ou gravar os dados. Você também pode executar uma consulta DML ou DDL em um banco de dados SQL do Azure ou SQL Server banco de dados.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Ler dados do banco de dados SQL do Azure ou SQL Server

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
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Lendo dados do banco de dados SQL do Azure ou SQL Server com a consulta SQL especificada
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

### <a name="write-data-to-azure-sql-database-or-sql-server"></a>Gravar dados no banco de dados SQL do Azure ou SQL Server
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

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>Executar a consulta DML ou DDL no banco de dados SQL do Azure ou SQL Server
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

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>Conectar o Spark ao banco de dados SQL do Azure usando a autenticação do AAD
Você pode se conectar ao banco de dados SQL do Azure usando a autenticação do Azure Active Directory (AAD). Use a autenticação do AAD para gerenciar centralmente identidades de usuários de banco de dados e como uma alternativa à autenticação SQL Server.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Conectando usando o modo de autenticação ActiveDirectoryPassword
#### <a name="setup-requirement"></a>Requisito de instalação
Se você estiver usando o modo de autenticação ActiveDirectoryPassword, será necessário baixar [Azure-ActiveDirectory-library-for-Java](https://github.com/AzureAD/azure-activedirectory-library-for-java) e suas dependências e incluí-las no caminho de compilação do Java.

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

### <a name="connecting-using-access-token"></a>Conectando usando o token de acesso
#### <a name="setup-requirement"></a>Requisito de instalação
Se você estiver usando o modo de autenticação baseada em token de acesso, será necessário baixar o [Azure-ActiveDirectory-library-for-Java](https://github.com/AzureAD/azure-activedirectory-library-for-java) e suas dependências e incluí-los no caminho de compilação do Java.

Consulte [usar a autenticação Azure Active Directory para autenticação com o banco de dados SQL](sql-database-aad-authentication.md) para saber como obter o token de acesso para seu banco de dados SQL do Azure.

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

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Gravar dados no banco de dados SQL do Azure ou SQL Server usando inserção em massa
O conector JDBC tradicional grava dados no banco de dados SQL do Azure ou SQL Server usando a inserção linha por linha. Você pode usar o conector do Spark ao banco de dados SQL para gravar dados no SQL Database usando BULK INSERT. Ele melhora significativamente o desempenho de gravação ao carregar grandes conjuntos de dados ou carregar dados em tabelas em que um índice de repositório de coluna é usado.

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

## <a name="next-steps"></a>Passos Seguintes
Se você ainda não fez isso, baixe o conector do Spark para o banco de dados SQL do Azure e SQL Server do [repositório GitHub Azure-SQLDB-Spark](https://github.com/Azure/azure-sqldb-spark) e explore os recursos adicionais no repositório:

-   [Blocos de anotações de Azure Databricks de exemplo](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Scripts de exemplo (escalabilidade)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Talvez você também queira examinar o [guia Apache Spark SQL, Dataframes e conjuntos de valores](https://spark.apache.org/docs/latest/sql-programming-guide.html) e a [documentação do Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).

