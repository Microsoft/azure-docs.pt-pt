---
title: Adicione uma camada de azulejos aos mapas Android | Microsoft Azure Maps
description: Aprenda a adicionar uma camada de azulejos a um mapa. Veja um exemplo que usa o Azure Maps Android SDK para adicionar uma sobreposição de radares meteorológicos a um mapa.
author: rbrundritt
ms.author: richbrun
ms.date: 3/25/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: ac37a4e6d68decdf6780560963a0c534689e8dbb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608990"
---
# <a name="add-a-tile-layer-to-a-map-android-sdk"></a>Adicione uma camada de azulejo a um mapa (Android SDK)

Este artigo mostra-lhe como renderizar uma camada de azulejos num mapa usando o Azure Maps Android SDK. As camadas de azulejos permitem-lhe sobrepor imagens em cima dos azulejos do mapa base do Azure Maps. Mais informações sobre o sistema de inclinação Azure Maps podem ser encontradas nos níveis de Zoom e documentação [da grelha de azulejos.](zoom-levels-and-tile-grid.md)

Uma camada de azulejos carrega em azulejos de um servidor. Estas imagens podem ser pré-renderizadas e armazenadas como qualquer outra imagem num servidor, usando uma convenção de nomeação que a camada de azulejos compreende. Ou, estas imagens podem ser renderizadas com um serviço dinâmico que gera as imagens perto do tempo real. Existem três diferentes convenções de nomeação de azulejos apoiadas pela classe Azure Maps TileLayer:

