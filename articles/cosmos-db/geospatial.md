---
title: Trabalhar com dados geoespaciais na conta do Azure Cosmos DB SQL API
description: Aprenda a criar, indexar e consultar objetos espaciais com o Azure Cosmos DB e a API de SQL.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: 2d8d62dd8426b2ab1c60f8e50e8ab0c17a37abfb
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241056"
---
# <a name="use-geospatial-and-geojson-location-data-with-azure-cosmos-db-sql-api-account"></a>Utilizar dados de localização Geoespacial e GeoJSON com a conta do Azure Cosmos DB SQL API

Este artigo é uma introdução para a funcionalidade de geoespaciais no Azure Cosmos DB. Atualmente, armazenar e aceder aos dados geoespacial é suportada pelo apenas as contas do Cosmos DB SQL API. Depois de ler este artigo, será capaz de responder às seguintes perguntas:

* Como posso armazenar dados geográficos no Azure Cosmos DB?
* Como posso consultar dados geoespaciais no Azure Cosmos DB em SQL e LINQ?
* Como ativar ou desativar a indexação espacial no Azure Cosmos DB?

Este artigo mostra como trabalhar com dados geográficos com a API de SQL. Ver isso [projeto GitHub](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) para exemplos de código.

## <a name="introduction-to-spatial-data"></a>Introdução aos dados geográficos
Dados espaciais descreve a posição e a forma dos objetos no espaço. Na maioria dos aplicativos, elas correspondem aos objetos nos dados de terra e geoespaciais. Dados espaciais podem ser usados para representar a localização de uma pessoa, local de interesse ou o limite de uma cidade ou um lake. Casos de utilização comuns, muitas vezes, envolvem consultas de proximidade, por exemplo, "encontrar todos os cafés perto minha localização atual." 

