---
title: Gerir duplos digitais
titleSuffix: Azure Digital Twins
description: Veja como recuperar, atualizar e eliminar gémeos e relacionamentos individuais.
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4945e89232ee9a15b2700dac49ccd829b7a52dac
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494783"
---
# <a name="manage-digital-twins"></a>Gerir duplos digitais

As entidades do seu ambiente são representadas por [gémeos digitais.](concepts-twins-graph.md) Gerir os seus gémeos digitais pode incluir criação, modificação e remoção. Para estas operações, pode utilizar as [**APIs DigitalTwins,**](/rest/api/digital-twins/dataplane/twins)a [.NET (C#) SDK,](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet-preview&preserve-view=true)ou o [Azure Digital Twins CLI](how-to-use-cli.md).

Este artigo centra-se na gestão de gémeos digitais; para trabalhar com relacionamentos e o [gráfico gémeo](concepts-twins-graph.md) como um todo, ver [*Como-a- Gerir o gráfico gémeo com relacionamentos*](how-to-manage-graph.md).

> [!TIP]
> Todas as funções SDK vêm em versões sincronizadas e assíncronos.

## <a name="create-a-digital-twin"></a>Criar um gémeo digital

Para criar um gémeo, utiliza-se o `CreateDigitalTwin()` método no cliente de serviço como este:

```csharp
await client.CreateDigitalTwinAsync("myTwinId", initData);
```

Para criar um gémeo digital, é necessário fornecer:
* O iD desejado para o gémeo digital
* O [modelo](concepts-models.md) que quer usar

Opcionalmente, pode fornecer valores iniciais para todas as propriedades do twin digital. 

O modelo e os valores iniciais da propriedade são fornecidos através do `initData` parâmetro, que é uma cadeia JSON contendo os dados relevantes. Para obter mais informações sobre a estruturação deste objeto, continue para a secção seguinte.

> [!TIP]
> Após a criação ou atualização de um gémeo, pode haver uma latência de até 10 segundos antes de as alterações se refletirem em [consultas](how-to-query-graph.md). A `GetDigitalTwin` API (descrita [mais tarde neste artigo)](#get-data-for-a-digital-twin)não experimenta este atraso, por isso, se precisar de uma resposta imediata, use a chamada da API em vez de consultar os seus gémeos recém-criados. 

### <a name="initialize-model-and-properties"></a>Inicializar modelo e propriedades

A API de criação gémea aceita um objeto que é serializado numa descrição válida do JSON das propriedades gémeas. Ver [*Conceitos: Gémeos digitais e o gráfico gémeo*](concepts-twins-graph.md) para uma descrição do formato JSON para um gémeo. 

Em primeiro lugar, pode criar um objeto de dados para representar o gémeo e os seus dados de propriedade. Em seguida, você pode usar `JsonSerializer` para passar uma versão serializada deste objeto na chamada API para o `initdata` parâmetro, como este:

```csharp
await client.CreateDigitalTwinAsync(srcId, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
```
Pode criar um objeto de parâmetro manualmente ou utilizando uma classe de ajudante fornecida. Aqui está um exemplo de cada um.

#### <a name="create-twins-using-manually-created-data"></a>Criar gémeos usando dados criados manualmente

Sem o uso de quaisquer classes de ajudantes personalizadas, pode representar as propriedades de um gémeo `Dictionary<string, object>` num, onde `string` o nome da propriedade e o é um objeto que representa a propriedade e o seu `object` valor.

[!INCLUDE [Azure Digital Twins code: create twin](../../includes/digital-twins-code-create-twin.md)]

#### <a name="create-twins-with-the-helper-class"></a>Criar gémeos com a classe de ajudante

A classe de ajudante `BasicDigitalTwin` permite-lhe armazenar campos de propriedade num objeto "twin" diretamente. Pode ainda querer construir a lista de propriedades utilizando um `Dictionary<string, object>` , que pode ser adicionado ao objeto gémeo como seu `CustomProperties` diretamente.

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
props.Add("Humidity", 50.0);
twin.CustomProperties = props;

client.CreateDigitalTwinAsync("myRoomId", JsonSerializer.Serialize<BasicDigitalTwin>(twin));
Console.WriteLine("The twin is created successfully");
```

>[!NOTE]
> `BasicDigitalTwin` objetos vêm com um `Id` campo. Pode deixar este campo vazio, mas se adicionar um valor de ID, tem de corresponder ao parâmetro de ID passado à `CreateDigitalTwin()` chamada. Por exemplo:
>
>```csharp
>twin.Id = "myRoomId";
>```

## <a name="get-data-for-a-digital-twin"></a>Obtenha dados para um gémeo digital

Pode aceder aos detalhes de qualquer gémeo digital chamando o `GetDigitalTwin()` método como este:

```csharp
object result = await client.GetDigitalTwin(id);
```
Esta chamada devolve dados gémeos como uma cadeia JSON. Aqui está um exemplo de como usar isto para ver detalhes gémeos:

```csharp
Response<string> res = client.GetDigitalTwin("myRoomId");
twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.CustomProperties.Keys)
{
  if (twin.CustomProperties.TryGetValue(prop, out object value))
  Console.WriteLine($"Property '{prop}': {value}");
}
```
Apenas as propriedades que foram definidas pelo menos uma vez são devolvidas quando se recupera um gémeo com o `GetDigitalTwin()` método.

>[!TIP]
>O `displayName` para um gémeo faz parte dos seus metadados-modelo, pelo que não vai mostrar quando se obter dados para a instância gémea. Para ver este valor, pode [recuperá-lo do modelo.](how-to-manage-model.md#retrieve-models)

Para recuperar vários gémeos utilizando uma única chamada de API, consulte os exemplos de consulta da API em [*Como-a-: Consultar o gráfico gémeo*](how-to-query-graph.md).

Considere o seguinte modelo (escrito em [Linguagem de Definição de Gémeos Digitais (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL)que define uma *Lua*:

```json
{
    "@id": "dtmi:example:Moon;1",
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
O resultado de chamar `object result = await client.GetDigitalTwinAsync("my-moon");` um gémeo do tipo *Lua*pode ser assim:

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:example:Moon;1",
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
* `$etag`, um campo HTTP padrão atribuído pelo servidor web.
* Outras propriedades numa `$metadata` secção. Estes incluem:
    - O DTMI do modelo do gémeo digital.
    - Estado de sincronização para cada propriedade escrita. Isto é mais útil para dispositivos, onde é possível que o serviço e o dispositivo tenham estatutos divergentes (por exemplo, quando um dispositivo está offline). Atualmente, esta propriedade aplica-se apenas a dispositivos físicos ligados ao IoT Hub. Com os dados na secção de metadados, é possível compreender o estado total de uma propriedade, bem como os últimos timetamps modificados. Para obter mais informações sobre o estado de sincronização, consulte [este tutorial do IoT Hub](../iot-hub/tutorial-device-twins.md) sobre o estado do dispositivo sincronizado.
    - Metadados específicos do serviço, como do IoT Hub ou da Azure Digital Twins. 

Pode analisar o JSON devolvido para o gémeo utilizando uma biblioteca de análise JSON à sua escolha, tal como `System.Text.Json` .

Também pode utilizar a classe de ajudante de serialização `BasicDigitalTwin` que está incluída com o SDK, que irá devolver os metadados e propriedades gémeos principais em forma pré-analisada. Segue-se um exemplo:

```csharp
Response<string> res = client.GetDigitalTwin(twin_Id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

Pode ler mais sobre as aulas de ajudante de serialização em [*Como-a-: Use as APIs e SDKs de Gémeos Digitais Azure*](how-to-use-apis-sdks.md).

## <a name="update-a-digital-twin"></a>Atualize um gémeo digital

Para atualizar as propriedades de um gémeo digital, escreve as informações que pretende substituir no formato [JSON Patch.](http://jsonpatch.com/) Desta forma, pode substituir várias propriedades ao mesmo tempo. Em seguida, passe o documento do Patch JSON para um `UpdateDigitalTwin()` método:

```csharp
await client.UpdateDigitalTwin(id, patch);
```

Uma chamada de patch pode atualizar todas as propriedades de um único gémeo quanto você gostaria (mesmo todas elas). Se precisar de atualizar propriedades em vários gémeos, precisará de uma chamada de atualização separada para cada gémeo.

> [!TIP]
> Após a criação ou atualização de um gémeo, pode haver uma latência de até 10 segundos antes de as alterações se refletirem em [consultas](how-to-query-graph.md). A `GetDigitalTwin` API (descrita [anteriormente neste artigo)](#get-data-for-a-digital-twin)não experimenta este atraso, por isso use a chamada da API em vez de consultar os seus gémeos recém-atualizados se precisar de uma resposta imediata. 

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

await client.UpdateDigitalTwinAsync(twin_Id, JsonSerializer.Serialize(twinData));
Console.WriteLine("Updated twin properties");
FetchAndPrintTwin(twin_Id, client);
}
```

#### <a name="create-patches-using-the-helper-class"></a>Criar patches usando a classe de ajudante

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", 25.0);
await client.UpdateDigitalTwinAsync(twin_Id, uou.Serialize());
```

### <a name="update-properties-in-digital-twin-components"></a>Atualizar propriedades em componentes gémeos digitais

Recorde-se que um modelo pode conter componentes, permitindo que seja composto por outros modelos. 

Para corrigir propriedades nos componentes de um gémeo digital, pode utilizar a sintaxe do caminho no JSON Patch:

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

A `UpdateDigitalTwin()` função também pode ser usada para migrar um gémeo digital para um modelo diferente. 

Por exemplo, considere o seguinte documento JSON Patch que substitui o campo de metadados do gémeo `$model` digital:

```json
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:example:foo;1"
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
    "value": "dtmi:example:foo_new"
  },
  {
    "op": "add",
    "path": "temperature",
    "value": 60
  }
]
```

### <a name="handle-conflicting-update-calls"></a>Lidar com chamadas de atualização conflituosas

A Azure Digital Twins garante que todos os pedidos de entrada são processados um após o outro. Isto significa que mesmo que várias funções tentem atualizar a mesma propriedade em um gémeo ao mesmo tempo, não há **necessidade de** escrever código de bloqueio explícito para lidar com o conflito.

Este comportamento é por gémeo. 

Como exemplo, imagine um cenário em que estas três chamadas cheguem ao mesmo tempo: 
*   Escreva propriedade A em *Twin1*
*   Escreva propriedade B em *Twin1*
*   Escreva propriedade A em *Twin2*

As duas chamadas que modificam *o Twin1* são executadas uma após a outra e as mensagens de alteração são geradas para cada mudança. O apelo para modificar *o Twin2* pode ser executado simultaneamente sem qualquer conflito, assim que chegar.

## <a name="delete-a-digital-twin"></a>Excluir um gémeo digital

Pode eliminar gémeos utilizando o `DeleteDigitalTwin()` método. No entanto, só se pode apagar um gémeo quando não tem mais relacionamentos. Então, apaguem primeiro as relações de entrada e saída do gémeo.

Aqui está um exemplo do código para apagar gémeos e suas relações:

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
        Log.Error($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
    }
}

async Task FindAndDeleteIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        await foreach (IncomingRelationship incomingRel in incomingRels)
        {
            await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
            Log.Ok($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"_*_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
    }
}
```
### <a name="delete-all-digital-twins"></a>Apagar todos os gémeos digitais

Para um exemplo de como eliminar todos os gémeos de uma só vez, descarregue a aplicação de amostras utilizada no [_Tutorial: Explore o básico com uma aplicação de cliente de amostra*](tutorial-command-line-app.md). O *ficheiro CommandLoop.cs* faz isto numa `CommandDeleteAllTwins()` função.

## <a name="manage-twins-using-runnable-code-sample"></a>Gerir gémeos usando amostra de código runável

Pode utilizar a amostra de código runable abaixo para criar um gémeo, atualizar os seus dados e eliminar o gémeo. 

### <a name="set-up-the-runnable-sample"></a>Configurar a amostra runnable

O snippet usa o [Room.jsna](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) definição de modelo a partir de [*Tutorial: Explore Azure Digital Twins com uma aplicação de cliente de amostra.*](tutorial-command-line-app.md) Pode utilizar este link para ir diretamente ao ficheiro ou descarregá-lo como parte do projeto de amostra completa de ponta a [ponta aqui](/samples/azure-samples/digital-twins-samples/digital-twins-samples/).

Antes de executar a amostra, faça o seguinte:
1. Descarregue o ficheiro do modelo, coloque-o no seu projeto e substitua o `<path-to>` espaço reservado no código abaixo para dizer ao seu programa onde encontrá-lo.
2. Substitua o espaço reservado `<your-instance-hostname>` pelo nome de anfitrião da sua instância Azure Digital Twins.
3. Adicione estes pacotes ao seu projeto:
    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

Também terá de configurar credenciais locais se quiser executar a amostra diretamente. A próxima secção passa por isto.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>Executar o exemplo

Depois de completar os passos acima, pode executar diretamente o seguinte código de amostra.

```csharp
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {

        public static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");

            //Create the Azure Digital Twins client for API calls
            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            Console.WriteLine($"Service client created – ready to go");
            Console.WriteLine();

            //Upload models
            Console.WriteLine($"Upload a model");
            Console.WriteLine();
            string dtdl = File.ReadAllText("<path-to>/Room.json");
            var typeList = new List<string>();
            typeList.Add(dtdl);
            // Upload the model to the service
            await client.CreateModelsAsync(typeList);

            //Create new digital twin
            BasicDigitalTwin twin = new BasicDigitalTwin();
            string twin_Id = "myRoomId";
            twin.Metadata = new DigitalTwinMetadata();
            twin.Metadata.ModelId = "dtmi:example:Room;1";
            // Initialize properties
            Dictionary<string, object> props = new Dictionary<string, object>();
            props.Add("Temperature", 35.0);
            props.Add("Humidity", 55.0);
            twin.CustomProperties = props;
            await client.CreateDigitalTwinAsync(twin_Id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
            Console.WriteLine("Twin created successfully");
            Console.WriteLine();

            //Print twin
            Console.WriteLine("--- Printing twin details:");
            twin = FetchAndPrintTwin(twin_Id, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Update twin data
            List<object> twinData = new List<object>();
            twinData.Add(new Dictionary<string, object>() 
            {
                { "op", "add"},
                { "path", "/Temperature"},
                { "value", 25.0}
            });
            await client.UpdateDigitalTwinAsync(twin_Id, JsonSerializer.Serialize(twinData));
            Console.WriteLine("Twin properties updated");
            Console.WriteLine();

            //Print twin again
            Console.WriteLine("--- Printing twin details (after update):");
            FetchAndPrintTwin(twin_Id, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Delete twin
            await DeleteTwin(client, twin_Id);
        }

        private static BasicDigitalTwin FetchAndPrintTwin(string twin_Id, DigitalTwinsClient client)
        {
            BasicDigitalTwin twin;
            Response<string> res = client.GetDigitalTwin(twin_Id);
            twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
            Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
            foreach (string prop in twin.CustomProperties.Keys)
            {
                if (twin.CustomProperties.TryGetValue(prop, out object value))
                    Console.WriteLine($"Property '{prop}': {value}");
            }

            return twin;
        }
        private static async Task DeleteTwin(DigitalTwinsClient client, string id)
        {
            await FindAndDeleteOutgoingRelationshipsAsync(client, id);
            await FindAndDeleteIncomingRelationshipsAsync(client, id);
            try
            {
                await client.DeleteDigitalTwinAsync(id);
                Console.WriteLine("Twin deleted successfully");
            }
            catch (RequestFailedException exc)
            {
                Console.WriteLine($"*** Error:{exc.Message}");
            }
        }

        private static async Task FindAndDeleteOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
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
                    Console.WriteLine($"Deleted relationship {rel.Id} from {dtId}");
                }
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
            }
        }

       private static async Task FindAndDeleteIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin

            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
                    Console.WriteLine($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
                }
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"_*_ Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
            }
        }

    }
}

```
Aqui está a saída da consola do programa acima: 

:::image type="content" source="./media/how-to-manage-twin/console-output-manage-twins.png" alt-text="Saída da consola mostrando que o gémeo é criado, atualizado e eliminado" lightbox="./media/how-to-manage-twin/console-output-manage-twins.png":::

## <a name="manage-twins-with-cli"></a>Gerir gémeos com CLI

Os gémeos também podem ser geridos usando o CLI das Gémeas Digitais Azure. Os comandos podem ser encontrados em [_How-to: Use o Azure Digital Twins CLI*](how-to-use-cli.md).

## <a name="view-all-digital-twins"></a>Ver todos os gémeos digitais

Para ver todos os gémeos digitais no seu caso, use uma [consulta.](how-to-query-graph.md) Pode fazer uma consulta com as [APIs de Consulta](/rest/api/digital-twins/dataplane/query) ou os [comandos CLI](how-to-use-cli.md).

Aqui está o corpo da consulta básica que devolverá uma lista de todos os gémeos digitais no caso:

```sql
SELECT *
FROM DIGITALTWINS
``` 

## <a name="next-steps"></a>Passos seguintes

Veja como criar e gerir relações entre os seus gémeos digitais:
* [*Como fazer: Gerir o gráfico gémeo com relacionamentos*](how-to-manage-graph.md)