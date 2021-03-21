---
title: Introdução às funções geoespaciais Azure Stream Analytics
description: Este artigo descreve funções geoespaciais que são usadas em trabalhos Azure Stream Analytics.
ms.service: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: a5e65ddacfd4c168d4b97816e448951bacc2b534
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438545"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Introdução às funções geoespaciais stream Analytics

As funções geoespaciais no Azure Stream Analytics permitem análises em tempo real no streaming de dados geoespaciais. Com apenas algumas linhas de código, pode desenvolver uma solução de grau de produção para cenários complexos. Estas funções suportam todos os tipos de WKT e GeoJSON Point, Polygon e LineString.

Exemplos de cenários que podem beneficiar de funções geoespaciais incluem:

* Partilha de boleias
* Gestão da frota
* Controlo de recursos
* Geo-esgrima
* Rastreio de telefone através de sites de celular

A Linguagem de Consulta de Análise de Fluxo tem sete funções geoespaciais incorporadas: **CreateLineString,** **CreatePoint,** **CreatePolygon**, **ST_DISTANCE**, **ST_OVERLAPS**, **ST_INTERSECTS** e **ST_WITHIN**.

## <a name="createlinestring"></a>CreateLineString

A `CreateLineString` função aceita pontos e devolve um GeoJSON LineString, que pode ser traçado como uma linha num mapa. Deve ter pelo menos dois pontos para criar um LineString. Os pontos LineString serão ligados por ordem.

A seguinte consulta utiliza `CreateLineString` para criar um LineString utilizando três pontos. O primeiro ponto é criado a partir de dados de entrada de streaming, enquanto os outros dois são criados manualmente.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>Exemplo de entrada  
  
|latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Exemplo de saída  

 {"type" : "LineString", "coordinates": [-10.2, 3.0], [10.0, 10.0], [10.5, 10.5] ]}

 {"type" : "LineString", "coordinates" : [ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5]

Para saber mais, visite a referência [CreateLineString.](/stream-analytics-query/createlinestring)

## <a name="createpoint"></a>CreatePoint

A `CreatePoint` função aceita uma latitude e longitude e devolve um ponto GeoJSON, que pode ser traçado num mapa. Suas latitudes e longitudes devem ser um tipo de dados **flutuantes.**

A consulta de exemplo a seguir `CreatePoint` utiliza-se para criar um ponto utilizando latitudes e longitudes a partir de dados de entrada de streaming.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>Exemplo de entrada  
  
|latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Exemplo de saída
  
 {"type" : "Point", "coordenadas" : [-10.2, 3.0]}  
  
 {"type" : "Point", "coordinate" : [20.2321, -87.33]}  

Para saber mais, visite a referência [CreatePoint.](/stream-analytics-query/createpoint)

## <a name="createpolygon"></a>CreatePolygon

A `CreatePolygon` função aceita pontos e devolve um registo de polígono GeoJSON. A ordem dos pontos deve seguir a orientação do anel da direita ou no sentido contrário ao dos ponteiros do relógio. Imagine mover-se de um ponto para o outro na ordem em que foram declarados. O centro do polígono seria à sua esquerda o tempo todo.

O exemplo seguinte consulta usa `CreatePolygon` para criar um polígono a partir de três pontos. Os dois primeiros pontos são criados manualmente, e o último ponto é criado a partir de dados de entrada.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>Exemplo de entrada  
  
|latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Exemplo de saída  

 {"type" : "Polygon", "coordinates": [-10.2, 3.0], [10.0, 10.0], [10.5, 10.5], [-10.2, 3.0] ]}
 
 {"type" : "Polygon", "coordinates": [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5], [20.2321, -87.33] ]]

Para saber mais, visite a referência [CreatePolygon.](/stream-analytics-query/createpolygon)


## <a name="st_distance"></a>ST_DISTANCE
A `ST_DISTANCE` função devolve a distância entre duas geometrias em metros. 

A seguinte consulta usa `ST_DISTANCE` para gerar um evento quando um posto de gasolina está a menos de 10 km do carro.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

Para saber mais, visite a [referência ST_DISTANCE.](/stream-analytics-query/st-distance)

## <a name="st_overlaps"></a>ST_OVERLAPS
A `ST_OVERLAPS` função compara duas geometrias. Se as geometrias se sobrepõem, a função retorna a 1. A função retorna 0 se as geometrias não se sobreporem. 

A seguinte consulta usa `ST_OVERLAPS` para gerar um evento quando um edifício está dentro de uma possível zona de inundação.

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

O exemplo seguinte gera um evento quando uma tempestade se dirige para um carro.

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

Para saber mais, visite a [referência ST_OVERLAPS.](/stream-analytics-query/st-overlaps)

## <a name="st_intersects"></a>ST_INTERSECTS
A `ST_INTERSECTS` função compara duas geometrias. Se as geometrias se cruzam, então a função retorna 1. A função retorna 0 se as geometrias não se cruzarem.

A seguinte consulta de exemplo utiliza `ST_INTERSECTS` para determinar se uma estrada pavimentada intersecta uma estrada de terra batida.

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>Exemplo de entrada  
  
|datacenterArea|tempestadeArea|  
|--------------------|---------------|  
|{"type":"LineString", "coordinates": [-10.0, 0.0], [0.0, 0.0], [10.0, 0.0] ]}|{"type":"LineString", "coordinates": [ 0.0, 10.0], [0.0, 0.0], [0.0, -10.0] ]}|  
|{"type":"LineString", "coordinates": [-10.0, 0.0], [0.0, 0.0], [10.0, 0.0] ]}|{"type":"LineString", "coordinates": [-10.0, 10.0], [0.0, 10.0], [10.0, 10.0] ]}|  
  
### <a name="output-example"></a>Exemplo de saída  

 1  
  
 0  

Para saber mais, visite a [referência ST_INTERSECTS.](/stream-analytics-query/st-intersects)

## <a name="st_within"></a>ST_WITHIN
A `ST_WITHIN` função determina se uma geometria está dentro de outra geometria. Se a primeira estiver contida na última, a função retornará 1. A função retornará 0 se a primeira geometria não estiver localizada dentro da última.

A seguinte consulta de exemplo utiliza `ST_WITHIN` para determinar se o ponto de destino de entrega está dentro do polígono do armazém dado.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>Exemplo de entrada  
  
|entregaDestinação|armazém|  
|-------------------------|---------------|  
|{"type":"Point", "coordenadas": [76.6, 10.1]}|{"type":"Polygon", "coordinates": [ 0.0, 0.0], [10.0, 0.0], [10.0, 10.0], [0.0, 10.0], [0.0, 0.0] ]}|  
|{"type":"Point", "coordenadas": [15.0, 15.0]}|{"type":"Polygon", "coordinates": [ 10.0, 10.0], [20.0, 10.0], [20.0, 20.0], [10.0, 20.0], [10.0, 10.0] ]]|  
  
### <a name="output-example"></a>Exemplo de saída  

 0  
  
 1  

Para saber mais, visite a [referência ST_WITHIN.](/stream-analytics-query/st-within)

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](/rest/api/streamanalytics/)
