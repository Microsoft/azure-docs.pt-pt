---
title: Adicionar uma camada de bloco a um mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a sobrepor uma camada de bloco em um mapa usando o SDK da Web do Microsoft Azure Maps. As camadas de bloco permitem renderizar imagens em um mapa.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 61d7a11df499e6b740adb45968721b6a9bb1af22
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988605"
---
# <a name="add-a-tile-layer-to-a-map"></a>Adicionar uma camada de mosaico a um mapa

Este artigo mostra-lhe como sobrepor uma camada de azulejos no mapa. As camadas de bloco permitem sobreimpor imagens na parte superior dos blocos de mapa base do Azure Maps. Para obter mais informações sobre o sistema de azulejos Azure Maps, consulte os níveis de [zoom e a grelha de azulejos.](zoom-levels-and-tile-grid.md)

Uma camada de bloco é carregada em blocos de um servidor. Estas imagens podem ser pré-renderizadas ou renderizadas dinamicamente. As imagens pré-renderizadas são armazenadas como qualquer outra imagem num servidor usando uma convenção de nomeação que a camada de azulejos entende. Imagens renderizadas dinamicamente usam um serviço para carregar as imagens perto do tempo real. Há três convenções de nomenclatura de serviço de bloco diferentes com suporte pela classe [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) do Azure Maps: 

* X, Y, Zoom notação - X é a coluna, Y é a posição da linha do azulejo na grelha de azulejos, e a notação zoom um valor baseado no nível de zoom.
* Notação quadkey - Combina informações x, y e zoom num único valor de corda. Este valor de cadeia torna-se um identificador único para um único azulejo.
* Caixa de delimitação - Especifique uma imagem no formato de coordenadas de delimitação: `{west},{south},{east},{north}`. Este formato é comumente utilizado pelos Serviços de [Mapeamento Web (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> Um [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) é uma ótima maneira de Visualizar grandes conjuntos de dados no mapa. Não só uma camada de azulejo pode ser gerada a partir de uma imagem, como os dados vetoriais também podem ser renderizados como uma camada de azulejos também. Ao tornar os dados vetoriais como uma camada de azulejos, o controlo do mapa só precisa de carregar os azulejos que são menores no tamanho do ficheiro do que os dados vetoriais que representam. Esta técnica é comumente usada para renderizar milhões de linhas de dados no mapa.

O URL de azulejos passado para uma camada de azulejos deve ser um http ou um URL https para um recurso TileJSON ou um modelo de URL de azulejos que usa os seguintes parâmetros: 

* posição `{x}`-X do bloco. Também precisa de `{y}` e `{z}`.
* posição `{y}`-Y do bloco. Também precisa de `{x}` e `{z}`.
* `{z}`-nível de zoom do bloco. Também precisa de `{x}` e `{y}`.
* identificador de quadkey de bloco de `{quadkey}` com base na Convenção de nomenclatura do sistema de blocos do Bing Maps.
* `{bbox-epsg-3857}`-uma cadeia de caracteres de caixa delimitadora com o formato `{west},{south},{east},{north}` no sistema de referência espacial do EPSG 3857.
* `{subdomain}` - Um espaço reservado para os valores do subdomínio, se especificado o `subdomain` será adicionado.

## <a name="add-a-tile-layer"></a>Adicionar uma camada de mosaico

 Esta amostra mostra como criar uma camada de azulejos que aponta para um conjunto de azulejos. Esta amostra utiliza o sistema de tiling x, y, zoom. A origem dessa camada de peça é uma sobreposição de radar do [Iowa ambiente Mesonet da Universidade de estado do Iowa](https://mesonet.agron.iastate.edu/ogc/). Ao visualizar dados de radar, idealmente os utilizadores veriam claramente os rótulos das cidades enquanto navegam no mapa. Este comportamento pode ser implementado inserindo a camada de azulejos abaixo da camada `labels`.

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

Abaixo está o exemplo de código completo em execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Camada de bloco usando X, Y e Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a camada de bloco de caneta <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>usando X, Y e Z</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Personalizar uma camada de peça

A classe da camada do bloco tem muitas opções de estilo. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='Opções da camada de peças' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>Opções da camada de bloco</a> de caneta pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar uma camada de imagem](./map-add-image-layer.md)
