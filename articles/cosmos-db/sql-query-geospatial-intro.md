---
title: Dados de localização Geospatial e GeoJSON em Azure Cosmos DB
description: Entenda como criar objetos espaciais com Azure Cosmos DB e a API SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 10882a0f4daf5c0d9aa1d4aeb53b2f14f9d1dea0
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100835"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Dados de localização Geospatial e GeoJSON em Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo é uma introdução à funcionalidade geoespacial em Azure Cosmos DB. Atualmente, armazenar e aceder a dados geoespaciais é suportado apenas pelas contas Azure Cosmos DB SQL API. Depois de ler a nossa documentação sobre a indexação geoespacial, poderá responder às seguintes perguntas:

* Como posso armazenar dados espaciais em Azure Cosmos DB?
* Como posso consultar dados geoespaciais em Azure Cosmos DB em SQL e LINQ?
* Como posso ativar ou desativar a indexação espacial em Azure Cosmos DB?

## <a name="spatial-data-use-cases"></a>Casos de utilização de dados espaciais

Os dados geoespacial envolvem muitas vezes consultas de proximidade, por exemplo, "encontrar todos os cafés perto da minha localização atual". Os casos de uso comum são:

* Geolocalização Analytics, impulsionando iniciativas específicas de marketing localizadas.
* Personalização baseada em localização, para várias indústrias como o Retail and Healthcare.
* Melhoria logística, para otimização de transportes.
* Análise de Risco, especialmente para empresas de seguros e finanças.
* Sensibilização para a situação, para alertas e notificações.

## <a name="introduction-to-spatial-data"></a>Introdução aos dados espaciais

Os dados espaciais descrevem a posição e a forma dos objetos no espaço. Na maioria das aplicações, estas correspondem a objetos na terra e dados geoespacial. Dados espaciais podem ser usados para representar a localização de uma pessoa, um lugar de interesse, ou o limite de uma cidade, ou um lago.

A AZure Cosmos DB's SQL API suporta dois tipos de dados espaciais: o tipo de dados **de geometria** e o tipo de dados **de geografia.**

- O tipo **de geometria** representa dados num sistema de coordenadas euclidídeos (plano)
- O tipo **de geografia** representa dados num sistema de coordenadas de terra redonda.

## <a name="supported-data-types"></a>Tipos de dados suportados

