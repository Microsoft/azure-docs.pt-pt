---
title: Adicione uma camada de azulejos a um mapa Microsoft Azure Maps
description: Neste artigo, aprenderá a sobrepor uma camada de azulejos num mapa utilizando o Microsoft Azure Maps Web SDK. As camadas de azulejos permitem-lhe renderizar imagens num mapa.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 61d7a11df499e6b740adb45968721b6a9bb1af22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988605"
---
# <a name="add-a-tile-layer-to-a-map"></a>Adicionar uma camada de mosaico a um mapa

Este artigo mostra-lhe como sobrepor uma camada de azulejos no mapa. As camadas de azulejos permitem-lhe sobrepor imagens em cima dos azulejos do mapa base do Azure Maps. Para obter mais informações sobre o sistema de azulejos Azure Maps, consulte os níveis de [zoom e a grelha de azulejos.](zoom-levels-and-tile-grid.md)

Uma camada de azulejos carrega em azulejos de um servidor. Estas imagens podem ser pré-renderizadas ou renderizadas dinamicamente. As imagens pré-renderizadas são armazenadas como qualquer outra imagem num servidor usando uma convenção de nomeação que a camada de azulejos entende. Imagens renderizadas dinamicamente usam um serviço para carregar as imagens perto do tempo real. Existem três diferentes convenções de nomeação de serviços de azulejos apoiadas pela classe Azure Maps [TileLayer:](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) 

* X, Y, Zoom notação - X é a coluna, Y é a posição da linha do azulejo na grelha de azulejos, e a notação zoom um valor baseado no nível de zoom.
* Notação quadkey - Combina informações x, y e zoom num único valor de corda. Este valor de cadeia torna-se um identificador único para um único azulejo.
* Caixa de delimitação - Especifique uma `{west},{south},{east},{north}`imagem no formato de coordenadas de delimitação: . Este formato é comumente utilizado pelos Serviços de [Mapeamento Web (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> A [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) é uma ótima maneira de visualizar grandes conjuntos de dados no mapa. Não só uma camada de azulejo pode ser gerada a partir de uma imagem, como os dados vetoriais também podem ser renderizados como uma camada de azulejos também. Ao tornar os dados vetoriais como uma camada de azulejos, o controlo do mapa só precisa de carregar os azulejos que são menores no tamanho do ficheiro do que os dados vetoriais que representam. Esta técnica é comumente usada para renderizar milhões de linhas de dados no mapa.

O URL de azulejos passado para uma camada de azulejos deve ser um http ou um URL https para um recurso TileJSON ou um modelo de URL de azulejos que usa os seguintes parâmetros: 

* `{x}`- Posição X do azulejo. Também `{y}` precisa `{z}`e.
* `{y}`- Posição Y do azulejo. Também `{x}` precisa `{z}`e.
* `{z}`- Nível de zoom do azulejo. Também `{x}` precisa `{y}`e.
* `{quadkey}`- Identificador quadkey de azulejos baseado na convenção de nomeação do sistema de azulejos Bing Maps.
* `{bbox-epsg-3857}`- Uma cadeia de caixa `{west},{south},{east},{north}` de delimitação com o formato no Sistema de Referência Espacial EPSG 3857.
* `{subdomain}`- Um espaço reservado para os valores `subdomain` do subdomínio, se especificado o será adicionado.

## <a name="add-a-tile-layer"></a>Adicionar uma camada de mosaico

 Esta amostra mostra como criar uma camada de azulejos que aponta para um conjunto de azulejos. Esta amostra utiliza o sistema de tiling x, y, zoom. A fonte desta camada de azulejos é uma sobreposição de radares meteorológicos do [Iowa Environmental Mesonet da Universidade Estadual de Iowa.](https://mesonet.agron.iastate.edu/ogc/) Ao visualizar dados de radar, idealmente os utilizadores veriam claramente os rótulos das cidades enquanto navegam no mapa. Este comportamento pode ser implementado inserindo `labels` a camada de azulejos abaixo da camada.

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

Abaixo está a amostra completa de código de execução da funcionalidade acima referida.

<br/>

<iframe height='500' scrolling='no' title='Camada de azulejousando X, Y e Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Camada de Azulejos da Caneta <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>utilizando X, Y e Z</a> por Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Personalize uma camada de azulejos

A classe de camada de azulejos tem muitas opções de estilo. Aqui está uma ferramenta para experimentá-los.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de azulejos' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>opções</a> de camada de<a href='https://codepen.io/azuremaps'>@azuremaps</a>pen tile por Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar uma camada de imagem](./map-add-image-layer.md)
