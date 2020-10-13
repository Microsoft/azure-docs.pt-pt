---
title: Gerir o grafo duplo com relações
titleSuffix: Azure Digital Twins
description: Veja como gerir um gráfico de gémeos digitais ligando-os a relacionamentos.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a0ab8f8ff3f2134c205338dfe8e6f2e887a5a053
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91949620"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Gerir um gráfico de gémeos digitais usando relacionamentos

O coração de Azure Digital Twins é o [gráfico gémeo](concepts-twins-graph.md) que representa todo o seu ambiente. O gráfico gémeo é composto por gémeos digitais individuais ligados através de **relacionamentos.**

Uma vez que tenha uma [instância Azure Digital Twins](how-to-set-up-instance-portal.md) em funcionamento e tenha configurado código de [autenticação](how-to-authenticate-client.md) na sua aplicação de cliente, pode utilizar as [**APIs digitalTwins**](how-to-use-apis-sdks.md) para criar, modificar e eliminar gémeos digitais e as suas relações num caso Azure Digital Twins. Também pode utilizar o [.NET (C#) SDK,](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)ou o [CLI das Gémeas Digitais Azure](how-to-use-cli.md).

Este artigo centra-se na gestão das relações e do gráfico como um todo; para trabalhar com gémeos digitais individuais, ver [*Como-a- Gerir gémeos digitais.*](how-to-manage-twin.md)

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Criar relacionamentos

As relações descrevem como diferentes gémeos digitais estão ligados entre si, o que constitui a base do gráfico gémeo.

As relações são criadas usando a `CreateRelationship` chamada. 

Para criar uma relação, precisa de especificar:
* O ID gémeo de origem (o gémeo onde a relação se origina)
* O ID gémeo alvo (o gémeo onde a relação chega)
* Um nome de relacionamento
* Uma iD de relacionamento

A identificação da relação deve ser única dentro do gémeo de origem dada. Não precisa de ser globalmente único.
Por exemplo, para o *twin foo,* cada identificação de relacionamento específico deve ser único. No entanto, outro *bar* gémeo pode ter uma relação de saída que corresponde à mesma identificação de uma relação *foo.* 

A seguinte amostra de código ilustra como adicionar uma relação à sua instância Azure Digital Twins.

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
{
    var relationship = new BasicRelationship
    {
        TargetId = targetId,
        Name = "contains"
    };

    try
    {
        string relId = $"{srcId}-contains->{targetId}";
        await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

Para obter mais informações sobre a classe de `BasicRelationship` ajudante, consulte [*Como-a-fazer: Use as APIs e SDKs de gémeos digitais Azure*](how-to-use-apis-sdks.md).

### <a name="create-multiple-relationships-between-twins"></a>Criar relações múltiplas entre gémeos

Não há restrição no número de relacionamentos que pode ter entre dois gémeos — pode ter tantas relações entre gémeos quanto quiser. 

Isto significa que pode expressar vários tipos diferentes de relacionamentos entre dois gémeos ao mesmo tempo. Por exemplo, *a Twin A* pode ter uma relação *armazenada* e uma relação *manufaturada* com *a Twin B*.

Pode até criar múltiplos casos do mesmo tipo de relação entre os mesmos dois gémeos, se desejar. Neste exemplo, isto significa que *twin A* poderia ter duas relações *distintas armazenadas* com *Twin B*.

## <a name="list-relationships"></a>Relações de lista

Para aceder à lista de relações **de saída** provenientes de um dado gémeo no gráfico, pode utilizar:

```csharp
await client.GetRelationshipsAsync(id);
```

Isto devolve `Azure.Pageable<T>` um `Azure.AsyncPageable<T>` ou, dependendo se utilizar a versão sincronizada ou assíncrona da chamada.

Aqui está um exemplo completo que recupera uma lista de relacionamentos:

```csharp
public async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw if an error occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);
        List<BasicRelationship> results = new List<BasicRelationship>();
        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            results.Add(rel);
        }
        return results;
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
        return null;
    }
}
```

Pode usar as relações recuperadas para navegar para outros gémeos no seu gráfico. Para isso, leia o `target` campo a partir da relação que é devolvida, e use-o como ID para a sua próxima chamada para `GetDigitalTwin` . 

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Encontre relacionamentos de entrada para um gémeo digital

A Azure Digital Twins também tem uma API para encontrar todas as relações **recebidas** com um dado gémeo. Isto é frequentemente útil para a navegação inversa, ou quando elimina um gémeo.

A amostra de código anterior focava-se em encontrar relações de saída de um gémeo. O exemplo a seguir é estruturado da mesma forma, mas encontra relações *de entrada* com o gémeo.

Note que as `IncomingRelationship` chamadas não devolvem todo o corpo da relação.

```csharp
async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        List<IncomingRelationship> results = new List<IncomingRelationship>();
        await foreach (IncomingRelationship incomingRel in incomingRels)
            results.Add(incomingRel);
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
    }
}
```

## <a name="delete-relationships"></a>Eliminar relações

Pode eliminar relações utilizando `DeleteRelationship(source, relId);` .

O primeiro parâmetro especifica o gémeo de origem (o gémeo de onde a relação se origina). O outro parâmetro é a identificação da relação. Precisas tanto da identificação gémea como da identificação da relação, porque as identificações de relacionamento são únicas no âmbito de um gémeo.

## <a name="create-a-twin-graph"></a>Criar um gráfico gémeo 

O seguinte código snippet usa as operações de relacionamento deste artigo para criar um gráfico gémeo a partir de gémeos digitais e relacionamentos.

```csharp
static async Task CreateTwins()
{
    // Create twins - see utility functions below 
    await CreateRoom("Room01", 68, 50, false, "");
    await CreateRoom("Room02", 70, 66, true, "EId-00124");
    await CreateFloorOrBuilding("Floor01", makeFloor:true);

    // Create relationships
    await AddRelationship("Floor01", "contains", "Floor-to-Room01", "Room01");
    await AddRelationship("Floor01", "contains", "Floor-to-Room02", "Room02");
}

static async Task<bool> AddRelationship(string source, string relationship, string id, string target)
{
    var relationship = new BasicRelationship
    {
        TargetId = target,
        Name = relationship
    };

    try
    {
        string relId = $"{source}-contains->{target}";
        await client.CreateRelationshipAsync(source, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
        return true;
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
        return false;
    }
}

static async Task<bool> CreateRoom(string id, double temperature, double humidity)
{
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = "dtmi:com:contoso:Room;2";
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("Temperature", temperature);
    props.Add("Humidity", humidity);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin)); 
        return true;       
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}

static async Task<bool> CreateFloorOrBuilding(string id, bool makeFloor=true)
{
    string type = "dtmi:com:contoso:Building;3";
    if (makeFloor==true)
        type = "dtmi:com:contoso:Floor;2";
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = type;
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("AverageTemperature", 0);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));  
        return true;      
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}
```

### <a name="create-a-twin-graph-from-a-spreadsheet"></a>Criar um gráfico gémeo a partir de uma folha de cálculo

Em casos práticos de utilização, as hierarquias gémeas serão muitas vezes criadas a partir de dados armazenados numa base de dados diferente, ou talvez numa folha de cálculo. Esta secção ilustra como uma folha de cálculo pode ser analisada.

Considere a seguinte tabela de dados, descrevendo um conjunto de gémeos digitais e relações a criar.

| Modelo    | ID | Principal | Nome da relação | Outros dados |
| --- | --- | --- | --- | --- |
| chão    | Piso01 | | | … |
| sala    | Sala10 | Piso01 | contains | … |
| sala    | Sala11 | Piso01 | contains | … |
| sala    | Sala12 | Piso01 | contains | … |
| chão    | Piso02 | | | … |
| sala    | Sala21 | Piso02 | contains | … |
| sala    | Sala22 | Piso02 | contains | … |

O código que se segue utiliza a [API do Gráfico microsoft](https://docs.microsoft.com/graph/overview) para ler uma folha de cálculo e construir um gráfico gémeo Azure Digital Twins a partir dos resultados.

```csharp
var range = msftGraphClient.Me.Drive.Items["BuildingsWorkbook"].Workbook.Worksheets["Building"].usedRange;
JsonDocument data = JsonDocument.Parse(range.values);
List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
foreach (JsonElement row in data.RootElement.EnumerateArray())
{
    string type = row[0].GetString();
    string id = row[1].GetString();
    string relSource = row[2].GetString();
    string relName = row[3].GetString();
    // Parse spreadsheet extra data into a JSON string to initialize the digital twin
    // Left out for compactness
    Dictionary<string, object> initData = new Dictionary<string, object>() { ... };

    if (relSource != null)
    {
        BasicRelationship br = new BasicRelationship()
        {
            SourceId = relSource,
            TargetId = id,
            Name = relName
        };
        RelationshipRecordList.Add(br);
    }

    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.CustomProperties = initData;
    // Set the type of twin to be created
    switch (type)
    {
        case "room":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Room;2" };
            break;
        case "floor":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Floor;2" };
            break;
        ... handle additional types
    }
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
    }
    catch (RequestFailedException e)
    {
        Log.Error($"Error {e.Status}: {e.Message}");
    }
    foreach (BasicRelationship rec in RelationshipRecordList)
    { 
        try { 
            client.CreateRelationship(rec.SourceId, Guid.NewGuid().ToString(), JsonSerializer.Serialize<BasicRelationship>(rec));
        }
        catch (RequestFailedException e)
        {
            Log.Error($"Error {e.Status}: {e.Message}");
        }
    }
}
```
## <a name="manage-relationships-with-cli"></a>Gerir relações com o CLI

As gémeas e as suas relações também podem ser geridas usando o CLI das Gémeas Digitais Azure. Os comandos podem ser encontrados em [*Como-a-: Use o CLI das Gémeas Digitais Azure*](how-to-use-cli.md).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a consulta de um gráfico gémeo Azure Digital Twins:
* [*Conceitos: Linguagem de consulta*](concepts-query-language.md)
* [*Como fazer: Consulta do gráfico gémeo*](how-to-query-graph.md)