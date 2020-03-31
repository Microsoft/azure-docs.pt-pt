---
title: Geofencing e agregação geoespacial com Azure Stream Analytics
description: Este artigo descreve como usar o Azure Stream Analytics para geofencing e agregação geoespacial.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 5a3aa3786469c3df37b53cb82bdd396871689297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443643"
---
# <a name="geofencing-and-geospatial-aggregation-scenarios-with-azure-stream-analytics"></a>Cenários de geofencing e agregação geoespacial com Azure Stream Analytics

Com funções geoespaciais incorporadas, pode utilizar o Azure Stream Analytics para construir aplicações para cenários como gestão de frotas, partilha de boleias, carros conectados e rastreio de ativos.

## <a name="geofencing"></a>Geofencing

O Azure Stream Analytics suporta computação seletiva em tempo real de baixa latência na nuvem e no tempo de execução do IoT Edge.

### <a name="geofencing-scenario"></a>Cenário de geofencing

Uma empresa de fabrico precisa de rastrear os ativos dos seus edifícios. Equiparam todos os dispositivos com UM GPS e querem receber notificações se um dispositivo deixar uma determinada área.

Os dados de referência utilizados neste exemplo têm a informação geovete para os edifícios e os dispositivos que são permitidos em cada um dos edifícios. Lembre-se que os dados de referência podem ser estáticos ou mudanças lentas. Para este cenário são utilizados dados de referência estáticos. Um fluxo de dados emite continuamente o ID do dispositivo e a sua posição atual.

### <a name="define-geofences-in-reference-data"></a>Definir geovedações em dados de referência

Uma geovedapode ser definida usando um objeto GeoJSON. Para trabalhos com a versão 1.2 e superior da compatibilidade, as geoveses `NVARCHAR(MAX)`também podem ser definidas usando texto bem conhecido (WKT) como . A WKT é um padrão do Consórcio Geoespacial Aberto (OGC) que é usado para representar dados espaciais num formato textual.

As funções geoespaciais incorporadas podem usar geovetes definidas para descobrir se um elemento está dentro ou fora de um polígono geovelado específico.

A tabela a seguir é um exemplo de dados de referência geoveveque que poderiam ser armazenados no armazenamento de blob Azure ou numa tabela Azure SQL. Cada site é representado por um polígono geoespacial, e cada dispositivo está associado a um ID do site permitido.

|SiteID|SiteName|Geofence|AllowedDeviceID|
|------|--------|--------|---------------|
|1|"Redmond Building 41"|"POLÍgoNO (-122.1337357922017 47.6378299832932,-122.133730427778369 47.63733737.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.673.6720222530954,-122.13348902897235 47.6375082806,-122.13361777500506 47.63750828068888880606,-122.13312415030 47.63732393354484,-122.13265754417773 47.63730947490855,-122.13266290859576 47.637519124743164,-122.13302232460376 47.637515510097955,-122.13301696018573 47.63764925180358,-122.13272728161212 47.63764925180358,-122.13274873928424 47.63784082716388,-122.13373579220172 47.63782998329432))"|"B"|
|2|"Redmond Building 40"|"POLÍgoNO (-122.1336154507967 47.636674594477009,-122.133610863786378 940034775,-122.13349743360004 47.63636377773-122.13372810357532 47.63636372927573-122.1333334679333 47.63617576323771,-122.13263912671528 47.63616491902258,-122.13264985555134 47.63635649982525,-122.13304682248554 47.636367344000604,-122.13305218690357 47.63650831807564,-122.13276250832996 47.636497473929516,-122.13277323716602 47.63668543881025,-122.1336154507967 47.6366745947009))"|"A"|
|3|"Redmond Building 22"|"POLÍgono (-122.13611660248233 47.63758544698554,-122.136352636363 3603619712-122.13622389084293 47.63717699101473-122.13581619507266 47.636927777-122.13559625393444 47.637046862778135,-122.13569281345798 47.637144458985965,-122.13570890671207 47.637314348246214,-122.13611660248233 47.63758544698554))"|"C"|

### <a name="generate-alerts-with-geofence"></a>Gerar alertas com geovedação

Os dispositivos podem emitir a sua identificação `DeviceStreamInput`e localização a cada minuto através de um fluxo chamado . A tabela a seguir é um fluxo de entrada.

|DispositivoID|Geoposição|
|--------|-----------|
|"A"|"PONTO (-122.13292341559497 47.636318374032726)"|
|"B"|"PONTO (-122.133847555553 47.63743331308874)"|
|"C"|"PONTO (-122.13354001095752 47.636276225050500)"|

Pode escrever uma consulta que une o fluxo do dispositivo com os dados de referência geoveda e gera um alerta sempre que um dispositivo está fora de um edifício permitido.

```SQL
SELECT DeviceStreamInput.DeviceID, SiteReferenceInput.SiteID, SiteReferenceInput.SiteName 
INTO Output
FROM DeviceStreamInput 
JOIN SiteReferenceInput
ON st_within(DeviceStreamInput.GeoPosition, SiteReferenceInput.Geofence) = 0
WHERE DeviceStreamInput.DeviceID = SiteReferenceInput.AllowedDeviceID
```

A imagem seguinte representa as geoveses. Pode ver onde os dispositivos estão de acordo com a entrada de dados de fluxo.

![Geoves de construção](./media/geospatial-scenarios/building-geofences.png)

O dispositivo "C" está localizado dentro do edifício ID 2, o que não é permitido de acordo com os dados de referência. Este dispositivo deve estar localizado dentro do edifício ID 3. Gerir este trabalho vai gerar um alerta para esta violação específica.

