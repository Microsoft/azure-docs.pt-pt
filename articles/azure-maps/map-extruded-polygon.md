---
title: Add an extrusion polygon layer to Azure Maps | Microsoft Docs
description: How to add an extrusion polygon layer to the Azure Maps Web SDK.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: e6858359549f6a54513eda7bc692adcbc7d7e71b
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484340"
---
# <a name="add-an-extrusion-polygon-layer-to-the-map"></a>Add an extrusion polygon layer to the map

This article shows you how to use the polygon extrusion layer to render areas of `Polygon` and `MultiPolygon` feature geometries as extruded shapes on the map. The Azure Maps Web SDK also supports the creation of Circle geometries as defined in the [extended GeoJSON schema](extend-geojson.md#circle). These circles are transformed into polygons when rendered on the map. All feature geometries can also be easily updated if wrapped with the [atlas.Shape](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape?view=azure-iot-typescript-latest) class.


## <a name="use-a-polygon-extrusion-layer"></a>Use a polygon extrusion layer

When a [polygon extrusion layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest) is connected to the data source and loaded on the map, it renders the areas of a `Polygon` and `MultiPolygon` features as extruded shapes. The  `height` and `base` properties of the polygon extrusion layer define the base distance from the ground and height of the extruded shape in **meters**. The following code shows how to create a polygon, add it to a data source and render it using the Polygon extrusion layer class.

> [!Note]
> The `base` value defined in the polygon extrusion layer should be less than or equal to that of the `height`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Extruded polygon" src="https://codepen.io/azuremaps/embed/wvvBpvE?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
See the Pen <a href='https://codepen.io/azuremaps/pen/wvvBpvE'>Extruded polygon</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) on <a href='https://codepen.io'>CodePen</a>.</iframe>


## <a name="add-data-driven-multipolygons"></a>Add data driven multipolygons

A choropleth map can be rendered using the polygon extrusion layer, by setting its `height` and `fillColor` properties in proportion to the measurement of the statistical variable in the `Polygon` and `MultiPolygon` feature geometries. The following code sample shows an extruded choropleth Map of the U.S based on the measurement of the population density by state.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Extruded choropleth map" src="https://codepen.io/azuremaps/embed/eYYYNox?height=265&theme-id=0&default-tab=result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
See the Pen <a href='https://codepen.io/azuremaps/pen/eYYYNox'>Extruded choropleth map</a> by Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-a-circle-to-the-map"></a>Add a circle to the map

Azure Maps uses an extended version of the GeoJSON schema that provides a definition for circles as noted [here](https://docs.microsoft.com/azure/azure-maps/extend-geojson#circle). An extruded circle can be rendered on the map by creating a `point` feature with a `subType` property of `Circle` and a numbered `Radius` property representing the radius in **meters**. Por exemplo:

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

The Azure Maps Web SDK converts these `Point` features into `Polygon` features under the hood and can be rendered on the map using polygon extrusion layer as shown in the following code sample.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Drone airspace polygon" src="https://codepen.io/azuremaps/embed/zYYYrxo?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
See the Pen <a href='https://codepen.io/azuremaps/pen/zYYYrxo'>Drone airspace polygon</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-polygon-extrusion-layer"></a>Customize a polygon extrusion layer

The Polygon Extrusion layer several styling options. Here is a tool to try them out.

<br/>

<iframe height='700' scrolling='no' title='PoogBRJ' src='//codepen.io/azuremaps/embed/PoogBRJ/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/azuremaps/pen/PoogBRJ/'>PoogBRJ</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Learn more about the classes and methods used in this article:

> [!div class="nextstepaction"]
> [Polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [polygon extrusion layer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonextrusionlayer?view=azure-maps-typescript-latest)

Recursos adicionais:

> [!div class="nextstepaction"]
> [Azure Maps GeoJSON specification extension](extend-geojson.md#circle)
