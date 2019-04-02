---
title: Cenários de agregação de barreira geográfica e geoespaciais com o Azure Stream Analytics
description: Este artigo descreve como utilizar o Azure Stream Analytics para agregação de barreira geográfica e geoespaciais.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: cc301855e4cdcb8eb687e753835577399cfe72b0
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58807173"
---
# <a name="geofencing-and-geospatial-aggregation-scenarios-with-azure-stream-analytics"></a>Cenários de agregação de barreira geográfica e geoespaciais com o Azure Stream Analytics

Com funções geoespaciais incorporadas, pode utilizar o Azure Stream Analytics para criar aplicativos para cenários como a gestão de frotas e é garantida de partilha, veículos e acompanhamento de recursos.

## <a name="geofencing"></a>Perímetro geográfico

O Azure Stream Analytics suporta os cálculos de barreira geográfica em tempo real de baixa latência na nuvem e o tempo de execução do IoT Edge.

### <a name="geofencing-scenario"></a>Cenário de barreira geográfica

Precisa de uma empresa industrial controlar ativos em seus edifícios. Eles equipado com todos os dispositivos com um GPS e pretendem receber notificações, se um dispositivo deixa uma determinada área.

Dados de referência utilizados neste exemplo tem as informações do perímetro geográfico para os prédios e os dispositivos que são permitidos em cada um dos prédios. Lembre-se de que os dados de referência podem ser lenta ou estático a alteração. Dados de referência estáticos são utilizados para este cenário. Um fluxo de dados continuamente emite o ID de dispositivo e sua posição atual.

### <a name="define-geofences-in-reference-data"></a>Definir perímetros geográficos em dados de referência

Um perímetro geográfico pode ser definido usando um objeto GeoJSON. Para as tarefas com a versão de compatibilidade 1.2 e superior, perímetros geográficos também podem ser definidos usando o texto de Well Known (well-) como `NVARCHAR(MAX)`. WKT é um aberto Geoespacial Consortium (OGC) padrão que é utilizada para representar os dados geográficos num formato de texto.

As funções geoespaciais incorporadas podem utilizar os perímetros geográficos definidos para descobrir se um elemento será dentro ou fora de um polígono do perímetro geográfico específico.

A tabela seguinte é um exemplo de dados de referência do perímetro geográfico que poderiam ser armazenados no armazenamento de Blobs do Azure ou uma tabela SQL do Azure. Cada site é representado por um polígono geoespaciais, e cada dispositivo está associado um ID de site permitido.

|SiteID|SiteName|Geofence|AllowedDeviceID|
|------|--------|--------|---------------|
|1|"Redmond Building 41"|"POLÍGONO ((-122.1337357922017 47.63782998329432,-122.13373042778369 47.637634793257305,-122.13346757130023 47.637642022530954,-122.13348902897235 47.637508280806806,-122.13361777500506 47.637508280806806,-122.13361241058703 47.63732393354484,-122.13265754417773 47.63730947490855,-122.13266290859576 47.637519124743164,-122.13302232460376 47.637515510097955,-122.13301696018573 47.63764925180358,-122.13272728161212 47.63764925180358,-122.13274873928424 47.63784082716388,-122.13373579220172 47.63782998329432)) "|"B"|
|2|"Redmond Building 40"|"POLÍGONO ((-122.1336154507967 47.6366745947009,-122.13361008637867 47.636483015064535,-122.13349206918201 47.636479400347675,-122.13349743360004 47.63636372927573,-122.13372810357532 47.63636372927573,-122.13373346799335 47.63617576323771,-122.13263912671528 47.63616491902258,-122.13264985555134 47.63635649982525,-122.13304682248554 47.636367344000604,-122.13305218690357 47.63650831807564,-122.13276250832996 47.636497473929516,-122.13277323716602 47.63668543881025,-122.1336154507967 47.6366745947009)) "|"A"|
|3|"Building 22 de Redmond"|"POLÍGONO ((-122.13611660248233 47.63758544698554,-122.13635263687564 47.6374083293018,-122.13622389084293 47.63733603619712,-122.13622389084293 47.63717699101473,-122.13581619507266 47.63692757827657,-122.13559625393344 47.637046862778135,-122.13569281345798 47.637144458985965,-122.13570890671207 47.637314348246214,-122.13611660248233 47.63758544698554)) "|"C"|

### <a name="generate-alerts-with-geofence"></a>Gere alertas com o perímetro geográfico

Dispositivos podem emitir o ID e a localização de cada minuto por meio de um fluxo chamado `DeviceStreamInput`. A tabela seguinte é um fluxo de entrada.

|DeviceID|GeoPosition|
|--------|-----------|
|"A"|"POINT(-122.13292341559497 47.636318374032726)"|
|"B"|"POINT(-122.13338475554553 47.63743531308874)"|
|"C"|"POINT(-122.13354001095752 47.63627622505007)"|

Pode escrever uma consulta que associa o fluxo de dispositivo com os dados de referência do perímetro geográfico e gera um alerta sempre que um dispositivo não está incluído um edifício permitido.

```SQL
SELECT DeviceStreamInput.DeviceID, SiteReferenceInput.SiteID, SiteReferenceInput.SiteName 
INTO Output
FROM DeviceStreamInput 
JOIN SiteReferenceInput
ON st_within(DeviceStreamInput.GeoPosition, SiteReferenceInput.Geofence) = 0
WHERE DeviceStreamInput.DeviceID = SiteReferenceInput.AllowedDeviceID
```

A imagem seguinte representa os perímetros geográficos. Pode ver em que os dispositivos estejam de acordo com os dados de transmissão de entrada.

![Perímetros geográficos de criação](./media/geospatial-scenarios/building-geofences.png)

