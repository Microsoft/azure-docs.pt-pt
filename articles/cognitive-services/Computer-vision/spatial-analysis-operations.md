---
title: Operações de Análise Espacial
titleSuffix: Azure Cognitive Services
description: As operações de Análise Espacial.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: 80f0d29de6b3013ad02ed1a5d34bebdf81a8766b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91628218"
---
# <a name="spatial-analysis-operations"></a>Operações de análise espacial

A análise espacial permite a análise do vídeo de transmissão em fluxo em tempo real de dispositivos com câmara. Para cada dispositivo com câmara configurado, as operações para a análise espacial gerarão um fluxo de saída de mensagens JSON enviadas para a instância do Hub IoT do Azure. 

O recipiente de análise espacial implementa as seguintes operações:

| Identificador de Operação| Descrição|
|---------|---------|
| serviços cognitivos.vision.spatialanalysis-personcount | Conta pessoas numa zona designada no campo de visão da câmara. <br> Emite um evento _personevent_ inicial e, em seguida, _eventos do Número Devent_ quando a contagem muda.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline | Rastreia quando uma pessoa cruza uma linha designada no campo de visão da câmara. <br>Emite um evento _personLineEvent_ quando a pessoa cruza a linha e fornece informações direcionais. 
| serviços cognitivos.vision.spatialanalysis-personcrossingpolygon | Rastreia quando uma pessoa cruza uma linha designada no campo de visão da câmara. <br> Emite um evento _personLineEvent_ quando a pessoa atravessa a zona e fornece informações direcionais. |
| serviços cognitivos.vision.spatialanalysis-persondistance | Faixas quando as pessoas violam uma regra de distância. <br> Emite uma _pessoaSstevente_ periodicamente com a localização de cada violação à distância. |

Acima das operações estão também disponíveis na `.debug` versão, que têm a capacidade de visualizar os quadros de vídeo à medida que estão a ser processados. Terá de ser executado `xhost +` no computador anfitrião para permitir a visualização de quadros de vídeo e eventos.

| Identificador de Operação| Descrição|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.debug | Conta pessoas numa zona designada no campo de visão da câmara. <br> Emite um evento _personevent_ inicial e, em seguida, _eventos do Número Devent_ quando a contagem muda.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.debug | Rastreia quando uma pessoa cruza uma linha designada no campo de visão da câmara. <br>Emite um evento _personLineEvent_ quando a pessoa cruza a linha e fornece informações direcionais. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.debug | Rastreia quando uma pessoa cruza uma linha designada no campo de visão da câmara. <br> Emite um evento _personLineEvent_ quando a pessoa atravessa a zona e fornece informações direcionais. |
| cognitiveservices.vision.spatialanalysis-persondistance.debug | Faixas quando as pessoas violam uma regra de distância. <br> Emite uma _pessoaSstevente_ periodicamente com a localização de cada violação à distância. |

