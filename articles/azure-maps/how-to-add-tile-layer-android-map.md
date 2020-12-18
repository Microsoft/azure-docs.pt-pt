---
title: Adicione uma camada de azulejos aos mapas Android Microsoft Azure Maps
description: Aprenda a adicionar uma camada de azulejos a um mapa. Veja um exemplo que usa o Azure Maps Android SDK para adicionar uma sobreposição de radares meteorológicos a um mapa.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 8ea6f44c47c5cd4d223b053640f65827f46db482
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679306"
---
# <a name="add-a-tile-layer-to-a-map-android-sdk"></a>Adicione uma camada de azulejo a um mapa (Android SDK)

Este artigo mostra-lhe como renderizar uma camada de azulejos num mapa usando o Azure Maps Android SDK. As camadas de azulejos permitem-lhe sobrepor imagens em cima dos azulejos do mapa base do Azure Maps. Mais informações sobre o sistema de inclinação Azure Maps podem ser encontradas nos níveis de Zoom e documentação [da grelha de azulejos.](zoom-levels-and-tile-grid.md)

Uma camada de azulejos carrega em azulejos de um servidor. Estas imagens podem ser pré-renderizadas e armazenadas como qualquer outra imagem num servidor, usando uma convenção de nomeação que a camada de azulejos compreende. Ou, estas imagens podem ser renderizadas com um serviço dinâmico que gera as imagens perto do tempo real. Existem três diferentes convenções de nomeação de azulejos apoiadas pela classe Azure Maps TileLayer:

* X, Y, Notação zoom - Com base no nível de zoom, x é a coluna e y é a posição de linha do azulejo na grelha de azulejos.
* Notação quadkey - Combinação x, y, zoom informação em um único valor de corda que é um identificador único para um azulejo.
* Caixa limite - As coordenadas de caixa de limites podem ser usadas para especificar uma imagem no formato `{west},{south},{east},{north}` , que é comumente usada pelos [Serviços de Mapeamento Web (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> Um TileLayer é uma ótima maneira de visualizar grandes conjuntos de dados no mapa. Não só uma camada de azulejo pode ser gerada a partir de uma imagem, mas os dados vetoriais também podem ser renderizados como uma camada de azulejos. Ao renderizar dados vetoriais como uma camada de azulejos, o controlo do mapa só precisa de carregar os azulejos, que podem ser muito menores no tamanho do ficheiro do que os dados vetoriais que representam. Esta técnica é usada por muitos que precisam de renderizar milhões de linhas de dados no mapa.

O URL de azulejos passados para uma camada de azulejos deve ser um URL http/https para um recurso TileJSON ou um modelo de URL de azulejos que utiliza os seguintes parâmetros: 

* `{x}` - X posição do azulejo. Também precisa `{y}` `{z}` e. .
* `{y}` - Posição Y do azulejo. Também precisa `{x}` `{z}` e. .
* `{z}` - Zoom nível do azulejo. Também precisa `{x}` `{y}` e. .
* `{quadkey}` - Identificador de azulejos de azulejos baseado na convenção de nomeação do sistema de azulejos Bing Maps.
* `{bbox-epsg-3857}` - Uma cadeia de caixa de limitação com o formato `{west},{south},{east},{north}` no Sistema de Referência Espacial EPSG 3857.
* `{subdomain}` - Um espaço reservado para os valores do subdomínio, se o valor do subdomínio for especificado.

## <a name="prerequisites"></a>Pré-requisitos

Para completar o processo neste artigo, precisa instalar [o Azure Maps Android SDK](how-to-use-android-map-control-library.md) para carregar um mapa.

## <a name="add-a-tile-layer-to-the-map"></a>Adicione uma camada de azulejo ao mapa

Esta amostra mostra como criar uma camada de azulejos que aponta para um conjunto de azulejos. Esta amostra utiliza o sistema de inclinação "x, y, zoom". A fonte desta camada de azulejos é o [projeto OpenSeaMap,](https://openseamap.org/index.php)que contém gráficos náuticos de multidões. Muitas vezes, ao ver camadas de azulejos, é desejável ser capaz de ver claramente os rótulos das cidades no mapa. Este comportamento pode ser alcançado inserindo a camada de azulejos abaixo das camadas de etiqueta do mapa.

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

A imagem que se segue mostra o código acima mostrando uma camada de azulejos de informações náuticas num mapa que tem um estilo escuro em tons de cinza.

![Mapa android exibindo camada de azulejo](media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)

## <a name="next-steps"></a>Passos seguintes

Veja o seguinte artigo para saber mais sobre formas de definir estilos de mapa

> [!div class="nextstepaction"]
> [Alterar o estilo do mapa](set-android-map-styles.md)

> [!div class="nextstepaction"]
> [Adicione um mapa de calor](map-add-heat-map-layer-android.md)
