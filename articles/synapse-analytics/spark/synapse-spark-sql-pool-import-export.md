---
title: Dados de importação e exportação entre piscinas spark (pré-visualização) e piscinas SQL
description: Este artigo fornece informações sobre como usar o conector personalizado para mover dados para trás e para a frente entre piscinas SQL e piscinas Spark (pré-visualização).
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: 515fd9bfedc5bc5d3cefda2a357c351f515fb5f5
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2020
ms.locfileid: "85194676"
---
# <a name="introduction"></a>Introdução

O conector Azure Synapse Apache para Synapse SQL foi concebido para transferir eficientemente dados entre piscinas Spark (pré-visualização) e SQL em Azure Synapse. O conector Azure Synapse Apache para Synapse SQL funciona apenas em piscinas SQL, não funciona com SQL a pedido.

## <a name="design"></a>Design (Estruturar)

A transferência de dados entre piscinas Spark e sql pode ser feita usando JDBC. No entanto, tendo em conta dois sistemas distribuídos, como piscinas Spark e SQL, o JDBC tende a ser um estrangulamento com a transferência de dados em série.

O azure Synapse Apache Spark pool to Synapse SQL connector é uma implementação de fonte de dados para Apache Spark. Utiliza o Azure Data Lake Storage Gen2 e Polybase em piscinas SQL para transferir eficientemente dados entre o cluster Spark e a instância Synapse SQL.

![Arquitetura do Conector](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Autenticação em Azure Synapse Analytics

A autenticação entre sistemas é feita sem emenda no Azure Synapse Analytics. Existe um Serviço Token que se conecta com o Azure Ative Directory para obter fichas de segurança para utilização ao aceder à conta de armazenamento ou ao servidor do armazém de dados. 

Por esta razão, não há necessidade de criar credenciais ou especificá-las na API do conector, desde que a AAD-Auth esteja configurada na conta de armazenamento e no servidor do armazém de dados. Caso contrário, o SQL Auth pode ser especificado. Veja mais detalhes na secção [Utilização.](#usage)

## <a name="constraints"></a>Restrições

- Este conector funciona apenas em Scala.

## <a name="prerequisites"></a>Pré-requisitos

- Tenha **db_exporter** papel na base de dados/pool SQL que pretende transferir dados de/para.

Para criar utilizadores, ligue-se à base de dados e siga estes exemplos:

```sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Para atribuir uma função:

```sql
EXEC sp_addrolemember 'db_exporter', 'Mary';
```

## <a name="usage"></a>Utilização

As declarações de importação não são necessárias, são pré-importadas para a experiência do caderno.

### <a name="transferring-data-to-or-from-a-sql-pool-attached-with-the-workspace"></a>Transferência de dados de ou para uma piscina SQL anexada ao espaço de trabalho

> [!NOTE]
> **Importações não necessárias na experiência de cadernos**

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Ler API

```scala
val df = spark.read.sqlanalytics("[DBName].[Schema].[TableName]")
```

A API acima funcionará tanto para as Tabelas Internas (Geridas) como para as Tabelas Externas na piscina SQL.

#### <a name="write-api"></a>Escrever API

```scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

onde o TableType pode ser Constants.INTERNAL ou Constants.EXTERNAL

```scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.INTERNAL)
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.EXTERNAL)
```

A autenticação para armazenamento e o SqL Server é feito

### <a name="if-you-are-transferring-data-to-or-from-a-sql-pool-or-database-outside-the-workspace"></a>Se estiver a transferir dados para ou a partir de uma piscina SQL ou base de dados fora do espaço de trabalho

> [!NOTE]
> Importações não necessárias na experiência de cadernos

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Ler API

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Escrever API

```scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-sql-auth-instead-of-aad"></a>Usando SQL Auth em vez de AAD

#### <a name="read-api"></a>Ler API

Atualmente, o conector não suporta auth baseado em token para uma piscina SQL que está fora do espaço de trabalho. Terá de usar o SQL Auth.

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Escrever API

```scala
df.write.
option(Constants.SERVER, "[samplews].[database.windows.net]").
option(Constants.USER, [SQLServer Login UserName]).
option(Constants.PASSWORD, [SQLServer Login Password]).
sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

### <a name="using-the-pyspark-connector"></a>Utilizando o conector PySpark

> [!NOTE]
> Este exemplo é dado apenas com a experiência do caderno em mente.

Assuma que tem um "pyspark_df" de data que quer escrever no DW.

Criar uma tabela temporária utilizando o dataframe no PySpark:

```py
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

Executar uma célula Scala no caderno PySpark usando magias:

```scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

pysparkdftemptable.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```

Da mesma forma, no cenário de leitura, leia os dados usando o Scala e escreva-os numa tabela temporária, e use o Spark SQL em PySpark para consultar a tabela temporária num dataframe.

## <a name="allowing-other-users-to-use-the-dw-connector-in-your-workspace"></a>Permitir que outros utilizadores utilizem o conector DW no seu espaço de trabalho

Você precisa ser Proprietário de Dados Blob de Armazenamento na conta de armazenamento ADLS Gen2 ligada ao espaço de trabalho para alterar permissões em falta para outros. Certifique-se de que o utilizador tem acesso ao espaço de trabalho e permissões para executar cadernos.

### <a name="option-1"></a>Opção 1

- Faça do utilizador um contribuinte/proprietário de dados blob de armazenamento

### <a name="option-2"></a>Opção 2

- Especificar os seguintes ACLs na estrutura da pasta:

| Pasta | / | sinapse | áreas de trabalho  | <workspacename> | faíscas | <sparkpoolname>  | sparkpoolinstances  |
|--|--|--|--|--|--|--|--|
| Permissões de Acesso | --X | --X | --X | --X | --X | --X | -WX |
| Permissões predefinidos | ---| ---| ---| ---| ---| ---| ---|

- Deve ser capaz de acl todas as pastas de "sinapse" e para baixo a partir do portal Azure. Para ACL a pasta raiz "/" raiz, siga as instruções abaixo.

- Ligue-se à conta de armazenamento ligada ao espaço de trabalho do Storage Explorer utilizando a AAD
- Selecione a sua Conta e dê o URL de Gen2 da ADLS e o sistema de ficheiros predefinidos para o espaço de trabalho
- Assim que conseguir ver a conta de armazenamento listada, clique no espaço de trabalho da listagem e selecione "Gerir o Acesso"
- Adicione o Utilizador à pasta /pasta com a Permissão de Acesso "Executar". Selecione "Ok"

> [!IMPORTANT]
> Certifique-se de que não seleciona "Predefinição" se não pretende.

## <a name="next-steps"></a>Passos seguintes

- [Criar uma piscina SQL utilizando o portal Azure](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
- [Criar uma nova piscina Apache Spark utilizando o portal Azure](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md) 
