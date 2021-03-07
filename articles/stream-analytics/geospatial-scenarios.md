---
title: Geofencing e agregação geoespacial com Azure Stream Analytics
description: Este artigo descreve como usar o Azure Stream Analytics para geofencing e agregação geoespacial.
ms.service: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.topic: how-to
ms.date: 04/02/2019
ms.openlocfilehash: 2576e147f0cda86bc4d7143ccc5b5985182f271e
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102436350"
---
# <a name="geofencing-and-geospatial-aggregation-scenarios-with-azure-stream-analytics"></a>Cenários de geofencing e agregação geoespacial com Azure Stream Analytics

Com funções geoespaciais incorporadas, pode utilizar o Azure Stream Analytics para construir aplicações para cenários como gestão de frotas, partilha de boleias, carros conectados e rastreio de ativos.

## <a name="geofencing"></a>Geofencing

O Azure Stream Analytics suporta computaçãos geofencing em tempo real de baixa latência na nuvem e no tempo de execução IoT Edge.

### <a name="geofencing-scenario"></a>Cenário de geofencing

Uma empresa de fabrico precisa de rastrear os ativos dos seus edifícios. Eles equiparam todos os dispositivos com um GPS e querem receber notificações se um dispositivo deixar uma determinada área.

Os dados de referência utilizados neste exemplo têm a informação de geofência para os edifícios e os dispositivos que são permitidos em cada um dos edifícios. Lembre-se que os dados de referência podem ser estáticos ou mudar lentamente. Os dados de referência estática são utilizados para este cenário. Um fluxo de dados emite continuamente o ID do dispositivo e a sua posição atual.

### <a name="define-geofences-in-reference-data"></a>Definir geofences em dados de referência

Uma geofence pode ser definida usando um objeto GeoJSON. Para trabalhos com compatibilidade versão 1.2 e superior, as geofências também podem ser definidas usando texto bem conhecido (WKT) como `NVARCHAR(MAX)` . WKT é um padrão open Geospatial Consortium (OGC) que é usado para representar dados espaciais em formato textual.

As funções geoespaciais incorporadas podem usar geofências definidas para descobrir se um elemento está dentro ou fora de um polígono geofence específico.

O quadro a seguir é um exemplo de dados de referência de geofência que podem ser armazenados no armazenamento de blob Azure ou numa tabela Azure SQL. Cada site é representado por um polígono geoespacial, e cada dispositivo está associado a um ID de site permitido.

|SiteID|SiteName|Geofência|PermitidoDeviceID|
|------|--------|--------|---------------|
|1|"Edifício Redmond 41"|"POLYGON((-122.1337357922017 47.63782998329432,-122.13373042778369 47.637634793257305,-122.13346757130023 47.637642022530954,-122.13348902897235 47.637508280806806,-122.13361777500506 47.637508280806806,-122.13361241058703 47.63732393354484,-122.13265754417773 47.63730947490855,-122.13266290859576 47.637519124743164,-122.13302232460376 47.637515510097955,-122.13301696018573 47.63764925180358,-122.13272728161212 47.63764925180358,-122.13274873928424 47.63784082716388,-122.13373579220172 47.63782998329432))"|"B"|
|2|"Edifício Redmond 40"|"POLYGON((-122.1336154507967 47.6366745947009,-122.13361008637867 47.636483015064535,-122.13349206918201 47.636479400347675,-122.13349743360004 47.63636372927573,-122.13372810357532 47.63636372927573,-122.13373346799335 47.63617576323771,-122.13263912671528 47.63616491902258,-122.13264985555134 47.63635649982525,-122.13304682248554 47.636367344000604,-122.13305218690357 47.63650831807564,-122.13276250832996 47.636497473929516,-122.13277323716602 47.63668543881025,-122.1336154507967 47.6366745947009))"|"A"|
|3|"Edifício Redmond 22"|"POLYGON((-122.13611660248233 47.637585444698554,-122.13635263687564 47.6374083293018-122.13622389084293 47.63733603619712,-122.13622389084293 47.63717699101473,-122.13581195072666 647.63692757827657,-122.13559625333444 47.637046862778135,-122.135692813458 47.637144458985965,-122.13570890671207 47.637314348246214,-122.13611660248233 47.63758544698554)".|"C"|

### <a name="generate-alerts-with-geofence"></a>Gerar alertas com geofence

Os dispositivos podem emitir o seu ID e localização a cada minuto através de um fluxo chamado `DeviceStreamInput` . A tabela a seguir é um fluxo de entrada.

|DeviceID|GeoPosição|
|--------|-----------|
|"A"|"PONTO(-122.13292341559497 47.636318374032726)"|
|"B"|"PONTO(-122.133384755554553 47.63743531308874)"|
|"C"|"PONTO(-122.13354001095752 47.63627622505007)"|

Pode escrever uma consulta que une o fluxo do dispositivo com os dados de referência de geofência e gera um alerta sempre que um dispositivo está fora de um edifício permitido.

```SQL
SELECT DeviceStreamInput.DeviceID, SiteReferenceInput.SiteID, SiteReferenceInput.SiteName 
INTO Output
FROM DeviceStreamInput 
JOIN SiteReferenceInput
ON st_within(DeviceStreamInput.GeoPosition, SiteReferenceInput.Geofence) = 0
WHERE DeviceStreamInput.DeviceID = SiteReferenceInput.AllowedDeviceID
```

A imagem a seguir representa as geo-posições. Pode ver onde os dispositivos estão de acordo com a entrada de dados de fluxo.

![Geofências de construção](./media/geospatial-scenarios/building-geofences.png)

