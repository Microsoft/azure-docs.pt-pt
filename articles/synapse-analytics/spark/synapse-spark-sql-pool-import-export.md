---
title: Dados de importação e exportação entre piscinas Apache Spark sem servidor e piscinas SQL
description: Este artigo fornece informações sobre como usar o conector personalizado para mover dados entre piscinas SQL dedicadas e piscinas Apache Spark sem servidor.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 11/19/2020
ms.author: prgomata
ms.reviewer: euang
ms.openlocfilehash: 66a804f645ee56cce62053a51e6aa4fc25b3c1a9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671331"
---
# <a name="introduction"></a>Introdução

O conector Azure Synapse Apache Para Synapse SQL foi projetado para transferir eficientemente dados entre piscinas Apache Spark sem servidor e piscinas SQL dedicadas em Azure Synapse. O conector Azure Synapse Apache para Synapse SQL funciona apenas em piscinas SQL dedicadas, não funciona com piscina SQL sem servidor.

> [!WARNING]
> O nome da função **sqlanalytics** foi alterado para **sinapsesql()**. A função sqlanalytics continuará a funcionar, mas será depreciada.  Por favor, altere qualquer referência da **sqlanalytics para** **a sinapsesql()** para evitar qualquer perturbação no futuro.

## <a name="design"></a>Design

A transferência de dados entre piscinas Spark e sql pode ser feita usando JDBC. No entanto, tendo em conta dois sistemas distribuídos, como piscinas Spark e SQL, o JDBC tende a ser um estrangulamento com a transferência de dados em série.

O azure Synapse Apache Spark pool to Synapse SQL connector é uma implementação de fonte de dados para Apache Spark. Utiliza o Azure Data Lake Storage Gen2 e o Polybase em piscinas SQL dedicadas para transferir eficientemente dados entre o cluster Spark e a instância SQL dedicada à Synapse.

