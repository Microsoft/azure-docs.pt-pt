---
title: Gerir duplos digitais
titleSuffix: Azure Digital Twins
description: Veja como recuperar, atualizar e eliminar gémeos e relacionamentos individuais.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 707cfb2e9bea3286daa92ea54f7bb9659a455caf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85390521"
---
# <a name="manage-digital-twins"></a>Gerir duplos digitais

As entidades do seu ambiente são representadas por [gémeos digitais.](concepts-twins-graph.md) Gerir os seus gémeos digitais pode incluir criação, modificação e remoção. Para estas operações, pode utilizar as [**APIs DigitalTwins,**](how-to-use-apis-sdks.md)a [.NET (C#) SDK,](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)ou o [Azure Digital Twins CLI](how-to-use-cli.md).

Este artigo centra-se na gestão de gémeos digitais; para trabalhar com relacionamentos e o [gráfico gémeo](concepts-twins-graph.md) como um todo, ver [Como-a- Gerir o gráfico gémeo com relacionamentos](how-to-manage-graph.md).

> [!TIP]
> Todas as funções SDK vêm em versões sincronizadas e assíncronos.

## <a name="create-a-digital-twin"></a>Criar um gémeo digital

Para criar um gémeo, utiliza-se o `CreateDigitalTwin` método no cliente de serviço como este:

```csharp
await client.CreateDigitalTwinAsync("myNewTwinID", initData);
```

Para criar um gémeo digital, é necessário fornecer:
* O iD desejado para o gémeo digital
* O [modelo](concepts-models.md) que quer usar 

Opcionalmente, pode fornecer valores iniciais para todas as propriedades do twin digital. 

> [!TIP]
> Apenas as propriedades que foram definidas pelo menos uma vez são devolvidas quando você recupera um gémeo com GetDigitalTwin.  

O modelo e os valores iniciais da propriedade são fornecidos através do `initData` parâmetro, que é uma cadeia JSON contendo os dados relevantes.

### <a name="initialize-properties"></a>Inicializar propriedades

A API de criação gémea aceita um objeto que pode ser serializado numa descrição válida do JSON das propriedades gémeas. Ver [Conceitos: Gémeos digitais e o gráfico gémeo](concepts-twins-graph.md) para uma descrição do formato JSON para um gémeo.

Pode criar um objeto de parâmetro manualmente ou utilizando uma classe de ajudante fornecida. Aqui está um exemplo de cada um.

#### <a name="create-twins-using-manually-created-data"></a>Criar gémeos usando dados criados manualmente

```csharp
// Define the model type for the twin to be created
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:com:contoso:Room;1" }
};
// Initialize the twin properties
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", temperature},
    { "Humidity", humidity},
};
client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<Dictionary<string, object>>(twin));
```

#### <a name="create-twins-with-the-helper-class"></a>Criar gémeos com a classe de ajudante

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
props.Add("Humidity", 50.0);
twin.CustomProperties = props;

client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<BasicDigitalTwin>(twin));
```

## <a name="get-data-for-a-digital-twin"></a>Obtenha dados para um gémeo digital

Pode aceder a todos os dados de qualquer gémeo digital chamando:

```csharp
object result = await client.GetDigitalTwin(id);
```

Esta chamada devolve dados gémeos como uma cadeia JSON. 

Para recuperar vários gémeos utilizando uma única chamada de API, consulte os exemplos de consulta da API em [Como-a-: Consultar o gráfico gémeo](how-to-query-graph.md).

Considere o seguinte modelo (escrito em [Linguagem de Definição de Gémeos Digitais (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL)que define uma *Lua*:

```json
{
    "@id": " dtmi:com:contoso:Moon;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "contents": [
        {
            "@type": "Property",
            "name": "radius",
            "schema": "double",
            "writable": true
        },
        {
            "@type": "Property",
            "name": "mass",
            "schema": "double",
            "writable": true
        }
    ]
}
```

O resultado de chamar `object result = await client.DigitalTwins.GetByIdAsync("my-moon");` um gémeo do tipo *Lua*pode ser assim:

```json
{
  "$dtId": "myMoon-001",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:com:contoso:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

As propriedades definidas do gémeo digital são devolvidas como propriedades de alto nível no twin digital. Os metadados ou informações do sistema que não fazem parte da definição DTDL são devolvidos com um `$` prefixo. As propriedades dos metadados incluem:
* A identificação do gémeo digital neste exemplo de Azure Digital Twins, como `$dtId` .
* Outras propriedades numa `$metadata` secção. O que está incluído:
    - O DTMI do modelo do gémeo digital.
    - Estado de sincronização para cada propriedade escrita. Isto é mais útil para dispositivos, onde é possível que o serviço e o dispositivo tenham estatutos divergentes (por exemplo, quando um dispositivo está offline). Atualmente, esta propriedade aplica-se apenas a dispositivos físicos ligados ao IoT Hub. Com os dados na secção de metadados, é possível compreender o estado total de uma propriedade, bem como os últimos timetamps modificados. Para obter mais informações sobre o estado de sincronização, consulte [este tutorial do IoT Hub](../iot-hub/tutorial-device-twins.md) sobre o estado do dispositivo sincronizado.
    - Metadados específicos do serviço, como do IoT Hub ou da Azure Digital Twins. 

Pode analisar o JSON devolvido para o gémeo utilizando uma biblioteca de análise JSON à sua escolha, tal como `System.Text.Json` .

Também pode utilizar a classe de ajudante de serialização `BasicDigitalTwin` que está incluída com o SDK, que irá devolver os metadados e propriedades gémeos principais em forma pré-analisada. Segue-se um exemplo:

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

Pode ler mais sobre as aulas de ajudante de serialização em [Como-a-: Use as APIs e SDKs de Gémeos Digitais Azure](how-to-use-apis-sdks.md).

## <a name="update-a-digital-twin"></a>Atualize um gémeo digital

Para atualizar propriedades de um twin digital, escreve a informação que pretende substituir no formato [JSON Patch.](http://jsonpatch.com/) Desta forma, pode substituir várias propriedades ao mesmo tempo. Em seguida, passe o documento do Patch JSON para um `Update` método:

`await client.UpdateDigitalTwin(id, patch);`.

Aqui está um exemplo do código de patch JSON. Este documento substitui os *valores* de propriedade em massa e *raio* do gémeo digital a que é aplicado.

```json
[
  {
    "op": "replace",
    "path": "/mass",
    "value": 0.0799
  },
  {
    "op": "replace",
    "path": "/radius",
    "value": 0.800
  }
]
```

Pode criar patches manualmente ou utilizando uma aula de ajudante de serialização no [SDK](how-to-use-apis-sdks.md). Aqui está um exemplo de cada um.

#### <a name="create-patches-manually"></a>Criar patches manualmente
```csharp
List<object> twinData = new List<object>();
twinData.Add(new Dictionary<string, object>() {
    { "op", "add"},
    { "path", "/Temperature"},
    { "value", 25.0}
});

await client.UpdateDigitalTwinAsync(twinId, JsonConvert.SerializeObject(twinData));
```

#### <a name="create-patches-using-the-helper-class"></a>Criar patches usando a classe de ajudante

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", 25.0);
await client.UpdateDigitalTwinAsync(twinId, uou.Serialize());
```

### <a name="update-properties-in-digital-twin-components"></a>Atualizar propriedades em componentes gémeos digitais

Recorde-se que um modelo pode conter componentes, permitindo que seja composto por outros modelos. 

Para corrigir propriedades nos componentes de um gémeo digital, utilizará a sintaxe do caminho no Patch JSON:

```json
[
  {
    "op": "replace",
    "path": "/mycomponentname/mass",
    "value": 0.0799
  }
]
```

### <a name="update-a-digital-twins-model"></a>Atualize o modelo de um gémeo digital

A `Update` função também pode ser usada para migrar um gémeo digital para um modelo diferente. 

Por exemplo, considere o seguinte documento JSON Patch que substitui o campo de metadados do gémeo `$model` digital:

```json
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:contoso:foo;1"
  }
]
```

Esta operação só será bem sucedida se o gémeo digital for modificado pelo patch em conformidade com o novo modelo. 

Considere o exemplo seguinte:
1. Imagine um gémeo digital com um modelo de *foo_old.* *foo_old* define uma *massa*de propriedade necessária.
2. O novo modelo *foo_new* define uma massa de propriedade, e adiciona uma nova *temperatura*de propriedade necessária.
3. Após o patch, o gémeo digital deve ter uma propriedade de massa e temperatura. 

O patch para esta situação precisa atualizar tanto o modelo como a propriedade de temperatura do gémeo, como este:

```json
[
  {
    "op": "replace",
    "path": "$metadata.$model",
    "value": "dtmi:com:contoso:foo_new"
  },
  {
    "op": "add",
    "path": "temperature",
    "value": 60
  }
]
```

## <a name="delete-a-digital-twin"></a>Excluir um gémeo digital

Pode eliminar gémeos utilizando `DeleteDigitalTwin(ID)` . No entanto, só se pode apagar um gémeo quando não tem mais relacionamentos. Primeiro, tens de apagar todas as relações. 

Aqui está um exemplo do código para isto:

```csharp
static async Task DeleteTwin(string id)
{
    await FindAndDeleteOutgoingRelationshipsAsync(id);
    await FindAndDeleteIncomingRelationshipsAsync(id);
    try
    {
        await client.DeleteDigitalTwin(id);
    } catch (RequestFailedException exc)
    {
        Console.WriteLine($"*** Error:{exc.Error}/{exc.Message}");
    }
}

