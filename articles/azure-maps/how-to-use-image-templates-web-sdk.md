---
title: Modelos de imagem no Azure Maps Web SDK / Microsoft Azure Maps
description: Neste artigo, você vai aprender a usar modelos de imagem com marcadores HTML e várias camadas no Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 28fee67ccfc1e67d89d0151c8e14bd7c0b688749
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85207096"
---
# <a name="how-to-use-image-templates"></a>Como utilizar modelos de imagem

As imagens podem ser usadas com marcadores HTML e várias camadas dentro da web SDK do Azure Maps:

 - As camadas de símbolo podem renderizar pontos no mapa com um ícone de imagem. Os símbolos também podem ser renderizados ao longo de um caminho de linhas.
 - As camadas de polígono podem ser renderizadas com uma imagem de padrão de preenchimento. 
 - Os marcadores HTML podem renderizar pontos usando imagens e outros elementos HTML.

Para garantir um bom desempenho com camadas, carregue as imagens no recurso de imagem do mapa antes de renderizar. O [IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions), do SymbolLayer, pré-load um par de imagens de marcador num punhado de cores no sprite de imagem do mapa, por padrão. Estas imagens de marcador e muito mais estão disponíveis como modelos SVG. Podem ser usados para criar imagens com escalas personalizadas, ou usadas como cor primária e secundária do cliente. No total existem 42 modelos de imagem fornecidos: 27 ícones de símbolo e 15 padrões de enchimento de polígono.

Os modelos de imagem podem ser adicionados aos recursos de imagem do mapa utilizando a `map.imageSprite.createFromTemplate` função. Esta função permite passar até cinco parâmetros;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

É `id` um identificador único que se cria. O `id` é atribuído à imagem quando é adicionado ao sprite de imagem de mapas. Utilize este identificador nas camadas para especificar qual o recurso de imagem a renderizar. O `templateName` especifica qual o modelo de imagem a utilizar. A `color` opção define a cor primária da imagem e as `secondaryColor` opções definem a cor secundária da imagem. A `scale` opção escala o modelo de imagem antes de aplicá-lo ao sprite de imagem. Quando a imagem é aplicada ao sprite de imagem, é convertida num PNG. Para garantir a renderização nítida, é melhor escalar o modelo de imagem antes de adicioná-lo ao sprite, do que escaloná-lo em uma camada.

Esta função carrega assincroticamente a imagem no sprite de imagem. Assim, devolve uma Promessa que pode esperar que esta função esteja concluída.

O código que se segue mostra como criar uma imagem a partir de um dos modelos incorporados, e usá-la com uma camada de símbolo.

```javascript
map.imageSprite.createFromTemplate('myTemplatedIcon', 'marker-flat', 'teal', '#fff').then(function () {

    //Add a symbol layer that uses the custom created icon.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'myTemplatedIcon'
        }
    }));
});
```

## <a name="use-an-image-template-with-a-symbol-layer"></a>Use um modelo de imagem com uma camada de símbolo

Uma vez que um modelo de imagem é carregado no sprite de imagem do mapa, ele pode ser renderizado como um símbolo em uma camada de símbolo, referindo o ID do recurso de imagem na `image` opção do `iconOptions` .

A amostra seguinte torna uma camada de símbolo usando o `marker-flat` modelo de imagem com uma cor primária teal e uma cor secundária branca. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Camada de símbolo com modelo de ícone incorporado" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a camada de símbolo de caneta <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>com o modelo de ícone incorporado</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Use um modelo de imagem ao longo de um caminho de linhas

Uma vez que um modelo de imagem é carregado no sprite de imagem do mapa, ele pode ser renderizado ao longo do caminho de uma linha adicionando um LineString a uma fonte de dados e usando uma camada de símbolo com uma `lineSpacing` opção e referindo o ID do recurso de imagem na `image` opção de th `iconOptions` . 

A amostra seguinte torna uma linha rosa no mapa e usa uma camada de símbolo usando o modelo de `car` imagem com uma cor primária azul dodger e uma cor secundária branca. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Camada de linha com modelo de ícone incorporado" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a camada da Linha pen <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>com o modelo de ícone incorporado</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Se o modelo de imagem apontar, deite `rotation` a opção de ícone da camada de símbolo para 90 se quiser que aponte na mesma direção que a linha.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Use um modelo de imagem com uma camada de polígono

Uma vez que um modelo de imagem é carregado no sprite de imagem do mapa, ele pode ser renderizado como um padrão de preenchimento em uma camada de polígono, referindo o ID do recurso de imagem na `fillPattern` opção da camada.

A amostra a seguir torna uma camada de polígono usando o `dot` modelo de imagem com uma cor primária vermelha e uma cor secundária transparente.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Preencha o polígono com o modelo de ícone incorporado" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>polígono</a> Pen Fill com o modelo de ícone incorporado por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Definir a cor secundária dos padrões de preenchimento torna mais fácil ver o mapa subjacente ainda fornecer o padrão primário. 

