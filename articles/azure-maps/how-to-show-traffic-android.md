---
title: Mostrar dados de tráfego em mapas Android Microsoft Azure Maps
description: Neste artigo você vai aprender, como exibir dados de tráfego em um mapa usando o Microsoft Azure Maps Android SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 12/04/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 113f39ac2976b870c9e07851cdd0919e2578940f
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680436"
---
# <a name="show-traffic-data-on-the-map-android-sdk"></a>Mostrar dados de tráfego no mapa (Android SDK)

Dados de fluxo e dados de incidentes são os dois tipos de dados de tráfego que podem ser exibidos no mapa. Este guia mostra-lhe como exibir ambos os tipos de dados de tráfego. Os dados relativos aos incidentes consistem em dados pontuais e baseados em linha para coisas como construções, encerramentos de estradas e acidentes. Os dados do fluxo mostram métricas sobre o fluxo de tráfego na estrada.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de completar os passos no [Quickstart: Criar um documento de aplicação Android.](quick-android-map.md) Os blocos de código deste artigo podem ser inseridos no manipulador de eventos de `onReady` mapas.

## <a name="show-traffic-on-the-map"></a>Mostrar tráfego no mapa

Existem dois tipos de dados de tráfego disponíveis no Azure Maps:

- Dados de incidentes - consiste em dados de pontos e linhas para coisas como construção, encerramentos de estradas e acidentes.
- Flow data - fornece métricas sobre o fluxo de tráfego nas estradas. Muitas vezes, os dados de fluxo de tráfego são usados para colorir as estradas. As cores baseiam-se na quantidade de tráfego que está a abrandar o fluxo, em relação ao limite de velocidade, ou noutra métrica. Há quatro valores que podem ser passados para a opção de tráfego `flow` do mapa.

    |Valor do fluxo | Descrição|
    | :-- | :-- |
    | TrafficFlow.NONE | Não exibe dados de tráfego no mapa |
    | TrafficFlow.RELATIVE | Mostra dados de tráfego relativos à velocidade de fluxo livre da estrada |
    | TrafficFlow.RELATIVE_DELAY | Exibe áreas mais lentas do que a média esperada |
    | TrafficFlow.ABSOLUTE | Mostra a velocidade absoluta de todos os veículos na estrada |

O código que se segue mostra como exibir dados de tráfego no mapa.

```java
//Show traffic on the map using the traffic options.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);
```

A imagem que se segue mostra o código acima que mostra informações de tráfego em tempo real no mapa.

![Mapa mostrando informações de tráfego em tempo real](media/how-to-show-traffic-android/android-show-traffic.png)

## <a name="get-traffic-incident-details"></a>Obtenha detalhes do incidente de tráfego

Detalhes sobre um incidente de trânsito estão disponíveis dentro das propriedades da funcionalidade utilizada para exibir o incidente no mapa. Incidentes de tráfego são adicionados ao mapa usando o serviço de vetor de vetor de incidente de tráfego Azure Maps. O formato dos dados nestes azulejos vetoriais se [documentado aqui.](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles) O código seguinte adiciona um evento de clique ao mapa e recupera a funcionalidade de incidente de tráfego que foi clicada e exibe uma mensagem de torrada com alguns dos detalhes.

```java
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);

//Add a click event to the map.
map.events.add((OnFeatureClick) (features) -> {

    if (features != null && features.size() > 0) {
        Feature incident = features.get(0);

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {

            StringBuilder sb = new StringBuilder();
            String incidentType = incident.getStringProperty("incidentType");

            if (incidentType != null) {
                sb.append(incidentType);
            }

            if (sb.length() > 0) {
                sb.append("\n");
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed".equals(incidentType)) {
                String from = incident.getStringProperty("from");

                if (from != null) {
                    sb.append(from);
                }
            } else {
                //Get the description of the traffic incident.
                String description = incident.getStringProperty("description");

                if (description != null) {
                    sb.append(description);
                }
            }

            String message = sb.toString();

            if (message.length() > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show();
            }
        }
    }
});
```

A imagem que se segue mostra o código acima que mostra informações de tráfego em tempo real no mapa com uma mensagem de torrada mostrando detalhes do incidente.

![Mapa mostrando informações de tráfego em tempo real com uma mensagem de torrada exibindo detalhes do incidente](media/how-to-show-traffic-android/android-traffic-details.png)

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes guias para saber como adicionar mais dados ao seu mapa:

> [!div class="nextstepaction"]
> [Adicionar uma camada de mosaico](how-to-add-tile-layer-android-map.md)