public async Task FindAndDeleteOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);

        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            await client.DeleteRelationshipAsync(dtId, rel.Id).ConfigureAwait(false);
            Log.Ok($"Deleted relationship {rel.Id} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
    }
}

async Task FindAndDeleteIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipssAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        await foreach (IncomingRelationship incomingRel in incomingRels)
        {
            await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
            Log.Ok($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
    }
}
```

### <a name="delete-all-digital-twins"></a>Apagar todos os gémeos digitais

Para um exemplo de como eliminar todos os gémeos de uma só vez, descarregue a aplicação de amostras utilizada no [Tutorial: Explore o básico com uma aplicação de cliente de amostra.](tutorial-command-line-app.md) O *ficheiro CommandLoop.cs* faz isto numa `CommandDeleteAllTwins` função.

## <a name="manage-twins-with-cli"></a>Gerir gémeos com CLI

Os gémeos também podem ser geridos usando o CLI das Gémeas Digitais Azure. Os comandos podem ser encontrados em [Como-a-: Use o CLI das Gémeas Digitais Azure](how-to-use-cli.md).

## <a name="next-steps"></a>Próximos passos

Veja como criar e gerir relações entre os seus gémeos digitais:
* [Como fazer: Gerir o gráfico gémeo com relacionamentos](how-to-manage-graph.md)