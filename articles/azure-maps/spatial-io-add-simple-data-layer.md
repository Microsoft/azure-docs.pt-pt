---
title: Adicione uma simples camada de dados  Microsoft Azure Maps
description: Aprenda a adicionar uma simples camada de dados utilizando o módulo Espacial IO, fornecido pelo Azure Maps Web SDK.
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3fa54e3227496c11fcafc2f42e980daa5c716365
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370989"
---
# <a name="add-a-simple-data-layer"></a>Adicione uma camada de dados simples

O módulo IO espacial proporciona uma aula de `SimpleDataLayer`. Esta aula facilita a renderização de funcionalidades de estilo no mapa. Pode até renderizar conjuntos de dados que têm propriedades de estilo e conjuntos de dados que contêm tipos de geometria mista. A simples camada de dados obtém esta funcionalidade envolvendo várias camadas de renderização e usando expressões de estilo. As expressões de estilo procuram propriedades de estilo comum das características dentro destas camadas embrulhadas. A função `atlas.io.read` e a função `atlas.io.write` usam estas propriedades para ler e escrever estilos num formato de ficheiro suportado. Depois de adicionar as propriedades a um formato de ficheiro suportado, o ficheiro pode ser utilizado para vários fins. Por exemplo, o ficheiro pode ser utilizado para exibir as características de estilo no mapa.

Além das funcionalidades de estilo, o `SimpleDataLayer` fornece uma funcionalidade popup incorporada com um modelo popup. O popup exibe quando uma funcionalidade é clicada. A função popup padrão pode ser desativada, se desejar. Esta camada também suporta dados agrupados. Quando um cluster é clicado, o mapa irá ampliar para o cluster e expandi-lo em pontos e subclusters individuais.

A classe `SimpleDataLayer` destina-se a ser usada em grandes conjuntos de dados com muitos tipos de geometria e muitos estilos aplicados nas funcionalidades. Quando utilizada, esta classe adiciona uma sobrecarga de seis camadas contendo expressões de estilo. Então, há casos em que é mais eficiente usar as camadas de renderização do núcleo. Por exemplo, use uma camada central para renderizar um par de tipos de geometria e alguns estilos em uma característica

## <a name="default-supported-style-properties"></a>Propriedades de estilo suportado por padrão

Como mencionado anteriormente, a simples camada de dados envolve várias camadas de renderização do núcleo: bolha, símbolo, linha, polígono e polígono extrudido. Em seguida, utiliza expressões para procurar propriedades de estilo válidas em características individuais.

As propriedades do estilo Azure Maps e GitHub são os dois principais conjuntos de nomes de propriedade suportados. A maioria dos nomes de propriedade das diferentes opções de camada de mapas azure são suportadas como propriedades de estilo de características na camada de dados simples. Expressões foram adicionadas a algumas opções de camada para apoiar nomes de propriedade de estilo que são comumente usados pelo GitHub. Estes nomes de propriedade são definidos pelo suporte de [mapa GeoJSON da GitHub,](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)e são usados para modelar ficheiros GeoJSON que são armazenados e renderizados dentro da plataforma. Todas as propriedades de estilo do GitHub são suportadas na simples camada de dados, exceto as propriedades de estilo `marker-symbol`.

Se o leitor encontrar uma propriedade de estilo menos comum, irá convertê-la para a propriedade estilo Azure Maps mais próxima. Além disso, as expressões de estilo padrão podem ser ultrapassadas utilizando a função `getLayers` da simples camada de dados e atualizando as opções em qualquer uma das camadas.

A secção seguinte fornece detalhes sobre as propriedades de estilo padrão que são suportadas pela simples camada de dados. A ordem do nome do imóvel suportado é também a prioridade do imóvel. Se duas propriedades de estilo são definidas para a mesma opção de camada, então a primeira na lista tem maior precedência.

## <a name="simple-data-layer-options"></a>Opções simples de camada de dados

### <a name="bubble-layer-style-properties"></a>Propriedades de estilo de camada de bolha

Se uma funcionalidade for uma `Point` ou uma `MultiPoint`, e a funcionalidade não tiver uma propriedade `image` que seria usada como um ícone personalizado para tornar o ponto como símbolo, então a funcionalidade será renderizada com um `BubbleLayer`.