O dispositivo "C" está localizado dentro do ID 2 do edifício, o que não é permitido de acordo com os dados de referência. Este dispositivo deve estar localizado no interior do ID 3 do edifício. Executar este trabalho gerará um alerta para esta violação específica.

### <a name="site-with-multiple-allowed-devices"></a>Site com vários dispositivos permitidos

Se um site permitir vários dispositivos, uma série de IDs do dispositivo pode ser definida `AllowedDeviceID` e uma função User-Defined pode ser usada na `WHERE` cláusula para verificar se o ID do dispositivo de fluxo corresponde a qualquer ID do dispositivo nessa lista. Para mais informações, consulte o tutorial [do Javascript UDF](stream-analytics-javascript-user-defined-functions.md) para trabalhos em nuvem e o tutorial [C# UDF](stream-analytics-edge-csharp-udf.md) para trabalhos de borda.

## <a name="geospatial-aggregation"></a>Agregação geoespacial

O Azure Stream Analytics suporta baixa latência em tempo real agregação geoespacial na nuvem e no tempo de execução IoT Edge.

### <a name="geospatial-aggregation-scenario"></a>Cenário de agregação geoespacial

Uma empresa de táxis quer construir uma aplicação em tempo real para guiar os seus taxistas à procura de boleia para as zonas das cidades que atualmente sofrem de maior procura.

A empresa armazena regiões lógicas da cidade como dados de referência. Cada região é definida por um RegionalID, RegionalName e Geofence.

### <a name="define-the-geofences"></a>Definir as geo-posições

O quadro a seguir é um exemplo de dados de referência de geofência que podem ser armazenados no armazenamento de blob Azure ou numa tabela Azure SQL. Todas as regiões são representadas por um polígono geoespacial, que é usado para se correlacionar com os pedidos provenientes de dados de streaming.

Estes polígonos são apenas para referência e não representam separações lógicas ou físicas reais da cidade.

|RegionID|Nome regional|Geofência|
|--------|----------|--------|
|1|"SoHo"|"POLYGON((-74.00279525078275 40.72833625216264--74.00547459797765 40.72192915863244,-74.00125029839018 40.71893680218994,-73.9957785919998 40.72521409075776,-73.9972377137039 40.72557184584898,-74.00279525078275 40.72833625216264)".|
|2|"Chinatown"|"POLYGON((-73.99712367114876 40.71281582267133,-73.9901070123658 40.71336881907936,-73.99023575839851 40.71452359088633,-73.98976368961189 40.71554823078944,-73.99551434573982 40.717337246783735,-73.99480624255989 40.718491949759304,-73.99652285632942 40.719109951574,-73.99776740131233 40.7168005470334,-73.99903340396736 40.71727219249899,-74.00193018970344 40.71938642421256,-74.00409741458748 40.71688186545551,-74.00051398334358 40.71517415773184,-74.0004281526551 40.714377212470005,-73.99849696216438 40.713450141693166,-73.99748845157478 40.71405192594819,-73.99712367114876 40.71281582267133))"|
|3|"Tribeca"|"POLYGON((-74.01091641815208 40.72583120006787-74.01338405044578 40.71436586362705,-74.013705915527577 40.7136177702123415,-74.00862044723333 40.711308107057235,-74.00194711120628 40.7194238654018,-74.01091641815208 40.72583120006787)".|

### <a name="aggregate-data-over-a-window-of-time"></a>Dados agregados ao longo de uma janela de tempo

A tabela seguinte contém dados de streaming de "rides".

|UserID|FromLocation|ToLocação|TripRequestedTime|
|------|------------|----------|-----------------|
|"A"|"POINT(-74.00726861389182 40.71610611981975)"|"PONTO(-73.98615095917779 40.703107386025835)"|"2019-03-12T07:00:00Z"|
|"B"|"POINT(-74.00249841021645 40.7238272388956666)"|"POINT(-74.01160699942085 40.71378884930115)"|"2019-03-12T07:01:00Z"|
|"C"|"PONTO(-73.99680120565864 40.716439898624024)"|"PONTO(-73.98289663412544 40.72582343969828)"|"2019-03-12T07:02:00Z"|
|"D"|"PONTO(-74.00741090068288 40.71615626755086)"|"PONTO(-73.97999843120539 40.73477895807408)"|"2019-03-12T07:03:00Z"|

A seguinte consulta junta o fluxo do dispositivo com os dados de referência de geofência e calcula o número de pedidos por região num intervalo de tempo de 15 minutos a cada minuto.

```SQL
SELECT count(*) as NumberOfRequests, RegionsRefDataInput.RegionName 
FROM UserRequestStreamDataInput
JOIN RegionsRefDataInput 
ON st_within(UserRequestStreamDataInput.FromLocation, RegionsRefDataInput.Geofence) = 1
GROUP BY RegionsRefDataInput.RegionName, hoppingwindow(minute, 1, 15)
```

Esta consulta produz uma contagem de pedidos a cada minuto para os últimos 15 minutos por cada região dentro da cidade. Estas informações podem ser exibidas facilmente pelo painel Power BI, ou podem ser transmitidas a todos os controladores como mensagens de texto SMS através da integração com serviços como funções Azure.

A imagem abaixo ilustra a saída da consulta para o painel Power BI. 

![Saída de resultado no painel Power BI](./media/geospatial-scenarios/power-bi-output.png)


## <a name="next-steps"></a>Passos seguintes

* [Introdução às funções geoespaciais stream Analytics](stream-analytics-geospatial-functions.md)
* [Funções GeoSpatiais (Azure Stream Analytics)](/stream-analytics-query/geospatial-functions)