![Arquitetura do Conector](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Autenticação em Azure Synapse Analytics

A autenticação entre sistemas é feita sem emenda no Azure Synapse Analytics. O Serviço Token conecta-se com o Azure Ative Directory para obter fichas de segurança para utilização ao aceder à conta de armazenamento ou ao servidor do armazém de dados.

Por esta razão, não há necessidade de criar credenciais ou especificá-las na API do conector, desde que o Azure AD-Auth esteja configurado na conta de armazenamento e no servidor do armazém de dados. Caso contrário, o SQL Auth pode ser especificado. Veja mais detalhes na secção [Utilização.](#usage)

## <a name="constraints"></a>Restrições

- Este conector funciona apenas em Scala.
- Para o pySpark, consulte os detalhes na secção [Use Python.](#use-pyspark-with-the-connector)
- Este Conector não suporta consultas SQL Views.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ser um membro da **db_exporter** papel na base de dados/pool SQL que pretende transferir dados de/para.
- Deve ser um membro da função de Contribuinte de Dados blob de armazenamento na conta de armazenamento predefinido.

Para criar utilizadores, ligue-se à base de dados da piscina SQL e siga estes exemplos:

```sql
--SQL User
CREATE USER Mary FROM LOGIN Mary;

--Azure Active Directory User
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Para atribuir uma função:

```sql
--SQL User
EXEC sp_addrolemember 'db_exporter', 'Mary';

--Azure Active Directory User
EXEC sp_addrolemember 'db_exporter',[mike@contoso.com]
```

## <a name="usage"></a>Utilização

As declarações de importação não são necessárias, são pré-importadas para a experiência do caderno.

### <a name="transfer-data-to-or-from-a-dedicated-sql-pool-attached-within-the-workspace"></a>Transferir dados para ou de uma piscina DE SQL dedicada anexada dentro do espaço de trabalho

> [!NOTE]
> **Importações não necessárias na experiência de cadernos**

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Ler API

```scala
val df = spark.read.synapsesql("<DBName>.<Schema>.<TableName>")
```

A API acima funcionará tanto para as Tabelas Internas (Geridas) como para as Tabelas Externas na piscina SQL.

#### <a name="write-api"></a>Escrever API

```scala
df.write.synapsesql("<DBName>.<Schema>.<TableName>", <TableType>)
```

A API de escrita cria a tabela na piscina dedicada SQL e, em seguida, invoca a Polybase para carregar os dados.  A tabela não deve existir na piscina sql dedicada ou será devolvido um erro afirmando que "Já existe um objeto nomeado..."

Valores de TableType

- Constants.INTERNAL - Mesa gerida em piscina SQL dedicada
- Constants.EXTERNAL - Tabela externa em piscina SQL dedicada

Mesa gerida pela piscina SQL

```scala
df.write.synapsesql("<DBName>.<Schema>.<TableName>", Constants.INTERNAL)
```

Mesa externa de piscina SQL

Para escrever para uma mesa externa de piscina SQL dedicada, deve existir uma FONTE DE DADOS EXTERNA e um formato de ficheiro externo no pool de SQL dedicado.  Para mais informações, leia [a criação de uma fonte de dados externa](/sql/t-sql/statements/create-external-data-source-transact-sql?view=azure-sqldw-latest&preserve-view=true) e [formatos de ficheiros externos](/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest&preserve-view=true) no pool de SQL dedicado.  Abaixo estão exemplos para criar uma fonte de dados externa e formatos de ficheiros externos em pool DE SQL dedicado.

```sql
--For an external table, you need to pre-create the data source and file format in dedicated SQL pool using SQL queries:
CREATE EXTERNAL DATA SOURCE <DataSourceName>
WITH
  ( LOCATION = 'abfss://...' ,
    TYPE = HADOOP
  ) ;

CREATE EXTERNAL FILE FORMAT <FileFormatName>
WITH (  
    FORMAT_TYPE = PARQUET,  
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'  
);
```

Não é necessário um objeto de CREDENCIAL EXTERNO quando utilizar a autenticação pass-through do Azure Ative Directory na conta de armazenamento.  Certifique-se de que é membro da função "Storage Blob Data Contributor" na conta de armazenamento.

```scala

df.write.
    option(Constants.DATA_SOURCE, <DataSourceName>).
    option(Constants.FILE_FORMAT, <FileFormatName>).
    synapsesql("<DBName>.<Schema>.<TableName>", Constants.EXTERNAL)

```

### <a name="transfer-data-to-or-from-a-dedicated-sql-pool-or-database-outside-the-workspace"></a>Transferir dados para ou de uma piscina ou base de dados SQL dedicada fora do espaço de trabalho

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
synapsesql("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Escrever API

```scala
df.write.
option(Constants.SERVER, "samplews.database.windows.net").
synapsesql("<DBName>.<Schema>.<TableName>", <TableType>)
```

### <a name="use-sql-auth-instead-of-azure-ad"></a>Use SQL Auth em vez de Azure AD

#### <a name="read-api"></a>Ler API

Atualmente, o conector não suporta auth baseado em token para uma piscina SQL dedicada que está fora do espaço de trabalho. Terá de usar o SQL Auth.

```scala
val df = spark.read.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, <SQLServer Login UserName>).
option(Constants.PASSWORD, <SQLServer Login Password>).
synapsesql("<DBName>.<Schema>.<TableName>")
```

#### <a name="write-api"></a>Escrever API

```scala
df.write.
option(Constants.SERVER, "samplews.database.windows.net").
option(Constants.USER, <SQLServer Login UserName>).
option(Constants.PASSWORD, <SQLServer Login Password>).
synapsesql("<DBName>.<Schema>.<TableName>", <TableType>)
```

### <a name="use-pyspark-with-the-connector"></a>Use pySpark com o conector

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

scala_df.write.synapsesql("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```

Da mesma forma, no cenário de leitura, leia os dados usando o Scala e escreva-os numa tabela temporária, e use o Spark SQL em PySpark para consultar a tabela temporária num dataframe.

## <a name="allow-other-users-to-use-the-azure-synapse-apache-spark-to-synapse-sql-connector-in-your-workspace"></a>Permitir que outros utilizadores utilizem o Azure Synapse Apache Spark para synapse conector SQL no seu espaço de trabalho

Você precisa ser Proprietário de Dados Blob de Armazenamento na conta de armazenamento ADLS Gen2 ligada ao espaço de trabalho para alterar permissões em falta para outros. Certifique-se de que o utilizador tem acesso ao espaço de trabalho e permissões para executar cadernos.

### <a name="option-1"></a>Opção 1

- Faça do utilizador um contribuinte/proprietário de dados blob de armazenamento

### <a name="option-2"></a>Opção 2

- Especificar os seguintes ACLs na estrutura da pasta:

| Pasta | / | sinapse | áreas de trabalho  | \<workspacename> | faíscas | \<sparkpoolname>  | sparkpoolinstances  |
|--|--|--|--|--|--|--|--|
| Permissões de Acesso | --X | --X | --X | --X | --X | --X | -WX |
| Permissões predefinidos | ---| ---| ---| ---| ---| ---| ---|

- Deve ser capaz de acl todas as pastas de "sinapse" e para baixo a partir do portal Azure. Para ACL a pasta raiz "/" raiz, siga as instruções abaixo.

- Ligue-se à conta de armazenamento ligada ao espaço de trabalho do Storage Explorer utilizando o Azure AD
- Selecione a sua Conta e dê o URL de Gen2 da ADLS e o sistema de ficheiros predefinidos para o espaço de trabalho
- Assim que conseguir ver a conta de armazenamento listada, clique no espaço de trabalho da listagem e selecione "Gerir o Acesso"
- Adicione o Utilizador à pasta /pasta com a Permissão de Acesso "Executar". Selecione "Ok"

> [!IMPORTANT]
> Certifique-se de que não seleciona "Predefinição" se não pretende.


## <a name="next-steps"></a>Passos seguintes

- [Crie uma piscina SQL dedicada utilizando o portal Azure](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
- [Criar uma nova piscina Apache Spark utilizando o portal Azure](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md) 
