---
title: Mostrar informações no Azure Maps Android SDK [ Microsoft Azure Maps
description: Neste artigo, você aprenderá a exibir informações de funcionalidades num mapa usando o Microsoft Azure Maps Android SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 26f41a7fd88a3c2018592e89ae95e3b962c1a9e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75911696"
---
# <a name="display-feature-information"></a>Apresentar informações da funcionalidade

Os dados espaciais são frequentemente representados usando pontos, linhas e polígonos. Estes dados têm frequentemente informações de metadados associadas a ele. Por exemplo, um ponto pode representar a localização de uma loja e metadados sobre aquele restaurante podem ser o seu nome, endereço e tipo de comida que serve. Estes metadados podem ser adicionados como `JsonObject`propriedades destas funcionalidades usando a . O seguinte código cria uma `title` característica de ponto simples com uma propriedade que tem um valor de "Hello World!"

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

Quando um utilizador interage com uma funcionalidade no mapa, os eventos podem ser usados para reagir a essas ações. Um cenário comum é exibir uma mensagem feita das propriedades dos metadados de uma funcionalidade com a quais o utilizador interagiu. O `OnFeatureClick` evento é o evento principal usado para detetar quando o utilizador tocou numa funcionalidade no mapa. Há também um `OnLongFeatureClick` evento. Ao adicionar `OnFeatureClick` o evento ao mapa, pode limitar-se a uma única camada, passando a identificação de uma camada para limitá-lo. Se não for passado nenhum ID de camada, tocando em qualquer recurso no mapa, independentemente da camada em que se encontre, dispararia este evento. O código seguinte cria uma camada de símbolo para `OnFeatureClick` renderizar dados de pontos no mapa, em seguida, adiciona um evento e limita-o a esta camada de símbolo.

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

Uma mensagem de torrada é uma das formas mais fáceis de mostrar informações ao utilizador e está disponível em todas as versões do Android. Não suporta qualquer tipo de entrada do utilizador e é apresentado apenas por um curto período de tempo. Se quiser informar rapidamente o utilizador sobre o que tocou, uma mensagem de torrada pode ser uma boa opção. O código seguinte mostra como uma mensagem `OnFeatureClick` de torrada pode ser usada com o evento.

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

Além de mensagens de torradas, existem muitas outras formas de apresentar as propriedades dos metadados de uma funcionalidade, tais como:

- [Snakbar widget](https://developer.android.com/training/snackbar/showing.html) - Snackbars fornecem feedback leve sobre uma operação. Mostram uma breve mensagem na parte inferior do ecrã no telemóvel e na esquerda inferior em dispositivos maiores. As snackbars aparecem acima de todos os outros elementos no ecrã e apenas um pode ser exibido de cada vez.
- [Diálogos](https://developer.android.com/guide/topics/ui/dialogs) - Um diálogo é uma pequena janela que leva o utilizador a tomar uma decisão ou a introduzir informações adicionais. Um diálogo não preenche o ecrã e é normalmente utilizado para eventos modais que exigem que os utilizadores tomem uma ação antes de poderem continuar.
- Adicione um [Fragmento](https://developer.android.com/guide/components/fragments) à atividade atual.
- Navegue para outra atividade ou vista.

## <a name="next-steps"></a>Passos seguintes

Para adicionar mais dados ao seu mapa:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicione formas a um mapa Android](how-to-add-shapes-to-android-map.md)
