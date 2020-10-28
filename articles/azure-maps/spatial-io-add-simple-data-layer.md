---
title: Adicione uma camada de dados simples ! Microsoft Azure Maps
description: Saiba como adicionar uma camada de dados simples utilizando o módulo Spatial IO, fornecido pela Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 90f3cb0ae44be176d3ae248988d098039c140c3e
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896162"
---
# <a name="add-a-simple-data-layer"></a>Adicionar uma camada de dados simples

O módulo de IO espacial proporciona uma `SimpleDataLayer` classe. Esta classe torna fácil renderizar características de estilo no mapa. Pode até renderizar conjuntos de dados que tenham propriedades de estilo e conjuntos de dados que contenham tipos de geometria mistos. A camada de dados simples consegue esta funcionalidade envolvendo várias camadas de renderização e usando expressões de estilo. As expressões de estilo procuram propriedades de estilo comum das características dentro destas camadas embrulhadas. A `atlas.io.read` função e a `atlas.io.write` função utilizam estas propriedades para ler e escrever estilos num formato de ficheiro suportado. Depois de adicionar as propriedades a um formato de ficheiro suportado, o ficheiro pode ser utilizado para vários fins. Por exemplo, o ficheiro pode ser usado para exibir as funcionalidades de estilo no mapa.

Além das funcionalidades de styling, `SimpleDataLayer` o fornece uma funcionalidade pop-up incorporada com um modelo popup. O popup aparece quando uma funcionalidade é clicada. A função popup predefinida pode ser desativada, se desejar. Esta camada também suporta dados agrupados. Quando um cluster é clicado, o mapa irá ampliar para o cluster e expandi-lo em pontos individuais e subclusters.

A `SimpleDataLayer` classe destina-se a ser usada em grandes conjuntos de dados com muitos tipos de geometria e muitos estilos aplicados nas características. Quando usada, esta classe adiciona uma sobrecarga de seis camadas contendo expressões de estilo. Então, há casos em que é mais eficiente usar as camadas de renderização do núcleo. Por exemplo, use uma camada de núcleo para renderizar um par de tipos de geometria e alguns estilos em uma característica

## <a name="use-a-simple-data-layer"></a>Use uma camada de dados simples

A `SimpleDataLayer` classe é usada como as outras camadas de renderização são usadas. O código abaixo mostra como usar uma camada de dados simples num mapa:

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

Adicione funcionalidades à fonte de dados. Em seguida, a camada de dados simples descobrirá a melhor forma de renderizar as funcionalidades. Os estilos para funcionalidades individuais podem ser definidos como propriedades na funcionalidade. O código que se segue mostra uma característica de ponto GeoJSON com uma `color` propriedade definida para `red` . 

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

O código a seguir torna a função ponto acima utilizando a camada de dados simples. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Utilize a camada de dados simples" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"> Ver a caneta <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>Utilize a camada de dados simples</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

O verdadeiro poder da camada de dados simples vem quando:

- Existem vários tipos diferentes de funcionalidades numa fonte de dados; ou
- As funcionalidades do conjunto de dados têm várias propriedades de estilo definidas individualmente; ou
- Não sabe o que o conjunto de dados contém exatamente.

