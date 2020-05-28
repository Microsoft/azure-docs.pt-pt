---
title: Tabelas de metadados partilhadas da Azure Synapse Analytics
description: A Azure Synapse Analytics fornece um modelo de metadados partilhadoonde a criação de uma tabela em Apache Spark tornará acessível a partir dos seus motores de piscina SQL on-demand (pré-visualização) e SQL sem duplicar os dados.
services: sql-data-warehouse
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: ff8d82966d479491bc9600a0331f69ffd09253d1
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84112713"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Azure Synapse Analytics partilhou tabelas de metadados

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

A Azure Synapse Analytics permite que os diferentes motores computacionais do espaço de trabalho partilhem bases de dados e mesas apoiadas por Parquet entre as suas piscinas Apache Spark (pré-visualização) e o motor on-demand (pré-visualização) da SQL.

Uma vez criada uma base de dados por um trabalho spark, pode criar tabelas com a Spark que usam o Parquet como formato de armazenamento. Estas tabelas ficarão imediatamente disponíveis para consulta por qualquer uma das piscinas de spark do espaço de trabalho Azure Synapse. Também podem ser utilizados a partir de qualquer um dos trabalhos spark sujeitos a permissões.

As tabelas Spark criadas, geridas e externas também são disponibilizadas como tabelas externas com o mesmo nome na base de dados sincronizada correspondente na SQL a pedido. [Expor uma mesa Spark em SQL](#exposing-a-spark-table-in-sql) fornece mais detalhes sobre a sincronização da mesa.

Uma vez que as tabelas são sincronizadas para a SQL a pedido assíncrona, haverá um atraso até que apareçam.

## <a name="manage-a-spark-created-table"></a>Gerir uma mesa criada spark

Use a Spark para gerir as bases de dados criadas pela Spark. Por exemplo, apague-o através de um trabalho de piscina spark, e crie mesas nele a partir de Spark.

Se criar objetos numa base de dados a partir da SQL a pedido ou tentar deixar cair a base de dados, a operação terá sucesso, mas a base de dados original spark não será alterada.

## <a name="exposing-a-spark-table-in-sql"></a>Expondo uma mesa de faísca sQL

### <a name="which-spark-tables-are-shared"></a>Que mesas de faísca são partilhadas

A Spark fornece dois tipos de tabelas que o Azure Synapse expõe automaticamente no SQL:

- Mesas geridas

  A Spark fornece muitas opções de como armazenar dados em tabelas geridas, tais como TEXTO, CSV, JSON, JDBC, PARQUET, ORC, HIVE, DELTA e LIBSVM. Estes ficheiros são normalmente armazenados no diretório onde os dados de `warehouse` tabela geridos são armazenados.

- Tabelas externas

  A Spark também fornece formas de criar tabelas externas sobre os dados existentes, seja fornecendo a `LOCATION` opção ou utilizando o formato Hive. Estas tabelas externas podem ser sobre uma variedade de formatos de dados, incluindo parquet.

Atualmente, a Azure Synapse apenas partilha as tabelas de Spark geridas e externas que armazenam os seus dados em formato Parquet com os motores SQL. As tabelas apoiadas por outros formatos não são automaticamente sincronizadas. Poderá sincronizar explicitamente essas tabelas como uma tabela externa na sua própria base de dados SQL se o motor SQL suportar o formato subjacente da tabela.

### <a name="how-are-spark-tables-shared"></a>Como são partilhadas as mesas de faíscas

As tabelas de faíscas geridas e externas partilhadas expostas no motor SQL como tabelas externas com as seguintes propriedades:

- A fonte de dados da tabela externa SQL é a fonte de dados que representa a pasta de localização da tabela Spark.
- O formato de ficheiro da tabela externa SQL é Parquet.
- A credencial de acesso da tabela externa SQL é pass-through.

Uma vez que todos os nomes de mesa Spark são nomes de mesa SQL válidos e todos os nomes de colunas Spark são nomes de colunaS SQL válidos, a tabela Spark e os nomes das colunas serão usados para a tabela externa SQL.

As tabelas de faíscas fornecem diferentes tipos de dados que os motores SYnapse SQL. Os seguintes mapas de tabela Sérs em tabela tipos de dados mapeiam para os tipos SQL:

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
| `string`    |    `varchar(max)`   | Com colagem`Latin1_General_CP1_CI_AS_UTF8` |
| `binary`    |    `varbinary(max)` ||
| `boolean`   |    `bit`            ||
| `array`     |    `varchar(max)`   | Serializa-se em JSON com colagem`Latin1_General_CP1_CI_AS_UTF8` |
| `map`       |    `varchar(max)`   | Serializa-se em JSON com colagem`Latin1_General_CP1_CI_AS_UTF8` |
| `struct`    |    `varchar(max)`   | Serializa-se em JSON com colagem`Latin1_General_CP1_CI_AS_UTF8` |

<!-- TODO: Add precision and scale to the types mentioned above -->

## <a name="security-model"></a>Modelo de segurança

As bases de dados e tabelas Spark, bem como as suas representações sincronizadas no motor SQL serão fixadas ao nível de armazenamento subjacente. Uma vez que não têm permissões nos objetos em si, os objetos podem ser vistos no explorador de objetos.

O diretor de segurança que cria uma tabela gerida é considerado o proprietário dessa tabela e tem todos os direitos à mesa, bem como as pastas e ficheiros subjacentes. Além disso, o proprietário da base de dados passará automaticamente a ser coproprietário da tabela.

Se criar uma tabela externa Spark ou SQL com passagem de autenticação, os dados só são protegidos nos níveis de pasta e ficheiros. Se alguém consultar este tipo de tabela externa, a identidade de segurança do submissão de consulta é passada para o sistema de ficheiros, que verificará os direitos de acesso.

Para obter mais informações sobre como definir permissões nas pastas e ficheiros, consulte a base de [dados partilhada Azure Synapse Analytics](database.md).

## <a name="examples"></a>Exemplos

### <a name="create-a-managed-table-backed-by-parquet-in-spark-and-query-from-sql-on-demand"></a>Crie uma tabela gerida apoiada pela Parquet em Spark e consulta da SQL a pedido

Neste cenário, tem uma base de dados spark chamada `mytestdb` . Consulte [Criar & ligar à base de dados Spark - SQL on-demand](database.md#create--connect-to-spark-database---sql-on-demand).

Crie uma mesa Spark gerida com a SparkSQL executando o seguinte comando:

```sql
    CREATE TABLE mytestdb.myParquetTable(id int, name string, birthdate date) USING Parquet
```

Tudo na minha parte `myParquetTable` de `mytestdb` dados. Depois de um curto atraso, pode ver a mesa na SQL a pedido. Por exemplo, executar a seguinte declaração da SQL a pedido.

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

Verifique se `myParquetTable` está incluído nos resultados.

>[!NOTE]
>Uma tabela que não esteja a utilizar o Parquet, uma vez que o seu formato de armazenamento não será sincronizado.

Em seguida, insira alguns valores na tabela a partir de Spark, por exemplo, com as seguintes declarações De C# Spark num caderno C# :

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

Agora pode ler os dados da SQL a pedido da seguinte forma:

```sql
SELECT * FROM mytestdb.dbo.myParquetTable WHERE name = 'Alice';
```

Como resultado, deve obter a seguinte linha:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="creating-an-external-table-backed-by-parquet-in-spark-and-querying-it-from-sql-on-demand"></a>Criar uma tabela externa apoiada pela Parquet em Spark e consulta-la a partir da SQL a pedido

Neste exemplo, crie uma tabela de Faíscas externa sobre os ficheiros de dados do Parquet que foram criados no exemplo anterior para a tabela gerida.

Por exemplo, com a execução SparkSQL:

```sql
CREATE TABLE mytestdb.myExternalParquetTable
    USING Parquet
    LOCATION "abfss://<fs>@arcadialake.dfs.core.windows.net/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

Substitua o espaço reservado pelo nome do sistema de ficheiros que é o sistema de ficheiros predefinido do espaço de `<fs>` trabalho e o espaço reservado com o nome do espaço de trabalho `<synapse_ws>` sinapse que está a utilizar para executar este exemplo.

O exemplo anterior cria a tabela `myExtneralParquetTable` na base de dados `mytestdb` . Depois de um curto atraso, pode ver a mesa na SQL a pedido. Por exemplo, executar a seguinte declaração da SQL a pedido.

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

Verifique se `myExternalParquetTable` está incluído nos resultados.

Agora pode ler os dados da SQL a pedido da seguinte forma:

```sql
SELECT * FROM mytestdb.dbo.myExternalParquetTable WHERE name = 'Alice';
```

Como resultado, deve obter a seguinte linha:

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

## <a name="next-steps"></a>Próximos passos

- [Saiba mais sobre os metadados partilhados da Azure Synapse Analytics](overview.md)
- [Saiba mais sobre a base de dados de metadados partilhados da Azure Synapse Analytics](database.md)


