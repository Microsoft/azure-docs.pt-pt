---
title: Mostrar informações sobre funcionalidades em mapas Android | Microsoft Azure Maps
description: Saiba como exibir informações quando os utilizadores interagem com as funcionalidades do mapa. Utilize o Azure Maps Android SDK para exibir mensagens torradas e outros tipos de mensagens.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: b9926d5d6a70d959c0baacd9602341bb69abe924
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097249"
---
# <a name="display-feature-information"></a>Apresentar informações da funcionalidade

Os dados espaciais são frequentemente representados usando pontos, linhas e polígonos. Estes dados têm frequentemente informações de metadados associadas aos mesmos. Por exemplo, um ponto pode representar a localização de um restaurante e metadados sobre aquele restaurante pode ser o seu nome, endereço e tipo de comida que serve. Estes metadados podem ser adicionados como propriedades de um `Feature` GeoJSON. O seguinte código cria uma característica de ponto simples com uma `title` propriedade que tem um valor de "Hello World!"

::: zone pivot="programming-language-java-android"

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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64))

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!")

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature)
```

::: zone-end

Consulte a [Documentação de fonte de dados](create-data-source-android-sdk.md) para obter formas de criar e adicionar dados ao mapa.

Quando um utilizador interage com uma funcionalidade no mapa, os eventos podem ser usados para reagir a essas ações. Um cenário comum é exibir uma mensagem feita das propriedades dos metadados de uma funcionalidade com a qual o utilizador interagiu. O `OnFeatureClick` evento é o principal evento utilizado para detetar quando o utilizador tocou numa funcionalidade no mapa. Há também um `OnLongFeatureClick` evento. Ao adicionar o `OnFeatureClick` evento ao mapa, pode limitar-se a uma única camada, passando no ID de uma camada para limitá-lo. Se nenhum ID de camada for passado, tocar em qualquer característica no mapa, independentemente da camada em que se encontre, dispararia este evento. O código a seguir cria uma camada de símbolo para render os dados de pontos no mapa, em seguida, adiciona um `OnFeatureClick` evento e limita-o a esta camada de símbolo.

::: zone pivot="programming-language-java-android"

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

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a symbol and add it to the map.
val layer = SymbolLayer(source)
map.layers.add(layer)

//Add a feature click event to the map.
map.events.add(OnFeatureClick { features: List<Feature> ->
    //Retrieve the title property of the feature as a string.
    val msg = features[0].getStringProperty("title")

    //Do something with the message.
}, layer.getId()) //Limit this event to the symbol layer.
```

::: zone-end

## <a name="display-a-toast-message"></a>Mostrar uma mensagem de torrada

Uma mensagem de brinde é uma das formas mais fáceis de apresentar informação ao utilizador e está disponível em todas as versões do Android. Não suporta qualquer tipo de entrada do utilizador e é apresentado apenas por um curto período de tempo. Se quiser informar rapidamente o utilizador sobre o que tocou, uma mensagem de torrada pode ser uma boa opção. O código que se segue mostra como uma mensagem de torrada pode ser usada com o `OnFeatureClick` evento.

