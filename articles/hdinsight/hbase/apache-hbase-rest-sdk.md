---
title: Utilize o HBase .NET SDK - Azure HDInsight
description: Utilize o HBase .NET SDK para criar e apagar tabelas e para ler e escrever dados.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: eba7d7ad009b2ef0442a916983489489eb5cceb8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74806665"
---
# <a name="use-the-net-sdk-for-apache-hbase"></a>Utilize o SDK .NET para Apache HBase

[A Apache HBase](apache-hbase-overview.md) fornece duas opções primárias para trabalhar com os seus dados: [consultas apache hive, e chamadas para a API RESTful da HBase](apache-hbase-tutorial-get-started-linux.md). Pode trabalhar diretamente com a API REST utilizando o `curl` comando ou um utilitário semelhante.

Para aplicações C# e .NET, a [Microsoft HBase REST Client Library para .NET](https://www.nuget.org/packages/Microsoft.HBase.Client/) fornece uma biblioteca de clientes em cima da API HBase REST.

## <a name="install-the-sdk"></a>Instalar o SDK

O HBase .NET SDK é fornecido como um pacote NuGet, que pode ser instalado a partir da Consola de Gestor de **Pacotes NuGet** do Estúdio Visual com o seguinte comando:

    Install-Package Microsoft.HBase.Client

## <a name="instantiate-a-new-hbaseclient-object"></a>Instantiate um novo objeto HBaseClient

Para utilizar o SDK, `HBaseClient` instantaneamente um `ClusterCredentials` novo `Uri` objeto, passando composto pelo cluster, e o nome de utilizador hadoop e senha.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

Substitua o CLUSTERNAME pelo nome do cluster HDInsight HBase e userNAME e PASSWORD pelas credenciais Apache Hadoop especificadas na criação do cluster. O nome de utilizador de Hadoop predefinido é **administrador**.

## <a name="create-a-new-table"></a>Criar uma nova tabela

A HBase armazena dados em tabelas. Uma tabela é constituída por um *Rowkey,* a chave principal, e um ou mais grupos de colunas chamadas famílias de *colunas.* Os dados de cada tabela são distribuídos horizontalmente por uma gama Rowkey em *regiões*. Cada região tem uma chave de início e fim. Uma mesa pode ter uma ou mais regiões. À medida que os dados em tabela crescem, o HBase divide grandes regiões em regiões mais pequenas. As regiões são armazenadas em *servidores da região,* onde um servidor de região pode armazenar várias regiões.

Os dados são armazenados fisicamente em *HFiles*. Um único HFile contém dados para uma tabela, uma região e uma família de colunas. As filas em HFile são armazenadas ordenadas no Rowkey. Cada HFile tem um índice *B+ Tree* para uma rápida recuperação das linhas.

Para criar uma nova `TableSchema` tabela, especifique a e colunas. O código que se segue verifica se o quadro 'RestSDKTable' já existe - se não, a tabela é criada.

```csharp
if (!client.ListTablesAsync().Result.name.Contains("RestSDKTable"))
{
    // Create the table
    var newTableSchema = new TableSchema {name = "RestSDKTable" };
    newTableSchema.columns.Add(new ColumnSchema() {name = "t1"});
    newTableSchema.columns.Add(new ColumnSchema() {name = "t2"});

    await client.CreateTableAsync(newTableSchema);
}
```