### <a name="geojson"></a>GeoJSON
Azure Cosmos DB suporta a indexação e consulta de dados de ponto de geoespaciais que tenham representado através de [especificação GeoJSON](https://tools.ietf.org/html/rfc7946). GeoJSON estruturas de dados são sempre objetos JSON válidos, para que eles podem ser armazenados e consultados com o Azure Cosmos DB sem quaisquer ferramentas especializadas ou bibliotecas. Os SDKs do Azure Cosmos DB fornecem classes auxiliares e métodos que tornam fácil trabalhar com dados geográficos. 

### <a name="points-linestrings-and-polygons"></a>Pontos e polígonos LineStrings
R **ponto** denota uma única posição no espaço. Um ponto de dados geoespaciais, representa o local exato, o que poderia ser um endereço de rua de uma linha de supermercado, um quiosque, um automóvel ou uma cidade.  Um ponto é representado no par GeoJSON (e do Azure Cosmos DB) usando a coordenada ou longitude e latitude. Eis um exemplo JSON para um ponto.

**Pontos no Azure Cosmos DB**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

> [!NOTE]
> A especificação GeoJSON Especifica longitude primeiro e latitude segundo. Tal como em outros aplicativos de mapeamento, longitude e latitude são ângulos e representada em termos de graus. Os valores de longitude são medidos desde o primeiro meridiano e são entre-180 graus e 180.0 graus e valores de latitude são medidas a partir do Equador e são entre graus -90,0 e 90,0 graus. 
> 
> O Azure Cosmos DB interpreta coordenadas conforme representado pelo sistema de referência WGS 84. Veja abaixo para obter mais detalhes sobre os sistemas de coordenadas de referência.
> 
> 

Isso pode ser incorporado em documentos do Azure Cosmos DB como mostrado neste exemplo de um perfil de utilizador que contém dados de localização:

**Utilizar o perfil com a localização armazenada no Azure Cosmos DB**

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

Para além dos pontos, GeoJSON também suporta LineStrings e polígonos. **LineStrings** representam uma série de duas ou mais pontos no espaço e os segmentos de linha que os ligam. Dados geoespaciais, LineStrings são frequentemente utilizadas para representar autoestradas ou rios. R **polígono** é um limite de pontos ligados de que forma uma LineString fechada. Polígonos são frequentemente utilizados para representar atmosféricas naturais, como lakes ou jurisdições políticas, como Estados e cidades. Eis um exemplo de um polígono no Azure Cosmos DB. 

**Polígonos no GeoJSON**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ],
        [ 32, -4.7 ],
        [ 32, -5 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> A especificação GeoJSON requer que, para polígonos válidos, o par de coordenadas última fornecido deve ser o mesmo que o primeiro, para criar uma forma fechada.
> 
> Pontos de dentro de um polígono tem de ser especificados na ordem no sentido. Um polígono especificado por ordem para a direita representa o inverso da região na mesma.
> 
> 

Além de ponto, LineString e Polygon, GeoJSON também especifica a representação para agrupar várias localizações geoespaciais, bem como associar propriedades arbitrárias a geolocalização como uma **funcionalidade**. Uma vez que esses objetos são um JSON válido, podem todos ser armazenados e processados no Azure Cosmos DB. No entanto o Azure Cosmos DB só suporta a indexação automática de pontos.

### <a name="coordinate-reference-systems"></a>Coordenar a sistemas de referência
Como a forma da terra é irregular, as coordenadas dos dados geoespaciais são representadas em muitos sistemas de coordenadas de referência (CRS), cada qual com seus próprios quadros de referência e unidades de medida. Por exemplo, o "nacionais grelha da Grã-Bretanha" é um sistema de referência é preciso para o Reino Unido, mas não fora. 

O CRS mais populares em uso atualmente é o sistema geodésico de mundo [WGS 84](http://earth-info.nga.mil/GandG/wgs84/). Dispositivos GPS e muitos serviços de mapeamento, incluindo Google Maps e as APIs do Bing Maps utilizam WGS 84. O Azure Cosmos DB oferece suporte a indexação e consulta de dados geoespaciais com o CRS WGS 84 apenas. 

## <a name="creating-documents-with-spatial-data"></a>Criar documentos com dados geográficos
Ao criar documentos que contenham valores GeoJSON, eles são automaticamente indexados com um índice geográfico de acordo com a política de indexação do contentor. Se estiver trabalhando com um SDK do Azure Cosmos DB numa linguagem de tipada dinâmico como Python ou node. js, tem de criar GeoJSON válido.

**Criar documentos com dados Geoespaciais em node. js**

```json
var userProfileDocument = {
    "name":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

Se estiver a trabalhar com as APIs do SQL, pode utilizar o `Point` e `Polygon` classes dentro do `Microsoft.Azure.Documents.Spatial` espaço de nomes de incorporar informações de localização em seus objetos de aplicação. Essas classes ajudam a simplificar a serialização e desserialização de dados geográficos em GeoJSON.

**Criar documentos com dados Geoespaciais no .NET**

```json
using Microsoft.Azure.Documents.Spatial;

public class UserProfile
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "profiles"), 
    new UserProfile 
    { 
        Name = "cosmosdb", 
        Location = new Point (-122.12, 47.66) 
    });
