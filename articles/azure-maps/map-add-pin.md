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
ms.openlocfilehash: 8c39c7b57167d65dfa639d41665f5d5b38110183
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76933131"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Adicionar uma camada de símbolo a um mapa

Um símbolo ligado a uma fonte de dados, e usado para renderizar um ícone e/ou um texto num determinado ponto. As camadas de símbolo são renderizadas usando o WebGL e são usadas para renderizar grandes coleções de pontos no mapa. Em comparação com o marcador HTML, a camada de símbolo supor um grande número de dados de pontos no mapa, com melhor desempenho. No entanto, a camada de símbolo não dá suporte a elementos CSS tradicionais e HTML para estilização.  

> [!TIP]
> As camadas de símbolo, por padrão, renderizarão as coordenadas de todas as geometrias em uma fonte de dados. Para limitar a camada de tal forma que apenas torna as características de geometria de pontos definir a propriedade `filter` da camada para `['==', ['geometry-type'], 'Point']` ou `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` se quiser, você pode incluir características MultiPoint também.

O gestor de sprite de imagem maps carrega imagens personalizadas usadas pela camada de símbolo. Suporta os seguintes formatos de imagem:

- JPEG
- PNG
- ESCALÁVEIS
- BMP
- GIF (sem animações)

## <a name="add-a-symbol-layer"></a>Adicionar uma camada de símbolo

Antes de poder adicionar uma camada de símbolo ao mapa, tem de fazer alguns passos. Primeiro, crie uma fonte de dados e adicione-a ao mapa. Uma camada de símbolo pode então ser criada e passada na fonte de dados para recuperar os dados da fonte de dados. Finalmente, os dados precisam de ser adicionados à fonte de dados para que haja algo a ser prestado. O código seguinte mostra o código que deve ser adicionado ao mapa depois de carregado. O código torna um único ponto no mapa usando uma camada de símbolo. 

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

Existem quatro tipos diferentes de dados de pontos que podem ser adicionados ao mapa:

- Geometria de ponto geojson-esse objeto contém apenas uma coordenada de um ponto e nada mais. A classe auxiliar `atlas.data.Point` pode ser usada para criar facilmente esses objetos.
- GeoJSON MultiPoint geometria - Este objeto contém as coordenadas de vários pontos e nada mais. A classe auxiliar `atlas.data.MultiPoint` pode ser usada para criar facilmente esses objetos.
- Recurso geojson – esse objeto consiste em qualquer geometria geojson e um conjunto de propriedades que contêm metadados associados à geometria. A classe auxiliar `atlas.data.Feature` pode ser usada para criar facilmente esses objetos.
- `atlas.Shape` classe é semelhante à funcionalidade GeoJSON. Ambos consistem numa geometria GeoJSON e num conjunto de propriedades que contêm metadados associados à geometria. Se um objeto GeoJSON for adicionado a uma fonte de dados, pode facilmente ser renderizado numa camada. No entanto, se a propriedade coordenada desse objeto GeoJSON for atualizada, a fonte de dados e o mapa não mudam. Isso é porque não há nenhum mecanismo no objeto JSON para desencadear uma atualização. A classe de forma fornece funções para atualizar os dados que contém. Quando uma alteração é feita, a fonte de dados e o mapa são automaticamente notificados e atualizados. 

O exemplo de código a seguir cria uma geometria de ponto geojson e a transmite para a classe `atlas.Shape` para facilitar a atualização. O centro do mapa é inicialmente usado para renderizar um símbolo. Um evento de clique é adicionado ao mapa de modo que quando dispara, as coordenadas do rato são usadas com as formas `setCoordinates` função. As coordenadas do rato são gravadas no momento do clique. Em seguida, o `setCoordinates` atualiza a localização do símbolo no mapa.

<br/>

<iframe height='500' scrolling='no' title='Alternar local do PIN' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>opção fixar local do pino</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Por padrão, as camadas de símbolo otimizam a renderização dos símbolos escondendo símbolos que se sobrepõem. À medida que se aproxima, os símbolos ocultos tornam-se visíveis. Para desabilitar esse recurso e renderizar todos os símbolos em todos os momentos, defina a propriedade `allowOverlap` das opções de `iconOptions` como `true`.

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Adicionar um ícone personalizado a uma camada de símbolo

As camadas de símbolo são renderizadas usando WebGL. Como todos os recursos, como imagens de ícone, devem ser carregados no contexto WebGL. Esta amostra mostra como adicionar um ícone personalizado aos recursos do mapa. Este ícone é então usado para renderizar dados de pontos com um símbolo personalizado no mapa. A propriedade `textField` da camada de símbolo requer que uma expressão seja especificada. Neste caso, queremos tornar a propriedade da temperatura. Como a temperatura é um número, tem de ser convertida numa corda. Além disso, queremos anexar -lhe "°F". Uma expressão pode ser usada para fazer esta concatenação; `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

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
> Quando pretende renderizar apenas texto com uma camada de símbolo, pode esconder o ícone definindo a propriedade `image` das opções de ícone para `'none'`.

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
