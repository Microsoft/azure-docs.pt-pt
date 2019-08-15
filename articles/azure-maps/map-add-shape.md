---
title: Adicionar uma camada de polígono ao Azure Maps | Microsoft Docs
description: Como adicionar uma camada de polígono ao SDK da Web do Azure Maps.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ca6c0f5e6fde5a31655ed17f4a016bf44216643f
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976143"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Adicionar uma camada de polígono ao mapa

Este artigo mostra como renderizar as áreas do e `Polygon` `MultiPolygon` as geometrias de recursos no mapa usando uma camada de polígono. O SDK da Web do Azure Maps também dá suporte à criação de geometrias de círculo, conforme definido no [esquema geojson estendido](extend-geojson.md#circle). Esses círculos são transformados em polígonos quando renderizados no mapa. Todas as geometrias de recursos também podem ser facilmente atualizadas se encapsuladas com o [Atlas. ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)Classe de forma.

## <a name="use-a-polygon-layer"></a>Usar uma camada de polígono 

Quando uma camada de polígono é conectada a uma fonte de dados e carregada no mapa, ela renderiza a área de `Polygon` um `MultiPolygon` e recursos. O código a seguir mostra como criar um polígono, adicioná-lo a uma fonte de dados e renderizá-lo com uma camada de polígono usando a classe [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest) .

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

//Create and add a polygon layer to render the polygon to the map.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    opacaty: 0.5
}));
```

Abaixo está o exemplo de código completo em execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Adicionar um polígono a um mapa ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Adicionar um polígono a um mapa</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Usar um polígono e uma camada de linha juntas

Uma camada de linha pode ser usada para renderizar a estrutura de polígonos. O exemplo de código a seguir renderiza um polígono como o exemplo anterior, mas agora adiciona uma camada de linha como uma segunda camada conectada à fonte de dados.  

<iframe height='500' scrolling='no' title='Polígono e camada de linha para adicionar polígono' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o polígono da caneta <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>e a camada de linha para adicionar polígono</a> pelo<a href='https://codepen.io/azuremaps'>@azuremaps</a>mapas do Azure () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Preencher um polígono com um padrão

Além de preencher um polígono com uma cor, um padrão de imagem também pode ser usado. Carregue um padrão de imagem na imagem de mapas recursos de Sprite e, em seguida, `fillPattern` faça referência a essa imagem com a propriedade da camada de polígono.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Padrão de preenchimento do polígono" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>padrão de preenchimento do polígono</a> da caneta pelo<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> O SDK da Web do Azure Maps fornece vários modelos de imagem personalizáveis que você pode usar como padrões de preenchimento. Para obter mais informações, consulte o documento [como usar modelos de imagem](how-to-use-image-templates-web-sdk.md) .

## <a name="customize-a-polygon-layer"></a>Personalizar uma camada de polígono

A camada de polígono tem apenas algumas opções de estilo. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> de caneta por mapas do<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Adicionar um círculo ao mapa

O Azure Maps usa uma versão estendida do esquema geojson que fornece uma definição para círculos, conforme observado [aqui](extend-geojson.md#circle). Um círculo pode `Point` ser renderizado no mapa criando um recurso que tem uma `subType` Propriedade com um valor de `"Circle"` e uma `radius` propriedade que tem um número que representa o raio em metros. Por exemplo:

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

O SDK da Web do Azure Maps `Point` converte esses `Polygon` recursos em recursos nos bastidores e pode ser renderizado no mapa usando as camadas de polígono e linha, conforme mostrado no exemplo de código a seguir.

<br/>

<iframe height='500' scrolling='no' title='Adicionar um círculo a um mapa' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Adicionar um círculo a um mapa</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Facilitar a atualização de uma geometria

Uma `Shape` classe envolve uma [geometria](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.geometry?view=azure-iot-typescript-latest) ou um [recurso](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) e torna mais fácil atualizá-las e mantê-las. Uma forma pode ser criada passando uma geometria e um conjunto de propriedades, ou passando um recurso, conforme mostrado no código a seguir.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

O exemplo de código a seguir mostra como encapsular um objeto de círculo geojson com uma classe Shape e atualizar facilmente a propriedade RADIUS usando um controle deslizante. À medida que o valor de raio é alterado na forma, a renderização do círculo é atualizada automaticamente no mapa.

<br/>

<iframe height='500' scrolling='no' title='Atualizar propriedades da forma' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>Propriedades da forma de atualização</a> da caneta por<a href='https://codepen.io/azuremaps'>@azuremaps</a>mapas do Azure () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

Para obter mais exemplos de código a serem adicionados aos seus mapas, consulte os seguintes artigos:

> [!div class="nextstepaction"]
> [Criar uma fonte de dados](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar um pop-up](map-add-popup.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Como usar modelos de imagem](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linha](map-add-line-layer.md)

Recursos adicionais:

> [!div class="nextstepaction"]
> [Extensão de especificação geojson do Azure Maps](extend-geojson.md#circle)