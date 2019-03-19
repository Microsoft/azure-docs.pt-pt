---
title: Adicionar uma camada de mosaico para o Azure Maps | Documentos da Microsoft
description: Como adicionar um mosaico de camada para o mapa de Javascript
author: rbrundritt
ms.author: richbrun
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 3a773c24993d229f20df698113ff7535fea634ca
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58170042"
---
# <a name="add-a-tile-layer-to-a-map"></a>Adicionar uma camada de mosaico a um mapa

Este artigo mostra como pode sobrepor uma camada de mosaico do mapa. A camadas em mosaico permitem-lhe superimpose imagens sobre mosaicos de mapa base do Azure Maps. Obter mais informações sobre o sistema de lado a lado do Azure Maps podem ser encontradas no [níveis de Zoom e grelha de mosaico](zoom-levels-and-tile-grid.md) documentação.

Uma carga de camada de mosaico em mosaicos a partir de um servidor. Essas imagens podem ser previamente processadas e armazenadas como qualquer outra imagem num servidor usando uma convenção de nomenclatura que compreende a camada de mosaico, ou um serviço dinâmico que gera as imagens em tempo real. Existem três diferentes mosaico convenções de nomenclatura de serviço suportadas pelo Azure Maps [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) classe; 

* X, Y, notação de Zoom - com base no nível de zoom, x é a coluna e y é a posição de linha do mosaico na grelha de mosaico.
* Notação Quadkey - combinação x, y, informações de zoom num valor de cadeia única que é um identificador exclusivo para um mosaico.
* Caixa delimitadora - delimitação de coordenadas de caixa, pode ser utilizado para especificar uma imagem no formato `{west},{south},{east},{north}` que é normalmente utilizado pelo [serviços de mapeamento de Web (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> R [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) é uma excelente forma de visualizar grandes conjuntos de dados no mapa. Não só pode ser gerada uma camada de mosaico a partir de uma imagem, mas os dados de vetor podem também ser compostos como uma camada de mosaico demasiado. Por meio do processamento de dados de vetor como uma camada de mosaico, o controle de mapa só precisa de carregar os mosaicos que podem ser muito menores do tamanho do ficheiro que os dados de vetor que elas representam. Essa técnica é usada por muitos que precisam para processar milhões de linhas de dados no mapa.

O URL do mosaico passado para uma camada de mosaico tem de ser um URL de http/https para um recurso de TileJSON ou um modelo de URL do mosaico que utiliza os seguintes parâmetros: 

* `{x}` -X da posição do mosaico. Também precisa `{y}` e `{z}`.
* `{y}` -Posição de Y do mosaico. Também precisa `{x}` e `{z}`.
* `{z}` -Nível de zoom do mosaico. Também precisa `{x}` e `{y}`.
* `{quadkey}` -Mosaico identificador quadkey com base na Convenção de nomenclatura do sistema de mosaico de Bing Maps.
* `{bbox-epsg-3857}` -Uma cadeia de caixa delimitadora com o formato `{west},{south},{east},{north}` no sistema de referência EPSG 3857 espaciais.
* `{subdomain}` -Um marcador de posição em que serão adicionados os valores de subdomínio se for especificado.

## <a name="add-a-tile-layer"></a>Adicionar uma camada de mosaico

 Este exemplo mostra como criar uma camada de mosaicos que aponta para um conjunto de mosaicos que utilizam os x, y, sistema de lado a lado de zoom. A origem desta camada de mosaico é uma sobreposição de planos de Meteorologia dos [Iowa ambiental Mesonet de Iowa State University](https://mesonet.agron.iastate.edu/ogc/).

<br/>

<iframe height='500' scrolling='no' title='Mosaico camada com X, Y e Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>camada de mosaicos a utilizar o X, Y e Z</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa. Pode ver [criar um mapa](./map-create.md) para obter instruções.

No segundo bloco de código, um [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest) é criado pela transmissão de um URL formatado a um serviço de mosaico, o tamanho do mosaico e uma opacidade para torná-lo semitransparente. Além disso, ao adicionar a camada de mosaicos para o mapa, é adicionado abaixo o `labels` camada para que as etiquetas ainda estão claramente visíveis.

## <a name="customize-a-tile-layer"></a>Personalizar uma camada de mosaico

A camada de mosaico tem apenas uma muitas opções de estilo. Aqui está uma ferramenta de experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de mosaico' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>opções de camada de mosaico</a> através do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

Veja os artigos seguintes para obter mais amostras de código adicionar a seus mapas:

> [!div class="nextstepaction"]
> [Adicionar uma camada de imagem](./map-add-image-layer.md)
