---
title: Adicione uma camada de símbolo aos mapas Android | Microsoft Azure Maps
description: Aprenda a adicionar um marcador a um mapa. Veja um exemplo que usa o Azure Maps Android SDK para adicionar uma camada de símbolo que contém dados baseados em pontos a partir de uma fonte de dados.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: edb758469a06dcb7914025ea449b9d952e939533
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097215"
---
# <a name="add-a-symbol-layer-android-sdk"></a>Adicione uma camada de símbolo (Android SDK)

Este artigo mostra-lhe como renderizar dados de pontos de uma fonte de dados como uma camada de símbolo num mapa usando o Azure Maps Android SDK. As camadas de símbolos tornam os pontos como uma imagem e texto no mapa.

> [!TIP]
> As camadas de símbolo por defeito tornarão as coordenadas de todas as geometrias numa fonte de dados. Para limitar a camada de modo a que apenas torne as características de geometria do ponto, deite `filter` a opção da camada para `eq(geometryType(), "Point")` . Se também pretender incluir funcionalidades MultiPoint, deite `filter` a opção da camada para `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` .

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de completar os passos no [Quickstart: Criar um documento de aplicação Android.](quick-android-map.md) Os blocos de código deste artigo podem ser inseridos no manipulador de eventos de `onReady` mapas.

## <a name="add-a-symbol-layer"></a>Adicionar uma camada de símbolo

Antes de poder adicionar uma camada de símbolo ao mapa, tem de dar alguns passos. Primeiro, crie uma fonte de dados e adicione-a ao mapa. Crie uma camada de símbolo. Em seguida, passe na fonte de dados para a camada de símbolo, para recuperar os dados da fonte de dados. Finalmente, adicione dados na fonte de dados, para que haja algo a ser renderizado.

O código abaixo demonstra o que deve ser adicionado ao mapa depois de carregado. Esta amostra torna um único ponto no mapa usando uma camada de símbolo.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source);

//Add the layer to the map.
map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0))

//Create a symbol layer to render icons and/or text at points on the map.
val layer = SymbolLayer(source)

//Add the layer to the map.
map.layers.add(layer)
```

::: zone-end

Existem três tipos diferentes de dados de pontos que podem ser adicionados ao mapa:

- Geometria geojson Point - Este objeto contém apenas uma coordenada de um ponto e nada mais. O `Point.fromLngLat` método estático pode ser usado para criar facilmente estes objetos.
- GeoJSON MultiPoint geometria - Este objeto contém as coordenadas de vários pontos e nada mais. Passe uma série de pontos para a `MultiPoint` classe para criar estes objetos.
- Característica GeoJSON - Este objeto consiste em qualquer geometria GeoJSON e um conjunto de propriedades que contêm metadados associados à geometria.

Para obter mais informações, consulte o [Documento de Origem de Dados](create-data-source-android-sdk.md) da Criação e a adição de dados ao mapa.

A amostra de código que se segue cria uma geometria geoJSON Point e passa-a para a Funcionalidade GeoJSON e tem um `title` valor acrescentado às suas propriedades. A `title` propriedade é exibida como texto acima do ícone do símbolo no mapa.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(0, 0));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source, 
    //Get the title property of the feature and display it on the map.
    textField(get("title"))
);

//Add the layer to the map.
map.layers.add(layer);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(0, 0))

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!")

//Add the feature to the data source.
source.add(feature)

//Create a symbol layer to render icons and/or text at points on the map.
val layer = SymbolLayer(
    source,  //Get the title property of the feature and display it on the map.
    textField(get("title"))
)

//Add the layer to the map.
map.layers.add(layer)
```

::: zone-end

A imagem que se segue mostra o código acima a rending uma função de ponto usando um ícone e etiqueta de texto com uma camada de símbolo.

![Mapa com ponto renderizado usando uma camada de símbolo exibindo um ícone e rótulo de texto para uma característica de ponto](media/how-to-add-symbol-to-android-map/android-map-pin.png)

> [!TIP]
> Por padrão, as camadas de símbolos otimizam a renderização de símbolos escondendo símbolos que se sobrepõem. À medida que se aproxima, os símbolos ocultos tornam-se visíveis. Para desativar esta função e tornar todos os símbolos em todos os momentos, desative as `iconAllowOverlap` opções e `textAllowOverlap` as opções para `true` .

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Adicione um ícone personalizado a uma camada de símbolo

As camadas de símbolo são renderizadas através do WebGL. Como tal, todos os recursos, como imagens de ícones, devem ser carregados no contexto WebGL. Esta amostra mostra como adicionar um ícone personalizado aos recursos do mapa. Este ícone é então utilizado para renderizar dados de pontos com um símbolo personalizado no mapa. A `textField` propriedade da camada de símbolo requer uma expressão a especificar. Neste caso, queremos tornar a propriedade da temperatura. Como a temperatura é um número, tem de ser convertido numa corda. Além disso, queremos anexar-lhe "°F". Uma expressão pode ser usada para fazer esta concatenação; `concat(Expression.toString(get("temperature")), literal("°F"))`.

