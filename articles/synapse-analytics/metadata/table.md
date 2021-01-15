---
title: Tabelas de metadados partilhadas
description: O Azure Synapse Analytics fornece um modelo de metadados partilhado onde a criação de uma tabela no conjunto Apache Spark sem servidor tornará acessível a partir de piscina SQL sem servidor e piscina DE SQL dedicada sem duplicar os dados.
services: sql-data-warehouse
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: b93addfe659847187dffe61f12f5a2bfac9dca21
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209632"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Azure Synapse Analytics partilhou tabelas de metadados


O Azure Synapse Analytics permite que os diferentes motores computacionais do espaço de trabalho partilhem bases de dados e mesas apoiadas por Parquet entre as suas piscinas Apache Spark e a piscina SQL sem servidor.

Uma vez criada uma base de dados por uma função Spark, pode criar tabelas com a Spark que usam o Parquet como formato de armazenamento. Estas mesas ficarão imediatamente disponíveis para consulta por qualquer uma das piscinas de spark do espaço de trabalho Azure Synapse. Também podem ser usados a partir de qualquer um dos trabalhos de Faísca sujeitos a permissões.

As tabelas Spark criadas, geridas e externas também são disponibilizadas como tabelas externas com o mesmo nome na base de dados sincronizada correspondente na piscina SQL sem servidor. [Expor uma tabela Spark em SQL](#expose-a-spark-table-in-sql) fornece mais detalhes sobre a sincronização da tabela.

Uma vez que as tabelas são sincronizadas para a piscina SQL sem servidor assíncronea, haverá um atraso até que apareçam.

## <a name="manage-a-spark-created-table"></a>Gerir uma mesa criada por Faíscas

Use o Spark para gerir as bases de dados criadas pela Spark. Por exemplo, elimine-o através de uma conta de piscina Apache Spark sem servidor e crie mesas no mesmo a partir do Spark.

Se criar objetos numa base de dados a partir de uma piscina SQL sem servidor ou tentar largar a base de dados, a operação falhará. A base de dados original do Spark não pode ser alterada através de uma piscina SQL sem servidor.

## <a name="expose-a-spark-table-in-sql"></a>Expor uma mesa de faíscas em SQL

### <a name="shared-spark-tables"></a>Mesas de faísca compartilhadas

A Spark fornece dois tipos de tabelas que a Azure Synapse expõe automaticamente em SQL:

- Mesas geridas

  A Spark oferece muitas opções para como armazenar dados em tabelas geridas, tais como TEXTO, CSV, JSON, JDBC, PARQUET, ORC, HIVE, DELTA e LIBSVM. Estes ficheiros são normalmente armazenados no `warehouse` diretório onde os dados de tabela geridos são armazenados.

- Tabelas externas

  A Spark também fornece formas de criar tabelas externas sobre os dados existentes, quer fornecendo a `LOCATION` opção, quer utilizando o formato Hive. Tais tabelas externas podem ser sobre uma variedade de formatos de dados, incluindo Parquet.

Atualmente, a Azure Synapse apenas partilha tabelas de Faíscas geridas e externas que armazenam os seus dados em formato Parquet com os motores SQL. As tabelas apoiadas por outros formatos não são sincronizadas automaticamente. Poderá sincronizar essas tabelas explicitamente como uma tabela externa na sua própria base de dados SQL se o motor SQL suportar o formato subjacente da tabela.

### <a name="share-spark-tables"></a>Partilhar tabelas de faíscas

As tabelas de faíscas geridas e externas comparticuais expostas no motor SQL como tabelas externas com as seguintes propriedades:

- A fonte de dados da tabela externa SQL é a fonte de dados que representa a pasta de localização da tabela Spark.
- O formato de ficheiro da tabela externa SQL é Parquet.
- A credencial de acesso da mesa externa SQL é de passagem.

Uma vez que todos os nomes de mesa Spark são nomes de tabelas SQL válidos e todos os nomes de coluna spark são nomes de colunas SQL válidos, a tabela Spark e os nomes das colunas serão utilizados para a tabela externa SQL.

As tabelas de faíscas fornecem diferentes tipos de dados do que os motores Synapse SQL. Os seguintes mapas de tabelas Mapa de tipos de dados de tabela spark para os tipos DE SQL:

| Tipo de dados de faísca | Tipo de dados SQL | Comentários |
|---|---|---|
| `byte`      | `smallint`       ||
| `short`     | `smallint`       ||
| `integer`   |    `int`            ||
| `long`      |    `bigint`         ||
| `float`     | `real`           |<!-- need precision and scale-->|
| `double`    | `float`          |<!-- need precision and scale-->|
| `decimal`      | `decimal`        |<!-- need precision and scale-->|
| `timestamp` |    `datetime2`      |<!-- need precision and scale-->|
| `date`      | `date`           ||
| `string`    |    `varchar(max)`   | Com colagem `Latin1_General_100_BIN2_UTF8` |
| `binary`    |    `varbinary(max)` ||
| `boolean`   |    `bit`            ||
| `array`     |    `varchar(max)`   | Serializa em JSON com colagem `Latin1_General_100_BIN2_UTF8` |
| `map`       |    `varchar(max)`   | Serializa em JSON com colagem `Latin1_General_100_BIN2_UTF8` |
| `struct`    |    `varchar(max)`   | Serializa em JSON com colagem `Latin1_General_100_BIN2_UTF8` |

<!-- TODO: Add precision and scale to the types mentioned above -->

## <a name="security-model"></a>Modelo de segurança

As bases de dados e tabelas Spark, bem como as suas representações sincronizadas no motor SQL serão asseguradas ao nível de armazenamento subjacente. Uma vez que não têm atualmente permissões nos próprios objetos, os objetos podem ser vistos no explorador de objetos.

O principal de segurança que cria uma tabela gerida é considerado o proprietário dessa tabela e tem todos os direitos sobre a mesma, bem como sobre as pastas e ficheiros subjacentes. Além disso, o proprietário da base de dados passará automaticamente a ser coproprietário da tabela.

Se criar uma tabela externa do Spark ou SQL com pass-through de autenticação, os dados só são protegidos aos níveis da pasta e do ficheiro. Se alguém consultar este tipo de tabela externa, a identidade de segurança do submissor da consulta é passada para o sistema de ficheiros, que verificará os direitos de acesso.

Para obter mais informações sobre como definir permissões nas pastas e ficheiros, consulte [a base de dados partilhada Azure Synapse Analytics](database.md).

## <a name="examples"></a>Exemplos

### <a name="create-a-managed-table-backed-by-parquet-in-spark-and-query-from-serverless-sql-pool"></a>Crie uma tabela gerida apoiada pela Parquet em Spark e consulta a partir de piscina SQL sem servidor

Neste cenário, tem uma base de dados Spark chamada `mytestdb` . Consulte [Criar e ligue-se a uma base de dados Spark com piscina SQL sem servidor](database.md#create-and-connect-to-spark-database-with-serverless-sql-pool).

Crie uma tabela spark gerida com o SparkSQL executando o seguinte comando:

```sql
    CREATE TABLE mytestdb.myParquetTable(id int, name string, birthdate date) USING Parquet
```

Este comando cria a tabela `myParquetTable` na base de `mytestdb` dados. Após um curto atraso, pode ver a tabela na sua piscina SQL sem servidor. Por exemplo, execute a seguinte declaração a partir da sua piscina SQL sem servidor.

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

Verifique se `myParquetTable` está incluído nos resultados.

>[!NOTE]
>Uma tabela que não esteja a utilizar o Parquet como seu formato de armazenamento não será sincronizada.

Em seguida, insira alguns valores na tabela da Spark, por exemplo com as seguintes declarações de C# Spark num caderno C#:

```csharp
using Microsoft.Spark.Sql.Types;

var data = new List<GenericRow>();

data.Add(new GenericRow(new object[] { 1, "Alice", new Date(2010, 1, 1)}));
data.Add(new GenericRow(new object[] { 2, "Bob", new Date(1990, 1, 1)}));

var schema = new StructType
    (new List<StructField>()
        {
            new StructField("id", new IntegerType()),
            new StructField("name", new StringType()),
            new StructField("birthdate", new DateType())
        }
    );

var df = spark.CreateDataFrame(data, schema);
df.Write().Mode(SaveMode.Append).InsertInto("mytestdb.myParquetTable");
```

Agora pode ler os dados da sua piscina SQL sem servidor da seguinte forma:

```sql
SELECT * FROM mytestdb.dbo.myParquetTable WHERE name = 'Alice';
```

Como resultado, deverá obter a seguinte linha:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="create-an-external-table-backed-by-parquet-in-spark-and-query-from-serverless-sql-pool"></a>Crie uma tabela externa apoiada por Parquet em Faísca e consulta a partir de piscina SQL sem servidor

Neste exemplo, crie uma tabela De Spark externa sobre os ficheiros de dados parquet que foram criados no exemplo anterior para a tabela gerida.

Por exemplo, com a execução sparkSQL:

```sql
CREATE TABLE mytestdb.myExternalParquetTable
    USING Parquet
    LOCATION "abfss://<fs>@arcadialake.dfs.core.windows.net/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

Substitua o espaço reservado `<fs>` pelo nome do sistema de ficheiros que é o sistema de ficheiros predefinidos do espaço de trabalho e o espaço reservado com o nome do espaço de trabalho `<synapse_ws>` sinapse que está a usar para executar este exemplo.

O exemplo anterior cria a tabela `myExtneralParquetTable` na base de `mytestdb` dados. Após um curto atraso, pode ver a tabela na sua piscina SQL sem servidor. Por exemplo, execute a seguinte declaração a partir da sua piscina SQL sem servidor.

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

Verifique se `myExternalParquetTable` está incluído nos resultados.

Agora pode ler os dados da sua piscina SQL sem servidor da seguinte forma:

```sql
SELECT * FROM mytestdb.dbo.myExternalParquetTable WHERE name = 'Alice';
```

Como resultado, deverá obter a seguinte linha:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre os metadados partilhados da Azure Synapse Analytics](overview.md)
- [Saiba mais sobre a base de dados de metadados partilhados da Azure Synapse Analytics](database.md)