| Opção camada | Nomes de propriedade suportados | Valor predefinido |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | <sup>`size`1</sup>, `marker-size`<sup>2,</sup>`scale`<sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1\] Os valores `size` e `scale` são considerados valores escalar, e serão multiplicados por `8`

\[2\] Se for especificada a opção GitHub `marker-size`, serão utilizados os seguintes valores para o raio.

| Tamanho do marcador | raio |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

Os clusters também são renderizados usando a camada de bolha. Por padrão, o raio de um cluster está definido para `16`. A cor do cluster varia consoante o número de pontos no cluster, conforme definido abaixo:

| # de pontos | Cor    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt; 10     | `green`  |

### <a name="symbol-style-properties"></a>Propriedades de estilo símbolo

Se uma funcionalidade for uma `Point` ou uma `MultiPoint`, e a funcionalidade e tiver uma propriedade `image` que seria usada como um ícone personalizado para tornar o ponto como símbolo, então a funcionalidade será renderizada com um `SymbolLayer`.

| Opção camada | Nomes de propriedade suportados | Valor predefinido |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`, `marker-size`<sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1\] Se for especificada a opção `marker-size` GitHub, serão utilizados os seguintes valores para a opção tamanho do ícone.

| Tamanho do marcador | Tamanho do símbolo |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

Se a característica de ponto for um cluster, a propriedade `point_count_abbreviated` será renderizada como uma etiqueta de texto. Nenhuma imagem será renderizada.

### <a name="line-style-properties"></a>Propriedades de estilo de linha

Se a funcionalidade for uma `LineString`, `MultiLineString`, `Polygon`ou `MultiPolygon`, então a funcionalidade será renderizada com um `LineLayer`.

| Opção camada | Nomes de propriedade suportados | Valor predefinido |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>Propriedades estilo polígono

Se a funcionalidade for uma `Polygon` ou uma `MultiPolygon`, e a funcionalidade não tiver uma propriedade `height` ou a propriedade `height` for zero, então a funcionalidade será renderizada com um `PolygonLayer`.

| Opção camada | Nomes de propriedade suportados | Valor predefinido |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity``fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>Propriedades de estilo polígono extrudido

Se a funcionalidade for uma `Polygon` ou uma `MultiPolygon`, e tiver uma propriedade `height` com um valor superior a 0, a funcionalidade será renderizada com um `PolygonExtrusionLayer`.

| Opção camada | Nomes de propriedade suportados | Valor predefinido |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="use-a-simple-data-layer"></a>Use uma camada de dados simples

A classe `SimpleDataLayer` é usada como as outras camadas de renderização são usadas. O código abaixo mostra como usar uma simples camada de dados num mapa:

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

Adicione funcionalidades à fonte de dados. Em seguida, a camada de dados simples descobrirá a melhor forma de renderizar as funcionalidades. Os estilos para características individuais podem ser definidos como propriedades na funcionalidade. O código seguinte mostra uma característica de ponto GeoJSON com uma propriedade `color` definida para `red`. 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [0, 0]
    },
    "properties": {
        "color": "red"
    }
}
```

O código seguinte torna a característica de ponto acima utilizando a simples camada de dados. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Use a camada de dados simples" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true"> Consulte a caneta <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>Utilize a simples camada</a> de dados do Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

O verdadeiro poder da simples camada de dados vem quando:

- Existem vários tipos diferentes de funcionalidades numa fonte de dados; ou
- As funcionalidades do conjunto de dados têm várias propriedades de estilo individualmente definidas neles; ou
- Não tem certeza do que o conjunto de dados contém exatamente.

Por exemplo, ao analisar os feeds de dados XML, pode não saber os estilos exatos e os tipos de geometria das funcionalidades. A amostra que se segue mostra o poder da camada de dados simples, através da renderização das características de um ficheiro KML. Também demonstra várias opções que a simples classe de camadade dados fornece.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opções simples de camada de dados" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true"> Consulte as <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>opções</a> de camada de dados Simple Pen da Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [SimpleDataLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [Opções simpledatalayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Ler e escrever dados espaciais](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Adicione uma camada de mapa OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Ligue-se a um serviço WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Alavancar operações de núcleo](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Detalhes do formato de dados suportados](spatial-io-supported-data-format-details.md)