```

Se não tiver as informações de latitude e longitude, mas possuem os endereços físicos ou o nome da localização como cidade ou país/região, pode procurar as coordenadas reais com um serviço de geocodificação, como serviços de REST do Bing Maps. Saiba mais sobre geocodificação do Bing Maps [aqui](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="querying-spatial-types"></a>Consultar os tipos geográficos
Agora que já vimos uma olhada em como inserir dados geoespaciais, vamos dar uma olhada em como consultar estes dados com o Azure Cosmos DB com o SQL e LINQ.

### <a name="spatial-sql-built-in-functions"></a>Espaciais funções internas de SQL
Azure Cosmos DB suporta as seguintes funções internas de Open Geoespacial Consortium (OGC) para consultar dados geoespaciais. Para obter mais informações sobre o conjunto completo de funções incorporadas em linguagem SQL, consulte [consulta do Azure Cosmos DB](how-to-sql-query.md).

|**Utilização**|**Descrição**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | Devolve a distância entre as duas expressões LineString, Polygon ou GeoJSON ponto.|
|ST_WITHIN (spatial_expr, spatial_expr) | Devolve uma expressão booleana que indica se o primeiro objeto GeoJSON (ponto, polígono ou LineString) está dentro do objeto de GeoJSON segundo (ponto, polígono ou LineString).|
|ST_INTERSECTS (spatial_expr, spatial_expr)| Devolve uma expressão booleana que indica se os dois objetos de GeoJSON especificados (ponto, polígono ou LineString) intersect.|
|ST_ISVALID| Devolve um valor booleano que indica se a expressão de LineString, Polygon ou GeoJSON ponto especificada é válida.|
| ST_ISVALIDDETAILED| Devolve um valor JSON que contém um valor booleano valor se a expressão de LineString, Polygon ou GeoJSON ponto especificada é válida e se for inválido, além do motivo pelo qual como um valor de cadeia de caracteres.|

Funções espaciais podem ser utilizadas para executar consultas de proximidade contra dados geográficos. Por exemplo, eis uma consulta que devolve todos os documentos de famílias que estão dentro de 30 km de distância do local especificado usando a função incorporada ST_DISTANCE. 

**Consulta**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Resultados**

    [{
      "id": "WakefieldFamily"
    }]

Se incluir a indexação espacial na sua política de indexação, em seguida, "consultas de distância" serão fornecidas com eficiência por meio do índice. Para obter mais informações sobre indexação espacial, consulte a secção abaixo. Se não tiver um índice geográfico para caminhos especificados, ainda pode efetuar consultas espaciais especificando `x-ms-documentdb-query-enable-scan` cabeçalho do pedido com o valor definido como "true". No .NET, isso pode ser feito ao passar o opcional **FeedOptions** argumento para consultas com [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) definido como true. 

ST_WITHIN pode ser utilizado para verificar se um ponto de reside dentro de um polígono. Normalmente os polígonos são usados para representar limites, como códigos postais, limites de estado ou atmosféricas naturais. Novamente se incluem a indexação espacial na sua política de indexação, em seguida, "dentro" consultas serão fornecidas com eficiência por meio do índice. 

Argumentos de polígono no ST_WITHIN podem conter apenas um toque único, ou seja, os polígonos não pode conter buracos nas mesmas. 

**Consulta**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Resultados**

    [{
      "id": "WakefieldFamily",
    }]

> [!NOTE]
> Semelhante a como sem correspondência de tipos de trabalho, na consulta do Azure Cosmos DB, se o valor de localização especificado no argumento é inválido ou com formato incorreto, em seguida, ela é avaliada como **indefinido** e o documento avaliado de ser ignorada nos resultados da consulta. Se a consulta não devolve nenhum resultado, execute ST_ISVALIDDETAILED para depuração por que o tipo espacial é inválido.     
> 
> 

O Azure Cosmos DB também suporta a execução de consultas inversa, ou seja, pode indexar polígonos ou linhas no Azure Cosmos DB, em seguida, consultar para as áreas que contêm um determinado ponto. Esse padrão é comumente usado de logística para identificar, por exemplo, quando um camião entra ou sai de uma área designada. 

**Consulta**

    SELECT * 
    FROM Areas a 
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)


**Resultados**

    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon", 
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]

ST_ISVALID e ST_ISVALIDDETAILED podem ser utilizados para verificar se um objeto espacial é válido. Por exemplo, a consulta seguinte verifica a validade de um ponto com um fora do valor de intervalo de latitude (-132.8). ST_ISVALID retorna apenas um valor Booleano e ST_ISVALIDDETAILED retorna o valor de booleano e uma cadeia de caracteres contendo o motivo por que é considerado inválido.

**Consulta**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Resultados**

    [{
      "$1": false
    }]

Essas funções também podem ser utilizadas para validar polígonos. Por exemplo, aqui usamos ST_ISVALIDDETAILED para validar um polígono que não seja fechado. 

**Consulta**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Resultados**

    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]

### <a name="linq-querying-in-the-net-sdk"></a>LINQ consultar no SDK do .NET
O SDK de .NET de SQL também provedores de stub de métodos `Distance()` e `Within()` para utilização nas expressões de LINQ. O provedor SQL LINQ traduz-se esse método chamadas para as chamadas de função incorporada de SQL equivalentes (ST_DISTANCE e ST_WITHIN respectivamente). 

Eis um exemplo de uma consulta LINQ que localiza todos os documentos na coleção do Azure Cosmos DB cujo valor de "localização" é dentro de um raio de 30 km de distância de especificado apontar usando LINQ.

**Consulta LINQ para distância**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

Da mesma forma, aqui está uma consulta para encontrar todos os documentos cuja "localização" está dentro do caixa/polígono especificado. 

**LINQ consultar dentro**

    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


Agora que já vimos uma olhada em como consultar documentos usando LINQ e SQL, vamos dar uma olhada em como configurar o Azure Cosmos DB para indexação espacial.

## <a name="indexing"></a>Indexação
Conforme é descrito na [indexação de independente de esquema com o Azure Cosmos DB](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) paper, projetamos motor de base de dados do Azure Cosmos DB para ser realmente independente de esquema e fornecer suporte de primeira classe para JSON. O motor de base de dados otimizado para escrita do Azure Cosmos DB compreende nativamente os dados geográficos (pontos de polígonos e linhas) representados no padrão GeoJSON.

Em resumo, geometry é projetado de coordenadas geodésico num plano 2D, em seguida, dividido progressivamente em células com um **quadtree**. Essas células são mapeadas para 1 dia, com base na localização da célula dentro de um **curva de preenchimento do espaço de Hilbert**, que preserva a localidade de pontos. Além disso quando os dados de localização são indexados, ele passa por um processo conhecido como **tecelagem**, ou seja, todas as células que fazem interseção uma localização identificadas e armazenadas como chaves no índice do Azure Cosmos DB. No momento da consulta, argumentos como pontos e polígonos são também presentes para extrair os intervalos de ID de célula relevantes, em seguida, usados para recuperar dados do índice.

Se especificar uma política de indexação que inclui o índice geográfico para / * (todos os caminhos), em seguida, todos os pontos de encontrado dentro da coleção são indexados para consultas espaciais eficientes (ST_WITHIN e ST_DISTANCE). Os índices espaciais não ter um valor de precisão e utilize sempre um valor de precisão padrão.

> [!NOTE]
> Azure Cosmos DB suporta a indexação automática de pontos de polígonos e LineStrings
> 
> 

O fragmento JSON seguinte mostra uma política de indexação com indexação espacial ativado, ou seja, qualquer ponto de GeoJSON encontrado em documentos para consultar o geográficos de índice. Se pretender modificar a política de indexação no portal do Azure, pode especificar o seguinte JSON para a política de indexação para ativar a sua coleção de indexação espacial.

**Coleção JSON de política de indexação com Geográ ativada para os pontos e polígonos**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Range",
                   "dataType":"String",
                   "precision":-1
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                },
                {
                   "kind":"Spatial",
                   "dataType":"Polygon"
                }                
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

Aqui está um trecho de código no .NET que mostra como criar uma coleção com o serviço de indexação espacial ativada para todos os caminhos que contenham pontos. 

**Criar uma coleção com indexação espacial**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

E aqui está como modificar uma coleção existente para tirar partido da indexação espacial sobre quaisquer pontos que são armazenados dentro de documentos.

**Modificar uma coleção existente com indexação espacial**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point));
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [!NOTE]
> Se a localização do valor GeoJSON dentro do documento é um formato incorreto ou é inválido, em seguida, este será não obter indexado para consultas espaciais. Pode validar valores de localização usando ST_ISVALID e ST_ISVALIDDETAILED.
> 
> Se a definição da coleção inclui uma chave de partição, a indexação de progresso de transformação não é comunicado. 
> 
> 

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu como começar com o suporte geoespacial no Azure Cosmos DB, em seguida, pode:

* Começar a codificar a [exemplos de código .NET Geoespaciais no GitHub](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
* Tire partido de com as consultas de geoespacial no [recreio de consultas do Azure Cosmos DB](https://www.documentdb.com/sql/demo#geospatial)
* Saiba mais sobre [consulta do Azure Cosmos DB](how-to-sql-query.md)
* Saiba mais sobre [políticas de indexação do Azure Cosmos DB](index-policy.md)

