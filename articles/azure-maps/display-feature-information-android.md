---
title: Mostrar informações sobre funcionalidades no Azure Maps Android SDK Microsoft Azure Maps
description: Saiba como exibir informações quando os utilizadores interagem com as funcionalidades do mapa. Utilize o Azure Maps Android SDK para exibir mensagens torradas e outros tipos de mensagens.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 44c5f9f1a7c0d014d101ad45a80e1e53c42f69a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009040"
---
# <a name="display-feature-information"></a>Apresentar informações da funcionalidade

Os dados espaciais são frequentemente representados usando pontos, linhas e polígonos. Estes dados têm frequentemente informações de metadados associadas aos mesmos. Por exemplo, um ponto pode representar a localização de uma loja e metadados sobre esse restaurante podem ser o seu nome, endereço e tipo de comida que serve. Estes metadados podem ser adicionados como propriedades destas funcionalidades usando um `JsonObject` . O seguinte código cria uma característica de ponto simples com uma `title` propriedade que tem um valor de "Hello World!"

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Create a point feature with some properties and add it to the data source.
JsonObject properties = new JsonObject();
properties.addProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64), properties));
```

Quando um utilizador interage com uma funcionalidade no mapa, os eventos podem ser usados para reagir a essas ações. Um cenário comum é exibir uma mensagem feita das propriedades dos metadados de uma funcionalidade com a qual o utilizador interagiu. O `OnFeatureClick` evento é o principal evento utilizado para detetar quando o utilizador tocou numa funcionalidade no mapa. Há também um `OnLongFeatureClick` evento. Ao adicionar o `OnFeatureClick` evento ao mapa, pode limitar-se a uma única camada, passando no ID de uma camada para limitá-lo. Se nenhum ID de camada for passado, tocar em qualquer característica no mapa, independentemente da camada em que se encontre, dispararia este evento. O código a seguir cria uma camada de símbolo para render os dados de pontos no mapa, em seguida, adiciona um `OnFeatureClick` evento e limita-o a esta camada de símbolo.

```java
//Create a symbol and add it to the map.
SymbolLayer symbolLayer = new SymbolLayer(dataSource);
map.layers.add(symbolLayer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).properties().get("title").getAsString();

    //Do something with the message.
}, symbolLayer.getId());    //Limit this event to the symbol layer.
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
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

<center>

![Animação de uma funcionalidade a ser tocada e uma mensagem de torrada a ser exibida](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)</center>

Além de brindar mensagens, existem muitas outras formas de apresentar as propriedades dos metadados de uma funcionalidade, tais como:

- [Widget Snakbar](https://developer.android.com/training/snackbar/showing.html) - Snackbars fornecem feedback leve sobre uma operação. Mostram uma breve mensagem na parte inferior do ecrã em dispositivos móveis e inferiores à esquerda em dispositivos maiores. As barras de lanche aparecem acima de todos os outros elementos no ecrã e apenas um pode ser exibido de cada vez.
- [Diálogos](https://developer.android.com/guide/topics/ui/dialogs) - Um diálogo é uma pequena janela que leva o utilizador a tomar uma decisão ou a introduzir informações adicionais. Um diálogo não preenche o ecrã e é normalmente utilizado para eventos modais que exigem que os utilizadores tomem medidas antes de poderem continuar.
- Adicione um [fragmento](https://developer.android.com/guide/components/fragments) à atividade atual.
- Navegue para outra atividade ou vista.

## <a name="next-steps"></a>Passos seguintes

Para adicionar mais dados ao seu mapa:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicione formas a um mapa Android](how-to-add-shapes-to-android-map.md)
