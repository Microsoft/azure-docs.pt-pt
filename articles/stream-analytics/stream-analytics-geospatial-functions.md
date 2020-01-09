---
title: Introdução às funções geoespaciais de Azure Stream Analytics
description: Este artigo descreve as funções geoespaciais que são usadas em tarefas do Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: f47f34b60c858bb9a0feafd25176e4a811046630
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426230"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Introdução às funções geoespaciais de Stream Analytics

Funções Geoespaciais no Azure Stream Analytics ativar análise em tempo real em dados geoespaciais de transmissão em fluxo. Com apenas algumas linhas de código, pode desenvolver uma solução de nível de produção para cenários complexos. 

Exemplos de cenários que podem se beneficiar funções geoespaciais incluem:

* Jornada de partilha
* Gestão de frotas
* Controlo de recursos
* Perímetro geográfico
* Controlo de telefone em todos os sites de célula

Linguagem de consulta do Stream Analytics tem sete funções geoespaciais incorporadas: **CreateLineString**, **Createpoint(0.0**, **CreatePolygon**, **ST_DISTANCE** , **ST_OVERLAPS**, **ST_INTERSECTS**, e **ST_WITHIN**.

## <a name="createlinestring"></a>CreateLineString

O `CreateLineString` função aceita pontos e retorna um GeoJSON LineString, que podem ser representados como uma linha num mapa. Tem de ter, pelo menos, dois pontos para criar uma LineString. Os pontos de LineString serão ligados por ordem.

A seguinte consulta utiliza `CreateLineString` para criar uma LineString usando três pontos. O primeiro ponto é criado a partir de transmissão em fluxo de dados de entrada, enquanto as outras duas são criadas manualmente.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>Exemplo de entrada  
  
|Latitude|Longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Exemplo de saída  

 {"type": "LineString", "coordenadas": [[-10.2, 3.0], [10.0, 10.0], [10.5, 10.5]]}

 {"type": "LineString", "coordenadas": [[20.2321,-87.33], [10.0, 10.0], [10.5, 10.5]]}

Para saber mais, visite o [CreateLineString](https://docs.microsoft.com/stream-analytics-query/createlinestring) referência.

## <a name="createpoint"></a>CreatePoint

O `CreatePoint` função aceita uma latitude e longitude e retorna um ponto de GeoJSON, que pode ser representado num mapa. As latitudes e longitudes tem de ser um **float** tipo de dados.

A consulta de exemplo seguinte utiliza `CreatePoint` para criar um ponto com latitudes e longitudes da transmissão em fluxo de dados de entrada.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>Exemplo de entrada  
  
|Latitude|Longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Exemplo de saída
  
 {"type": "Point", "coordenadas": [-10.2, 3.0]}  
  
 {"type": "Point", "coordenadas": [20.2321,-87.33]}  

Para saber mais, visite o [Createpoint(0.0](https://docs.microsoft.com/stream-analytics-query/createpoint) referência.

## <a name="createpolygon"></a>CreatePolygon

O `CreatePolygon` função aceita pontos e devolve um registo de polígono de GeoJSON. A ordem dos pontos têm de seguir a orientação de anel da direita, ou anti-horário. Imagine a movimentação de um ponto para outro na ordem em que foram declarados. O centro do polígono seria, à esquerda, o tempo todo.

A consulta de exemplo seguinte utiliza `CreatePolygon` para criar um polígono de três pontos. Os dois primeiros pontos são criados manualmente e o último ponto é criado a partir de dados de entrada.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>Exemplo de entrada  
  
|Latitude|Longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Exemplo de saída  

 {"type": "Polígono", "coordenadas": [[[-10.2, 3.0], [10.0, 10.0], [10.5, 10.5], [-10.2, 3.0]]]}
 
 {"type": "Polígono", "coordenadas": [[[20.2321,-87.33], [10.0, 10.0], [10.5, 10.5], [20.2321,-87.33]]]}

Para saber mais, visite o [CreatePolygon](https://docs.microsoft.com/stream-analytics-query/createpolygon) referência.


## <a name="st_distance"></a>ST_DISTANCE
O `ST_DISTANCE` função retorna a distância entre dois pontos em metros. 

A seguinte consulta utiliza `ST_DISTANCE` para gerar um evento quando uma bomba de gasolina é inferior a 10 km de distância do carro.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

Para saber mais, visite o [ST_DISTANCE](https://docs.microsoft.com/stream-analytics-query/st-distance) referência.

## <a name="st_overlaps"></a>ST_OVERLAPS
O `ST_OVERLAPS` função compara dois polígonos. Se os polígonos se sobrepuserem, a função devolve um 1. A função devolve 0 se os polígonos não se sobrepõem. 

A seguinte consulta utiliza `ST_OVERLAPS` para gerar um evento quando um edifício é dentro de uma possível inundação de zona.

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

A seguinte consulta de exemplo gera um evento quando um storm é do cabeçalho para um carro.

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

Para saber mais, visite o [ST_OVERLAPS](https://docs.microsoft.com/stream-analytics-query/st-overlaps) referência.

## <a name="st_intersects"></a>ST_INTERSECTS
O `ST_INTERSECTS` função compara dois LineString. Se o LineString intersect, em seguida, a função devolve 1. A função devolve 0 se o LineString não intersect.

A consulta de exemplo seguinte utiliza `ST_INTERSECTS` para determinar se um caminho paved intersetar-um caminho de uma valiosa descoberta.

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

Para saber mais, visite o [ST_INTERSECTS](https://docs.microsoft.com/stream-analytics-query/st-intersects) referência.

## <a name="st_within"></a>ST_WITHIN
O `ST_WITHIN` função determina se um ponto ou polígonos dentro de um polígono. Se o polígono contém o ponto ou polígonos, a função retornará 1. A função retornará 0 se o ponto ou polígono não estiver localizado em polígono declarado.

A consulta de exemplo seguinte utiliza `ST_WITHIN` para determinar se o ponto de entrega de destino está dentro do polígono armazém determinado.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>Exemplo de entrada  
  
|deliveryDestination|Armazém|  
|-------------------------|---------------|  
|{"Type": "ponto", "coordenadas": [76,6, 10,1]}|{"tipo": "Polygon", "coordenadas": [[0,0, 0,0], [10,0, 0,0], [10,0, 10,0], [0,0, 10,0], [0,0, 0,0]]}|  
|{"Type": "ponto", "coordenadas": [15,0, 15,0]}|{"tipo": "Polygon", "coordenadas": [[10,0, 10,0], [20,0, 10,0], [20,0, 20,0], [10,0, 20,0], [10,0, 10,0]]}|  
  
### <a name="output-example"></a>Exemplo de saída  

 0  
  
 1  

Para saber mais, visite o [ST_WITHIN](https://docs.microsoft.com/stream-analytics-query/st-within) referência.

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência do idioma de consulta do Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de API do REST de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
