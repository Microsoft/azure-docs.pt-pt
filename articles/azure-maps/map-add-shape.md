---
title: Adicione uma camada de polígono a um mapa Microsoft Azure Maps
description: Neste artigo, você aprenderá a renderizar um polígono e multipolígo num mapa no Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9191f054ca3c7374bcbc7bec46573289a512612c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535057"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Adicione uma camada de polígono ao mapa

Este artigo mostra-lhe como tornar `Polygon` `MultiPolygon` as áreas de e apresentam geometrias no mapa usando uma camada de polígono. O Azure Maps Web SDK também apoia a criação de geometrias Circle, tal como definida si [no esquema geojson alargado.](extend-geojson.md#circle) Estes círculos são transformados em polígonos quando renderizados no mapa. Todas as geometrias de recursos podem ser facilmente atualizadas quando embrulhadas com o [atlas. Classe](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) de forma.

## <a name="use-a-polygon-layer"></a>Use uma camada de polígono 

Quando uma camada de polígono é ligada a uma fonte de `Polygon` `MultiPolygon` dados e carregada no mapa, torna a área com e características. Para criar um polígono, adicione-o a uma fonte de dados e torne-o com uma camada de polígono utilizando a classe [PolygonLayer.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

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

Abaixo está a amostra completa e em execução do código acima.

<br/>

<iframe height='500' scrolling='no' title='Adicione um polígono a um mapa ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a caneta <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Adicione um polígono a um mapa</a> de Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Use um polígono e camada de linha juntos

Uma camada de linha é usada para fazer o contorno dos polígonos. A amostra de código seguinte torna um polígono como o exemplo anterior, mas agora adiciona uma camada de linha. Esta camada de linha é uma segunda camada ligada à fonte de dados.  

<iframe height='500' scrolling='no' title='Polígono e camada de linha para adicionar polígono' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>Pen Polygon e a camada de linha para adicionar polígono</a> por Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Encha um polígono com um padrão

Além de encher um polígono com uma cor, pode utilizar um padrão de imagem para encher o polígono. Carregue um padrão de imagem nos mapas de recursos de `fillPattern` sprite de imagem e, em seguida, referenciar esta imagem com a propriedade da camada de polígono.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Padrão de enchimento de polígono" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o padrão de preenchimento pen<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>polygon</a> por Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> O Web SDK do Azure Maps fornece vários modelos de imagem personalizáveis que pode usar como padrões de preenchimento. Para mais informações, consulte o documento [de como utilizar modelos de imagem.](how-to-use-image-templates-web-sdk.md)

## <a name="customize-a-polygon-layer"></a>Personalize uma camada de polígono

A camada de Polígono tem apenas algumas opções de estilo. Aqui está uma ferramenta para experimentá-los.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Pen <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> by<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Adicione um círculo ao mapa

O Azure Maps utiliza uma versão estendida do esquema GeoJSON que fornece uma definição para círculos, como se nota [aqui](extend-geojson.md#circle). Um círculo é renderizado no `Point` mapa criando uma característica. Este `Point` tem `subType` um imóvel `"Circle"` com `radius` um valor e um imóvel com um número que representa o raio em metros. 

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

<iframe height='500' scrolling='no' title='Adicione um círculo a um mapa' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a caneta <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Adicione um círculo a um mapa</a> de Azure Maps ()<a href='https://codepen.io/azuremaps'>@azuremaps</a>em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Tornar uma geometria fácil de atualizar

Uma `Shape` classe envolve uma [Geometria](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) ou [Recurso](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) e facilita a atualização e manutenção destas funcionalidades. Para instantaneamente uma variável de forma, passe uma geometria ou um conjunto de propriedades para o construtor de formas.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

A amostra de código que se segue mostra como embrulhar um objeto GeoJSON em círculo com uma classe de forma. À medida que o valor do raio muda na forma, o círculo torna-se automaticamente no mapa.

<br/>

<iframe height='500' scrolling='no' title='Atualizar propriedades de forma' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as propriedades da <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>forma</a> da<a href='https://codepen.io/azuremaps'>@azuremaps</a>Pen Update por Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Opções polygonLayer](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

Para mais exemplos de código para adicionar aos seus mapas, consulte os seguintes artigos:

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
> [Extensão da especificação Do GeoJSON do Azure Maps](extend-geojson.md#circle)