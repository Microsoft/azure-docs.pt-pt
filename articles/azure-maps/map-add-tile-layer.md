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
ms.openlocfilehash: 83e8f6d684d6d39102fd682653cd19816a9f7b10
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911093"
---
# <a name="add-a-tile-layer-to-a-map"></a>Adicionar uma camada de mosaico a um mapa

Este artigo mostra como você pode sobrepor uma camada de bloco no mapa. As camadas de bloco permitem sobreimpor imagens na parte superior dos blocos de mapa base do Azure Maps. Mais informações sobre o sistema de divisão de mapas do Azure podem ser encontradas na documentação [níveis de zoom e grade de blocos](zoom-levels-and-tile-grid.md) .

Uma camada de bloco é carregada em blocos de um servidor. Essas imagens podem ser previamente renderizadas e armazenadas como qualquer outra imagem em um servidor usando uma Convenção de nomenclatura que a camada de bloco entenda ou um serviço dinâmico que gera as imagens em tempo real. Há três convenções de nomenclatura de serviço de bloco diferentes com suporte pela classe [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) do Azure Maps: 

* X, Y, aplicar notação de zoom-com base no nível de zoom, x é a coluna e Y é a posição de linha do bloco na grade de blocos.
* Notação de Quadkey – combinação x, y, informações de zoom em um único valor de cadeia de caracteres que é um identificador exclusivo para um bloco.
* A caixa delimitadora – as coordenadas da caixa delimitadora podem ser usadas para especificar uma imagem no formato `{west},{south},{east},{north}` que é normalmente usado pelos [serviços de mapeamento da Web (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> Um [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) é uma ótima maneira de Visualizar grandes conjuntos de dados no mapa. Uma camada de bloco não só pode ser gerada a partir de uma imagem, mas os dados vetoriais também podem ser renderizados como uma camada de bloco. Ao renderizar dados de vetor como uma camada de bloco, o controle de mapa só precisa carregar os blocos que podem ser muito menores no tamanho do arquivo do que os dados de vetor que eles representam. Essa técnica é usada por muitos que precisam renderizar milhões de linhas de dados no mapa.

A URL do bloco passada para uma camada de bloco deve ser uma URL http/https para um recurso TileJSON ou um modelo de URL de bloco que usa os seguintes parâmetros: 

* posição `{x}`-X do bloco. Também precisa de `{y}` e `{z}`.
* posição `{y}`-Y do bloco. Também precisa de `{x}` e `{z}`.
* `{z}`-nível de zoom do bloco. Também precisa de `{x}` e `{y}`.
* identificador de quadkey de bloco de `{quadkey}` com base na Convenção de nomenclatura do sistema de blocos do Bing Maps.
* `{bbox-epsg-3857}`-uma cadeia de caracteres de caixa delimitadora com o formato `{west},{south},{east},{north}` no sistema de referência espacial do EPSG 3857.
* `{subdomain}`-um espaço reservado onde os valores de subdomínio se especificados serão adicionados.

## <a name="add-a-tile-layer"></a>Adicionar uma camada de mosaico

 Este exemplo mostra como criar uma camada de peça que aponta para um conjunto de blocos que usam o sistema de divisão x, y e zoom. A origem dessa camada de peça é uma sobreposição de radar do [Iowa ambiente Mesonet da Universidade de estado do Iowa](https://mesonet.agron.iastate.edu/ogc/). Ao exibir dados de radar, o ideal é que os usuários possam ver claramente os rótulos das cidades à medida que navegam no mapa, o que pode ser feito inserindo-se a camada de peça abaixo da camada de `labels`.

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
