---
title: Adicione uma camada de imagem a um mapa Android | Microsoft Azure Maps
description: Saiba como adicionar imagens a um mapa. Veja como usar o Azure Maps Android SDK para personalizar camadas de imagem e sobrepor imagens em conjuntos fixos de coordenadas.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: e1d99297c0357039606149bdf7e5a526258fc7c5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102054694"
---
# <a name="add-an-image-layer-to-a-map-android-sdk"></a>Adicione uma camada de imagem a um mapa (Android SDK)

Este artigo mostra como sobrepor uma imagem a um conjunto fixo de coordenadas. Aqui estão alguns exemplos de diferentes tipos de imagens que podem ser sobrepostos em mapas:

* Imagens captadas de drones
* Plantações de edifícios
* Imagens históricas ou outras imagens de mapas especializados
* Plantas de locais de trabalho

> [!TIP]
> Uma camada de imagem é uma maneira fácil de sobrepor uma imagem num mapa. Note que grandes imagens podem consumir muita memória e potencialmente causar problemas de desempenho. Neste caso, considere dividir a sua imagem em azulejos e carregá-las no mapa como uma camada de azulejos.

## <a name="add-an-image-layer"></a>Adicionar uma camada de imagem

O seguinte código sobrepõe uma imagem de um [mapa de Newark, Nova Jersey, de 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) no mapa. Esta imagem é adicionada à `drawable` pasta do projeto. Uma camada de imagem é criada definindo a imagem e coordenadas para os quatro cantos no formato `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` . Adicionar camadas de imagem abaixo da `label` camada é muitas vezes desejável.

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setImage(R.drawable.newark_nj_1922)
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216)   //Bottom Left Corner
        )
    ),
    setImage(R.drawable.newark_nj_1922)
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

Alternativamente, pode ser especificado um URL a uma imagem hospedada na internet. No entanto, se o seu cenário permitir, adicione a imagem à pasta dos seus `drawable` projetos, que será carregada mais rapidamente uma vez que a imagem estará disponível localmente e não terá de ser descarregada.

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216) //Bottom Left Corner
        )
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

A imagem que se segue mostra um mapa de Newark, New Jersey, de 1922 sobreposto usando uma camada de imagem.

![Mapa de Newark, Nova Jersey, de 1922 sobreposto usando uma camada de imagem](media/map-add-image-layer-android/android-image-layer.gif)

## <a name="import-a-kml-file-as-ground-overlay"></a>Importar um ficheiro KML como sobreposição de solo

Esta amostra demonstra como adicionar informações sobrepostas ao solo KML como uma camada de imagem no mapa. As sobreposições de terra kml fornecem coordenadas norte, sul, leste e oeste, e uma rotação no sentido contrário ao dos ponteiros do relógio. Mas, a camada de imagem espera coordenadas para cada canto da imagem. A sobreposição de terra KML nesta amostra é para a catedral de Chartres, e é proveniente da [Wikimedia.](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kml xmlns="http://www.opengis.net/kml/2.2" xmlns:gx="http://www.google.com/kml/ext/2.2" xmlns:kml="http://www.opengis.net/kml/2.2" xmlns:atom="http://www.w3.org/2005/Atom">
<GroundOverlay>
    <name>Map of Chartres cathedral</name>
    <Icon>
        <href>https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png</href>
        <viewBoundScale>0.75</viewBoundScale>
    </Icon>
    <LatLonBox>
        <north>48.44820923628113</north>
        <south>48.44737203258976</south>
        <east>1.488833825534365</east>
        <west>1.486788581643038</west>
        <rotation>46.44067597839695</rotation>
    </LatLonBox>
</GroundOverlay>
</kml>
```

O código utiliza o método estático `getCoordinatesFromEdges` da `ImageLayer` classe. Este método calcula os quatro cantos da imagem utilizando a informação de norte, sul, leste, oeste e rotação da sobreposição do solo KML.

::: zone pivot="programming-language-java-android"

```java
//Calculate the corner coordinates of the ground overlay.
Position[] corners = ImageLayer.getCoordinatesFromEdges(
    //North, south, east, west
    48.44820923628113, 48.44737203258976, 1.488833825534365, 1.486788581643038,

    //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
    360 - 46.44067597839695
);

//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Calculate the corner coordinates of the ground overlay.
val corners: Array<Position> =
    ImageLayer.getCoordinatesFromEdges( //North, south, east, west
        48.44820923628113,
        48.44737203258976,
        1.488833825534365,
        1.486788581643038,  //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
        360 - 46.44067597839695
    )

//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

A imagem que se segue mostra um mapa com uma sobreposição de solo KML sobreposta usando uma camada de imagem.

![Mapa com uma sobreposição de solo KML sobreposta usando uma camada de imagem](media/map-add-image-layer-android/android-ground-overlay.jpg)

> [!TIP]
> Utilize os `getPixels` métodos e `getPositions` métodos da classe de camada de imagem para converter entre coordenadas geográficas da camada de imagem posicionada e as coordenadas de pixel de imagem local.

## <a name="next-steps"></a>Passos seguintes

Veja o seguinte artigo para saber mais sobre formas de sobrepor imagens num mapa.

> [!div class="nextstepaction"]
> [Adicionar uma camada de mosaico](how-to-add-tile-layer-android-map.md)