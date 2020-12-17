---
title: Utilize o HBase .NET SDK - Azure HDInsight
description: Utilize o HBase .NET SDK para criar e apagar tabelas e para ler e escrever dados.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/02/2019
ms.openlocfilehash: 9b5693ddef5e512b0a95c87a700fd12acd4b5fae
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97654647"
---
# <a name="use-the-net-sdk-for-apache-hbase"></a>Utilize o .NET SDK para Apache HBase

[Apache HBase](apache-hbase-overview.md) fornece duas opções primárias para trabalhar com os seus dados: [consultas de Hive Apache, e chamadas para a API RESTful RESTful da HBase](apache-hbase-tutorial-get-started-linux.md). Pode trabalhar diretamente com a API REST utilizando o `curl` comando ou uma utilidade semelhante.

Para aplicações C# e .NET, a [Microsoft HBase REST Client Library for .NET](https://www.nuget.org/packages/Microsoft.HBase.Client/) fornece uma biblioteca de clientes no topo da API HBase REST.

## <a name="install-the-sdk"></a>Instalar o SDK

O HBase .NET SDK é fornecido como um pacote NuGet, que pode ser instalado a partir da Consola Visual Studio **NuGet Package Manager** com o seguinte comando:

```console
Install-Package Microsoft.HBase.Client
```

## <a name="instantiate-a-new-hbaseclient-object"></a>Instantaneamente um novo objeto HBaseClient

Para utilizar o SDK, instantânea um novo `HBaseClient` objeto, passando `ClusterCredentials` composto pelo seu `Uri` cluster, e o nome de utilizador e senha de Hadoop.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

Substitua o CLUSTERNAME pelo seu nome de cluster HDInsight HBase e USERNAME e PASSWORD pelas credenciais Apache Hadoop especificadas na criação do cluster. O nome de utilizador hadoop predefinido é **administrador.**

## <a name="create-a-new-table"></a>Criar uma nova tabela

A HBase armazena dados em tabelas. Uma tabela é constituída por um *Rowkey,* a chave primária, e um ou mais grupos de colunas chamados famílias de *colunas.* Os dados de cada tabela são distribuídos horizontalmente por uma gama Rowkey em *regiões.* Cada região tem uma chave de partida e fim. Uma mesa pode ter uma ou mais regiões. À medida que os dados em tabela crescem, a HBase divide grandes regiões em regiões mais pequenas. As regiões são armazenadas em *servidores da região,* onde um servidor de região pode armazenar várias regiões.

Os dados são armazenados fisicamente em *HFiles*. Um único HFile contém dados para uma tabela, uma região e uma família de colunas. As linhas em HFile são armazenadas no Rowkey. Cada HFile tem um índice *B+ Tree* para uma rápida recuperação das linhas.

Para criar uma nova tabela, especifique uma `TableSchema` e colunas. O código seguinte verifica se a tabela 'RestSDKTable' já existe - se não, a tabela é criada.

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

Esta nova tabela tem famílias de duas colunas, t1 e t2. Uma vez que as famílias de colunas são armazenadas separadamente em diferentes HFiles, faz sentido ter uma família de colunas separada para dados frequentemente consultados. No seguinte exemplo [de dados insira,](#insert-data) as colunas são adicionadas à família da coluna T1.

## <a name="delete-a-table"></a>Eliminar uma tabela

Para eliminar uma tabela:

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>Inserir dados

Para inserir dados, especifique uma chave de linha única como o identificador de linha. Todos os dados são armazenados num `byte[]` conjunto. O seguinte código define e adiciona o `title` `director` , e `release_date` colunas à família da coluna T1, uma vez que estas colunas são as mais acessadas. As `description` `tagline` colunas e colunas são adicionadas à família da coluna T2. Pode dividir os seus dados em famílias de colunas, conforme necessário.

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

HBase implementa [Cloud BigTable,](https://cloud.google.com/bigtable/)para que o formato de dados se pareça com a seguinte imagem:

![Saída de dados da amostra Apache HBase](./media/apache-hbase-rest-sdk/hdinsight-table-roles.png)

## <a name="select-data"></a>Selecionar dados

Para ler os dados de uma tabela HBase, passe o nome da mesa e a chave de linha para o `GetCellsAsync` método para devolver o `CellSet` .

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

Neste caso, o código devolve apenas a primeira linha de correspondência, uma vez que deve haver apenas uma linha para uma chave única. O valor devolvido é alterado para `string` formato a partir da `byte[]` matriz. Também pode converter o valor para outros tipos, como um número inteiro para a data de lançamento do filme:

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

## <a name="scan-over-rows"></a>Digitalize sobre linhas

A HBase utiliza `scan` para recuperar uma ou mais linhas. Este exemplo solicita várias linhas em lotes de 10, e recupera dados cujos valores-chave estão entre 25 e 35. Depois de recuperar todas as linhas, elimine o scanner para limpar os recursos.

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
* Construa uma aplicação de ponta a ponta com o sentimento do [Twitter em tempo real com a Apache HBase](./apache-hbase-tutorial-get-started-linux.md)