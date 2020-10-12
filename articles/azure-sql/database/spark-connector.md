---
title: Utilize o conector Spark com o Microsoft Azure SQL e o SQL Server
description: Saiba como utilizar o Conector de Faíscas com Base de Dados Azure SQL, Instância Gerida Azure SQL e Servidor SQL.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 09/02/2020
ms.openlocfilehash: 36010ff0206ddf9dae08391eb6e4c3dd7762cc10
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319338"
---
# <a name="accelerate-real-time-big-data-analytics-using-the-spark-connector"></a>Acelere a análise de big data em tempo real usando o conector Spark
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> A partir de 2020, este conector não é mantido ativamente. No entanto, [o Conector De Faísca Apache para SQL Server e Azure SQL](https://docs.microsoft.com/sql/connect/spark/connector) está agora disponível, com suporte para ligações Python e R, uma interface mais fácil de usar para inserir dados em massa, e muitas outras melhorias. Encorajamo-lo a avaliar e usar o novo conector em vez deste. A informação sobre o conector antigo (esta página) só é mantida para fins de arquivo.

O conector Spark permite que as bases de dados na Base de Dados Azure SQL, Azure SQL Managed Instance e SQL Server ajam como fonte de dados de entrada ou sumidouro de dados de saída para trabalhos de Spark. Permite-lhe utilizar dados transacionais em tempo real em análise de big data e persistir em resultados para consultas ad hoc ou reportagens. Em comparação com o conector JDBC incorporado, este conector fornece a capacidade de inserir em massa dados na sua base de dados. Pode superar a inserção de linha a linha com um desempenho mais rápido de 10x a 20x. O conector Spark suporta a autenticação do Azure Ative Directory (Azure AD) para ligar à Base de Dados Azure SQL e à Azure SQL Managed Instance, permitindo-lhe ligar a sua base de dados a partir de Azure Databricks utilizando a sua conta Azure AD. Fornece interfaces semelhantes com o conector JDBC incorporado. É fácil migrar os seus trabalhos de Faísca existentes para usar este novo conector.

## <a name="download-and-build-a-spark-connector"></a>Faça o download e construa um conector Spark

O repo GitHub para o antigo conector anteriormente ligado a esta página não é mantido ativamente. Em vez disso, encorajamo-lo a avaliar e a utilizar o [novo conector.](https://github.com/microsoft/sql-spark-connector)

### <a name="official-supported-versions"></a>Versões oficiais apoiadas

| Componente                             | Versão                  |
| :-----------------------------------  | :----------------------- |
| Apache Spark                          | 2.0.2 ou mais tarde           |
| Scala                                 | 2.10 ou mais tarde            |
| Controlador Microsoft JDBC para SQL Server  | 6.2 ou mais tarde             |
| Microsoft SQL Server                  | SQL Server 2008 ou posterior |
| Base de Dados SQL do Azure                    | Suportado                |
| Instância Gerida do Azure SQL            | Suportado                |

O conector Spark utiliza o controlador Microsoft JDBC para o SQL Server para mover dados entre nós de trabalhadores spark e bases de dados:

O fluxo de dados é o seguinte:

1. O nó mestre spark conecta-se a bases de dados em SQL Database ou SQL Server e carrega dados a partir de uma tabela específica ou usando uma consulta SQL específica.
2. O nó mestre spark distribui dados aos nós dos trabalhadores para a transformação.
3. O nó do Trabalhador conecta-se a bases de dados que se ligam à Base de Dados SQL e ao SQL Server e escreve dados para a base de dados. O utilizador pode optar por utilizar a inserção de linha a linha ou a inserção a granel.

O diagrama seguinte ilustra o fluxo de dados.

   ![O diagrama mostra o fluxo descrito, com um nó principal ligado diretamente à base de dados e ligando-se a três nós operários, que se ligam à base de dados.](./media/spark-connector/architecture.png)

### <a name="build-the-spark-connector"></a>Construa o conector Spark

Atualmente, o projeto de conector utiliza maven. Para construir o conector sem dependências, pode correr:

- pacote mvn limpo
- Descarregue as versões mais recentes do JAR a partir da pasta de lançamento
- Incluir o SQL Database Spark JAR

## <a name="connect-and-read-data-using-the-spark-connector"></a>Conecte e leia dados usando o conector Spark

Pode ligar-se a bases de dados na BASE de Dados SQL e no SQL Server a partir de um trabalho de Spark para ler ou escrever dados. Também pode executar uma consulta DML ou DDL em bases de dados em SQL Database e SQL Server.

### <a name="read-data-from-azure-sql-and-sql-server"></a>Leia os dados do Azure SQL e do SQL Server

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

### <a name="read-data-from-azure-sql-and-sql-server-with-specified-sql-query"></a>Leia os dados do Azure SQL e sql Server com consulta SQL especificada

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

### <a name="write-data-to-azure-sql-and-sql-server"></a>Escreva dados para O Azure SQL e SQL Server

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

### <a name="run-dml-or-ddl-query-in-azure-sql-and-sql-server"></a>Executar consulta DML ou DDL em Azure SQL e SQL Server

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

## <a name="connect-from-spark-using-azure-ad-authentication"></a>Conecte-se a partir de Spark usando a autenticação AD Azure

Pode ligar-se à Base de Dados Azure SQL e à SQL Managed Instance utilizando a autenticação AZure AD. Utilize a autenticação Azure AD para gerir centralmente as identidades dos utilizadores da base de dados e como alternativa à autenticação do SQL Server.

### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Ligação utilizando o modo de autenticação activaDirectoryPassword

#### <a name="setup-requirement"></a>Requisito de configuração

Se estiver a utilizar o modo de autenticação ActiveDirectoryPassword, tem de descarregar [azure-active-director-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) e as suas dependências, e incluí-las no caminho de construção java.

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

### <a name="connecting-using-an-access-token"></a>Ligação usando um token de acesso

#### <a name="setup-requirement"></a>Requisito de configuração

Se estiver a utilizar o modo de autenticação baseado em tokens, precisa de baixar [azure-active-director-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) e suas dependências, e incluí-los no caminho de construção java.

Consulte a autenticação do [Diretório Ativo Azure para obter autenticação](authentication-aad-overview.md) para saber como obter um token de acesso à sua base de dados na Base de Dados Azure SQL ou na Instância Gerida Azure SQL.

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

## <a name="write-data-using-bulk-insert"></a>Escreva dados usando inserção a granel

O conector jdbc tradicional escreve dados na sua base de dados utilizando a inserção linha a linha. Pode utilizar o conector Spark para escrever dados para O Azure SQL e SQL Server utilizando inserção a granel. Melhora significativamente o desempenho da escrita ao carregar grandes conjuntos de dados ou carregar dados em tabelas onde é utilizado um índice de loja de colunas.

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

Se ainda não o fez, descarregue o conector Spark do [repositório GitHub de azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) e explore os recursos adicionais no repo:

- [Amostra de cadernos Azure Databricks](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Scripts de amostra (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Também pode querer rever o [Apache Spark SQL, DataFrames e Datasets Guide](https://spark.apache.org/docs/latest/sql-programming-guide.html) e a [documentação Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/).
