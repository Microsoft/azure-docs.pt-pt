---
title: Gerir o grafo duplo com relações
titleSuffix: Azure Digital Twins
description: Veja como gerir um gráfico de gémeos digitais ligando-os a relacionamentos.
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 28551cb201ab964a21461d6b3f97ce439e446011
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130294"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Gerir um gráfico de gémeos digitais usando relacionamentos

O coração de Azure Digital Twins é o [gráfico gémeo](concepts-twins-graph.md) que representa todo o seu ambiente. O gráfico gémeo é feito de gémeos digitais individuais ligados através de **relacionamentos.** 

Uma vez que tenha uma [instância Azure Digital Twins](how-to-set-up-instance-portal.md) em funcionamento e tenha configurado código de [autenticação](how-to-authenticate-client.md) na sua aplicação de cliente, pode utilizar as [**APIs digitalTwins**](/rest/api/digital-twins/dataplane/twins) para criar, modificar e eliminar gémeos digitais e as suas relações num caso Azure Digital Twins. Também pode utilizar o [.NET (C#) SDK,](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet-preview&preserve-view=true)ou o [CLI das Gémeas Digitais Azure](how-to-use-cli.md).

Este artigo centra-se na gestão das relações e do gráfico como um todo; para trabalhar com gémeos digitais individuais, ver [*Como-a- Gerir gémeos digitais.*](how-to-manage-twin.md)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]
    
[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Criar relacionamentos

As relações descrevem como diferentes gémeos digitais estão ligados entre si, o que constitui a base do gráfico gémeo.

As relações são criadas usando a `CreateRelationship()` chamada. 

Para criar uma relação, precisa de especificar:
* O ID gémeo de origem `srcId` (na amostra de código abaixo): A identificação do gémeo de onde a relação se origina.
* O ID gémeo alvo `targetId` (na amostra de código abaixo): A identificação do gémeo onde a relação chega.
* Um nome de relacionamento `relName` (na amostra de código abaixo): O tipo genérico de relacionamento, algo como _._
* Um ID de relacionamento `relId` (na amostra de código abaixo): O nome específico para esta relação, algo como _Relacionamento1_ .

A identificação da relação deve ser única dentro do gémeo de origem dada. Não precisa de ser globalmente único.
Por exemplo, para o *twin foo,* cada identificação de relacionamento específico deve ser único. No entanto, outro *bar* gémeo pode ter uma relação de saída que corresponde à mesma identificação de uma relação *foo.*

A seguinte amostra de código ilustra como criar uma relação no seu exemplo de Azure Digital Twins.

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
            
        }
```
No seu método principal, pode agora chamar a `CreateRelationship()` função para criar uma relação _como_ esta: 

```csharp
await CreateRelationship(client, srcId, targetId, "contains");
```
Se desejar criar múltiplas relações, pode repetir chamadas para o mesmo método, passando diferentes tipos de relacionamento para o argumento. 

Para obter mais informações sobre a classe de `BasicRelationship` ajudante, consulte [*Como-a-fazer: Use as APIs e SDKs de gémeos digitais Azure*](how-to-use-apis-sdks.md#serialization-helpers).

### <a name="create-multiple-relationships-between-twins"></a>Criar relações múltiplas entre gémeos

As relações podem ser classificadas como: 

* Relações de saída: Relacionamentos pertencentes a este gémeo que apontam para fora para ligá-lo a outros gémeos. O `GetRelationshipsAsync()` método é usado para obter relações de saída de um gémeo.
* Relações de entrada: Relações pertencentes a outras gémeas que apontam para este gémeo para criar uma ligação "incoming". O `GetIncomingRelationshipsAsync()` método é usado para obter relações de um gémeo.

Não há restrição no número de relacionamentos que pode ter entre dois gémeos — pode ter tantas relações entre gémeos quanto quiser. 

Isto significa que pode expressar vários tipos diferentes de relacionamentos entre dois gémeos ao mesmo tempo. Por exemplo, *a Twin A* pode ter uma relação *armazenada* e uma relação *manufaturada* com *a Twin B* .

Pode até criar múltiplos casos do mesmo tipo de relação entre os mesmos dois gémeos, se desejar. Neste exemplo, *Twin A* poderia ter duas relações diferentes *armazenadas* com *Twin B* , desde que as relações tenham iDs de relacionamento diferentes.

## <a name="list-relationships"></a>Relações de lista

Para aceder à lista de relações **de saída** para um dado gémeo no gráfico, pode utilizar o método `GetRelationships()` como este:

```csharp
await client.GetRelationships()
```

Isto devolve `Azure.Pageable<T>` uma `Azure.AsyncPageable<T>` ou, dependendo se utiliza a versão sincronizada ou assíncrona da chamada.

Aqui está um exemplo que recupera uma lista de relacionamentos:

```csharp
public static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
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
                    Console.WriteLine(relJson);
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

```
Agora pode chamar este método para ver as relações de saída dos gémeos assim:

```csharp
await FindOutgoingRelationshipsAsync(client, twin_Id);
```
Pode usar as relações recuperadas para navegar para outros gémeos no seu gráfico. Para isso, leia o `target` campo a partir da relação que é devolvida, e use-o como ID para a sua próxima chamada para `GetDigitalTwin()` .

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Encontre relacionamentos de entrada para um gémeo digital

A Azure Digital Twins também tem uma API para encontrar todas as relações *com* um dado gémeo. Isto é frequentemente útil para a navegação inversa, ou quando elimina um gémeo.

A amostra de código anterior focava-se em encontrar relações de saída de um gémeo. O exemplo a seguir é estruturado da mesma forma, mas encontra relações *de entrada* com o gémeo.

Note que as `IncomingRelationship` chamadas não devolvem todo o corpo da relação.

```csharp
public static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine(incomingRel);

                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }
