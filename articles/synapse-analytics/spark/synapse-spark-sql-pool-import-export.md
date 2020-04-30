---
title: Dados de importação e exportação entre piscinas Spark (pré-visualização) e piscinas SQL
description: Este artigo fornece informações sobre como utilizar o conector personalizado para mover dados para trás e para a frente entre piscinas SQL e piscinas Spark (pré-visualização).
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: f92c05476c9e85690fdeacade5463a43d0a4af42
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424294"
---
# <a name="introduction"></a>Introdução

O Conector Spark SQL Analytics foi concebido para transferir dados eficientemente entre o Spark Pool (pré-visualização) e os pools SQL em Azure Synapse. O Conector Spark SQL Analytics funciona apenas em piscinas SQL, não funciona com a SQL on-Demand.

## <a name="design"></a>Design

A transferência de dados entre piscinas Spark e Piscinas SQL pode ser feita usando JDBC. No entanto, tendo em conta dois sistemas distribuídos, como as piscinas Spark e SQL, a JDBC tende a ser um estrangulamento com a transferência de dados em série.

O Spark pools para SQL Analytics Connector é uma implementação de fonte de dados para Apache Spark. Utiliza o Azure Data Lake Storage Gen 2, e a Polybase em piscinas SQL para transferir dados eficientemente entre o cluster Spark e a instância SQL Analytics.

![Arquitetura do Conector](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Autenticação em Azure Synapse Analytics

A autenticação entre sistemas é feita sem emenda no Azure Synapse Analytics. Existe um Serviço Token que se conecta com o Azure Ative Directory para obter fichas de segurança para uso ao aceder à conta de armazenamento ou ao servidor de armazém de dados. Por esta razão, não há necessidade de criar credenciais ou especiá-las na API do conector, desde que a AAD-Auth esteja configurada na conta de armazenamento e no servidor de armazém de dados. Caso contrário, o SQL Auth pode ser especificado. Mais detalhes na secção [Utilização.](#usage)

## <a name="constraints"></a>Restrições

- Este conector funciona apenas em Scala.

## <a name="prerequisites"></a>Pré-requisitos

- Tenha **db_exporter** papel na base de dados/SQL que pretende transferir dados para/de.

Para criar utilizadores, ligue-se à base de dados e siga estes exemplos:

```Sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Atribuir um papel:

```Sql
EXEC sp_addrolemember 'db_exporter', 'Mary';
```

## <a name="usage"></a>Utilização

As declarações de importação não precisam de ser fornecidas, são pré-importadas para a experiência do caderno.

### <a name="transferring-data-to-or-from-a-sql-pool-in-the-logical-server-dw-instance-attached-with-the-workspace"></a>Transferência de dados para ou para um pool SQL no Servidor Lógico (DW Instance) ligado ao espaço de trabalho

> [!NOTE]
> **Importações não necessárias na experiência do caderno**

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Ler API

```Scala
val df = spark.read.sqlanalytics("[DBName].[Schema].[TableName]")
```

A API acima funcionará tanto para quadros internos (geridos) como para tabelas externas na piscina SQL.

#### <a name="write-api"></a>Escrever API

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

onde o TableType pode ser Constants.INTERNAL ou Constants.EXTERNAL

```Scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.INTERNAL)
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.EXTERNAL)
```

A autenticação ao Armazenamento e ao Servidor SQL está feita

### <a name="if-you-are-transferring-data-to-or-from-a-sql-pool-or-database-in-a-logical-server-outside-the-workspace"></a>Se estiver a transferir dados para ou para uma piscina Ou base de dados SQL num Servidor Lógico fora do espaço de trabalho

> [!NOTE]
> Importações não necessárias na experiência do caderno

```Scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Ler API

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Escrever API

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-sql-auth-instead-of-aad"></a>Usando o SQL Auth em vez de AAD

#### <a name="read-api"></a>Ler API

Atualmente, o conector não suporta auth baseado em token para uma piscina SQL que está fora do espaço de trabalho. Tens de usar o SQL Auth.

```Scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Escrever API

```Scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-the-pyspark-connector"></a>Utilização do conector PySpark

> [!NOTE]
> Este exemplo é dado apenas com a experiência do caderno em mente.

Assuma que tem um "pyspark_df" de dados que pretende escrever no DW.

Criar uma tabela temporária usando o dataframe em PySpark

```Python
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

Executar uma célula Scala no caderno PySpark usando magias

```Scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

pysparkdftemptable.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```
Da mesma forma, no cenário de leitura, leia os dados usando Scala e escreva-os numa tabela temporária, e use spark SQL em PySpark para consultar a tabela temporária em um quadro de dados.

## <a name="next-steps"></a>Passos seguintes

- [Criar uma piscina SQL]([Create a new Apache Spark pool for an Azure Synapse Analytics workspace](../../synapse-analytics/quickstart-create-apache-spark-pool.md))
- [Crie uma nova piscina Apache Spark para um espaço de trabalho Azure Synapse Analytics](../../synapse-analytics/quickstart-create-apache-spark-pool.md) 