A análise espacial também pode ser executada com [o Live Video Analytics](https://aka.ms/lva-spatial-analysis) como o seu módulo de IA de vídeo. 

<!--more details on the setup can be found in the [LVA Setup page](LVA-Setup.md). Below is the list of the operations supported with Live Video Analytics. -->

| Identificador de Operação| Descrição|
|---------|---------|
| cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics | Conta pessoas numa zona designada no campo de visão da câmara. <br> Emite um evento _personevent_ inicial e, em seguida, _eventos do Número Devent_ quando a contagem muda.  |
| cognitiveservices.vision.spatialanalysis-personcrossingline.livevideoanalytics | Rastreia quando uma pessoa cruza uma linha designada no campo de visão da câmara. <br>Emite um evento _personLineEvent_ quando a pessoa cruza a linha e fornece informações direcionais. 
| cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics | Rastreia quando uma pessoa cruza uma linha designada no campo de visão da câmara. <br> Emite um evento _personLineEvent_ quando a pessoa atravessa a zona e fornece informações direcionais. |
| cognitiveservices.vision.spatialanalysis-persondistance.livevideoanalytics | Faixas quando as pessoas violam uma regra de distância. <br> Emite uma _pessoaSstevente_ periodicamente com a localização de cada violação à distância. |

As operações live video analytics também estão disponíveis na `.debug` versão (por exemplo, serviços cognitivos.vision.spatialanalysis-personcount.livevideoanalytics.debug) que tem a capacidade de visualizar os quadros de vídeo como sendo processados. Terá de ser executado `xhost +` no computador anfitrião para permitir a visualização dos quadros de vídeo e eventos

> [!IMPORTANT]
> Os modelos de IA de visão computacional detetam e localizam a presença humana em imagens de vídeo e saída usando uma caixa de delimitação em torno de um corpo humano. Os modelos de IA não tentam detetar rostos ou descobrir as identidades ou demografia dos indivíduos.

Estes são os parâmetros exigidos por cada uma destas operações de análise espacial.

| Parâmetros de operação| Descrição|
|---------|---------|
| ID da Operação | O Identificador da Operação da tabela acima.|
| ativado | Boolean: verdadeiro ou falso|
| VIDEO_URL| O url RTSP para o dispositivo da câmara (Exemplo: `rtsp://username:password@url` ). A análise espacial suporta o fluxo codificado H.264 através do RTSP, http, ou mp4 |
| VIDEO_SOURCE_ID | Um nome amigável para o dispositivo da câmara ou transmissão de vídeo. Isto será devolvido com a saída do evento JSON.|
| VIDEO_IS_LIVE| Verdade para dispositivos de câmara; falso para vídeos gravados.|
| VIDEO_DECODE_GPU_INDEX| Que GPU para descodificar a moldura de vídeo. Por defeito é 0. Deve ser o mesmo que `gpu_index` no outro nó config `VICA_NODE_CONFIG` como, . `DETECTOR_NODE_CONFIG` .|
| DETECTOR_NODE_CONFIG | JSON indicando qual a GPU para executar o nó do detetor. Deve estar no seguinte formato: `"{ \"gpu_index\": 0 }",`|
| SPACEANALYTICS_CONFIG | Configuração JSON para zona e linha como descrito abaixo.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcount"></a>Configuração de zona para serviços cognitivos.vision.spatialanalysis-personcount

 Este é um exemplo de uma entrada JSON para o parâmetro SPACEANALYTICS_CONFIG que configura uma zona. Pode configurar várias zonas para esta operação.

```json
{
"zones":[{
    "name": "lobbycamera"
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 50.00,
    "events":[{
        "type": "count",
        "config":{
            "trigger": "event",
            "output_frequency": 1
      }
    }]
}
```

| Nome | Tipo| Descrição|
|---------|---------|---------|
| `zones` | lista| Lista de zonas. |
| `name` | string| Nome amigável para esta zona.|
| `polygon` | lista| Cada par de valor representa o x,y para vértices de um polígono. O polígono representa as áreas em que as pessoas são rastreadas ou contadas e os pontos de poligão são baseados em coordenadas normalizadas (0-1), onde o canto superior esquerdo é (0.0, 0.0) e o canto inferior direito é (1.0, 1.0).   
| `threshold` | float| Os eventos são elitados quando a confiança dos modelos de IA é maior ou igual a este valor. |
| `type` | string| Para **os serviços cognitivos.vision.spatialanalysis-personcount** isto deve ser `count` .|
| `trigger` | string| O tipo de gatilho para enviar um evento. Os valores suportados destinam-se `event` ao envio de eventos quando a contagem muda ou `interval` para envio de eventos periodicamente, independentemente de a contagem ter mudado ou não.
| `interval` | string| Um tempo em segundos que a contagem da pessoa será agregada antes de um evento ser disparado. A operação continuará a analisar a cena a um ritmo constante e devolve a contagem mais comum ao longo desse intervalo. O intervalo de agregação é aplicável a ambos `event` e `interval` .|

### <a name="line-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingline"></a>Configuração de linha para serviços cognitivos.vision.spatialanalysis-personcrossingline

Este é um exemplo de uma entrada JSON para o parâmetro SPACEANALYTICS_CONFIG que configura uma linha. Pode configurar várias linhas de passagem para esta operação.

```json
{
"lines":[{
    "name": "doorcamera" 
    "line": {
        "start": {"x": 0, "y": 0.5},
        "end": {"x": 1, "y": 0.5}
            },
    "threshold": 50.00,
    "events":[{
        "type": "linecrossing",
        "config":{
            "trigger": "event"
            }
        }]
    }]
}
```

| Nome | Tipo| Descrição|
|---------|---------|---------|
| `lines` | lista| Lista de linhas.|
| `name` | string| Nome amigável para esta linha.|
| `line` | lista| A definição da linha. Esta é uma linha direcional que lhe permite entender "entrada" vs. "saída".|
| `start` | par de valor| x, y coordenadas para o ponto de partida da linha. Os valores da boia representam a posição do vértice em relação ao canto superior esquerdo. Para calcular os valores x, y absolutos, multiplica estes valores com o tamanho do quadro. |
| `end` | par de valor| x, y coordenadas para o ponto final da linha. Os valores da boia representam a posição do vértice em relação ao canto superior esquerdo. Para calcular os valores x, y absolutos, multiplica estes valores com o tamanho do quadro. |
| `threshold` | float| Os eventos são elitados quando a confiança dos modelos de IA é maior ou igual a este valor. |
| `type` | string| Para **os serviços cognitivos.vision.spatialanalysis-personcrossingline** este deve ser `linecrossing` .|
|`trigger`|string|O tipo de gatilho para enviar um evento.<br>Valores suportados: "evento": fogo quando alguém cruza a linha.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon"></a>Configuração de zona para serviços cognitivos.vision.spatialanalysis-personcrossingpolygon

Este é um exemplo de uma entrada JSON para o parâmetro SPACEANALYTICS_CONFIG que configura uma zona. Pode configurar várias zonas para esta operação.

 ```json
{
"zones":[{
    "name": "queuecamera"
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 50.00,
    "events":[{
        "type": "zone_crossing",
        "config":{
            "trigger": "event"
            }
        }]
    }]
}
```

| Nome | Tipo| Descrição|
|---------|---------|---------|
| `zones` | lista| Lista de zonas. |
| `name` | string| Nome amigável para esta zona.|
| `polygon` | lista| Cada par de valor representa o x,y para vértices de polígono. O polígono representa as áreas em que as pessoas são rastreadas ou contadas. Os valores da boia representam a posição do vértice em relação ao canto superior esquerdo. Para calcular os valores x, y absolutos, multiplica estes valores com o tamanho do quadro. 
| `threshold` | float| Os eventos são elitados quando a confiança dos modelos de IA é maior ou igual a este valor. |
| `type` | string| Para **os serviços cognitivos.vision.spatialanalysis-personcrossingpolygon** este deve ser `enter` ou `exit` .|
| `trigger`|string|O tipo de gatilho para o envio de um evento<br>Valores suportados: "evento": fogo quando alguém entra ou sai da zona.|

### <a name="zone-configuration-for-cognitiveservicesvisionspatialanalysis-persondistance"></a>Configuração de zona para serviços cognitivos.vision.spatialanalysis-persondistance

Este é um exemplo de uma entrada JSON para o parâmetro SPACEANALYTICS_CONFIG que configura uma zona para **serviços cognitivos.vision.espacialanalysis-persondistance**. Pode configurar várias zonas para esta operação.

```json
{
"zones":[{
    "name": "lobbycamera",
    "polygon": [[0.3,0.3], [0.3,0.9], [0.6,0.9], [0.6,0.3], [0.3,0.3]],
    "threshold": 35.00,
    "events":[{
        "type": "persondistance",
        "config":{
            "trigger": "event",
            "output_frequency":1,
            "minimum_distance_threshold":6.0,
            "maximum_distance_threshold":35.0
            }
        }]
    }]
}
```

| Nome | Tipo| Descrição|
|---------|---------|---------|
| `zones` | lista| Lista de zonas. |
| `name` | string| Nome amigável para esta zona.|
| `polygon` | lista| Cada par de valor representa o x,y para vértices de polígono. O polígono representa as áreas em que as pessoas são contadas e a distância entre as pessoas é medida. Os valores da boia representam a posição do vértice em relação ao canto superior esquerdo. Para calcular os valores x, y absolutos, multiplica estes valores com o tamanho do quadro. 
| `threshold` | float| Os eventos são elitados quando a confiança dos modelos de IA é maior ou igual a este valor. |
| `type` | string| Para **os serviços cognitivos.vision.spatialanalysis-persondistance** isto deve ser `people_distance` .|
| `trigger` | string| O tipo de gatilho para enviar um evento. Os valores suportados destinam-se `event` ao envio de eventos quando a contagem muda ou `interval` para envio de eventos periodicamente, independentemente de a contagem ter mudado ou não.
| `interval` | string | Um tempo em segundos para que as violações sejam agregadas antes de um evento ser disparado. O intervalo de agregação é aplicável a ambos `event` e `interval` .|
| `output_frequency` | int | A taxa a que os eventos são efencados. Quando `output_frequency` = X, cada evento X é expulso, ex. `output_frequency` = 2 significa que cada outro evento é a saída. O output_frequency é aplicável a ambos `event` e `interval` .|
| `minimum_distance_threshold` | float| Uma distância nos pés que irá desencadear um evento "TooClose" quando as pessoas estão a menos de essa distância.|
| `maximum_distance_threshold` | float| Uma distância nos pés que irá desencadear um evento "TooFar" quando as pessoas são maiores do que essa distância.|

Este é um exemplo de uma entrada JSON para o DETETOR_NODE_CONFIG parâmetro que configura uma zona **cognitiva.vision.espacialanalysis-persondistance.**

```json
{ 
"gpu_index": 0, 
"do_calibration": true
}
```

| Nome | Tipo| Description|
|---------|---------|---------|
| `gpu_index` | cadeia (de carateres)| O índice de GPU em que esta operação será executada.|
| `do_calibration` | string | Indica que a calibração está ligada. `do_calibration` deve ser verdadeiro para **os serviços cognitivos.vision.espacialanalysis-persondistance** para funcionar corretamente.|

Consulte as diretrizes [de colocação](spatial-analysis-camera-placement.md)  da câmara para saber mais sobre configurações de zona e linha.

## <a name="spatial-analysis-operation-output"></a>Análise espacial Operação Saída

Os eventos de cada operação são retirados para Azure IoT Hub no formato JSON.

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcount-ai-insights"></a>Formato JSON para serviços cognitivos.vision.spatialanalysis-personcount AI Insights

Prove json para uma saída de evento por esta operação.

```json
{
    "events": [
        {
            "id": "b013c2059577418caa826844223bb50b",
            "type": "personCountEvent",
            "detectionIds": [
                "bc796b0fc2534bc59f13138af3dd7027",
                "60add228e5274158897c135905b5a019"
            ],
            "properties": {
                "personCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:57.224Z",
        "width": 608,
        "height": 342,
        "frameId": "1400",
        "cameraCalibrationInfo": {
            "status": "Complete",
            "cameraHeight": 10.306597709655762,
            "focalLength": 385.3199462890625,
            "tiltupAngle": 1.0969393253326416
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "bc796b0fc2534bc59f13138af3dd7027",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.612683747944079,
                        "y": 0.25340268765276636
                    },
                    {
                        "x": 0.7185954043739721,
                        "y": 0.6425260577285499
                    }
                ]
            },
            "confidence": 0.9559211134910583,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "60add228e5274158897c135905b5a019",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.22326200886776573,
                        "y": 0.17830915618361087
                    },
                    {
                        "x": 0.34922296122500773,
                        "y": 0.6297955429344847
                    }
                ]
            },
            "confidence": 0.9389744400978088,
            "centerGroundPoint": {
                "x": 0.0,
                "y": 0.0
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Nome do campo do evento | Tipo| Description|
|---------|---------|---------|
| `id` | cadeia (de carateres)| ID do Evento|
| `type` | string| Tipo de evento|
| `detectionsId` | matriz| Matriz do tamanho 1 do identificador único da deteção da pessoa que desencadeou este evento|
| `properties` | coleção| Coleção de valores|
| `trackinId` | string| Identificador único da pessoa detetada|
| `status` | string| 'Entrar' ou 'Sair'|
| `side` | int| O número do lado do polígono que a pessoa cruzou|
| `zone` | string | O campo de "nome" do polígono que representa a zona que foi atravessada|
| `trigger` | string| O tipo de gatilho é 'evento' ou 'intervalo' dependendo do valor de `trigger` SPACEANALYTICS_CONFIG|

| Nome do campo de deteções | Tipo| Description|
|---------|---------|---------|
| `id` | cadeia (de carateres)| ID de deteção|
| `type` | string| Tipo de deteção|
| `region` | coleção| Coleção de valores|
| `type` | string| Tipo de região|
| `points` | coleção| Pontos superiores esquerdos e inferiores direito quando o tipo de região é RETÂNGLE |
| `confidence` | float| Confiança do algoritmo|

| Nome do campo SourceInfo | Tipo| Description|
|---------|---------|---------|
| `id` | cadeia (de carateres)| ID da câmera|
| `timestamp` | date| Data utc quando a carga de JSON foi emitida|
| `width` | int | Largura do quadro de vídeo|
| `height` | int | Altura do quadro de vídeo|
| `frameId` | int | Identificador de quadro|
| `cameraCallibrationInfo` | coleção | Coleção de valores|
| `status` | string | Indica se a calibração da câmara para o plano terrestre é "Completa"|
| `cameraHeight` | float | A altura da câmara acima do chão em pés. Isto é deduzido da auto-calibração. |
| `focalLength` | float | O comprimento focal da câmara em pixels. Isto é deduzido da auto-calibração. |
| `tiltUpAngle` | float | O ângulo de inclinação da câmara da vertical. Isto é deduzido da auto-calibração.|

| Nome do campo SourceInfo | Tipo| Description|
|---------|---------|---------|
| `id` | cadeia (de carateres)| ID da câmera|
| `timestamp` | date| Data utc quando a carga de JSON foi emitida|
| `width` | int | Largura do quadro de vídeo|
| `height` | int | Altura do quadro de vídeo|
| `frameId` | int | Identificador de quadro|


### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingline-ai-insights"></a>Formato JSON para serviços cognitivos.vision.spatialanalysis-personcrossingline AI Insights

Amostra JSON para deteção de saída por esta operação.

```json
{
    "events": [
        {
            "id": "3733eb36935e4d73800a9cf36185d5a2",
            "type": "personLineEvent",
            "detectionIds": [
                "90d55bfc64c54bfd98226697ad8445ca"
            ],
            "properties": {
                "trackingId": "90d55bfc64c54bfd98226697ad8445ca",
                "status": "CrossLeft"
            },
            "zone": "doorcamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:06:53.261Z",
        "width": 608,
        "height": 342,
        "frameId": "1340",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "90d55bfc64c54bfd98226697ad8445ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.491627341822574,
                        "y": 0.2385801348769874
                    },
                    {
                        "x": 0.588894994635331,
                        "y": 0.6395559924387793
                    }
                ]
            },
            "confidence": 0.9005028605461121,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```
| Nome do campo do evento | Tipo| Description|
|---------|---------|---------|
| `id` | cadeia (de carateres)| ID do Evento|
| `type` | string| Tipo de evento|
| `detectionsId` | matriz| Matriz do tamanho 1 do identificador único da deteção da pessoa que desencadeou este evento|
| `properties` | coleção| Coleção de valores|
| `trackinId` | string| Identificador único da pessoa detetada|
| `status` | string| Direção dos cruzamentos de linha, quer 'CrossLeft' ou 'CrossRight'|
| `zone` | string | O campo de "nome" da linha que foi atravessada|

| Nome do campo de deteções | Tipo| Description|
|---------|---------|---------|
| `id` | cadeia (de carateres)| ID de deteção|
| `type` | string| Tipo de deteção|
| `region` | coleção| Coleção de valores|
| `type` | string| Tipo de região|
| `points` | coleção| Pontos superiores esquerdos e inferiores direito quando o tipo de região é RETÂNGLE |
| `confidence` | float| Confiança do algoritmo|

| Nome do campo SourceInfo | Tipo| Description|
|---------|---------|---------|
| `id` | cadeia (de carateres)| ID da câmera|
| `timestamp` | date| Data utc quando a carga de JSON foi emitida|
| `width` | int | Largura do quadro de vídeo|
| `height` | int | Altura do quadro de vídeo|
| `frameId` | int | Identificador de quadro|


> [!IMPORTANT]
> O modelo de IA deteta uma pessoa independentemente de a pessoa estar virada para ou para longe da câmara. O modelo de IA não funciona deteção ou reconhecimento facial e não emite nenhuma informação biométrica. 

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-personcrossingpolygon-ai-insights"></a>Formato JSON para serviços cognitivos.vision.spatialanalysis-personcrossingpolygon AI Insights

Amostra JSON para deteção de saída por esta operação.

```json
{
    "events": [
        {
            "id": "f095d6fe8cfb4ffaa8c934882fb257a5",
            "type": "personZoneEvent",
            "detectionIds": [
                "afcc2e2a32a6480288e24381f9c5d00e"
            ],
            "properties": {
                "trackingId": "afcc2e2a32a6480288e24381f9c5d00e",
                "status": "Enter",
                "side": ""
            },
            "zone": "queuecamera"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:15:09.680Z",
        "width": 608,
        "height": 342,
        "frameId": "428",
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "afcc2e2a32a6480288e24381f9c5d00e",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.8135572734631991,
                        "y": 0.6653949670624315
                    },
                    {
                        "x": 0.9937645761590255,
                        "y": 0.9925406829655519
                    }
                ]
            },
            "confidence": 0.6267998814582825,
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Nome do campo do evento | Tipo| Description|
|---------|---------|---------|
| `id` | cadeia (de carateres)| ID do Evento|
| `type` | string| Tipo de evento|
| `detectionsId` | matriz| Matriz do tamanho 1 do identificador único da deteção da pessoa que desencadeou este evento|
| `properties` | coleção| Coleção de valores|
| `trackinId` | string| Identificador único da pessoa detetada|
| `status` | string| Direção das travessias do polígono, quer 'Enter' ou 'Exit'|
| `zone` | string | O campo de "nome" do polígono que representa a zona que foi atravessada|

| Nome do campo de deteções | Tipo| Description|
|---------|---------|---------|
| `id` | cadeia (de carateres)| ID de deteção|
| `type` | string| Tipo de deteção|
| `region` | coleção| Coleção de valores|
| `type` | string| Tipo de região|
| `points` | coleção| Pontos superiores esquerdos e inferiores direito quando o tipo de região é RETÂNGLE |
| `confidence` | float| Confiança do algoritmo|

### <a name="json-format-for-cognitiveservicesvisionspatialanalysis-persondistance-ai-insights"></a>Formato JSON para serviços cognitivos.vision.spatialanalysis-persondistance AI Insights

Amostra JSON para deteção de saída por esta operação.

```json
{
    "events": [
        {
            "id": "9c15619926ef417aa93c1faf00717d36",
            "type": "personDistanceEvent",
            "detectionIds": [
                "9037c65fa3b74070869ee5110fcd23ca",
                "7ad7f43fd1a64971ae1a30dbeeffc38a"
            ],
            "properties": {
                "personCount": 5,
                "averageDistance": 20.807043981552123,
                "minimumDistanceThreshold": 6.0,
                "maximumDistanceThreshold": "Infinity",
                "eventName": "TooClose",
                "distanceViolationPersonCount": 2
            },
            "zone": "lobbycamera",
            "trigger": "event"
        }
    ],
    "sourceInfo": {
        "id": "camera_id",
        "timestamp": "2020-08-24T06:17:25.309Z",
        "width": 608,
        "height": 342,
        "frameId": "1199",
        "cameraCalibrationInfo": {
            "status": "Complete",
            "cameraHeight": 12.9940824508667,
            "focalLength": 401.2800598144531,
            "tiltupAngle": 1.057669997215271
        },
        "imagePath": ""
    },
    "detections": [
        {
            "type": "person",
            "id": "9037c65fa3b74070869ee5110fcd23ca",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.39988183975219727,
                        "y": 0.2719132942065858
                    },
                    {
                        "x": 0.5051516984638414,
                        "y": 0.6488402517218339
                    }
                ]
            },
            "confidence": 0.948630690574646,
            "centerGroundPoint": {
                "x": -1.4638760089874268,
                "y": 18.29732322692871
            },
            "metadataType": ""
        },
        {
            "type": "person",
            "id": "7ad7f43fd1a64971ae1a30dbeeffc38a",
            "region": {
                "type": "RECTANGLE",
                "points": [
                    {
                        "x": 0.5200299714740954,
                        "y": 0.2875368218672903
                    },
                    {
                        "x": 0.6457497446160567,
                        "y": 0.6183311060855263
                    }
                ]
            },
            "confidence": 0.8235412240028381,
            "centerGroundPoint": {
                "x": 2.6310102939605713,
                "y": 18.635927200317383
            },
            "metadataType": ""
        }
    ],
    "schemaVersion": "1.0"
}
```

| Nome do campo do evento | Tipo| Description|
|---------|---------|---------|
| `id` | cadeia (de carateres)| ID do Evento|
| `type` | string| Tipo de evento|
| `detectionsId` | matriz| Matriz do tamanho 1 do identificador único da deteção da pessoa que desencadeou este evento|
| `properties` | coleção| Coleção de valores|
| `personCount` | int| Número de pessoas detetadas quando o evento foi emitido|
| `averageDistance` | float| A distância média entre todas as pessoas detetadas nos pés|
| `minimumDistanceThreshold` | float| A distância nos pés que irá desencadear um evento "TooClose" quando as pessoas estão a menos de essa distância.|
| `maximumDistanceThreshold` | float| A distância nos pés que irá desencadear um evento "TooFar" quando as pessoas são maiores do que a distância.|
| `eventName` | string| O nome do evento está `TooClose` com o `minimumDistanceThreshold` violado, quando é `TooFar` `maximumDistanceThreshold` violado, ou quando a `unknown` auto-calibração não está concluída|
| `distanceViolationPersonCount` | int| Número de pessoas detetadas em violação de `minimumDistanceThreshold` ou `maximumDistanceThreshold`|
| `zone` | string | O campo de "nome" do polígono que representa a zona que foi monitorizada para o distanciamento entre as pessoas|
| `trigger` | string| O tipo de gatilho é 'evento' ou 'intervalo' dependendo do valor de `trigger` SPACEANALYTICS_CONFIG|

| Nome do campo de deteções | Tipo| Description|
|---------|---------|---------|
| `id` | cadeia (de carateres)| ID de deteção|
| `type` | string| Tipo de deteção|
| `region` | coleção| Coleção de valores|
| `type` | string| Tipo de região|
| `points` | coleção| Pontos superiores esquerdos e inferiores direito quando o tipo de região é RETÂNGLE |
| `confidence` | float| Confiança do algoritmo|
| `centerGroundPoint` | 2 valores de boia| `x`, `y` valores com as coordenadas da localização inferida da pessoa no chão em pés. `x` é distância da câmara perpendicular ao plano de imagem da câmara projetado no chão em pés. `y` é distância da câmara paralela ao plano de imagem projetado no chão em pés.|

| Nome do campo SourceInfo | Tipo| Description|
|---------|---------|---------|
| `id` | cadeia (de carateres)| ID da câmera|
| `timestamp` | date| Data utc quando a carga de JSON foi emitida|
| `width` | int | Largura do quadro de vídeo|
| `height` | int | Altura do quadro de vídeo|
| `frameId` | int | Identificador de quadro|
| `cameraCallibrationInfo` | coleção | Coleção de valores|
| `status` | string | Indica se a calibração da câmara para o plano terrestre é "Completa"|
| `cameraHeight` | float | A altura da câmara acima do chão em pés. Isto é deduzido da auto-calibração. |
| `focalLength` | float | O comprimento focal da câmara em pixels. Isto é deduzido da auto-calibração. |
| `tiltUpAngle` | float | O ângulo de inclinação da câmara da vertical. Isto é deduzido da auto-calibração.|


## <a name="use-the-output-generated-by-the-container"></a>Utilize a saída gerada pelo recipiente

Pode querer integrar a deteção de análises espaciais ou eventos na sua aplicação. Aqui estão algumas abordagens a considerar: 

* Utilize o Azure Event Hub SDK para a sua linguagem de programação escolhida para ligar ao ponto final do Azure IoT Hub e receber os eventos. Consulte [as mensagens dispositivo-nuvem do ponto final incorporado](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin) para obter mais informações. 
* Confense o **Encaminhamento de Mensagens** no seu Azure IoT Hub para enviar os eventos para outros pontos finais ou guardar os eventos para o seu armazenamento de dados. Consulte [o Encaminhamento de Mensagens IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) para obter mais informações. 
* Crie um trabalho Azure Stream Analytics para processar os eventos em tempo real à medida que chegam e criam visualizações. 

## <a name="deploying-spatial-analysis-operations-at-scale-multiple-cameras"></a>Implantação de operações de análise espacial à escala (múltiplas câmaras)

Para obter o melhor desempenho e utilização das GPUs, pode implementar quaisquer operações de análise espacial em várias câmaras usando instâncias de gráfico. Abaixo está uma amostra para executar a `cognitiveservices.vision.spatialanalysis-personcount` operação em cinco câmaras.

```json
 "properties.desired": {
      "globalSettings": {
          "PlatformTelemetryEnabled": false,
          "CustomerTelemetryEnabled": true
      },
      "graphs": {
          "personcount": {
              "operationId": "cognitiveservices.vision.spatialanalysis-personcount",
              "version": 1,
              "enabled": true,
              "sharedNodes": {
                  "shared_detector1": {
                      "node": "PersonCountGraph.detector",
                      "parameters": {
                          "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"batch_size\": 5}",
                      }
                  }
              },
              "parameters": {
                  "VIDEO_DECODE_GPU_INDEX": 0,
                  "VIDEO_IS_LIVE": true
              },
              "instances": {
                  "1": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 1>",
                          "VIDEO_SOURCE_ID": "camera 1",
                          "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"zone5\",\"polygon\":[[0,0],[1,0],[0,1],[1,1],[0,0]],\"threshold\":50.0, \"events\":[{\"type\":\"count\", \"output_frequency\": 1}]}]}"
                      }
                  },
                  "2": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 2>",
                          "VIDEO_SOURCE_ID": "camera 2",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "3": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 3>",
                          "VIDEO_SOURCE_ID": "camera 3",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "4": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 4>",
                          "VIDEO_SOURCE_ID": "camera 4",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  },
                  "5": {
                      "sharedNodeMap": {
                          "PersonCountGraph/detector": "shared_detector1"
                      },
                      "parameters": {
                          "VIDEO_URL": "<Replace RTSP URL for camera 5>",
                          "VIDEO_SOURCE_ID": "camera 5",
                          "SPACEANALYTICS_CONFIG": "<Replace the zone config value, same format as above>"
                      }
                  }
              }
          }
      }
  }
  ```
| Nome | Tipo| Descrição|
|---------|---------|---------|
| `batch_size` | int | Indica o número de câmaras que serão usadas na operação. |

## <a name="next-steps"></a>Passos seguintes

* [Implementar uma aplicação web People Counting](spatial-analysis-web-app.md)
* [Registo e resolução de problemas](spatial-analysis-logging.md)
* [Guia de colocação de câmera](spatial-analysis-camera-placement.md)
* [Guia de colocação de zona e linha](spatial-analysis-zone-line-placement.md)