## <a name="use-an-image-template-with-an-html-marker"></a>Use um modelo de imagem com um marcador HTML

Um modelo de imagem pode ser recuperado usando a `altas.getImageTemplate` função e usado como o conteúdo de um marcador HTML. O modelo pode ser passado para a `htmlContent` opção do marcador, e depois personalizado usando o `color` , e `secondaryColor` `text` opções.

A amostra a seguir utiliza o `marker-arrow` modelo com uma cor primária vermelha, uma cor secundária cor-de-rosa e um valor de texto de "00".

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Marcador HTML com modelo de ícone incorporado" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o marcador pen <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>HTML com o modelo de ícone incorporado</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () em <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> Os modelos de imagem também podem ser usados fora do mapa. O funciton getImageTemplate devolve uma cadeia SVG que tem espaços reservados; `{color}`, `{secondaryColor}`, `{scale}`, `{text}`. Substitua estes valores de espaço reservado para criar uma cadeia SVG válida. Em seguida, pode adicionar a cadeia SVG diretamente ao HTML DOM ou convertê-la num URI de dados e inseri-la numa etiqueta de imagem. Por exemplo:
> ```JavaScript
> //Retrieve an SVG template and replace the placeholder values.
> var svg = atlas.getImageTemplate('marker').replace(/{color}/, 'red').replace(/{secondaryColor}/, 'white').replace(/{text}/, '').replace(/{scale}/, 1);
>
> //Convert to data URI for use in image tags.
> var dataUri = 'data:image/svg+xml;base64,' + btoa(svg);
> ```

## <a name="create-custom-reusable-templates"></a>Crie modelos reutilizáveis personalizados

Se a sua aplicação utilizar o mesmo ícone com ícones diferentes ou se estiver a criar um módulo que adiciona modelos de imagem adicionais, pode facilmente adicionar e recuperar estes ícones a partir da web SDK do Azure Maps. Utilize as seguintes funções estáticas no `atlas` espaço de nomes.

| Name | Tipo de retorno | Descrição | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Adiciona um modelo de imagem SVG personalizado ao espaço de nome atlas. |
|  `getImageTemplate(templateName: string, scale?: number)`| string | Recupera um modelo SVG pelo nome. |
| `getAllImageTemplateNames()` | corda[] |  Recupera um modelo SVG pelo nome. |

Os modelos de imagem SVG suportam os seguintes valores de espaço reservado:

| Marcador de posição | Descrição |
|-|-|
| `{color}` | A cor primária. | 
| `{secondaryColor}` | A cor secundária. | 
| `{scale}` | A imagem SVG é convertida para uma imagem de png quando adicionada ao sprite de imagem do mapa. Este espaço reservado pode ser usado para escalar um modelo antes de ser convertido para garantir que torna claramente. | 
| `{text}` | A localização para renderizar texto quando utilizado com um marcador HTML. |

O exemplo a seguir mostra como pegar um modelo SVG e adicioná-lo à web SDK do Azure Maps como um modelo de ícone reutilizável. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Adicione modelo de ícone personalizado ao espaço de nome do atlas" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o modelo de ícone personalizado Pen <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>Add para atlas namespace</a> by Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="list-of-image-templates"></a>Lista de modelos de imagem

Esta tabela lista todos os modelos de imagem atualmente disponíveis dentro da web SDK do Azure Maps. O nome do modelo está acima de cada imagem. Por padrão, a cor primária é azul e a cor secundária é branca. Para tornar a cor secundária mais fácil de ver num fundo branco, as seguintes imagens têm a cor secundária definida para preto.

**Modelos de ícone de símbolo**

