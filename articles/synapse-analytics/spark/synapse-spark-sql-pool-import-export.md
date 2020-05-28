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
ms.openlocfilehash: 20b030079121104fe7bd75924a63ab0e12be9b19
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020868"
---
# <a name="introduction"></a>Introdução

O conector Azure Synapse Apache Spark para synapse SQL foi projetado para transferir dados eficientemente entre piscinas Spark (pré-visualização) e piscinas SQL em Azure Synapse. O conector Azure Synapse Apache Spark para synapse SQL funciona apenas em piscinas SQL, não funciona com a SQL a pedido.

## <a name="design"></a>Design

A transferência de dados entre piscinas Spark e Piscinas SQL pode ser feita usando JDBC. No entanto, tendo em conta dois sistemas distribuídos, como as piscinas Spark e SQL, a JDBC tende a ser um estrangulamento com a transferência de dados em série.

O pool Azure Synapse Apache Spark para o conector Synapse SQL é uma implementação de fonte de dados para a Apache Spark. Utiliza o Azure Data Lake Storage Gen2 e polybase em piscinas SQL para transferir dados eficientemente entre o cluster Spark e a instância SQL synapse.

![Arquitetura do Conector](./media/synapse-spark-sqlpool-import-export/arch1.png)

## <a name="authentication-in-azure-synapse-analytics"></a>Autenticação em Azure Synapse Analytics

A autenticação entre sistemas é feita sem emenda no Azure Synapse Analytics. Existe um Serviço Token que se conecta com o Azure Ative Directory para obter fichas de segurança para uso ao aceder à conta de armazenamento ou ao servidor de armazém de dados. 

Por esta razão, não há necessidade de criar credenciais ou especiá-las na API do conector, desde que a AAD-Auth esteja configurada na conta de armazenamento e no servidor de armazém de dados. Caso contrário, o SQL Auth pode ser especificado. Mais detalhes na secção [Utilização.](#usage)

## <a name="constraints"></a>Restrições

- Este conector funciona apenas em Scala.

## <a name="prerequisites"></a>Pré-requisitos

- Tenha **db_exporter** papel na base de dados/SQL que pretende transferir dados para/de.

Para criar utilizadores, ligue-se à base de dados e siga estes exemplos:

```sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Atribuir um papel:

```sql
EXEC sp_addrolemember 'db_exporter', 'Mary';
```

## <a name="usage"></a>Utilização

As declarações de importação não são necessárias, são pré-importadas para a experiência do caderno.

### <a name="transferring-data-to-or-from-a-sql-pool-attached-with-the-workspace"></a>Transferência de dados para ou para uma piscina SQL anexada ao espaço de trabalho

> [!NOTE]
> **Importações não necessárias na experiência do caderno**

```scala
 import com.microsoft.spark.sqlanalytics.utils.Constants
 import org.apache.spark.sql.SqlAnalyticsConnector._
```

#### <a name="read-api"></a>Ler API

```scala
val df = spark.read.sqlanalytics("[DBName].[Schema].[TableName]")
```

A API acima funcionará tanto para quadros internos (geridos) como para tabelas externas na piscina SQL.

#### <a name="write-api"></a>Escrever API

```scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", [TableType])
```

onde o TableType pode ser Constants.INTERNAL ou Constants.EXTERNAL

```scala
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.INTERNAL)
df.write.sqlanalytics("[DBName].[Schema].[TableName]", Constants.EXTERNAL)
```

A autenticação ao Armazenamento e ao Servidor SQL está feita

### <a name="if-you-are-transferring-data-to-or-from-a-sql-pool-or-database-outside-the-workspace"></a>Se estiver a transferir dados para ou para uma piscina SQL ou base de dados fora do espaço de trabalho

> [!NOTE]
> Importações não necessárias na experiência do caderno

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

### <a name="using-sql-auth-instead-of-aad"></a>Usando o SQL Auth em vez de AAD

#### <a name="read-api"></a>Ler API

Atualmente, o conector não suporta auth baseado em token para uma piscina SQL que está fora do espaço de trabalho. Tens de usar o SQL Auth.

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

### <a name="using-the-pyspark-connector"></a>Utilização do conector PySpark

> [!NOTE]
> Este exemplo é dado apenas com a experiência do caderno em mente.

Assuma que tem um "pyspark_df" de dados que pretende escrever no DW.

Criar uma tabela temporária utilizando o quadro de dados no PySpark:

```py
pyspark_df.createOrReplaceTempView("pysparkdftemptable")
```

Gereuma célula Scala no caderno PySpark usando magias:

```scala
%%spark
val scala_df = spark.sqlContext.sql ("select * from pysparkdftemptable")

pysparkdftemptable.write.sqlanalytics("sqlpool.dbo.PySparkTable", Constants.INTERNAL)
```

Da mesma forma, no cenário de leitura, leia os dados usando Scala e escreva-os numa tabela temporária, e use spark SQL em PySpark para consultar a tabela temporária em um quadro de dados.

## <a name="allowing-other-users-to-use-the-dw-connector-in-your-workspace"></a>Permitir que outros utilizadores utilizem o conector DW no seu espaço de trabalho

Você precisa ser Storage Blob Data Owner na conta de armazenamento ADLS Gen2 ligada ao espaço de trabalho para alterar permissões em falta para outros. Certifique-se de que o utilizador tem acesso ao espaço de trabalho e permissões para executar cadernos.

### <a name="option-1"></a>Opção 1

- Faça do utilizador um Colaborador/Proprietário de Dados Blob de Armazenamento

### <a name="option-2"></a>Opção 2

- Especifique os seguintes ACLs na estrutura da pasta:

| Pasta | / | sinapse | áreas de trabalho  | <workspacename> | faíscas | <sparkpoolname>  | sparkpoolinstances  |
|--|--|--|--|--|--|--|--|
| Permissões de acesso | --X | --X | --X | --X | --X | --X | -WX |
| Permissões por predefinição | ---| ---| ---| ---| ---| ---| ---|

- Você deve ser capaz de ACL todas as pastas de "sinapse" e para baixo do portal Azure. Para ACL a pasta raiz "/", siga as instruções abaixo.

- Ligue-se à conta de armazenamento ligada ao espaço de trabalho do Storage Explorer utilizando a AAD
- Selecione a sua Conta e forneça o URL e o sistema de ficheiros Padrão ADLS Gen2 para o espaço de trabalho
- Assim que puder ver a conta de armazenamento listada, clique no espaço de trabalho de listagem e selecione "Gerir o Acesso"
- Adicione o Utilizador à /pasta com "Executar" Permissão de acesso. Selecione "Ok"

> [!IMPORTANT]
> Certifique-se de que não seleciona "Padrão" se não pretender.

## <a name="next-steps"></a>Próximos passos

- [Crie uma piscina SQL utilizando o portal Azure](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md)
- [Crie uma nova piscina Apache Spark usando o portal Azure](../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md) 