```

Agora pode chamar este método para ver as relações dos gémeos como este:

```csharp
await FindIncomingRelationshipsAsync(client, twin_Id);
```
### <a name="list-all-twin-properties-and-relationships"></a>Listar todas as propriedades e relacionamentos gémeos

Utilizando os métodos acima referidos para listar relações de saída e entrada a um gémeo, pode criar um método que imprime informações gémeas completas, incluindo as propriedades do gémeo e ambos os tipos de relacionamentos. Aqui está um método de exemplo, chamado `FetchAndPrintTwinAsync()` , mostrando como fazê-lo.

```csharp  
private static async Task FetchAndPrintTwinAsync(DigitalTwinsClient client, string twin_Id)
        {
            BasicDigitalTwin twin;
            Response<string> res = client.GetDigitalTwin(twin_Id);
            twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
            
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }
```

Pode agora chamar esta função no seu método principal como este: 

```csharp
await FetchAndPrintTwinAsync(client, targetId);
```
## <a name="delete-relationships"></a>Eliminar relações

O primeiro parâmetro especifica o gémeo de origem (o gémeo de onde a relação se origina). O outro parâmetro é a identificação da relação. Precisas tanto da identificação gémea como da identificação da relação, porque as identificações de relacionamento são únicas no âmbito de um gémeo.

```csharp
private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
```

Pode agora chamar este método para eliminar uma relação como esta:

```csharp
await DeleteRelationship(client, srcId, relId);
```
## <a name="create-a-twin-graph"></a>Criar um gráfico gémeo 

O seguinte código runnable snippet usa as operações de relacionamento deste artigo para criar um gráfico gémeo a partir de gémeos digitais e relacionamentos.

### <a name="set-up-the-runnable-sample"></a>Configurar a amostra runnable

O snippet usa o [*Room.jse*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) [*Floor.jsem*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) definições de modelo a partir de [*Tutorial: Explore Azure Digital Twins com uma aplicação de cliente de amostra.*](tutorial-command-line-app.md) Pode utilizar estes links para ir diretamente aos ficheiros ou descarregá-los como parte do projeto de amostra completa de ponta a [ponta aqui](/samples/azure-samples/digital-twins-samples/digital-twins-samples/). 

Antes de executar a amostra, faça o seguinte:
1. Descarregue os ficheiros de modelos, coloque-os no seu projeto e substitua os `<path-to>` espaços reservados no código abaixo para dizer ao seu programa onde os encontrar.
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
            DigitalTwinsClient client = createDTClient();
            Console.WriteLine($"Service client created – ready to go");
            Console.WriteLine();

            //Upload models
            Console.WriteLine($"Upload models");
            Console.WriteLine();
            string dtdl = File.ReadAllText("<path-to>/Room.json");
            string dtdl1 = File.ReadAllText("<path-to>/Floor.json");
            var typeList = new List<string>();
            typeList.Add(dtdl);
            typeList.Add(dtdl1);
            // Upload the models to the service
            await client.CreateModelsAsync(typeList);

            //Create new (Floor) digital twin
            BasicDigitalTwin floorTwin = new BasicDigitalTwin();
            string srcId = "myFloorID";
            floorTwin.Metadata = new DigitalTwinMetadata();
            floorTwin.Metadata.ModelId = "dtmi:example:Floor;1";
            //Floor twins have no properties, so nothing to initialize
            //Create the twin
            await client.CreateDigitalTwinAsync(srcId, JsonSerializer.Serialize<BasicDigitalTwin>(floorTwin));
            Console.WriteLine("Twin created successfully");

            //Create second (Room) digital twin
            BasicDigitalTwin roomTwin = new BasicDigitalTwin();
            string targetId = "myRoomID";
            roomTwin.Metadata = new DigitalTwinMetadata();
            roomTwin.Metadata.ModelId = "dtmi:example:Room;1";
            // Initialize properties
            Dictionary<string, object> props = new Dictionary<string, object>();
            props.Add("Temperature", 35.0);
            props.Add("Humidity", 55.0);
            roomTwin.CustomProperties = props;
            //Create the twin
            await client.CreateDigitalTwinAsync(targetId, JsonSerializer.Serialize<BasicDigitalTwin>(roomTwin));
            
            //Create relationship between them
            await CreateRelationship(client, srcId, targetId, "contains");
            Console.WriteLine();

            //Print twins and their relationships
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Delete the relationship
            Console.WriteLine("Deleting the relationship");
            await DeleteRelationship(client, srcId, $"{srcId}-contains->{targetId}");
            Console.WriteLine();

            //Print twins and their relationships again
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();
        }

        private static DigitalTwinsClient createDTClient()
        {
            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            return client;
        }
        private async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            // Create relationship between twins
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }

        }

        private static async Task FetchAndPrintTwinAsync(string twin_Id, DigitalTwinsClient client)
        {
            BasicDigitalTwin twin;
            Response<string> res = client.GetDigitalTwin(twin_Id);
            twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }

        private static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
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
                    Console.WriteLine(relJson);
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine(incomingRel.RelationshipId);

                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
    }
}
```

