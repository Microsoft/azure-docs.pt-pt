---
title: Introdução às funções geoespaciais Azure Stream Analytics
description: Este artigo descreve as funções geoespaciais que são usadas em Azure Stream Analytics trabalhos.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: dfbe7e607395006f9bd7da0be0d5673353e2801f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162604"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Introdução às funções geoespaciais Stream Analytics

As funções geoespaciais no Azure Stream Analytics habilitam a análise em tempo real em dados geoespaciais de streaming. Com apenas algumas linhas de código, você pode desenvolver uma solução de nível de produção para cenários complexos. 

Exemplos de cenários que podem se beneficiar das funções geoespaciais incluem:

* Compartilhamento de Rides
* Gestão de frotas
* Controlo de recursos
* Isolamento geográfico
* Rastreamento de telefone em sites de células

Stream Analytics linguagem de consulta tem sete funções geoespaciais internas: **Createlinestring**, **createpoint**, **createpolygon**, **ST_DISTANCE**, **ST_OVERLAPS**, **ST_INTERSECTS**e **ST_WITHIN**.

## <a name="createlinestring"></a>CreateLineString

A função `CreateLineString` aceita pontos e retorna um geojson Linhastring, que pode ser plotado como uma linha em um mapa. Você deve ter pelo menos dois pontos para criar uma LineString. Os pontos de LineString serão conectados em ordem.

A consulta a seguir usa `CreateLineString` para criar uma LineString usando três pontos. O primeiro ponto é criado a partir do fluxo de dados de entrada, enquanto os outros dois são criados manualmente.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>Exemplo de entrada  
  
|Latitude|Longitude|  
|--------------|---------------|  
|3.0|-10,2|  
|-87,33|20,2321|  
  
### <a name="output-example"></a>Exemplo de saída  

 {"tipo": "LineString", "coordenadas": [[-10,2, 3,0], [10,0, 10,0], [10,5, 10,5]]}

 {"tipo": "LineString", "coordenadas": [[20,2321,-87,33], [10,0, 10,0], [10,5, 10,5]]}

