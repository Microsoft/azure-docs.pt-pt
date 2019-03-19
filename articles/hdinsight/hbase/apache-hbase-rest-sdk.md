---
title: Utilizar o SDK de .NET do HBase - Azure HDInsight
description: Utilize o SDK de .NET do HBase para criar e eliminar tabelas e para ler e escrever dados.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/13/2017
ms.author: ashishth
ms.openlocfilehash: 27a013bee98447de591d373917c2cbaae7ca08b3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57857662"
---
# <a name="use-the-net-sdk-for-apache-hbase"></a>Utilizar o SDK .NET para o Apache HBase

[O Apache HBase](apache-hbase-overview.md) fornece duas opções principais para trabalhar com os seus dados: [Consultas do Apache Hive e chamadas à API de RESTful do HBase](apache-hbase-tutorial-get-started-linux.md). Pode trabalhar diretamente com a REST API utilizando o `curl` comando ou um utilitário semelhante.

Para aplicativos de c# e .NET, o [biblioteca de cliente do Microsoft HBase REST para .NET](https://www.nuget.org/packages/Microsoft.HBase.Client/) fornece uma biblioteca de cliente com base na API de REST de HBase.

## <a name="install-the-sdk"></a>Instalar o SDK

O SDK de .NET do HBase é fornecido como um pacote do NuGet, que pode ser instalado a partir do Visual Studio **NuGet Package Manager Console** com o seguinte comando:

    Install-Package Microsoft.HBase.Client

## <a name="instantiate-a-new-hbaseclient-object"></a>Criar uma instância de um novo objeto de HBaseClient

Para utilizar o SDK, instanciar um novo `HBaseClient` objeto, passando `ClusterCredentials` composto o `Uri` para o seu cluster e o nome de utilizador do Hadoop e a palavra-passe.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

Substitua CLUSTERNAME seu nome de cluster do HBase do HDInsight e o nome de utilizador e a palavra-passe com as credenciais do Apache Hadoop especificadas na criação do cluster. O nome de utilizador do Hadoop de predefinido é **administrador**.

## <a name="create-a-new-table"></a>Criar uma nova tabela

HBase armazena os dados nas tabelas. Uma tabela é composta por um *Rowkey*, a chave primária e um ou mais grupos de colunas chamado *famílias de coluna*. Os dados em cada tabela na horizontal são distribuídos por um intervalo de Rowkey em *regiões*. Cada região tem uma chave de início e de fim. Uma tabela pode ter uma ou mais regiões. À medida que aumenta os dados na tabela, o HBase divide grandes regiões em regiões menores. Regiões são armazenadas no *servidores de região*, onde um servidor de região pode armazenar várias regiões.

Os dados fisicamente são armazenados no *HFiles*. Um único HFile contém dados para uma tabela, uma região e uma família de colunas. Linhas na HFile são armazenadas classificado em Rowkey. Cada HFile tem um *árvore B +* índice para obtenção rápida das linhas.

Para criar uma nova tabela, especifique um `TableSchema` e colunas. O código a seguir verifica se a tabela 'RestSDKTable' já existe - caso contrário, a tabela é criado.

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

Esta nova tabela tem famílias de duas colunas, t1 e t2. Uma vez que as famílias de coluna são armazenadas em separado no HFiles diferentes, faz sentido ter uma família de colunas separadas para dados consultados com frequência. A seguir [inserir dados](#insert-data) exemplo, são adicionadas colunas para a família de colunas t1.

## <a name="delete-a-table"></a>Eliminar uma tabela

Para eliminar uma tabela:

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>Inserir dados

Para inserir dados, especifique uma chave de linha exclusivo como o identificador de linha. Todos os dados são armazenados num `byte[]` matriz. O código a seguir define e adiciona a `title`, `director`, e `release_date` colunas a família de colunas t1, como estas colunas são acedidos com mais frequência. O `description` e `tagline` são adicionadas colunas para a família de colunas t2. Pode particionar os dados em famílias de coluna, conforme necessário.

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

HBase implementa [Cloud BigTable](https://cloud.google.com/bigtable/), por isso, o formato de dados é semelhante ao seguinte:

![Utilizador com função de utilizador do Cluster](./media/apache-hbase-rest-sdk/table.png)

## <a name="select-data"></a>Selecionar dados

Para ler dados a partir de uma tabela de HBase, passar a chave de linha e o nome de tabela para o `GetCellsAsync` método para retornar o `CellSet`.

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

Neste caso, o código retornará apenas a primeira linha correspondente, que deve haver apenas uma linha para uma chave exclusiva. O valor retornado é alterado para `string` formatar a partir do `byte[]` matriz. Também pode converter o valor para outros tipos, como um número inteiro para a data de lançamento do filme:

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

## <a name="scan-over-rows"></a>Analisar em linhas

Utiliza o HBase `scan` para obter uma ou mais linhas. Neste exemplo solicita várias linhas em lotes de 10 e recupera dados cujos valores de chave são entre 25 e 35. Depois de recuperar todas as linhas, elimine o scanner para limpar os recursos.

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

## <a name="next-steps"></a>Passos Seguintes

* [Comece com um exemplo de Apache HBase no HDInsight](apache-hbase-tutorial-get-started-linux.md)
* Criar um aplicativo ponto a ponto com [analisar sentimento do Twitter em tempo real com o Apache HBase](../hdinsight-hbase-analyze-twitter-sentiment.md)