O Azure Cosmos DB suporta a indexação e consulta de dados de pontos geoespaciais que são representados usando a [especificação GeoJSON](https://tools.ietf.org/html/rfc7946). As estruturas de dados da GeoJSON são sempre objetos JSON válidos, para que possam ser armazenados e consultados utilizando o Azure Cosmos DB sem ferramentas ou bibliotecas especializadas.

A Azure Cosmos DB suporta os seguintes tipos de dados espaciais:

- Ponto
- LineString
- Polígono
- MultiPolygon

### <a name="points"></a>Pontos

Um **ponto** denota uma única posição no espaço. Em dados geoespaciais, um Ponto representa a localização exata, que pode ser um endereço de rua de uma mercearia, um quiosque, um automóvel, ou uma cidade.  Um ponto é representado em GeoJSON (e Azure Cosmos DB) usando o seu par de coordenadas ou longitude e latitude.

Aqui está um exemplo JSON para um ponto:

**Pontos em Azure Cosmos DB**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

Os tipos de dados espaciais podem ser incorporados num documento DB da Azure Cosmos, como mostra este exemplo de um perfil de utilizador que contém dados de localização:

**Use o perfil com localização armazenada em Azure Cosmos DB**

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

Para o tipo de dados **de geometria,** a especificação geoJSON especifica primeiro o eixo horizontal e o eixo vertical em segundo.

### <a name="points-in-a-geography-coordinate-system"></a>Pontos num sistema de coordenadas de geografia

Para o tipo de dados **de geografia,** a especificação geoJSON especifica a longitude em primeiro lugar e a latitude em segundo lugar. Como em outras aplicações de mapeamento, a longitude e a latitude são ângulos e representados em termos de graus. Os valores de longitude são medidos a partir do Primeiro Meridiano e estão entre -180 graus e 180,0 graus, e os valores de latitude são medidos a partir do equador e estão entre -90,0 graus e 90,0 graus.

AZure Cosmos DB interpreta as coordenadas representadas pelo sistema de referência WGS-84. Veja abaixo mais detalhes sobre os sistemas de referência de coordenadas.

### <a name="linestrings"></a>LineStrings

**Os LineStrings** representam uma série de dois ou mais pontos no espaço e os segmentos de linha que os ligam. Em dados geoespaciais, os LineStrings são comumente usados para representar autoestradas ou rios.

**LineStrings em GeoJSON**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>Polígonos

Um **Polígono** é um limite de pontos conectados que forma um LineString fechado. Os polígonos são comumente usados para representar formações naturais como lagos ou jurisdições políticas como cidades e estados. Aqui está um exemplo de um Polígono em Azure Cosmos DB:

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
> A especificação GeoJSON requer que, para polígonos válidos, o último par de coordenadas fornecido deve ser o mesmo que o primeiro, para criar uma forma fechada.
>
> Os pontos dentro de um polígono devem ser especificados em ordem no sentido contrário ao dos ponteiros do relógio. Um polígono especificado na ordem do relógio representa o inverso da região dentro dela.
>
>

### <a name="multipolygons"></a>MultiPolygons

Um **MultiPolygon** é uma matriz de zero ou mais Polígonos. **Os MultiPolygons** não podem sobrepor-se a lados ou ter qualquer área comum. Podem tocar em um ou mais pontos.

**MultiPolygons em GeoJSON**

```json
{
    "type":"MultiPolygon",
    "coordinates":[[[
        [52.0, 12.0],
        [53.0, 12.0],
        [53.0, 13.0],
        [52.0, 13.0],
        [52.0, 12.0]
        ]],
        [[
        [50.0, 0.0],
        [51.0, 0.0],
        [51.0, 5.0],
        [50.0, 5.0],
        [50.0, 0.0]
        ]]]
}
```

## <a name="coordinate-reference-systems"></a>Coordenar sistemas de referência

Uma vez que a forma da terra é irregular, as coordenadas de dados geoespaciais de geografia estão representadas em muitos sistemas de referência de coordenadas (SIR), cada um com os seus próprios quadros de referência e unidades de medição. Por exemplo, a "Rede Nacional da Grã-Bretanha" é um sistema de referência que é preciso para o Reino Unido, mas não fora dele.

O CRS mais popular em uso hoje é o World Geodetic System  [WGS-84](https://earth-info.nga.mil/GandG/update/index.php). Dispositivos GPS, e muitos serviços de mapeamento, incluindo Google Maps e Bing Maps APIs usam WGS-84. O Azure Cosmos DB suporta a indexação e consulta de dados geoespaciais de geografia utilizando apenas o CRS WGS-84.

## <a name="creating-documents-with-spatial-data"></a>Criação de documentos com dados espaciais
Quando cria documentos que contenham valores GeoJSON, são automaticamente indexados a um índice espacial de acordo com a política de indexação do recipiente. Se estiver a trabalhar com um Azure Cosmos DB SDK numa linguagem dinâmica como Python ou Node.js, deve criar um GeoJSON válido.

**Criar Documento com dados Geoespaciais em Node.js**

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

Se estiver a trabalhar com as APIs SQL, pode utilizar o `Point` , e as classes dentro do espaço de `LineString` `Polygon` `MultiPolygon` `Microsoft.Azure.Cosmos.Spatial` nomes para incorporar informações de localização dentro dos objetos da sua aplicação. Estas aulas ajudam a simplificar a serialização e a desseialização dos dados espaciais em GeoJSON.

**Criar Documento com dados Geoespaciais em .NET**

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

Se você não tem a informação de latitude e longitude, mas tem os endereços físicos ou nome de localização como cidade ou país/região, você pode procurar as coordenadas reais usando um serviço de geocoding como Bing Maps REST Services. Saiba mais sobre a geocodagem Bing Maps [aqui.](/bingmaps/rest-services/)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a começar com o suporte geoespacial em Azure Cosmos DB, a seguir pode:

* Saiba mais sobre [a Consulta DB do Azure Cosmos](sql-query-getting-started.md)
* Saiba mais sobre [consulta de dados espaciais com a Azure Cosmos DB](sql-query-geospatial-query.md)
* Saiba mais sobre [os dados espaciais do Índice com a Azure Cosmos DB](sql-query-geospatial-index.md)