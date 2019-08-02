---
title: Adicionar uma camada de bloco ao Azure Maps | Microsoft Docs
description: Como adicionar uma camada de bloco ao mapa de JavaScript
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: e288e03b9e2c02ba963595f192dea7225c6d5762
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638997"
---
# <a name="add-a-tile-layer-to-a-map"></a>Adicionar uma camada de bloco a um mapa

Este artigo mostra como você pode sobrepor uma camada de bloco no mapa. As camadas de bloco permitem sobreimpor imagens na parte superior dos blocos de mapa base do Azure Maps. Mais informações sobre o sistema de divisão de mapas do Azure podem ser encontradas na documentação [níveis de zoom e grade de blocos](zoom-levels-and-tile-grid.md) .

Uma carga de camada de bloco em blocos de um servidor. Essas imagens podem ser previamente renderizadas e armazenadas como qualquer outra imagem em um servidor usando uma Convenção de nomenclatura que a camada de bloco entenda ou um serviço dinâmico que gera as imagens em tempo real. Há três convenções de nomenclatura de serviço de bloco diferentes com suporte pela classe [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) do Azure Maps; 

* X, Y, aplicar notação de zoom-com base no nível de zoom, x é a coluna e Y é a posição de linha do bloco na grade de blocos.
* Notação de Quadkey – combinação x, y, informações de zoom em um único valor de cadeia de caracteres que é um identificador exclusivo para um bloco.
* A caixa delimitadora – as coordenadas da caixa delimitadora podem ser usadas para especificar `{west},{south},{east},{north}` uma imagem no formato que é normalmente usado pelos [serviços de mapeamento da Web (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> Um [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) é uma ótima maneira de Visualizar grandes conjuntos de dados no mapa. Uma camada de bloco não só pode ser gerada a partir de uma imagem, mas os dados vetoriais também podem ser renderizados como uma camada de bloco. Ao renderizar dados de vetor como uma camada de bloco, o controle de mapa só precisa carregar os blocos que podem ser muito menores no tamanho do arquivo do que os dados de vetor que eles representam. Essa técnica é usada por muitos que precisam renderizar milhões de linhas de dados no mapa.

A URL do bloco passada para uma camada de bloco deve ser uma URL http/https para um recurso TileJSON ou um modelo de URL de bloco que usa os seguintes parâmetros: 

* `{x}`-X posição do bloco. Também precisa `{y}` de `{z}`e.
* `{y}`-A posição Y do bloco. Também precisa `{x}` de `{z}`e.
* `{z}`-Nível de zoom do bloco. Também precisa `{x}` de `{y}`e.
* `{quadkey}`-O identificador de quadkey de bloco com base na Convenção de nomenclatura do sistema de blocos do Bing Maps.
* `{bbox-epsg-3857}`-Uma cadeia de caracteres de caixa delimitadora com o formato `{west},{south},{east},{north}` no sistema de referência espacial do EPSG 3857.
* `{subdomain}`-Um espaço reservado onde os valores de subdomínio se especificados serão adicionados.

## <a name="add-a-tile-layer"></a>Adicionar uma camada de mosaico

 Este exemplo mostra como criar uma camada de peça que aponta para um conjunto de blocos que usam o sistema de divisão x, y e zoom. A origem dessa camada de peça é uma sobreposição de radar do [Iowa ambiente Mesonet da Universidade de estado do Iowa](https://mesonet.agron.iastate.edu/ogc/).

<br/>

<iframe height='500' scrolling='no' title='Camada de bloco usando X, Y e Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a camada de bloco de caneta <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>usando X, Y e Z</a> pelo Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa. Você pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, um [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) é criado passando uma URL formatada para um serviço de bloco, o tamanho do bloco e uma opacidade para torná-lo semitransparente. Além disso, ao adicionar a camada de bloco ao mapa, ela é adicionada abaixo `labels` da camada para que os rótulos ainda estejam claramente visíveis.

## <a name="customize-a-tile-layer"></a>Personalizar uma camada de peça

A camada do bloco tem apenas várias opções de estilo. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='Opções da camada de peças' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>Opções da camada de bloco</a> de caneta pelo<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar uma camada de imagem](./map-add-image-layer.md)