Aqui está a saída da consola do programa acima: 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="Saída da consola mostrando os detalhes gémeos, as relações de entrada e saída dos gémeos." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> O gráfico gémeo é um conceito de criação de relações entre gémeos. Se quiser ver a representação visual do gráfico gémeo, consulte a secção [_Visualization*](how-to-manage-graph.md#visualization) deste artigo. 

### <a name="create-a-twin-graph-from-a-spreadsheet"></a>Criar um gráfico gémeo a partir de uma folha de cálculo

Em casos práticos de utilização, as hierarquias gémeas serão muitas vezes criadas a partir de dados armazenados numa base de dados diferente, ou talvez numa folha de cálculo. Esta secção ilustra como uma folha de cálculo pode ser analisada.

Considere a seguinte tabela de dados, descrevendo um conjunto de gémeos digitais e relações a criar.

| ID do Modelo| Twin ID (deve ser único) | Nome da relação | ID gémeo alvo | Dados de informação dupla |
| --- | --- | --- | --- | --- |
| dtmi:exemplo:Piso;1 | Piso1 |  contains | Sala1 |{"Temperatura": 80, "Humidade": 60}
| dtmi:exemplo:Piso;1 | Piso0 |  tem      | Sala0 |{"Temperatura": 70, "Humidade": 30}
| dtmi:exemplo:Quarto;1  | Sala1 | 
| dtmi:exemplo:Quarto;1  | Sala0 |

O código que se segue utiliza a [API do Gráfico microsoft](/graph/overview) para ler uma folha de cálculo e construir um gráfico gémeo Azure Digital Twins a partir dos resultados.

```csharp
var range = msftGraphClient.Me.Drive.Items["BuildingsWorkbook"].Workbook.Worksheets["Building"].usedRange;
JsonDocument data = JsonDocument.Parse(range.values);
List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
foreach (JsonElement row in data.RootElement.EnumerateArray())
{
    string modelId = row[0].GetString();
    string sourceId = row[1].GetString();
    string relName = row[2].GetString();
    string targetId = row[3].GetString();
    string initData = row[4].GetString();
    
    // Parse spreadsheet extra data into a JSON string to initialize the digital twin
    // Left out for compactness
    Dictionary<string, object> initData = new Dictionary<string, object>() { ... };

    if (sourceId != null)
    {
        BasicRelationship br = new BasicRelationship()
        {
            SourceId = sourceId,
            TargetId = targetId,
            Name = relName
        };
        RelationshipRecordList.Add(br);
    }

    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.CustomProperties = initData;
    // Set the type of twin to be created
    twin.Metadata = new DigitalTwinMetadata() { ModelId = modelId };
    
    try
    {
        await client.CreateDigitalTwinAsync(sourceId, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
    }
    catch (RequestFailedException e)
    {
       Console.WriteLine($"Error {e.Status}: {e.Message}");
    }
    foreach (BasicRelationship rec in RelationshipRecordList)
    { 
        try { 
            await client.CreateRelationshipAsync(rec.sourceId, Guid.NewGuid().ToString(), JsonSerializer.Serialize<BasicRelationship>(rec));
        }
        catch (RequestFailedException e)
        {
            Console.WriteLine($"Error {e.Status}: {e.Message}");
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