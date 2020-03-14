---
title: Dados de localização Geospatial e GeoJSON em Azure Cosmos DB
description: Entenda como criar objetos espaciais com o Azure Cosmos DB e a API SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 655c3a96792fba83ac73365f02d48ce0347e9048
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137891"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Dados de localização Geospatial e GeoJSON em Azure Cosmos DB

Este artigo é uma introdução para a funcionalidade de geoespaciais no Azure Cosmos DB. Atualmente, armazenar e aceder a dados geoespaciais é suportado apenas pelas contas API Da Azure Cosmos DB SQL. Depois de ler a nossa documentação sobre indexação geoespacial poderá responder às seguintes perguntas:

* Como posso armazenar dados geográficos no Azure Cosmos DB?
* Como posso consultar dados geoespaciais no Azure Cosmos DB em SQL e LINQ?
* Como ativar ou desativar a indexação espacial no Azure Cosmos DB?

## <a name="introduction-to-spatial-data"></a>Introdução aos dados geográficos

Dados espaciais descreve a posição e a forma dos objetos no espaço. Na maioria dos aplicativos, elas correspondem aos objetos nos dados de terra e geoespaciais. Dados espaciais podem ser usados para representar a localização de uma pessoa, local de interesse ou o limite de uma cidade ou um lake. Casos de utilização comuns, muitas vezes, envolvem consultas de proximidade, por exemplo, "encontrar todos os cafés perto minha localização atual."

A API SQL da Azure Cosmos DB suporta dois tipos de dados espaciais: o tipo de dados de **geometria** e o tipo de dados **de geografia.**

- O tipo **de geometria** representa dados num sistema de coordenadas Euclideanos (plano)
- O tipo **de geografia** representa dados num sistema de coordenadas terra redonda.

## <a name="supported-data-types"></a>Tipos de dados suportados

