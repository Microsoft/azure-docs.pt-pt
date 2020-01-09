---
title: SDK do Phoenix Query Server REST-Azure HDInsight
description: Instale e use o SDK do REST para o Phoenix Query Server no Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 84c2bad1004029fe61dcfc19321957a170284587
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75612262"
---
# <a name="apache-phoenix-query-server-rest-sdk"></a>SDK REST do servidor de consulta Apache Phoenix

[Apache Phoenix](https://phoenix.apache.org/) é uma camada de banco de dados relacional de software livre, massivamente paralela, além do [Apache HBase](apache-hbase-overview.md). O Phoenix permite que você use consultas do tipo SQL com o HBase por meio de ferramentas SSH, como [sqlline](apache-hbase-query-with-phoenix.md). O Phoenix também fornece um servidor HTTP chamado Phoenix Query Server (PQS), um cliente fino que dá suporte a dois mecanismos de transporte para comunicação do cliente: JSON e buffers de protocolo. Buffers de protocolo é o mecanismo padrão e oferece comunicação mais eficiente do que o JSON.

Este artigo descreve como usar o SDK REST do PQS para criar tabelas, Upsert linhas individualmente e em massa e selecionar dados usando instruções SQL. Os exemplos usam o [driver Microsoft .net para Apache Phoenix servidor de consulta](https://www.nuget.org/packages/Microsoft.Phoenix.Client). Este SDK é criado em APIs [do Avatica do Apache calcite](https://calcite.apache.org/avatica/) , que usam exclusivamente buffers de protocolo para o formato de serialização.

Para obter mais informações, consulte [referência de buffers de protocolo Apache calcite Avatica](https://calcite.apache.org/avatica/docs/protobuf_reference.html).

## <a name="install-the-sdk"></a>Instalar o SDK

O driver de Microsoft .NET para Apache Phoenix servidor de consultas é fornecido como um pacote NuGet, que pode ser instalado no **console do Gerenciador de pacotes NuGet** do Visual Studio com o seguinte comando:

    Install-Package Microsoft.Phoenix.Client

## <a name="instantiate-new-phoenixclient-object"></a>Criar instância de novo objeto PhoenixClient

Para começar a usar a biblioteca, crie uma instância de um novo objeto `PhoenixClient`, passando `ClusterCredentials` contendo o `Uri` para o cluster e o nome de usuário e a senha do cluster Apache Hadoop.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net/"), "USERNAME", "PASSWORD");
client = new PhoenixClient(credentials);
```

Substitua CLUSTERname pelo nome do cluster do HBase do HDInsight e o nome de usuário e a senha pelas credenciais do Hadoop especificadas na criação do cluster. O nome de usuário do Hadoop padrão é **admin**.

## <a name="generate-unique-connection-identifier"></a>Gerar identificador de conexão exclusivo

Para enviar uma ou mais solicitações ao PQS, você precisa incluir um identificador de conexão exclusivo para associar a (s) solicitação (ões) à conexão.

```csharp
string connId = Guid.NewGuid().ToString();
```

Cada exemplo primeiro faz uma chamada para o método `OpenConnectionRequestAsync`, passando o identificador de conexão exclusivo. Em seguida, defina `ConnectionProperties` e `RequestOptions`, passando esses objetos e o identificador de conexão gerado para o método `ConnectionSyncRequestAsync`. O objeto `ConnectionSyncRequest` do PQS ajuda a garantir que o cliente e o servidor tenham uma exibição consistente das propriedades do banco de dados.

## <a name="connectionsyncrequest-and-its-connectionproperties"></a>ConnectionSyncRequest e suas ConnectionProperties

Para chamar `ConnectionSyncRequestAsync`, passe um objeto `ConnectionProperties`.

```csharp
ConnectionProperties connProperties = new ConnectionProperties
{
    HasAutoCommit = true,
    AutoCommit = true,
    HasReadOnly = true,
    ReadOnly = false,
    TransactionIsolation = 0,
    Catalog = "",
    Schema = "",
    IsDirty = true
};
await client.ConnectionSyncRequestAsync(connId, connProperties, options);
```

Aqui estão algumas propriedades de interesse:

| Propriedade | Descrição |
| -- | -- |
| Confirmação automática | Um booliano que indica se `autoCommit` está habilitado para transações Phoenix. |
| ReadOnly | Um booliano indicando se a conexão é somente leitura. |
| TransactionIsolation | Um inteiro que indica o nível de isolamento da transação por especificação JDBC – consulte a tabela a seguir.|
| Catálogo | O nome do catálogo a ser usado ao buscar as propriedades de conexão. |
| Esquema | O nome do esquema a ser usado ao buscar Propriedades de conexão. |
| IsDirty | Um booliano indicando se as propriedades foram alteradas. |

Aqui estão os valores de `TransactionIsolation`:

| Valor de isolamento | Descrição |
| -- | -- |
| 0 | Não há suporte para transações. |
| 1 | Leituras sujas, leituras não repetíveis e leituras fantasmas podem ocorrer. |
| 2 | Leituras sujas são impedidas, mas leituras não repetíveis e leituras fantasmas podem ocorrer. |
| 4 | Leituras sujas e leituras não repetíveis são impedidas, mas podem ocorrer leituras fantasmas. |
| 8 | Leituras sujas, leituras não repetíveis e leituras fantasmas são todas impedidas. |

## <a name="create-a-new-table"></a>Criar uma nova tabela

O HBase, como qualquer outro RDBMS, armazena dados em tabelas. O Phoenix usa consultas SQL padrão para criar novas tabelas, ao mesmo tempo que define os tipos de chave primária e de coluna.

Este exemplo e todos os exemplos posteriores usam o objeto de `PhoenixClient` instanciado conforme definido em [instanciar um novo objeto PhoenixClient](#instantiate-new-phoenixclient-object).

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// You can set certain request options, such as timeout in milliseconds:
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
CreateStatementResponse createStatementResponse = null;
OpenConnectionResponse openConnResponse = null;

try
{
    // Opening connection
    var info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Create the statement
    createStatementResponse = client.CreateStatementRequestAsync(connId, options).Result;
    
    // Create the table if it does not exist
    string sql = "CREATE TABLE IF NOT EXISTS Customers (Id varchar(20) PRIMARY KEY, FirstName varchar(50), " +
        "LastName varchar(100), StateProvince char(2), Email varchar(255), Phone varchar(15))";
    await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    Console.WriteLine($"Table \"Customers\" created.");
}
catch (Exception e)
{
    Console.WriteLine(e);
    throw;
}
finally
{
    if (createStatementResponse != null)
    {
        client.CloseStatementRequestAsync(connId, createStatementResponse.StatementId, options).Wait();
        createStatementResponse = null;
    }

    if (openConnResponse != null)
    {
        client.CloseConnectionRequestAsync(connId, options).Wait();
        openConnResponse = null;
    }
}
```

O exemplo anterior cria uma nova tabela chamada `Customers` usando a opção `IF NOT EXISTS`. A chamada `CreateStatementRequestAsync` cria uma nova instrução no servidor Avitica (PQS). O bloco de `finally` fecha o `CreateStatementResponse` retornado e os objetos `OpenConnectionResponse`.

## <a name="insert-data-individually"></a>Inserir dados individualmente

Este exemplo mostra uma inserção de dados individual, fazendo referência a uma coleção de `List<string>` de abreviações de estado americano e de território:

```csharp
var states = new List<string> { "AL", "AK", "AS", "AZ", "AR", "CA", "CO", "CT", "DE", "DC", "FM", "FL", "GA", "GU", "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MH", "MD", "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ", "NM", "NY", "NC", "ND", "MP", "OH", "OK", "OR", "PW", "PA", "PR", "RI", "SC", "SD", "TN", "TX", "UT", "VT", "VI", "VA", "WA", "WV", "WI", "WY" };
```

O valor da coluna `StateProvince` da tabela será usado em uma operação de seleção posterior.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = await client.PrepareRequestAsync(connId, sql, 100, options);
    statementHandle = prepareResponse.Statement;
    
    var r = new Random();

    // Insert 300 rows
    for (int i = 0; i < 300; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0,1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        Console.WriteLine("Inserting customer " + i);

        await client.ExecuteRequestAsync(statementHandle, list, long.MaxValue, true, options);
    }

    await client.CommitRequestAsync(connId, options);

    Console.WriteLine("Upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

A estrutura para executar uma instrução INSERT é semelhante à criação de uma nova tabela. No final do bloco de `try`, a transação é explicitamente confirmada. Este exemplo repete uma transação de inserção 300 vezes. O exemplo a seguir mostra um processo de inserção de lote mais eficiente.

## <a name="batch-insert-data"></a>Dados de inserção em lote

O código a seguir é praticamente idêntico ao código para inserir dados individualmente. Este exemplo usa o objeto `UpdateBatch` em uma chamada para `ExecuteBatchRequestAsync`, em vez de chamar repetidamente `ExecuteRequestAsync` com uma instrução preparada.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
CreateStatementResponse createStatementResponse = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Creating statement
    createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = client.PrepareRequestAsync(connId, sql, long.MaxValue, options).Result;
    var statementHandle = prepareResponse.Statement;
    var updates = new pbc.RepeatedField<UpdateBatch>();

    var r = new Random();

    // Insert 300 rows
    for (int i = 300; i < 600; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0, 1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        var batch = new UpdateBatch
        {
            ParameterValues = list
        };
        updates.Add(batch);

        Console.WriteLine($"Added customer {i} to batch");
    }

    var executeBatchResponse = await client.ExecuteBatchRequestAsync(connId, statementHandle.Id, updates, options);

    Console.WriteLine("Batch upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

Em um ambiente de teste, inserir individualmente 300 novos registros levou quase 2 minutos. Por outro lado, inserir registros de 300 como um lote exigiu apenas 6 segundos.

## <a name="select-data"></a>Selecionar dados

Este exemplo mostra como reutilizar uma conexão para executar várias consultas:

1. Selecione todos os registros e busque os registros restantes depois que o máximo padrão de 100 for retornado.
2. Use uma instrução SELECT de contagem de linha total para recuperar o único resultado escalar.
3. Execute uma instrução SELECT que retorna o número total de clientes por Estado ou território.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;

try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);
    var createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "SELECT * FROM Customers";
    ExecuteResponse executeResponse = await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> rows = executeResponse.Results[0].FirstFrame.Rows;
    // Loop through all of the returned rows and display the first two columns
    for (int i = 0; i < rows.Count; i++)
    {
        Row row = rows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + " " + row.Value[1].ScalarValue.StringValue);
    }
    
    // 100 is hard-coded on the server side as the default firstframe size
    // FetchRequestAsync is called to get any remaining rows
    Console.WriteLine("");
    Console.WriteLine($"Number of rows: {rows.Count}");

    // Fetch remaining rows, offset is not used, simply set to 0
    // When FetchResponse.Frame.Done is true, all rows were fetched
    FetchResponse fetchResponse = await client.FetchRequestAsync(connId, createStatementResponse.StatementId, 0, int.MaxValue, options);
    Console.WriteLine($"Frame row count: {fetchResponse.Frame.Rows.Count}");
    Console.WriteLine($"Fetch response is done: {fetchResponse.Frame.Done}");
    Console.WriteLine("");

    // Running query 2
    string sql2 = "select count(*) from Customers";
    ExecuteResponse countResponse = await client.PrepareAndExecuteRequestAsync(connId, sql2, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);
    long count = countResponse.Results[0].FirstFrame.Rows[0].Value[0].ScalarValue.NumberValue;

    Console.WriteLine($"Total customer records: {count}");
    Console.WriteLine("");

    // Running query 3
    string sql3 = "select StateProvince, count(*) as Number from Customers group by StateProvince order by Number desc";
    ExecuteResponse groupByResponse = await client.PrepareAndExecuteRequestAsync(connId, sql3, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> stateRows = groupByResponse.Results[0].FirstFrame.Rows;
    for (int i = 0; i < stateRows.Count; i++)
    {
        Row row = stateRows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + ": " + row.Value[1].ScalarValue.NumberValue);
    }
}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

A saída das instruções de `select` deve ser o seguinte resultado:

```
id0 first0
id1 first1
id10 first10
id100 first100
id101 first101
id102 first102
. . .
id185 first185
id186 first186
id187 first187
id188 first188

Number of rows: 100
Frame row count: 500
Fetch response is done: True

Total customer records: 600

NJ: 21
CA: 19
GU: 17
NC: 16
IN: 16
MA: 16
AZ: 16
ME: 16
IL: 15
OR: 15
. . . 
MO: 10
HI: 10
GA: 10
DC: 9
NM: 9
MD: 9
MP: 9
SC: 7
AR: 7
MH: 6
FM: 5
```

## <a name="next-steps"></a>Passos seguintes

* [Apache Phoenix no HDInsight](../hdinsight-phoenix-in-hdinsight.md)
* [Usando o SDK REST do Apache HBase](apache-hbase-rest-sdk.md)