O dispositivo "C" localizado dentro de 2 de ID, que não é permitido, de acordo com os dados de referência de criação. Este dispositivo deve estar localizado dentro de 3 de ID de criação. Executar esta tarefa irá gerar um alerta para este violação específico.

### <a name="site-with-multiple-allowed-devices"></a>Site com vários dispositivos permitidos

Se um site permite que vários dispositivos, uma matriz de IDs de dispositivo pode ser definida em `AllowedDeviceID` e uma função definida pelo usuário podem ser utilizada a `WHERE` cláusula para verificar se o ID de dispositivo do stream corresponde a qualquer ID de dispositivo nessa lista. Para obter mais informações, consulte a [UDF do Javascript](stream-analytics-javascript-user-defined-functions.md) tutorial para tarefas na cloud e o [ C# UDF](stream-analytics-edge-csharp-udf.md) para as tarefas do edge.

## <a name="geospatial-aggregation"></a>Agregação de dados Geoespaciais

O Azure Stream Analytics oferece suporte a agregação de dados geoespaciais em tempo real de baixa latência na nuvem e o tempo de execução do IoT Edge.

### <a name="geospatial-aggregation-scenario"></a>Cenário de agregação de dados Geoespaciais

Uma empresa de cab quer criar uma aplicação em tempo real para orientar seus drivers de cab procurando jornada em direção as áreas de cidades neste momento existe demanda mais elevada.

A empresa armazena regiões lógicas da cidade como dados de referência. Cada região é definido por um RegionID, RegionName e perímetro geográfico.

### <a name="define-the-geofences"></a>Definir os perímetros geográficos

A tabela seguinte é um exemplo de dados de referência do perímetro geográfico que poderiam ser armazenados no armazenamento de Blobs do Azure ou uma tabela SQL do Azure. Cada região é representado por um polígono geoespaciais, que é utilizado para correlacionar com as solicitações recebidas da transmissão em fluxo de dados.

Esses polígonos são apenas para referência e não representam separações de cidade real lógico ou físico.

|RegionID|RegionName|Geofence|
|--------|----------|--------|
|1|"SoHo"|"POLÍGONO ((-74.00279525078275 40.72833625216264,-74.00547745979765 40.721929158663244,-74.00125029839018 40.71893680218994,-73.9957785919998 40.72521409075776,-73.9972377137039 40.72557184584898,-74.00279525078275 40.72833625216264) )"|
|2|"Chinatown"|"POLÍGONO ((-73.99712367114876 40.71281582267133,-73.9901070123658 40.71336881907936,-73.99023575839851 40.71452359088633,-73.98976368961189 40.71554823078944,-73.99551434573982 40.717337246783735,-73.99480624255989 40.718491949759304,-73.99652285632942 40.719109951574,-73.99776740131233 40.7168005470334,-73.99903340396736 40.71727219249899,-74.00193018970344 40.71938642421256,-74.00409741458748 40.71688186545551,-74.00051398334358 40.71517415773184,-74.0004281526551 40.714377212470005,-73.99849696216438 40.713450141693166,-73.99748845157478 40.71405192594819,-73.99712367114876 40.71281582267133)) "|
|3|"Tribeca"|"POLÍGONO ((-74.01091641815208 40.72583120006787,-74.01338405044578 40.71436586362705,-74.01370591552757 40.713617702123415,-74.00862044723533 40.711308107057235,-74.00194711120628 40.7194238654018,-74.01091641815208 40.72583120006787))"|

### <a name="aggregate-data-over-a-window-of-time"></a>Dados agregados numa janela de tempo

A tabela seguinte contém dados de transmissão de "passes."

|ID de utilizador|FromLocation|ToLocation|TripRequestedTime|
|------|------------|----------|-----------------|
|"A"|"POINT(-74.00726861389182 40.71610611981975)"|"POINT(-73.98615095917779 40.703107386025835)"|"2019-03-12T07:00:00Z"|
|"B"|"POINT(-74.00249841021645 40.723827238895666)"|"POINT(-74.01160699942085 40.71378884930115)"|"2019-03-12T07:01:00Z"|
|"C"|"POINT(-73.99680120565864 40.716439898624024)"|"POINT(-73.98289663412544 40.72582343969828)"|"2019-03-12T07:02:00Z"|
|"D"|"POINT(-74.00741090068288 40.71615626755086)"|"POINT(-73.97999843120539 40.73477895807408)"|"2019-03-12T07:03:00Z"|

A consulta seguinte associa o fluxo de dispositivo com os dados de referência do perímetro geográfico e calcula o número de pedidos por região numa janela de tempo de 15 minutos a cada minuto.

```SQL
SELECT count(*) as NumberOfRequests, RegionsRefDataInput.RegionName 
FROM UserRequestStreamDataInput
JOIN RegionsRefDataInput 
ON st_within(UserRequestStreamDataInput.FromLocation, RegionsRefDataInput.Geofence) = 1
GROUP BY RegionsRefDataInput.RegionName, hoppingwindow(minute, 1, 15)
```

Esta consulta devolve uma contagem de pedidos nos últimos 15 minutos a cada minuto por cada região na cidade. Estas informações podem ser apresentadas facilmente ao dashboard do Power BI ou podem ser emitidas para todos os controladores, como mensagens de texto SMS por meio da integração com serviços como as funções do Azure.

A imagem a seguir ilustra o resultado da consulta para o dashboard do Power BI. 

![Saída de resultado no dashboard do Power BI](./media/geospatial-scenarios/power-bi-output.png)


## <a name="next-steps"></a>Passos Seguintes

* [Introdução às funções geoespaciais de Stream Analytics](stream-analytics-geospatial-functions.md)
* [Funções Geoespaciais (do Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/geospatial-functions)