A Azure Cosmos DB suporta a indexação e consulta de dados de pontos geoespaciais que estão representados usando a [especificação GeoJSON.](https://tools.ietf.org/html/rfc7946) GeoJSON estruturas de dados são sempre objetos JSON válidos, para que eles podem ser armazenados e consultados com o Azure Cosmos DB sem quaisquer ferramentas especializadas ou bibliotecas.

A Azure Cosmos DB suporta os seguintes tipos de dados espaciais:

- Ponto
- Linha string
- Polígono
- MultiPolygon

### <a name="points"></a>Pontos

Um **Ponto** denota uma única posição no espaço. Um ponto de dados geoespaciais, representa o local exato, o que poderia ser um endereço de rua de uma linha de supermercado, um quiosque, um automóvel ou uma cidade.  Um ponto é representado no par GeoJSON (e do Azure Cosmos DB) usando a coordenada ou longitude e latitude.

Aqui está um exemplo JSON para um ponto:

**Pontos em Azure Cosmos DB**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

Os tipos de dados espaciais podem ser incorporados num documento Azure Cosmos DB, como mostra este exemplo de um perfil de utilizador que contém dados de localização:

**Utilizar perfil com localização armazenada em Azure Cosmos DB**

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

### <a name="points-in-a-geometry-coordinate-system"></a>Pontos num sistema de coordenadas de geometria

Para o tipo de dados de **geometria,** a especificação GeoJSON especifica o eixo horizontal primeiro e o segundo eixo vertical.

### <a name="points-in-a-geography-coordinate-system"></a>Pontos num sistema de coordenadas de geografia

Para o tipo de dados **de geografia,** a especificação GeoJSON especifica a longitude em primeiro lugar e a latitude em segundo. Tal como em outros aplicativos de mapeamento, longitude e latitude são ângulos e representada em termos de graus. Os valores de longitude são medidos desde o primeiro meridiano e são entre-180 graus e 180.0 graus e valores de latitude são medidas a partir do Equador e são entre graus -90,0 e 90,0 graus.

O Azure Cosmos DB interpreta coordenadas conforme representado pelo sistema de referência WGS 84. Veja abaixo para obter mais detalhes sobre os sistemas de coordenadas de referência.

### <a name="linestrings"></a>Strings linestrings

**Os LineStrings** representam uma série de dois ou mais pontos no espaço e os segmentos de linha que os ligam. Dados geoespaciais, LineStrings são frequentemente utilizadas para representar autoestradas ou rios.

**LineStrings em GeoJSON**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>Polígonos

Um **Polygon** é um limite de pontos conectados que forma um LineString fechado. Polígonos são frequentemente utilizados para representar atmosféricas naturais, como lakes ou jurisdições políticas, como Estados e cidades. Aqui está um exemplo de um Polígono em Azure Cosmos DB:

**Polígonos em GeoJSON**

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

### <a name="multipolygons"></a>MultiPolygons

Um **MultiPolygon** é uma variedade de zero ou mais polígonos. **Os multiPolígnos** não podem sobrepor-se a lados ou ter qualquer área comum. Podem tocar num ou mais pontos.

**MultiPolygons em GeoJSON**

```json
{
    "type":"MultiPolygon",
    "coordinates":[ [
        [52.0, 12.0],
        [53.0, 12.0],
        [53.0, 13.0],
        [52.0, 13.0],
        [52.0, 12.0]
    ],
    [
        [50.0, 0.0],
        [51.0, 0.0],
        [51.0, 5.0],
        [50.0, 5.0],
        [50.0, 0.0]
    ] ]
}
```

## <a name="coordinate-reference-systems"></a>Coordenar a sistemas de referência

Uma vez que a forma da Terra é irregular, as coordenadas de dados geoespaciais geoespaciais de geografia estão representadas em muitos sistemas de referência de coordenadas (CRS), cada um com os seus próprios quadros de referência e unidades de medição. Por exemplo, o "nacionais grelha da Grã-Bretanha" é um sistema de referência é preciso para o Reino Unido, mas não fora.

O CRS mais popular em uso hoje é o Sistema Geodésico Mundial [WGS-84](https://earth-info.nga.mil/GandG/update/index.php). Dispositivos GPS e muitos serviços de mapeamento, incluindo Google Maps e as APIs do Bing Maps utilizam WGS 84. A Azure Cosmos DB suporta a indexação e consulta de dados geoespaciais de geografia usando apenas o CRS WGS-84.

## <a name="creating-documents-with-spatial-data"></a>Criar documentos com dados geográficos
Ao criar documentos que contenham valores GeoJSON, eles são automaticamente indexados com um índice geográfico de acordo com a política de indexação do contentor. Se estiver trabalhando com um SDK do Azure Cosmos DB numa linguagem de tipada dinâmico como Python ou node. js, tem de criar GeoJSON válido.

**Criar documento com dados geoespaciais no Nó.js**

```javascript
var userProfileDocument = {
    "id":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

Se estiver a trabalhar com as APIs SQL, pode utilizar as aulas de `Point`, `LineString`, `Polygon`e `MultiPolygon` dentro do espaço de nome `Microsoft.Azure.Cosmos.Spatial` para incorporar informações de localização dentro dos objetos da sua aplicação. Essas classes ajudam a simplificar a serialização e desserialização de dados geográficos em GeoJSON.

**Criar documento com dados geoespaciais em .NET**

```csharp
using Microsoft.Azure.Cosmos.Spatial;

public class UserProfile
{
    [JsonProperty("id")]
    public string id { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await container.CreateItemAsync( new UserProfile
    {
        id = "cosmosdb",
        Location = new Point (-122.12, 47.66)
    });
```

Se não tiver a latitude e a informação de longitude, mas tiver os endereços físicos ou o nome de localização como cidade ou país/região, pode procurar as coordenadas reais usando um serviço de geocodificação como o Bing Maps REST Services. Saiba mais sobre a geocodificação do Bing Maps [aqui.](https://msdn.microsoft.com/library/ff701713.aspx)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu como começar com o suporte geoespacial no Azure Cosmos DB, em seguida, pode:

* Saiba mais sobre [a consulta de DB Azure Cosmos](sql-query-getting-started.md)
* Saiba mais sobre [consulta de dados espaciais com Azure Cosmos DB](sql-query-geospatial-query.md)
* Saiba mais sobre [dados espaciais indexados com Azure Cosmos DB](sql-query-geospatial-index.md)