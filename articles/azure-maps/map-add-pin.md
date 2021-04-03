---
title: Adicione uma camada de símbolo a um mapa | Microsoft Azure Maps
description: Aprenda a adicionar símbolos personalizados, como texto ou ícones, aos mapas. Veja como utilizar fontes de dados e camadas de símbolo no Azure Maps Web SDK para este fim.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: c5434406af1f912c1c89123baa344dd3f9c7ff91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92891060"
---
# <a name="add-a-symbol-layer-to-a-map"></a>Adicione uma camada de símbolo a um mapa

Ligue um símbolo a uma fonte de dados e use-o para tornar um ícone ou um texto num dado ponto. 

As camadas de símbolo são renderizadas através do WebGL. Use uma camada de símbolo para render grandes coleções de pontos no mapa. Em comparação com o marcador HTML, a camada de símbolo apresenta um grande número de dados de pontos no mapa, com melhor desempenho. No entanto, a camada de símbolo não suporta elementos tradicionais de CSS e HTML para o styling.  

> [!TIP]
> As camadas de símbolo por defeito tornarão as coordenadas de todas as geometrias numa fonte de dados. Para limitar a camada de tal forma que apenas torna as características de geometria de ponto definir a `filter` propriedade da camada para `['==', ['geometry-type'], 'Point']` `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` ou, se quiser, também pode incluir funcionalidades MultiPoint.

O gestor de imagem de mapas carrega imagens personalizadas usadas pela camada de símbolo. Suporta os seguintes formatos de imagem:

- JPEG
- PNG
- SVG
- BMP
- GIF (sem animações)

## <a name="add-a-symbol-layer"></a>Adicionar uma camada de símbolo

Antes de poder adicionar uma camada de símbolo ao mapa, tem de dar alguns passos. Primeiro, crie uma fonte de dados e adicione-a ao mapa. Crie uma camada de símbolo. Em seguida, passe na fonte de dados para a camada de símbolo, para recuperar os dados da fonte de dados. Finalmente, adicione dados na fonte de dados, para que haja algo a ser renderizado. 

O código abaixo demonstra o que deve ser adicionado ao mapa depois de carregado. Esta amostra torna um único ponto no mapa usando uma camada de símbolo. 

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

- Geometria geojson Point - Este objeto contém apenas uma coordenada de um ponto e nada mais. A `atlas.data.Point` classe de ajudante pode ser usada para criar facilmente estes objetos.
- GeoJSON MultiPoint geometria - Este objeto contém as coordenadas de vários pontos e nada mais. A `atlas.data.MultiPoint` classe de ajudante pode ser usada para criar facilmente estes objetos.
- Característica GeoJSON - Este objeto consiste em qualquer geometria GeoJSON e um conjunto de propriedades que contêm metadados associados à geometria. A `atlas.data.Feature` classe de ajudante pode ser usada para criar facilmente estes objetos.
- `atlas.Shape` a classe é semelhante à característica GeoJSON. Ambos consistem numa geometria GeoJSON e num conjunto de propriedades que contêm metadados associados à geometria. Se um objeto GeoJSON for adicionado a uma fonte de dados, pode ser facilmente renderizado numa camada. No entanto, se a propriedade coordenada desse objeto GeoJSON for atualizada, a fonte de dados e o mapa não mudam. Isso é porque não há nenhum mecanismo no objeto JSON para desencadear uma atualização. A classe de forma fornece funções para atualizar os dados que contém. Quando uma alteração é feita, a fonte de dados e o mapa são automaticamente notificados e atualizados. 

A amostra de código a seguir cria uma geometria geoJSON Point e passa-a para a `atlas.Shape` classe para facilitar a sua atualização. O centro do mapa é inicialmente usado para renderizar um símbolo. Um evento de clique é adicionado ao mapa de modo que quando dispara, as coordenadas do rato são usadas com a função de `setCoordinates` formas. As coordenadas do rato são gravadas no momento do evento do clique. Em seguida, `setCoordinates` atualiza a localização do símbolo no mapa.

<br/>

<iframe height='500' scrolling='no' title='Localização do pino de comutação' src='//codepen.io/azuremaps/embed/ZqJjRP/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a localização do <a href='https://codepen.io/azuremaps/pen/ZqJjRP/'>pino do Pen Switch</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Por padrão, as camadas de símbolos otimizam a renderização de símbolos escondendo símbolos que se sobrepõem. À medida que se aproxima, os símbolos ocultos tornam-se visíveis. Para desativar esta função e tornar todos os símbolos em todos os momentos, desative `allowOverlap` a propriedade das `iconOptions` opções para `true` .

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>Adicione um ícone personalizado a uma camada de símbolo

As camadas de símbolo são renderizadas através do WebGL. Como tal, todos os recursos, como imagens de ícones, devem ser carregados no contexto WebGL. Esta amostra mostra como adicionar um ícone personalizado aos recursos do mapa. Este ícone é então utilizado para renderizar dados de pontos com um símbolo personalizado no mapa. A `textField` propriedade da camada de símbolo requer uma expressão a especificar. Neste caso, queremos tornar a propriedade da temperatura. Como a temperatura é um número, tem de ser convertido numa corda. Além disso, queremos anexar-lhe "°F". Uma expressão pode ser usada para fazer esta concatenação; `['concat', ['to-string', ['get', 'temperature']], '°F']`. 

<br/>

<iframe height='500' scrolling='no' title='Ícone de imagem de símbolo personalizado' src='//codepen.io/azuremaps/embed/WYWRWZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o <a href='https://codepen.io/azuremaps/pen/WYWRWZ/'>ícone de imagem do símbolo personalizado da</a> pen por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> A Azure Maps web SDK fornece vários modelos de imagem personalizáveis que você pode usar com a camada de símbolo. Para obter mais informações, consulte o documento [como usar modelos de imagem.](how-to-use-image-templates-web-sdk.md)

## <a name="customize-a-symbol-layer"></a>Personalize uma camada de símbolo 

A camada de símbolo tem muitas opções de estilo disponíveis. Aqui está uma ferramenta para testar estas várias opções de estilo.

<br/>

<iframe height='700' scrolling='no' title='Opções de camada de símbolo' src='//codepen.io/azuremaps/embed/PxVXje/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/PxVXje/'>opções de camada de símbolo de caneta</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Quando pretender renderizar apenas texto com uma camada de símbolo, pode ocultar o ícone definindo a `image` propriedade das opções do ícone para `'none'` .

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [SymbolLayer](/javascript/api/azure-maps-control/atlas.layer.symbollayer)

> [!div class="nextstepaction"]
> [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)

> [!div class="nextstepaction"]
> [IconOptions](/javascript/api/azure-maps-control/atlas.iconoptions)

> [!div class="nextstepaction"]
> [Opções de Texto](/javascript/api/azure-maps-control/atlas.textoptions)

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

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

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígonos](map-add-shape.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de bolha](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Adicionar fabricantes HTML](map-add-bubble-layer.md)