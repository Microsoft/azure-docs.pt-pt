---
title: Adicionar uma camada de extrusão de polígono a um mapa | Mapas do Microsoft Azure
description: Como adicionar uma camada de extrusão de polígono ao SDK da Web do Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 36914240caf3c1321dfa0102bd87cb29173f8b1d
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911059"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map"></a>Adicionar uma camada de extrusão de polígono ao mapa

Este artigo mostra como usar a camada de extrusão de polígono para renderizar áreas de `Polygon` e `MultiPolygon` geometrias de recursos como formas extrudadas no mapa. O SDK da Web do Azure Maps também dá suporte à criação de geometrias de círculo, conforme definido no [esquema geojson estendido](extend-geojson.md#circle). Esses círculos são transformados em polígonos quando renderizados no mapa. Todas as geometrias de recursos também podem ser facilmente atualizadas se encapsuladas com o [Atlas. ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest)Classe de forma.


## <a name="use-a-polygon-extrusion-layer"></a>Usar uma camada de extrusão de polígono

Quando uma [camada de extrusão de polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) é conectada à fonte de dados e carregada no mapa, ela renderiza as áreas de um `Polygon` e `MultiPolygon` recursos como formas extrudadas. As propriedades `height` e `base` da camada de extrusão do polígono definem a distância base do zero e a altura da forma extrudada em **metros**. O código a seguir mostra como criar um polígono, adicioná-lo a uma fonte de dados e renderizá-lo usando a classe da camada de extrusão do polígono.

> [!Note]
> O valor de `base` definido na camada de extrusão do polígono deve ser menor ou igual ao da `height`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Polígono extrudada" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>polígono extrudada</a> por caneta pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.</iframe>


## <a name="add-data-driven-multipolygons"></a>Adicionar multipolígonos controlados por dados

Um mapa coropléticos pode ser renderizado usando a camada de extrusão do polígono, definindo seu `height` e `fillColor` Propriedades em proporção à medida da variável estatística nas geometrias do recurso `Polygon` e `MultiPolygon`. O exemplo de código a seguir mostra um mapa coropléticos extrudada dos U. S com base na medida da densidade da população por Estado.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mapa de coropléticos extrudada" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Confira o <a href='https://codepen.io/azuremaps/pen/eYYYNox'>mapa coropléticos de extrusão</a> de caneta pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-circle-to-the-map"></a>Adicionar um círculo ao mapa

O Azure Maps usa uma versão estendida do esquema geojson que fornece uma definição para círculos, conforme observado [aqui](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle). Um círculo com extrusão pode ser renderizado no mapa criando um recurso `point` com uma propriedade `subType` de `Circle` e uma propriedade `Radius` numerada que representa o raio em **metros**. Por exemplo:

```Javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-105.203135, 39.664087]
    },
    "properties": {
        "subType": "Circle",
        "radius": 1000
    }
} 
```

O SDK da Web do Azure Maps converte esses recursos de `Point` em `Polygon` recursos nos bastidores e pode ser renderizado no mapa usando a camada de extrusão de polígono, conforme mostrado no exemplo de código a seguir.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Polígono do espaço aéreo do drone" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>polígono do drone de área</a> de tinta pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>Personalizar uma camada de extrusão de polígono

A camada de extrusão do polígono várias opções de estilo. Aqui está uma ferramenta para experimentá-las.

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> de caneta pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [camada de extrusão do polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

Recursos adicionais:

> [!div class="nextstepaction"]
> [Extensão de especificação geojson do Azure Maps](extend-geojson.md#circle)
