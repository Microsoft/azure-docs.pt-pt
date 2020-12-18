---
title: Mostrar informações sobre funcionalidades nos mapas do Android Microsoft Azure Maps
description: Saiba como exibir informações quando os utilizadores interagem com as funcionalidades do mapa. Utilize o Azure Maps Android SDK para exibir mensagens torradas e outros tipos de mensagens.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 37b5ab1c144ed81d995da40b87edeaccdcad7253
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679987"
---
# <a name="display-feature-information"></a>Apresentar informações da funcionalidade

Os dados espaciais são frequentemente representados usando pontos, linhas e polígonos. Estes dados têm frequentemente informações de metadados associadas aos mesmos. Por exemplo, um ponto pode representar a localização de um restaurante e metadados sobre aquele restaurante pode ser o seu nome, endereço e tipo de comida que serve. Estes metadados podem ser adicionados como propriedades de um `Feature` GeoJSON. O seguinte código cria uma característica de ponto simples com uma `title` propriedade que tem um valor de "Hello World!"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature);
```

Consulte a [Documentação de fonte de dados](create-data-source-android-sdk.md) para obter formas de criar e adicionar dados ao mapa.

Quando um utilizador interage com uma funcionalidade no mapa, os eventos podem ser usados para reagir a essas ações. Um cenário comum é exibir uma mensagem feita das propriedades dos metadados de uma funcionalidade com a qual o utilizador interagiu. O `OnFeatureClick` evento é o principal evento utilizado para detetar quando o utilizador tocou numa funcionalidade no mapa. Há também um `OnLongFeatureClick` evento. Ao adicionar o `OnFeatureClick` evento ao mapa, pode limitar-se a uma única camada, passando no ID de uma camada para limitá-lo. Se nenhum ID de camada for passado, tocar em qualquer característica no mapa, independentemente da camada em que se encontre, dispararia este evento. O código a seguir cria uma camada de símbolo para render os dados de pontos no mapa, em seguida, adiciona um `OnFeatureClick` evento e limita-o a esta camada de símbolo.

```java
//Create a symbol and add it to the map.
SymbolLayer layer = new SymbolLayer(source);
map.layers.add(layer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Do something with the message.
}, layer.getId());    //Limit this event to the symbol layer.
```

## <a name="display-a-toast-message"></a>Mostrar uma mensagem de torrada

Uma mensagem de brinde é uma das formas mais fáceis de apresentar informação ao utilizador e está disponível em todas as versões do Android. Não suporta qualquer tipo de entrada do utilizador e é apresentado apenas por um curto período de tempo. Se quiser informar rapidamente o utilizador sobre o que tocou, uma mensagem de torrada pode ser uma boa opção. O código que se segue mostra como uma mensagem de torrada pode ser usada com o `OnFeatureClick` evento.

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, layer.getId());    //Limit this event to the symbol layer.
```

![Animação de uma funcionalidade a ser tocada e uma mensagem de torrada a ser exibida](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)

Além de brindar mensagens, existem muitas outras formas de apresentar as propriedades dos metadados de uma funcionalidade, tais como:

- [Widget](https://developer.android.com/training/snackbar/showing.html)  -  `Snackbars` snackbar fornecer feedback leve sobre uma operação. Mostram uma breve mensagem na parte inferior do ecrã em dispositivos móveis e inferiores à esquerda em dispositivos maiores. `Snackbars` aparecem acima de todos os outros elementos no ecrã e apenas um pode ser exibido de cada vez.
- [Diálogos](https://developer.android.com/guide/topics/ui/dialogs) - Um diálogo é uma pequena janela que leva o utilizador a tomar uma decisão ou a introduzir informações adicionais. Um diálogo não preenche o ecrã e é normalmente utilizado para eventos modais que exigem que os utilizadores tomem medidas antes de poderem continuar.
- Adicione um [fragmento](https://developer.android.com/guide/components/fragments) à atividade atual.
- Navegue para outra atividade ou vista.

## <a name="next-steps"></a>Passos seguintes

Para adicionar mais dados ao seu mapa:

> [!div class="nextstepaction"]
> [Reagir a eventos de mapa](android-map-events.md)

> [!div class="nextstepaction"]
> [Criar uma origem de dados](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolha](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linhas](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígonos](how-to-add-shapes-to-android-map.md)
