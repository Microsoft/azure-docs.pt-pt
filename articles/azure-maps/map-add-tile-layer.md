---
title: Adicione uma camada de azulejo a um mapa | Microsoft Azure Maps
description: Saiba como sobrepor imagens nos mapas. Veja um exemplo que usa o Azure Maps Web SDK para adicionar uma camada de azulejos contendo uma sobreposição de radar meteorológico a um mapa.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: b3619995739c51d68b00f37ebea3a38680a6b6e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92890982"
---
# <a name="add-a-tile-layer-to-a-map"></a>Adicionar uma camada de mosaico a um mapa

Este artigo mostra como sobrepor uma camada de azulejos no mapa. As camadas de azulejos permitem-lhe sobrepor imagens em cima dos azulejos do mapa base do Azure Maps. Para obter mais informações sobre o sistema de inclinação Azure Maps, consulte [os níveis de Zoom e a grelha de azulejos.](zoom-levels-and-tile-grid.md)

Uma camada de azulejos carrega em azulejos de um servidor. Estas imagens podem ser pré-renderizadas ou dinamicamente renderizadas. As imagens pré-renderizadas são armazenadas como qualquer outra imagem num servidor usando uma convenção de nomeação que a camada de azulejos compreende. Imagens dinamicamente renderizadas usam um serviço para carregar as imagens perto de tempo real. Existem três diferentes convenções de nomeação de azulejos apoiadas pela classe Azure Maps [TileLayer:](/javascript/api/azure-maps-control/atlas.layer.tilelayer) 

* X, Y, Notação zoom - X é a coluna, Y é a posição de linha do azulejo na grelha de azulejos, e a notação zoom um valor baseado no nível de zoom.
* Notação quadkey - Combina informação x, y e zoom em um único valor de corda. Este valor de corda torna-se um identificador único para um único azulejo.
* Caixa limite - Especifique uma imagem no formato de coordenadas da caixa bounding: `{west},{south},{east},{north}` . Este formato é comumente utilizado pelos [Serviços de Mapeamento Web (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> Um [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) é uma ótima maneira de visualizar grandes conjuntos de dados no mapa. Não só uma camada de azulejo pode ser gerada a partir de uma imagem, os dados vetoriais também podem ser renderizados como uma camada de azulejos também. Ao renderizar dados vetoriais como uma camada de azulejos, o controlo do mapa só precisa de carregar os azulejos que são menores no tamanho do ficheiro do que os dados vetoriais que representam. Esta técnica é comumente usada para render milhões de linhas de dados no mapa.

O URL de azulejos passados para uma camada de azulejos deve ser um URL http ou https para um recurso TileJSON ou um modelo de URL de azulejos que utiliza os seguintes parâmetros: 

* `{x}` - X posição do azulejo. Também precisa `{y}` `{z}` e. .
* `{y}` - Posição Y do azulejo. Também precisa `{x}` `{z}` e. .
* `{z}` - Zoom nível do azulejo. Também precisa `{x}` `{y}` e. .
* `{quadkey}` - Identificador de azulejos de azulejos baseado na convenção de nomeação do sistema de azulejos Bing Maps.
* `{bbox-epsg-3857}` - Uma cadeia de caixa de limitação com o formato `{west},{south},{east},{north}` no Sistema de Referência Espacial EPSG 3857.
* `{subdomain}` - Um espaço reservado para os valores do subdomínio, se especificado, `subdomain` o será adicionado.
* `{azMapsDomain}` - Um espaço reservado para alinhar o domínio e a autenticação de pedidos de azulejos com os mesmos valores utilizados pelo mapa.

## <a name="add-a-tile-layer"></a>Adicionar uma camada de mosaico

 Esta amostra mostra como criar uma camada de azulejos que aponta para um conjunto de azulejos. Esta amostra usa o sistema de inclinação de zoom x, y. A fonte desta camada de azulejos é uma sobreposição de radares meteorológicos do [Mesonet Ambiental de Iowa da Universidade Estadual de Iowa.](https://mesonet.agron.iastate.edu/ogc/) Ao visualizar dados de radar, idealmente os utilizadores veriam claramente os rótulos das cidades enquanto navegam no mapa. Este comportamento pode ser implementado inserindo a camada de azulejos abaixo da `labels` camada.

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

Abaixo está a amostra completa do código de execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Camada de azulejos usando X, Y e Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Camada de Azulejos da Pen <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>usando X, Y e Z</a> by Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Personalize uma camada de azulejos

A classe de camada de azulejos tem muitas opções de estilo. Aqui está uma ferramenta para experimentá-los.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de azulejos' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>opções de camada de azulejos da</a> pen por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)

> [!div class="nextstepaction"]
> [Opções TileLayer](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar uma camada de imagem](./map-add-image-layer.md)