Esta nova mesa tem famílias de duas colunas, t1 e T2. Uma vez que as famílias de colunas são armazenadas separadamente em diferentes HFiles, faz sentido ter uma família de colunas separada para dados frequentemente consultados. No exemplo de [dados inserido,](#insert-data) as colunas são adicionadas à família da coluna T1.

## <a name="delete-a-table"></a>Eliminar uma tabela

Para apagar uma tabela:

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>Inserir dados

Para inserir dados, especifice uma chave de linha única como identificador de linha. Todos os dados são `byte[]` armazenados numa matriz. O seguinte código define `title`e `director`adiciona `release_date` as colunas e colunas à família das colunas T1, uma vez que estas colunas são as mais acedidas. As `description` `tagline` colunas e colunas são adicionadas à família da coluna T2. Pode dividir os seus dados em famílias de colunas, conforme necessário.

```csharp
var key = "fifth_element";
var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };
var value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:title"),
    data = Encoding.UTF8.GetBytes("The Fifth Element")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:director"),
    data = Encoding.UTF8.GetBytes("Luc Besson")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:release_date"),
    data = Encoding.UTF8.GetBytes("1997")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:description"),
    data = Encoding.UTF8.GetBytes("In the colorful future, a cab driver unwittingly becomes the central figure in the search for a legendary cosmic weapon to keep Evil and Mr Zorg at bay.")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:tagline"),
    data = Encoding.UTF8.GetBytes("The Fifth is life")
};
row.values.Add(value);

var set = new CellSet();
set.rows.Add(row);

await client.StoreCellsAsync("RestSDKTable", set);
```

A HBase implementa o [Cloud BigTable,](https://cloud.google.com/bigtable/)para que o formato de dados se pareça com a seguinte imagem:

![Saída de dados da amostra Apache HBase](./media/apache-hbase-rest-sdk/hdinsight-table-roles.png)

## <a name="select-data"></a>Selecionar dados

Para ler os dados de uma tabela HBase, `GetCellsAsync` passe o `CellSet`nome da tabela e a chave de linha para o método de devolução do .

```csharp
var key = "fifth_element";

var cells = await client.GetCellsAsync("RestSDKTable", key);
// Get the first value from the row.
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
// Get the value for t1:title
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:title").data));
// With the previous insert, it should yield: "The Fifth Element"
```

Neste caso, o código devolve apenas a primeira linha correspondente, uma vez que deve haver apenas uma linha para uma chave única. O valor devolvido `string` é alterado `byte[]` em formato a partir da matriz. Também pode converter o valor para outros tipos, como um inteiro para a data de lançamento do filme:

```csharp
var releaseDateField = cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:release_date");
int releaseDate = 0;

if (releaseDateField != null)
{
    releaseDate = Convert.ToInt32(Encoding.UTF8.GetString(releaseDateField.data));
}
Console.WriteLine(releaseDate);
// Should return 1997
```

## <a name="scan-over-rows"></a>Scaneie sobre linhas

A HBase usa `scan` para recuperar uma ou mais linhas. Este exemplo solicita várias linhas em lotes de 10, e recupera dados cujos valores-chave estão entre 25 e 35. Depois de recuperar todas as linhas, elimine o scanner para limpar os recursos.

```csharp
var tableName = "mytablename";

// Assume the table has integer keys and we want data between keys 25 and 35
var scanSettings = new Scanner()
{
    batch = 10,
    startRow = BitConverter.GetBytes(25),
    endRow = BitConverter.GetBytes(35)
};
RequestOptions scanOptions = RequestOptions.GetDefaultOptions();
scanOptions.AlternativeEndpoint = "hbaserest0/";
ScannerInformation scannerInfo = null;
try
{
    scannerInfo = await client.CreateScannerAsync(tableName, scanSettings, scanOptions);
    CellSet next = null;
    while ((next = client.ScannerGetNextAsync(scannerInfo, scanOptions).Result) != null)
    {
        foreach (var row in next.rows)
        {
            // ... read the rows
        }
    }
}
finally
{
    if (scannerInfo != null)
    {
        await client.DeleteScannerAsync(tableName, scannerInfo, scanOptions);
    }
}
```

## <a name="next-steps"></a>Passos seguintes

* [Introdução com um exemplo do Apache HBase no HDInsight](apache-hbase-tutorial-get-started-linux.md)
* Construa uma aplicação de ponta a ponta com o sentimento do Twitter em [tempo real com a Apache HBase](../hdinsight-hbase-analyze-twitter-sentiment.md)