* X, Y, Notação zoom - Com base no nível de zoom, x é a coluna e y é a posição de linha do azulejo na grelha de azulejos.
* Notação quadkey - Combinação x, y, zoom informação em um único valor de corda que é um identificador único para um azulejo.
* Caixa limite - As coordenadas de caixa de limites podem ser usadas para especificar uma imagem no `{west},{south},{east},{north}` formato, que é comumente utilizada pelos [Serviços de mapeamento web (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> Um TileLayer é uma ótima maneira de visualizar grandes conjuntos de dados no mapa. Não só uma camada de azulejo pode ser gerada a partir de uma imagem, mas os dados vetoriais também podem ser renderizados como uma camada de azulejos. Ao renderizar dados vetoriais como uma camada de azulejos, o controlo do mapa só precisa de carregar os azulejos, que podem ser muito menores no tamanho do ficheiro do que os dados vetoriais que representam. Esta técnica é usada por muitos que precisam de renderizar milhões de linhas de dados no mapa.

O URL de azulejos passados para uma camada de azulejos deve ser um URL http/https para um recurso TileJSON ou um modelo de URL de azulejos que utiliza os seguintes parâmetros:

* `{x}` - X posição do azulejo. Também precisa `{y}` `{z}` e. .
* `{y}` - Posição Y do azulejo. Também precisa `{x}` `{z}` e. .
* `{z}` - Zoom nível do azulejo. Também precisa `{x}` `{y}` e. .
* `{quadkey}` - Identificador de azulejos de azulejos baseado na convenção de nomeação do sistema de azulejos Bing Maps.
* `{bbox-epsg-3857}` - Uma cadeia de caixa de limitação com o formato `{west},{south},{east},{north}` no Sistema de Referência Espacial EPSG 3857.
* `{subdomain}` - Um espaço reservado para os valores do subdomínio, se o valor do subdomínio for especificado.
* `azmapsdomain.invalid` - Um espaço reservado para alinhar o domínio e a autenticação de pedidos de azulejos com os mesmos valores utilizados pelo mapa. Utilize isto ao chamar um serviço de azulejos hospedado pelo Azure Maps.

## <a name="prerequisites"></a>Pré-requisitos

Para completar o processo neste artigo, precisa instalar [o Azure Maps Android SDK](how-to-use-android-map-control-library.md) para carregar um mapa.

## <a name="add-a-tile-layer-to-the-map"></a>Adicione uma camada de azulejo ao mapa

Esta amostra mostra como criar uma camada de azulejos que aponta para um conjunto de azulejos. Esta amostra utiliza o sistema de inclinação "x, y, zoom". A fonte desta camada de azulejos é o [projeto OpenSeaMap,](https://openseamap.org/index.php)que contém gráficos náuticos de multidões. Muitas vezes, ao ver camadas de azulejos, é desejável ser capaz de ver claramente os rótulos das cidades no mapa. Este comportamento pode ser alcançado inserindo a camada de azulejos abaixo das camadas de etiqueta do mapa.

::: zone pivot="programming-language-java-android"

```java
TileLayer layer = new TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
)

map.layers.add(layer, "labels")
```

::: zone-end

A imagem que se segue mostra o código acima mostrando uma camada de azulejos de informações náuticas num mapa que tem um estilo escuro em tons de cinza.

![Mapa android exibindo camada de azulejo](media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)

## <a name="add-an-ogc-web-mapping-service-wms"></a>Adicione um serviço de mapeamento web OGC (WMS)

Um serviço de mapeamento web (WMTS) é um padrão de Consórcio Geoespacial Aberto (OGC) para servir imagens de dados do mapa. Existem muitos conjuntos de dados abertos disponíveis neste formato que pode utilizar com o Azure Maps. Este tipo de serviço pode ser utilizado com uma camada de azulejo se o serviço suportar o `EPSG:3857` sistema de referência de coordenadas (CRS). Ao utilizar um serviço WMS, desajuste os parâmetros de largura e altura para o mesmo valor que é suportado pelo serviço, certifique-se de definir este mesmo valor na `tileSize` opção. No URL formatado, desabrocora o `BBOX` parâmetro do serviço com o espaço `{bbox-epsg-3857}` reservado.

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
)

map.layers.add(layer, "labels")
```

::: zone-end

A imagem a seguir mostra o código acima que sobrepondo um serviço de mapeamento web de dados geológicos do [U.S. Geological Survey (USGS)](https://mrdata.usgs.gov/) em cima de um mapa, abaixo das etiquetas.

![Mapa android exibindo camada de azulejos WMS](media/how-to-add-tile-layer-android-map/android-tile-layer-wms.jpg)

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>Adicione um serviço de azulejos de mapeamento web OGC (WMTS)

Um serviço de azulejos de mapeamento web (WMTS) é um padrão de Consórcio Geoespacial Aberto (OGC) para servir sobreposições à base de azulejos para mapas. Existem muitos conjuntos de dados abertos disponíveis neste formato que pode utilizar com o Azure Maps. Este tipo de serviço pode ser utilizado com uma camada de azulejo se o serviço suportar o `EPSG:3857` sistema de referência ou coordenar `GoogleMapsCompatible` (CRS). Ao utilizar um serviço WMTS, desajuste os parâmetros de largura e altura para o mesmo valor que é suportado pelo serviço, certifique-se de definir este mesmo valor na `tileSize` opção. No URL formatado, substitua os seguintes espaços reservados em conformidade:

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
);

map.layers.add(layer, "transit");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
)

map.layers.add(layer, "transit")
```

::: zone-end

A imagem a seguir mostra o código acima sobrepondo um serviço de azulejos de mapeamento web de imagens do Mapa Nacional do [Inquérito Geológico dos EUA (USGS)](https://viewer.nationalmap.gov/services/) em cima de um mapa, abaixo das estradas e etiquetas.

![Mapa android mostrando camada de azulejos WMTS](media/how-to-add-tile-layer-android-map/android-tile-layer-wmts.jpg)

## <a name="next-steps"></a>Passos seguintes

Veja o seguinte artigo para saber mais sobre formas de sobrepor imagens num mapa.

> [!div class="nextstepaction"]
> [Camada de imagem](map-add-image-layer-android.md)
