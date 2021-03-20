---
title: Adicione uma camada de polígono a um mapa | Microsoft Azure Maps
description: Aprenda a adicionar polígonos ou círculos aos mapas. Veja como usar o Azure Maps Web SDK para personalizar formas geométricas e torná-las fáceis de atualizar e manter.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 96553e75d4b982cfe67d03961d4356a3844f253c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92891002"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Adicione uma camada de polígono ao mapa

Este artigo mostra-lhe como tornar as áreas `Polygon` e `MultiPolygon` características geometrias no mapa usando uma camada de polígono. O Azure Maps Web SDK também apoia a criação de geometrias Circle, tal como definidas no [esquema alargado de GeoJSON.](extend-geojson.md#circle) Estes círculos são transformados em polígonos quando renderizados no mapa. Todas as geometrias de características podem ser facilmente atualizadas quando embrulhadas com o [atlas. Classe de](/javascript/api/azure-maps-control/atlas.shape) forma.

## <a name="use-a-polygon-layer"></a>Use uma camada de polígono 

Quando uma camada de polígono é ligada a uma fonte de dados e carregada no mapa, torna a área com `Polygon` e `MultiPolygon` características. Para criar um polígono, adicione-o a uma fonte de dados e torne-o com uma camada de polígono utilizando a classe [PolygonLayer.](/javascript/api/azure-maps-control/atlas.layer.polygonlayer)

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a rectangular polygon.
dataSource.add(new atlas.data.Feature(
    new atlas.data.Polygon([[
        [-73.98235, 40.76799],
        [-73.95785, 40.80044],
        [-73.94928, 40.7968],
        [-73.97317, 40.76437],
        [-73.98235, 40.76799]
    ]])
));

//Create and add a polygon layer to render the polygon to the map, below the label layer.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    fillOpacity: 0.7
}), 'labels');
```

Abaixo está a amostra completa e de execução do código acima.

<br/>

<iframe height='500' scrolling='no' title='Adicione um polígono a um mapa ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Ver a Caneta <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Adicione um polígono a um mapa </a> de Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Use um polígono e uma camada de linha juntos

Uma camada de linha é usada para tornar o contorno dos polígonos. A amostra de código a seguir torna um polígono como o exemplo anterior, mas agora adiciona uma camada de linha. Esta camada de linha é uma segunda camada ligada à fonte de dados.  

<br/>

<iframe height='500' scrolling='no' title='Polígono e camada de linha para adicionar polígono' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Polígono pen e a camada de linha para adicionar polígono</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Encha um polígono com um padrão

Além de encher um polígono com uma cor, pode usar um padrão de imagem para preencher o polígono. Carregue um padrão de imagem nos recursos de imagem de mapas e, em seguida, refira esta imagem com a `fillPattern` propriedade da camada de polígono.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Padrão de enchimento de polígono" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>padrão de preenchimento de Pen Polygon</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () em <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> A Azure Maps web SDK fornece vários modelos de imagem personalizáveis que você pode usar como padrões de preenchimento. Para obter mais informações, consulte o documento [como usar modelos de imagem.](how-to-use-image-templates-web-sdk.md)

## <a name="customize-a-polygon-layer"></a>Personalize uma camada de polígono

A camada de Polígono tem apenas algumas opções de estilo. Aqui está uma ferramenta para experimentá-los.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Pen <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> by Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Adicione um círculo ao mapa

O Azure Maps utiliza uma versão estendida do esquema GeoJSON que fornece uma definição para círculos, como aqui [se](extend-geojson.md#circle)nota. Um círculo é renderizado no mapa criando uma `Point` característica. Este `Point` tem um `subType` imóvel com um valor e um `"Circle"` `radius` imóvel com um número que representa o raio em metros. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

O Azure Maps Web SDK converte estas `Point` funcionalidades em `Polygon` funcionalidades. Em seguida, estas características são renderizadas no mapa usando camadas de polígono e linha, como mostrado na amostra de código seguinte.

<br/>

<iframe height='500' scrolling='no' title='Adicione um círculo a um mapa' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Ver a Caneta <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Adicione um círculo a um mapa</a> de Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Faça uma geometria fácil de atualizar

Uma `Shape` classe envolve uma [Geometria](/javascript/api/azure-maps-control/atlas.data.geometry) ou [Recurso](/javascript/api/azure-maps-control/atlas.data.feature) e facilita a atualização e manutenção destas funcionalidades. Para instantaneaizar uma variável de forma, passe uma geometria ou um conjunto de propriedades para o construtor de forma.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

A seguinte amostra de código mostra como embrulhar um objeto GeoJSON de círculo com uma classe de forma. À medida que o valor do raio muda na forma, o círculo torna-se automaticamente no mapa.

<br/>

<iframe height='500' scrolling='no' title='Atualizar propriedades de forma' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as propriedades da <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>forma de atualização da</a> caneta por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Polígono](/javascript/api/azure-maps-control/atlas.data.polygon)

> [!div class="nextstepaction"]
> [PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

Para obter mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos:

> [!div class="nextstepaction"]
> [Criar uma origem de dados](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar um pop-up](map-add-popup.md)

> [!div class="nextstepaction"]
> [Utilizar expressões de estilo com base em dados](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Como utilizar modelos de imagem](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linhas](map-add-line-layer.md)

Recursos adicionais:

> [!div class="nextstepaction"]
> [Extensão de especificação Azure Maps GeoJSON](extend-geojson.md#circle)