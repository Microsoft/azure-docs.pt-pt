---
title: Exibir informações de recurso no Azure Maps SDK do Android | Microsoft Docs
description: Saiba como exibir informações de recurso no Azure Maps SDK do Android.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 0dcabb0e5141a92394f2be38cbe7e71fa6e03d58
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928543"
---
# <a name="display-feature-information"></a>Apresentar informações da funcionalidade

Os dados espaciais geralmente são representados usando pontos, linhas e polígonos. Esses dados geralmente têm informações de metadados associadas a ele. Por exemplo, um ponto pode representar o local de um repositório e os metadados sobre esse restaurante podem ser seu nome, endereço e tipo de comida que ele atende. Esses metadados podem ser adicionados como propriedades desses recursos usando uma `JsonObject`. O código a seguir cria um recurso de ponto simples com uma propriedade `title` que tem um valor de "Olá, Mundo!"

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

Quando um usuário interage com um recurso no mapa, os eventos podem ser usados para reagir a essas ações. Um cenário comum é exibir uma mensagem composta das propriedades de metadados de um recurso com o qual o usuário interagiu. O evento `OnFeatureClick` é o principal evento usado para detectar quando o usuário tocou em um recurso no mapa. Também há um evento `OnLongFeatureClick`. Ao adicionar o evento `OnFeatureClick` ao mapa, ele pode ser limitado a uma única camada passando a ID de uma camada para limitar a ela. Se nenhuma ID de camada for passada, tocar em qualquer recurso no mapa, independentemente da camada em que ele está, acionaria esse evento. O código a seguir cria uma camada de símbolo para renderizar dados de ponto no mapa e, em seguida, adiciona um evento de `OnFeatureClick` e o limita a essa camada de símbolo.

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

## <a name="display-a-toast-message"></a>Exibir uma mensagem do sistema

Uma mensagem do sistema é uma das maneiras mais fáceis de exibir informações para o usuário e está disponível em todas as versões do Android. Ele não dá suporte a nenhum tipo de entrada de usuário e só é exibido por um curto período de tempo. Se você quiser permitir que o usuário saiba rapidamente algo sobre o que ele tocou, uma mensagem do sistema poderá ser uma boa opção. O código a seguir mostra como uma mensagem do sistema pode ser usada com o evento `OnFeatureClick`.

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

![animação de um recurso que está sendo tocado e uma mensagem do sistema sendo exibida](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)</center>

Além das mensagens do sistema, há muitas outras maneiras de apresentar as propriedades de metadados de um recurso, como:

- [Snakbar widget](https://developer.android.com/training/snackbar/showing.html) – snackbars fornece comentários leves sobre uma operação. Eles mostram uma breve mensagem na parte inferior da tela no celular e na parte inferior esquerda em dispositivos maiores. Snackbars aparecem acima de todos os outros elementos na tela e apenas um pode ser exibido de cada vez.
- [Caixas de diálogo](https://developer.android.com/guide/topics/ui/dialogs) -uma caixa de diálogo é uma pequena janela que solicita que o usuário tome uma decisão ou insira informações adicionais. Uma caixa de diálogo não preenche a tela e é normalmente usada para eventos modais que exigem que os usuários executem uma ação antes que possam continuar.
- Adicione um [fragmento](https://developer.android.com/guide/components/fragments) à atividade atual.
- Navegue até outra atividade ou exibição.

## <a name="next-steps"></a>Passos seguintes

Para adicionar mais dados ao seu mapa:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Adicionar formas a um mapa do Android](how-to-add-shapes-to-android-map.md)