::: zone pivot="programming-language-java-android"

```java
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773));

//Add a property to the feature.
feature.addNumberProperty("temperature", 64);

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),

    //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(new Float[]{0f, -1.5f})
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers)

//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a point feature.
val feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773))

//Add a property to the feature.
feature.addNumberProperty("temperature", 64)

//Add the feature to the data source.
source.add(feature)

//Create a symbol layer to render icons and/or text at points on the map.
val layer = SymbolLayer(
    source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),  //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(arrayOf(0f, -1.5f))
)
```

::: zone-end

Para esta amostra, a imagem a seguir foi carregada na pasta desaudante da aplicação.

| ![Imagem de ícone do tempo de chuveiros de chuva](media/how-to-add-symbol-to-android-map/showers.png)|
|:-----------------------------------------------------------------------:|
| showers.png                                                  |

A imagem que se segue mostra o código acima a rending uma característica de ponto usando um ícone personalizado e etiqueta de texto formatada com uma camada de símbolo.

![Mapa com ponto renderizado usando uma camada de símbolo exibindo um ícone personalizado e etiqueta de texto formatada para uma característica de ponto](media/how-to-add-symbol-to-android-map/android-custom-symbol-layer.png)

> [!TIP]
> Quando pretender renderizar apenas texto com uma camada de símbolo, pode ocultar o ícone definindo a `iconImage` propriedade das opções do ícone para `"none"` .

## <a name="modify-symbol-colors"></a>Modificar as cores dos símbolos

O Azure Maps Android SDK vem com um conjunto de variações de cores predefinidas do ícone do marcador padrão. Por exemplo, `marker-red` pode ser passado para a `iconImage` opção de uma camada de símbolo para renderizar uma versão vermelha do ícone do marcador nessa camada.

::: zone pivot="programming-language-java-android"

```java
SymbolLayer layer = new SymbolLayer(source,
    iconImage("marker-red")
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = SymbolLayer(source,
    iconImage("marker-red")
)
```

::: zone-end

A tabela abaixo lista todos os nomes de imagem de ícone incorporados disponíveis. Todos estes marcadores retiram as suas cores dos recursos coloridos que podes sobrepor-te. Além de sobressarir a cor de preenchimento principal deste marcador. No entanto, note que sobrevair a cor de um destes marcadores seria aplicável a todas as camadas que usam essa imagem de ícone.

| Nome de imagem de ícone | Nome de recurso de cor |
|-----------------|---------------------|
| `marker-default` | `mapcontrol_marker_default` |
| `marker-black` | `mapcontrol_marker_black` |
| `marker-blue` | `mapcontrol_marker_blue` |
| `marker-darkblue` | `mapcontrol_marker_darkblue` |
| `marker-red` | `mapcontrol_marker_red` |
| `marker-yellow` | `mapcontrol_marker_yellow` |

Também pode sobrepor a cor da borda de todos os marcadores usando o `mapcontrol_marker_border` nome de recurso de cor. As cores destes marcadores podem ser ultrapassadas adicionando uma cor com o mesmo nome no `colors.xml` ficheiro da sua aplicação. Por exemplo, o ficheiro a seguir `colors.xml` tornaria a cor do marcador padrão verde brilhante.

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="mapcontrol_marker_default">#00FF00</color>
</resources>
```

Segue-se uma versão modificada do vetor de marcador padrão XML que pode modificar para criar versões personalizadas adicionais do marcador predefinido. A versão modificada pode ser adicionada à `drawable` pasta da sua aplicação e adicionada à imagem do mapa que o sprite `map.images.add` usa, depois usada com uma camada de símbolo.

```xml
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:width="24.5dp"
    android:height="36.5dp"
    android:viewportWidth="24.5"
    android:viewportHeight="36.5">
    <path
        android:pathData="M12.25,0.25a12.2543,12.2543 0,0 0,-12 12.4937c0,6.4436 6.4879,12.1093 11.059,22.5641 0.5493,1.2563 1.3327,1.2563 1.882,0C17.7621,24.8529 24.25,19.1857 24.25,12.7437A12.2543,12.2543 0,0 0,12.25 0.25Z"
        android:strokeWidth="0.5"
        android:fillColor="@color/mapcontrol_marker_default"
        android:strokeColor="@color/mapcontrol_marker_border"/>
</vector>
```

## <a name="next-steps"></a>Passos seguintes

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Criar uma origem de dados](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolha](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Utilizar expressões de estilo com base em dados](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Apresentar informações da funcionalidade](display-feature-information-android.md)