### <a name="site-with-multiple-allowed-devices"></a>Site com vários dispositivos permitidos

Se um site permitir vários dispositivos, um conjunto `AllowedDeviceID` de IDs do dispositivo pode `WHERE` ser definido e uma Função Definida pelo Utilizador pode ser usada na cláusula para verificar se o ID do dispositivo de fluxo corresponde a qualquer ID do dispositivo nessa lista. Para mais informações, consulte o tutorial da [Javascript UDF](stream-analytics-javascript-user-defined-functions.md) para trabalhos em nuvem e o tutorial [c# UDF](stream-analytics-edge-csharp-udf.md) para trabalhos de borda.

## <a name="geospatial-aggregation"></a>Agregação geoespacial

O Azure Stream Analytics suporta a agregação geoespacial em tempo real de baixa latência na nuvem e no tempo de execução do IoT Edge.

### <a name="geospatial-aggregation-scenario"></a>Cenário de agregação geoespacial

Uma empresa de táxis quer construir uma aplicação em tempo real para orientar os seus taxistas à procura de boleia para as áreas das cidades que atualmente sofrem uma maior procura.

A empresa armazena regiões lógicas da cidade como dados de referência. Cada região é definida por um RegionalID, RegionName e Geofence.

### <a name="define-the-geofences"></a>Defina as geovedações

A tabela a seguir é um exemplo de dados de referência geoveveque que poderiam ser armazenados no armazenamento de blob Azure ou numa tabela Azure SQL. Todas as regiões são representadas por um polígono geoespacial, que é usado para se relacionar com os pedidos provenientes de dados de streaming.

Estes polígonos são apenas para referência e não representam separações lógicas ou físicas reais da cidade.

|Id da região|Nome da Região|Geofence|
|--------|----------|--------|
|1|"SoHo"|"POLYGON(-74.00279525078275 40.72833625216264,-74.0054774597 99765 40.721929158663244,-74.00125029839018 40.71893680218 9994,-73.9957785919998 40.72521409075776-73.997237137039 40.7255718458888,-74.00279525078275 40.72833625216264) )"|
|2|"Chinatown"|"POLÍgono(-73.99712367114876 40.7128158267133,-73.9901070123658 40.71336881907936,-73.99023558398 35908633,-73.98976368961189 40.71554823078944,-73.9955514573982 40.7173372468335,739806242559999 40.718491949759304,-73.99652285632942 40.719109951574,-73.99776740131233 40.7168005470334,-73.99903340396736 40.71727219249899,-74.00193018970344 40.71938642421256,-74.00409741458748 40.71688186545551,-74.00051398334358 40.71517415773184,-74.0004281526551 40.714377212470005,-73.99849696216438 40.713450141693166,-73.99748845157478 40.71405192594819,-73.99712367114876 40.71281582267133))"|
|3|"Tribeca"|"POLYGON(-74.01091641815208 40.7258312006787,-74.01338405044578 40.71436586362705-74.0137059155555557577 40.71366 177702123415,-74.0086204472353 40.711308107057235,-74.00194711120628 40.7194238654018,-74.01091641414444444444418,-74.0109164141416418,-74.010916411111111111120628 40.72583120006787))"|

### <a name="aggregate-data-over-a-window-of-time"></a>Dados agregados ao longo de uma janela de tempo

A tabela seguinte contém dados de streaming de "passeios".

|UserID|A partir da localização|Localização de Torção|TripRequestedTime|
|------|------------|----------|-----------------|
|"A"|"PONTO (-74.00726861389182 40.71610611981975)"|"PONTO (-73.98615095917779 40.703107386025835)"|"2019-03-12t07:00:00Z"|
|"B"|"PONTO (-74.00249841021645 40.723827238895666)"|"PONTO (-74.01160699942085 40.71378884930115)"|"2019-03-12t07:01:00Z"|
|"C"|"PONTO (-73.99680120565864 40.7164398624024)"|"PONTO (-73.98289663412544 40.72582343969828)"|"2019-03-12t07:02:00Z"|
|"D"|"PONTO (-74.00741090682888 40.71615626755086)"|"PONTO (-73.97999843120539 40.73477895807408)"|"2019-03-12t07:03:00Z"|

A seguinte consulta junta-se ao fluxo do dispositivo com os dados de referência geoveveee e calcula o número de pedidos por região numa janela temporal de 15 minutos a cada minuto.

```SQL
SELECT count(*) as NumberOfRequests, RegionsRefDataInput.RegionName 
FROM UserRequestStreamDataInput
JOIN RegionsRefDataInput 
ON st_within(UserRequestStreamDataInput.FromLocation, RegionsRefDataInput.Geofence) = 1
GROUP BY RegionsRefDataInput.RegionName, hoppingwindow(minute, 1, 15)
```

Esta consulta produz uma contagem de pedidos a cada minuto durante os últimos 15 minutos por cada região dentro da cidade. Estas informações podem ser exibidas facilmente pelo painel power bi, ou podem ser transmitidas a todos os controladores como mensagens de texto SMS através da integração com serviços como funções Azure.

A imagem abaixo ilustra a saída da consulta ao painel power BI. 

![Saída de resultados no painel power bi](./media/geospatial-scenarios/power-bi-output.png)


## <a name="next-steps"></a>Passos seguintes

* [Introdução às funções geoespaciais Stream Analytics](stream-analytics-geospatial-functions.md)
* [Funções GeoEspaciais (Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/geospatial-functions)