Para saber mais, visite a referência de [Createlinestring](https://docs.microsoft.com/stream-analytics-query/createlinestring) .

## <a name="createpoint"></a>CreatePoint

A função `CreatePoint` aceita uma latitude e longitude e retorna um ponto geojson, que pode ser plotado em um mapa. Suas latitudes e longitudes devem ser um tipo de dados **float** .

A consulta de exemplo a seguir usa `CreatePoint` para criar um ponto usando latitudes e longitudes do streaming de dados de entrada.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>Exemplo de entrada  
  
|Latitude|Longitude|  
|--------------|---------------|  
|3.0|-10,2|  
|-87,33|20,2321|  
  
### <a name="output-example"></a>Exemplo de saída
  
 {"Type": "ponto", "coordenadas": [-10,2, 3,0]}  
  
 {"Type": "ponto", "coordenadas": [20,2321,-87,33]}  

Para saber mais, visite a referência de [createpoint](https://docs.microsoft.com/stream-analytics-query/createpoint) .

## <a name="createpolygon"></a>CreatePolygon

A função `CreatePolygon` aceita pontos e retorna um registro de polígono geojson. A ordem dos pontos deve seguir a orientação do anel direito, ou no sentido anti-horário. Imagine a movimentação de um ponto para outro na ordem em que eles foram declarados. O centro do polígono estaria à sua esquerda em todo o tempo.

A consulta de exemplo a seguir usa `CreatePolygon` para criar um polígono a partir de três pontos. Os dois primeiros pontos são criados manualmente e o último é criado a partir dos dados de entrada.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>Exemplo de entrada  
  
|Latitude|Longitude|  
|--------------|---------------|  
|3.0|-10,2|  
|-87,33|20,2321|  
  
### <a name="output-example"></a>Exemplo de saída  

 {"tipo": "Polygon", "coordenadas": [[[-10,2, 3,0], [10,0, 10,0], [10,5, 10,5], [-10,2, 3,0]]]}
 
 {"tipo": "Polygon", "coordenadas": [[[20,2321,-87,33], [10,0, 10,0], [10,5, 10,5], [20,2321,-87,33]]]}

Para saber mais, visite a referência de [createpolygon](https://docs.microsoft.com/stream-analytics-query/createpolygon) .


## <a name="st_distance"></a>ST_DISTANCE
A função `ST_DISTANCE` retorna a distância entre dois pontos em metros. 

A consulta a seguir usa `ST_DISTANCE` para gerar um evento quando uma estação de gás tem menos de 10 km do carro.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

Para saber mais, visite a referência do [ST_DISTANCE](https://docs.microsoft.com/stream-analytics-query/st-distance) .

## <a name="st_overlaps"></a>ST_OVERLAPS
A função `ST_OVERLAPS` compara dois polígonos. Se os polígonos se sobrepõem, a função retorna um 1. A função retornará 0 se os polígonos não se sobrepõem. 

A consulta a seguir usa `ST_OVERLAPS` para gerar um evento quando um edifício está dentro de uma possível zona de inundação.

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

A consulta de exemplo a seguir gera um evento quando um Storm é rumo em direção a um carro.

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

Para saber mais, visite a referência do [ST_OVERLAPS](https://docs.microsoft.com/stream-analytics-query/st-overlaps) .

## <a name="st_intersects"></a>ST_INTERSECTS
A função `ST_INTERSECTS` compara dois LineString. Se a LineString se interceptar, a função retornará 1. A função retornará 0 se o LineString não se Interseccionar.

A consulta de exemplo a seguir usa `ST_INTERSECTS` para determinar se uma estrada paved cruza uma estrada de sujeira.

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>Exemplo de entrada  
  
|datacenterArea|stormArea|  
|--------------------|---------------|  
|{"tipo": "LineString", "coordenadas": [[-10,0, 0,0], [0,0, 0,0], [10,0, 0,0]]}|{"tipo": "LineString", "coordenadas": [[0,0, 10,0], [0,0, 0,0], [0,0,-10,0]]}|  
|{"tipo": "LineString", "coordenadas": [[-10,0, 0,0], [0,0, 0,0], [10,0, 0,0]]}|{"tipo": "LineString", "coordenadas": [[-10,0, 10,0], [0,0, 10,0], [10,0, 10,0]]}|  
  
### <a name="output-example"></a>Exemplo de saída  

 1  
  
 0  

Para saber mais, visite a referência do [ST_INTERSECTS](https://docs.microsoft.com/stream-analytics-query/st-intersects) .

## <a name="st_within"></a>ST_WITHIN
A função `ST_WITHIN` determina se um ponto ou polígono está dentro de um polígono. Se o polígono contiver o ponto ou polígono, a função retornará 1. A função retornará 0 se o ponto ou polígono não estiver localizado dentro do polígono declarado.

A consulta de exemplo a seguir usa `ST_WITHIN` para determinar se o ponto de destino de entrega está dentro do polígono do depósito fornecido.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>Exemplo de entrada  
  
|deliveryDestination|armazén|  
|-------------------------|---------------|  
|{"Type": "ponto", "coordenadas": [76,6, 10,1]}|{"tipo": "Polygon", "coordenadas": [[0,0, 0,0], [10,0, 0,0], [10,0, 10,0], [0,0, 10,0], [0,0, 0,0]]}|  
|{"Type": "ponto", "coordenadas": [15,0, 15,0]}|{"tipo": "Polygon", "coordenadas": [[10,0, 10,0], [20,0, 10,0], [20,0, 20,0], [10,0, 20,0], [10,0, 10,0]]}|  
  
### <a name="output-example"></a>Exemplo de saída  

 0  
  
 1  

Para saber mais, visite a referência do [ST_WITHIN](https://docs.microsoft.com/stream-analytics-query/st-within) .

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)