::: zone pivot="programming-language-java-android"

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, layer.getId());    //Limit this event to the symbol layer.
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Add a feature click event to the map.
map.events.add(OnFeatureClick { features: List<Feature> ->
    //Retrieve the title property of the feature as a string.
    val msg = features[0].getStringProperty("title")

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show()
}, layer.getId()) //Limit this event to the symbol layer.
```

::: zone-end

![Animação de uma funcionalidade a ser tocada e uma mensagem de torrada a ser exibida](media/display-feature-information-android/symbol-layer-click-toast-message.gif)

Além de brindar mensagens, existem muitas outras formas de apresentar as propriedades dos metadados de uma funcionalidade, tais como:

- [Widget](https://developer.android.com/training/snackbar/showing.html)  -  `Snackbars` snackbar fornecer feedback leve sobre uma operação. Mostram uma breve mensagem na parte inferior do ecrã em dispositivos móveis e inferiores à esquerda em dispositivos maiores. `Snackbars` aparecem acima de todos os outros elementos no ecrã e apenas um pode ser exibido de cada vez.
- [Diálogos](https://developer.android.com/guide/topics/ui/dialogs) - Um diálogo é uma pequena janela que leva o utilizador a tomar uma decisão ou a introduzir informações adicionais. Um diálogo não preenche o ecrã e é normalmente utilizado para eventos modais que exigem que os utilizadores tomem medidas antes de poderem continuar.
- Adicione um [fragmento](https://developer.android.com/guide/components/fragments) à atividade atual.
- Navegue para outra atividade ou vista.

## <a name="display-a-popup"></a>Exibir um pop-up

O Azure Maps Android SDK fornece uma `Popup` classe que facilita a criação de elementos de anotação UI que estão ancorados a uma posição no mapa. Para popups você tem que passar numa vista com um layout relativo para a `content` opção do popup. Aqui está um exemplo de layout simples que exibe texto escuro em cima de um fundo de tempo.

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:orientation="vertical"
    android:background="#ffffff"
    android:layout_margin="8dp"
    android:padding="10dp"

    android:layout_height="match_parent">

    <TextView
        android:id="@+id/message"
        android:layout_width="wrap_content"
        android:text=""
        android:textSize="18dp"
        android:textColor="#222"
        android:layout_height="wrap_content"
        android:width="200dp"/>

</RelativeLayout>
```

Assumindo que o layout acima é armazenado num ficheiro chamado `popup_text.xml` na pasta de uma `res -> layout` aplicação, o seguinte código cria um pop-up, adiciona-o ao mapa. Quando uma funcionalidade é clicada, a `title` propriedade é exibida usando o `popup_text.xml` layout, com o centro inferior do layout ancorado à posição especificada no mapa.

::: zone pivot="programming-language-java-android"

```java
//Create a popup and add it to the map.
Popup popup = new Popup();
map.popups.add(popup);

map.events.add((OnFeatureClick)(feature) -> {
    //Get the first feature and it's properties.
    Feature f = feature.get(0);
    JsonObject props = f.properties();

    //Retrieve the custom layout for the popup.
    View customView = LayoutInflater.from(this).inflate(R.layout.popup_text, null);

    //Access the text view within the custom view and set the text to the title property of the feature.
    TextView tv = customView.findViewById(R.id.message);
    tv.setText(props.get("title").getAsString());

    //Get the coordinates from the clicked feature and create a position object.
    List<Double> c = ((Point)(f.geometry())).coordinates();
    Position pos = new Position(c.get(0), c.get(1));

    //Set the options on the popup.
    popup.setOptions(
        //Set the popups position.
        position(pos),

        //Set the anchor point of the popup content.
        anchor(AnchorType.BOTTOM),

        //Set the content of the popup.
        content(customView)

        //Optionally, hide the close button of the popup.
        //, closeButton(false)
    );

    //Open the popup.
    popup.open();
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a popup and add it to the map.
val popup = Popup()
map.popups.add(popup)

map.events.add(OnFeatureClick { feature: List<Feature> ->
    //Get the first feature and it's properties.
    val f = feature[0]
    val props = f.properties()

    //Retrieve the custom layout for the popup.
    val customView: View = LayoutInflater.from(this).inflate(R.layout.popup_text, null)

    //Access the text view within the custom view and set the text to the title property of the feature.
    val tv: TextView = customView.findViewById(R.id.message)
    tv.text = props!!["title"].asString

    //Get the coordinates from the clicked feature and create a position object.
    val c: List<Double> = (f.geometry() as Point?).coordinates()
    val pos = Position(c[0], c[1])

    //Set the options on the popup.
    popup.setOptions( 
        //Set the popups position.
        position(pos),  

        //Set the anchor point of the popup content.
        anchor(AnchorType.BOTTOM),  

        //Set the content of the popup.
        content(customView) 

        //Optionally, hide the close button of the popup.
        //, closeButton(false)
    )

    //Open the popup.
    popup.open()
})
```

::: zone-end

A captura de ecrã a seguir mostra popups aparecendo quando as funcionalidades são clicadas e ancoradas à sua localização especificada no mapa à medida que se move.

![Animação de um popup sendo exibido e o mapa movido com o popup ancorado a uma posição no mapa](./media/display-feature-information-android/android-popup.gif)

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
