---
title: SDK de REST - Azure HDInsight do Phoenix Query Server
description: Instalar e utilizar o SDK de REST para o Phoenix Query Server no Azure HDInsight.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/04/2017
ms.openlocfilehash: 1f468cac29579d8748f61a47b548a67d36ff8279
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64695957"
---
# <a name="apache-phoenix-query-server-rest-sdk"></a>SDK de REST do Apache Phoenix Query Server

[O Apache Phoenix](https://phoenix.apache.org/) é uma código-fonte aberto, camada de base de dados relacional paralelo em grande escala na parte superior do [Apache HBase](apache-hbase-overview.md). Permite-lhe utilizar consultas do tipo SQL com o HBase através de ferramentas SSH como Phoenix [SQLLine](apache-hbase-phoenix-squirrel-linux.md). Phoenix também fornece um servidor HTTP chamado servidor de consulta Phoenix (PQS), um cliente fino, que oferece suporte a dois mecanismos de transporte para comunicação do cliente: O JSON e Buffers de protocolo. Protocol Buffers é o mecanismo de padrão e oferece uma comunicação mais eficiente do que o JSON.

Este artigo descreve como utilizar o SDK de REST PQS para criar as tabelas de linhas de upsert individualmente e em massa e selecionar dados através de instruções SQL. Os exemplos utilizam o [controlador de Microsoft .NET para o Apache Phoenix Query Server](https://www.nuget.org/packages/Microsoft.Phoenix.Client). Este SDK baseia [Avatica do Apache Calcite](https://calcite.apache.org/avatica/) APIs, o que usar exclusivamente os Buffers de protocolo para o formato de serialização.

Para obter mais informações, consulte [referência de Buffers de protocolo do Apache Calcite Avatica](https://calcite.apache.org/avatica/docs/protobuf_reference.html).

## <a name="install-the-sdk"></a>Instalar o SDK

Controlador de Microsoft .NET para o Apache Phoenix Query Server é fornecido como um pacote do NuGet, que pode ser instalado a partir do Visual Studio **NuGet Package Manager Console** com o seguinte comando:

    Install-Package Microsoft.Phoenix.Client

## <a name="instantiate-new-phoenixclient-object"></a>Criar uma instância do novo objeto de PhoenixClient

Para começar a utilizar a biblioteca, instanciar um novo `PhoenixClient` objeto, passando `ClusterCredentials` que contém o `Uri` para o seu cluster e do cluster Apache Hadoop nome de utilizador e palavra-passe.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net/"), "USERNAME", "PASSWORD");
client = new PhoenixClient(credentials);
```

Substitua CLUSTERNAME seu nome de cluster do HBase do HDInsight e o nome de utilizador e a palavra-passe com as credenciais de Hadoop especificadas na criação do cluster. O nome de utilizador do Hadoop de predefinido é **administrador**.

## <a name="generate-unique-connection-identifier"></a>Gerar o identificador exclusivo de ligação

Para enviar um ou mais pedidos para PQS, terá de incluir um identificador exclusivo de ligação para associar a pedido (s) com a ligação.

```csharp
string connId = Guid.NewGuid().ToString();
```

Cada exemplo pela primeira vez faz uma chamada para o `OpenConnectionRequestAsync` método, passando o identificador de ligação exclusivo. Em seguida, defina `ConnectionProperties` e `RequestOptions`, passando esses objetos e o identificador de conexão gerada para o `ConnectionSyncRequestAsync` método. Do PQS `ConnectionSyncRequest` objeto ajuda a garantir que o cliente e o servidor tenham uma exibição consistente das propriedades da base de dados.

## <a name="connectionsyncrequest-and-its-connectionproperties"></a>ConnectionSyncRequest e seu ConnectionProperties

Para chamar `ConnectionSyncRequestAsync`, passar um `ConnectionProperties` objeto.

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
| Confirmação automática | Um booleano que denota se `autoCommit` está ativada para transações de Phoenix. |
| ReadOnly | Um valor booleano que indica se a ligação é só de leitura. |
| TransactionIsolation | Um número inteiro que indica o nível de isolamento de transação pela especificação de JDBC - consulte a tabela seguinte.|
| Catálogo | O nome do catálogo, para utilizar quando a obter propriedades de ligação. |
| Esquema | O nome do esquema a utilizar quando a obter propriedades de ligação. |
| IsDirty | Um valor booleano que indica se as propriedades foram alteradas. |

Seguem-se a `TransactionIsolation` valores:

| Valor de isolamento | Descrição |
| -- | -- |
| 0 | As transações não são suportadas. |
| 1 | Podem ocorrer leituras sujas, leituras não repetíveis e leituras fantasma. |
| 2 | São impedidas leituras sujas, mas podem ocorrer leituras não repetíveis e leituras fantasma. |
| 4 | São impedidas leituras sujas e leituras não repetíveis, mas podem ocorrer leituras fantasma. |
| 8 | Todas as leituras sujas, leituras não repetíveis e leituras fantasma são impedidas. |

## <a name="create-a-new-table"></a>Criar uma nova tabela

HBase, como outros RDBMS, armazena os dados nas tabelas. Phoenix utiliza consultas SQL padrão para criar novas tabelas, ao definir os principais tipos de chave e na coluna.

Neste exemplo e todos os exemplos subsequentes, utilize o instanciadas `PhoenixClient` objeto conforme definido na [instanciar um novo objeto de PhoenixClient](#instantiate-new-phoenixclient-object).

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

O exemplo anterior cria uma nova tabela chamada `Customers` usando o `IF NOT EXISTS` opção. O `CreateStatementRequestAsync` chamada cria uma nova declaração no servidor Avitica (PQS). O `finally` bloco fecha retornado `CreateStatementResponse` e o `OpenConnectionResponse` objetos.

## <a name="insert-data-individually"></a>Inserir dados individualmente

Este exemplo mostra um dados individuais inserir, referenciar um `List<string>` coleção de abreviaturas de estado e o território americano:

```csharp
var states = new List<string> { "AL", "AK", "AS", "AZ", "AR", "CA", "CO", "CT", "DE", "DC", "FM", "FL", "GA", "GU", "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MH", "MD", "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ", "NM", "NY", "NC", "ND", "MP", "OH", "OK", "OR", "PW", "PA", "PR", "RI", "SC", "SD", "TN", "TX", "UT", "VT", "VI", "VA", "WA", "WV", "WI", "WY" };
```

A tabela `StateProvince` será utilizado o valor de coluna numa operação de seleção subsequente.

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

A estrutura para a execução de uma instrução insert é semelhante a criar uma nova tabela. Tenha em atenção que no final o `try` bloco, a transação é consolidado explicitamente. Neste exemplo repete-se uma transação de inserção vezes 300. O exemplo seguinte mostra um processo de inserção de lote mais eficiente.

## <a name="batch-insert-data"></a>Dados de inserção de lote

O código a seguir é quase idêntico ao código para inserir dados individualmente. Este exemplo utiliza a `UpdateBatch` objeto numa chamada ao `ExecuteBatchRequestAsync`, em vez de chamar repetidamente `ExecuteRequestAsync` com uma instrução preparada.

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

Num ambiente de um teste, individualmente inserir 300 novos registos demorou quase 2 minutos. Por outro lado, a inserção de 300 registos como um lote necessário apenas 6 segundos.

## <a name="select-data"></a>Selecionar dados

Este exemplo mostra como reutilizar uma ligação para executar várias consultas:

1. Selecione todos os registos e, em seguida, busca registros restantes depois do máximo predefinido de 100 são devolvidos.
2. Utilize uma instrução select da contagem de total de linhas para obter o resultado de escalar único.
3. Execute uma instrução select que retorna o número total de clientes por Estado ou território.

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

A saída do `select` instruções devem ser o seguinte resultado:

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

## <a name="next-steps"></a>Passos Seguintes 

* [Apache Phoenix no HDInsight](../hdinsight-phoenix-in-hdinsight.md)
* [Com o SDK de REST de HBase do Apache](apache-hbase-rest-sdk.md)
