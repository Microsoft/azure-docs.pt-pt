---
title: Adicionar uma camada de símbolo a um mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a usar a camada de símbolo para personalizar e adicionar símbolos em um mapa usando o SDK da Web do Microsoft Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 744d5ecd3aab02071f7c3aaff7dd760fc14a2a62
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911168"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Adicionar uma camada de símbolo a um mapa

Um símbolo pode ser conectado a uma fonte de dados e usado para renderizar um ícone e/ou texto em um determinado ponto. As camadas de símbolo são renderizadas usando WebGL e podem ser usadas para renderizar grandes coleções de pontos no mapa. Essa camada pode renderizar muito mais dados de ponto no mapa, com bom desempenho do que o que pode ser alcançado usando marcadores de HTML. No entanto, a camada de símbolo não dá suporte a elementos CSS tradicionais e HTML para estilização.  

> [!TIP]
> As camadas de símbolo, por padrão, renderizarão as coordenadas de todas as geometrias em uma fonte de dados. Para limitar a camada de modo que ela só processe os recursos de geometria de ponto, defina a propriedade `filter` da camada como `['==', ['geometry-type'], 'Point']` ou `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` se você quiser incluir recursos do MultiPoint também.

O Gerenciador de Sprite da imagem de mapas, que é usado para carregar imagens personalizadas usadas pela camada de símbolo, dá suporte aos seguintes formatos de imagem:

- JPEG
- PNG
- ESCALÁVEIS
- BMP
- GIF (sem animações)

## <a name="add-a-symbol-layer"></a>Adicionar uma camada de símbolo

Para adicionar uma camada de símbolo ao mapa e renderizar dados, primeiro é necessário criar uma fonte de dados e adicionar o mapa. Uma camada de símbolo pode ser criada e passada na fonte de dados para a qual recuperar os dados. Por fim, os dados precisam ser adicionados à fonte de dados para que haja algo a ser renderizado. O código a seguir mostra o código que deve ser adicionado ao mapa depois que ele é carregado para renderizar um único ponto no mapa usando uma camada de símbolo. 

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a symbol layer to render icons and/or text at points on the map.
var layer = new atlas.layer.SymbolLayer(dataSource);

//Add the layer to the map.
map.layers.add(layer);

//Create a point and add it to the data source.
dataSource.add(new atlas.data.Point([0, 0]));
```

Há quatro tipos diferentes de dados de ponto para que podem ser adicionados ao mapa:

- Geometria de ponto geojson-esse objeto contém apenas uma coordenada de um ponto e nada mais. A classe auxiliar `atlas.data.Point` pode ser usada para criar facilmente esses objetos.
- Geometria geojson do MultiPoint – este objeto contém as coordenadas de vários pontos, mas nada mais. A classe auxiliar `atlas.data.MultiPoint` pode ser usada para criar facilmente esses objetos.
- Recurso geojson – esse objeto consiste em qualquer geometria geojson e um conjunto de propriedades que contêm metadados associados à geometria. A classe auxiliar `atlas.data.Feature` pode ser usada para criar facilmente esses objetos.
- `atlas.Shape` classe é semelhante ao recurso geojson, pois consiste em uma geometria geojson e um conjunto de propriedades que contêm metadados associados à geometria. Se um objeto geojson for adicionado a uma fonte de dados, ele poderá ser facilmente renderizado em uma camada, no entanto, se a propriedade coordenadas desse objeto geojson for atualizada, a fonte de dados e o mapa não serão alterados, pois não há nenhum mecanismo no objeto JSON para disparar uma atualização. A classe Shape fornece funções para atualizar os dados que ela contém e quando uma alteração é feita, a fonte de dados e o mapa são automaticamente notificados e atualizados. 

O exemplo de código a seguir cria uma geometria de ponto geojson e a transmite para a classe `atlas.Shape` para facilitar a atualização. O centro do mapa é usado inicialmente para renderizar um símbolo. Um evento de clique é adicionado ao mapa de modo que, quando ele é acionado, as coordenadas de onde o mouse foi clicado são usadas com as formas `setCoordinates` função que atualiza o local do símbolo no mapa.

<br/>

<iframe height='500' scrolling='no' title='Alternar local do PIN' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>opção fixar local do pino</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Por padrão, para desempenho, as camadas de símbolo otimizam a renderização de símbolos, ocultando os símbolos que se sobrepõem. À medida que você aumenta o zoom, os símbolos ocultos se tornam visíveis. Para desabilitar esse recurso e renderizar todos os símbolos em todos os momentos, defina a propriedade `allowOverlap` das opções de `iconOptions` como `true`.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Adicionar um ícone personalizado a uma camada de símbolo

As camadas de símbolo são renderizadas usando WebGL. Como todos os recursos, como imagens de ícone, devem ser carregados no contexto WebGL. Este exemplo mostra como adicionar um ícone personalizado aos recursos de mapa e, em seguida, usá-lo para renderizar dados de ponto com um símbolo personalizado no mapa. A propriedade `textField` da camada de símbolo requer que uma expressão seja especificada. Nesse caso, desejamos renderizar a propriedade de temperatura, mas como é um número, ela precisa ser convertida em uma cadeia de caracteres. Além disso, queremos acrescentar "° f" a ele. Uma expressão pode ser usada para fazer isso; `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Ícone de imagem de símbolo personalizado' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>ícone imagem de símbolo personalizado</a> da caneta pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> O SDK da Web do Azure Maps fornece vários modelos de imagem personalizáveis que você pode usar com a camada de símbolo. Para obter mais informações, consulte o documento [como usar modelos de imagem](how-to-use-image-templates-web-sdk.md) .

## <a name="customize-a-symbol-layer"></a>Personalizar uma camada de símbolo 

A camada de símbolo tem muitas opções de estilo disponíveis. Aqui está uma ferramenta para testar essas várias opções de estilo.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de símbolo' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/PxVXje/'>Opções da camada símbolo</a> de caneta pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Quando você só deseja renderizar texto com uma camada de símbolo, pode ocultar o ícone definindo a propriedade `image` das opções de ícone como `'none'`.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Íconeoptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

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

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](map-add-shape.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolha](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adicionar makes HTML](map-add-bubble-layer.md)