|||||
|:-:|:-:|:-:|:-:|
| marcador | marcador de espessura | marcador-círculo | marcador-plano |
|![ícone marcador](./media/image-templates/marker.png)|![ícone de espessura marcador](./media/image-templates/marker-thick.png)|![ícone marcador-círculo](./media/image-templates/marker-circle.png)|![ícone marcador-plano](./media/image-templates/marker-flat.png)|
||||
| marcador quadrado | marcador-quadrado-cluster | marcador-seta | marcador-bola-pin | 
|![ícone marcador quadrado](./media/image-templates/marker-square.png)|![ícone de marcador-quadrado cluster](./media/image-templates/marker-square-cluster.png)|![ícone marcador-seta](./media/image-templates/marker-arrow.png)|![ícone marcador-bola-pin](./media/image-templates/marker-ball-pin.png)|
||||
| marcador-quadrado arredondado | marcador-quadrado arredondado-aglomerado | flag | triângulo bandeira |
| ![ícone de marcador quadrado arredondado](./media/image-templates/marker-square-rounded.png) | ![marcador-quadrado-arredondado-cluster ícone](./media/image-templates/marker-square-rounded-cluster.png) | ![ícone de bandeira](./media/image-templates/flag.png) | ![ícone triângulo bandeira](./media/image-templates/flag-triangle.png) |
||||
| triângulo | triângulo-grosso | triângulo-seta-se | triângulo-seta-esquerda |
| ![ícone triângulo](./media/image-templates/triangle.png) | ![ícone triângulo-grosso](./media/image-templates/triangle-thick.png) | ![triângulo-seta-up ícone](./media/image-templates/triangle-arrow-up.png) | ![triângulo-seta-esquerda ícone](./media/image-templates/triangle-arrow-left.png) |
||||
| hexágono | hexágono-grosso | hexágono arredondado | hexágono arredondado de espessura |
| ![ícone hexágono](./media/image-templates/hexagon.png) | ![ícone hexágono-grosso](./media/image-templates/hexagon-thick.png) | ![ícone arredondado hexágono](./media/image-templates/hexagon-rounded.png) | ![ícone hexágono arredondado de espessura](./media/image-templates/hexagon-rounded-thick.png) |
||||
| afixar | pin-round | arredondado quadrado | arredondado quadrado-grosso |
| ![ícone afixar](./media/image-templates/pin.png) | ![ícone redondo pin-round](./media/image-templates/pin-round.png) | ![ícone quadrado arredondado](./media/image-templates/rounded-square.png) | ![ícone arredondado de espessura quadrada](./media/image-templates/rounded-square-thick.png) |
||||
| seta-se | seta-para-cima-fino | car ||
| ![ícone de seta-up](./media/image-templates/arrow-up.png) | ![seta-up-thin ícone](./media/image-templates/arrow-up-thin.png) | ![ícone de carro](./media/image-templates/car.png) | |

**Modelos de padrões de preenchimento de polígono**

|||||
|:-:|:-:|:-:|:-:|
| verificador | verificador-rodado | círculos | círculos espaçados |
| ![ícone de checker](./media/image-templates/checker.png) | ![ícone rotativo de checker](./media/image-templates/checker-rotated.png) | ![ícone de círculos](./media/image-templates/circles.png) | ![ícone espaçado de círculos](./media/image-templates/circles-spaced.png) |
|||||
| diagonal-line-up | diagonal-linhas para baixo | diagonal-listras-up | diagonal-listras para baixo |
| ![diagonal-line-up ícone](./media/image-templates/diagonal-lines-up.png) | ![diagonal-line-down ícone](./media/image-templates/diagonal-lines-down.png) | ![diagonal-listras-up ícone](./media/image-templates/diagonal-stripes-up.png) | ![diagonal-listras-para baixo ícone](./media/image-templates/diagonal-stripes-down.png) |
|||||
| linhas de grelha | linhas de grelha rotativas | listras rotativas | x-fill |
| ![ícone de linhas de grelha](./media/image-templates/grid-lines.png) | ![ícone de linhas de grelha rotativa](./media/image-templates/rotated-grid-lines.png) | ![ícone de riscas de grelha rotada](./media/image-templates/rotated-grid-stripes.png) | ![ícone x-preenchimento](./media/image-templates/x-fill.png) |
|||||
| zig-zag | zig-zag-vertical | pontos |  |
| ![ícone zig-zag](./media/image-templates/zig-zag.png) | ![zig-zag-vertical ícone](./media/image-templates/zig-zag-vertical.png) | ![ícone de pontos](./media/image-templates/dots.png) | |

**Ícones de imagem pré-carregados**

O mapa pré-coloca um conjunto de ícones no sprite de imagem dos mapas usando o `marker` `pin` , e `pin-round` modelos. Estes nomes de ícones e os seus valores de cor estão listados na tabela abaixo.

| nome de ícone | color | secundárioColor |
|-----------|-------|----------------|
| `marker-black` | `#231f20` | `#ffffff` |
| `marker-blue` | `#1a73aa` | `#ffffff` |
| `marker-darkblue` | `#003963` | `#ffffff` |
| `marker-red` | `#ef4c4c` | `#ffffff` |
| `marker-yellow` | `#f2c851` | `#ffffff` |
| `pin-blue` | `#2072b8` | `#ffffff` |
| `pin-darkblue` | `#003963` | `#ffffff` |
| `pin-red` | `#ef4c4c` | `#ffffff` |
| `pin-round-blue` | `#2072b8` | `#ffffff` |
| `pin-round-darkblue` | `#003963` | `#ffffff` |
| `pin-round-red` | `#ef4c4c` | `#ffffff` |


## <a name="try-it-now-tool"></a>Experimente agora ferramenta

Com a seguinte ferramenta, pode tornar os diferentes modelos de imagem incorporados de várias maneiras e personalizar as cores e escala primária e secundária.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Opções de modelo de ícone" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte as <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>opções do modelo do ícone</a> da caneta por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [ImageSpriteManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [espaço de nome atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-maps-typescript-latest#functions
)

Consulte os seguintes artigos para obter mais amostras de código onde os modelos de imagem podem ser utilizados:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linhas](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígonos](map-add-shape.md)

> [!div class="nextstepaction"]
> [Adicionar fabricantes HTML](map-add-bubble-layer.md)