Por exemplo, ao analisar os feeds de dados XML, pode não conhecer os estilos e tipos de geometria exatos das funcionalidades. A amostra que se segue mostra a potência da camada de dados simples, tornando as características de um ficheiro KML. Também demonstra várias opções que a classe de camada de dados simples fornece.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opções simples de camada de dados" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true"> Consulte as <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>opções de camada de dados Pen Simple</a> por Azure Maps ( ) no <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!NOTE]
> Esta camada de dados simples usa a classe [de modelo popup](map-add-popup.md#add-popup-templates-to-the-map) para exibir balões KML ou propriedades de características como uma mesa. Por padrão, todos os conteúdos renderizados no popup serão sandboxed dentro de um iframe como uma funcionalidade de segurança. No entanto, existem limitações:
>
> - Todos os scripts, formulários, bloqueio de ponteiro e funcionalidade de navegação superior estão desativadas. As ligações podem abrir-se num novo separador quando clicados. 
> - Os navegadores mais antigos que não suportam o `srcdoc` parâmetro em iframes serão limitados a renderizar uma pequena quantidade de conteúdo.
> 
> Se confia nos dados que estão a ser carregados nos popups e potencialmente pretende que estes scripts carregados em popups possam aceder à sua aplicação, pode desativá-lo definindo a opção de modelos pop-up `sandboxContent` para falso. 

## <a name="default-supported-style-properties"></a>Propriedades de estilo suportadas por padrão

Como mencionado anteriormente, a camada de dados simples envolve várias das camadas de renderização do núcleo: bolha, símbolo, linha, polígono e polígono extrudido. Em seguida, utiliza expressões para procurar propriedades de estilo válidos em características individuais.

As propriedades do estilo Azure Maps e GitHub são os dois principais conjuntos de nomes de propriedade suportados. A maioria dos nomes de propriedade das diferentes opções de camada de mapas Azure são suportados como propriedades de estilo de funcionalidades na camada de dados simples. Expressões foram adicionadas a algumas opções de camada para suportar nomes de propriedade de estilo que são comumente usados pelo GitHub. Estes nomes de propriedade são definidos pelo [suporte do mapa GeoJSON do GitHub,](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)e são usados para modelar ficheiros GeoJSON que são armazenados e renderizados dentro da plataforma. Todas as propriedades de estilo do GitHub são suportadas na camada de dados simples, exceto as propriedades de `marker-symbol` estilo.

Se o leitor encontrar uma propriedade de estilo menos comum, irá convertê-la na propriedade de estilo Azure Maps mais próxima. Além disso, as expressões de estilo predefinidos podem ser ultrapassadas utilizando a `getLayers` função da camada de dados simples e atualizando as opções em qualquer uma das camadas.

As secções seguintes fornecem detalhes sobre as propriedades de estilo padrão que são suportadas pela simples camada de dados. A ordem do nome da propriedade suportada é também a prioridade do imóvel. Se duas propriedades de estilo são definidas para a mesma opção de camada, então a primeira da lista tem maior precedência. As cores podem ser qualquer valor de cor CSS3; HEX, RGB, RGBA, HSL, HSLA ou valor de cor nomeado.

### <a name="bubble-layer-style-properties"></a>Propriedades de estilo de camada de bolha

Se uma funcionalidade for um `Point` ou um , e a funcionalidade não tiver uma propriedade que seria usada como um ícone personalizado `MultiPoint` para tornar o ponto como um `image` símbolo, então a funcionalidade será renderizada com um `BubbleLayer` .

| Opção camada | Nomes de propriedade suportados | Valor predefinido |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1,</sup> `marker-size` <sup>2</sup>, `scale` <sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1 \] Os `size` `scale` valores são considerados valores escalar, e serão multiplicados por `8`

\[2 \] Se a opção GitHub `marker-size` for especificada, os seguintes valores serão utilizados para o raio.

| Tamanho do marcador | Radius |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

Os aglomerados também são renderizados usando a camada de bolha. Por predefinição, o raio de um cluster é definido para `16` . A cor do cluster varia consoante o número de pontos no cluster, conforme definido abaixo:

| # de pontos | Color    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt; 10     | `green`  |

### <a name="symbol-style-properties"></a>Propriedades de estilo de símbolo

Se uma funcionalidade for um `Point` ou um , e a funcionalidade e tiver uma propriedade que seria usada como um ícone personalizado `MultiPoint` para tornar o ponto como um `image` símbolo, então a funcionalidade será renderizada com um `SymbolLayer` .

| Opção camada | Nomes de propriedade suportados | Valor predefinido |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size`, `marker-size` <sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1 \] Se a opção GitHub `marker-size` for especificada, os seguintes valores serão utilizados para a opção tamanho do ícone.

| Tamanho do marcador | Tamanho de símbolos |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

Se a característica de ponto for um cluster, a `point_count_abbreviated` propriedade será renderizada como uma etiqueta de texto. Nenhuma imagem será prestada.

### <a name="line-style-properties"></a>Propriedades de estilo de linha

Se a característica for um `LineString` , , ou , `MultiLineString` `Polygon` `MultiPolygon` então a função será renderizada com um `LineLayer` .

| Opção camada | Nomes de propriedade suportados | Valor predefinido |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>Propriedades de estilo polygon

Se a funcionalidade for um `Polygon` ou um , e a funcionalidade ou não tem uma propriedade ou a propriedade é `MultiPolygon` `height` `height` zero, então a funcionalidade será renderizada com um `PolygonLayer` .

| Opção camada | Nomes de propriedade suportados | Valor predefinido |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>Propriedades de estilo de poligon extrudido

Se a funcionalidade for um `Polygon` `MultiPolygon` ou um, e tiver um `height` imóvel com um valor superior a 0, a funcionalidade será renderizada com um `PolygonExtrusionLayer` .

| Opção camada | Nomes de propriedade suportados | Valor predefinido |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [SimpleDataLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [SimpleDataLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Ler e escrever dados espaciais](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de mapa OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Ligar a um serviço WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Tirar partido de operações principais](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Detalhes do formato de dados suportado](spatial-io-supported-data-format-details.md)