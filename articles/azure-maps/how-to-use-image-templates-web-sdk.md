---
title: Modelos de imagem no Azure Maps Web SDK  Microsoft Azure Maps
description: Neste artigo, você aprenderá a usar modelos de imagem com marcadores HTML e várias camadas no Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: f3b1141ea3c3c8e33b8a2ae12c22b6962a90d32b
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198229"
---
# <a name="how-to-use-image-templates"></a>Como utilizar modelos de imagem

As imagens podem ser usadas com marcadores HTML e várias camadas dentro da web SDK do Azure Maps:

 - As camadas de símbolo podem render pontos no mapa com um ícone de imagem. Os símbolos também podem ser renderizados ao longo de um caminho de linhas.
 - Camadas de polígono podem ser renderizadas com uma imagem de padrão de enchimento. 
 - Os marcadores HTML podem renderizar pontos utilizando imagens e outros elementos HTML.

Para garantir um bom desempenho com camadas, carregue as imagens no recurso sprite de imagem do mapa antes da renderização. As [IconOptions,](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions)da SymbolLayer, precarregam um par de imagens marcadoras num punhado de cores no sprite de imagem do mapa, por padrão. Estas imagens de marcador e muito mais estão disponíveis como modelos SVG. Podem ser usados para criar imagens com escamas personalizadas, ou usadas como cor primária e secundária do cliente. No total são fornecidos 42 modelos de imagem: 27 ícones de símbolo e 15 padrões de preenchimento de polígono.

Os modelos de imagem podem ser adicionados aos recursos de sprite de imagem do mapa utilizando a função `map.imageSprite.createFromTemplate`. Esta função permite a sua aprovação até cinco parâmetros;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

O `id` é um identificador único que se cria. O `id` é atribuído à imagem quando é adicionado ao sprite de imagem dos mapas. Utilize este identificador nas camadas para especificar qual o recurso de imagem a renderizar. O `templateName` especifica qual modelo de imagem usar. A opção `color` define a cor primária da imagem e as opções `secondaryColor` define a cor secundária da imagem. A opção `scale` escala o modelo de imagem antes de o aplicar no sprite de imagem. Quando a imagem é aplicada ao sprite de imagem, é convertida num PNG. Para garantir a renderização nítida, é melhor escalar o modelo de imagem antes de adicioná-lo ao sprite, do que escaloná-lo em uma camada.

Esta função carrega assincronicamente a imagem no sprite de imagem. Assim, devolve uma Promessa de que pode esperar que esta função esteja concluída.

O código seguinte mostra como criar uma imagem a partir de um dos modelos incorporados, e usá-la com uma camada de símbolo.

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

Uma vez que um modelo de imagem é carregado no sprite de imagem do mapa, pode ser renderizado como um símbolo numa camada de símbolo, referindo o ID do recurso de imagem na opção `image` do `iconOptions`.

A amostra seguinte torna uma camada de símbolo usando o modelo de imagem `marker-flat` com uma cor primária teal e uma cor secundária branca. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Camada de símbolo com modelo de ícone incorporado" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja a camada de símbolo da caneta <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>com o modelo de ícone incorporado</a> pelo Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Use um modelo de imagem ao longo de um caminho de linhas

Uma vez que um modelo de imagem é carregado no sprite de imagem do mapa, pode ser renderizado ao longo do caminho de uma linha adicionando um LineString a uma fonte de dados e usando uma camada de símbolo com uma opção `lineSpacing`e fazendo referência ao ID do recurso de imagem na opção `image` de th `iconOptions`. 

A amostra seguinte torna uma linha cor-de-rosa no mapa e usa uma camada de símbolo usando o modelo de imagem `car` com uma cor primária azul dodger e uma cor secundária branca. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Camada de linha com modelo de ícone incorporado" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja a camada de Pen <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>Line com o modelo de ícone incorporado</a> pelo Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Se o modelo de imagem apontar para cima, delineie a opção de ícone `rotation` da camada de símbolo para 90 se quiser que aponte na mesma direção que a linha.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Use um modelo de imagem com uma camada de polígono

Uma vez que um modelo de imagem é carregado no sprite de imagem do mapa, pode ser renderizado como um padrão de preenchimento em uma camada de polígono, referenciando o ID do recurso de imagem na opção `fillPattern` da camada.

A amostra seguinte torna uma camada de polígono usando o modelo de imagem `dot` com uma cor primária vermelha e uma cor secundária transparente.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Encha o polígono com o modelo de ícone incorporado" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>polígono</a> pen fill com o modelo de ícone incorporado pelo Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Definir a cor secundária dos padrões de enchimento torna mais fácil ver que o mapa subjacente ainda irá fornecer o padrão primário. 

## <a name="use-an-image-template-with-an-html-marker"></a>Use um modelo de imagem com um marcador HTML

Um modelo de imagem pode ser recuperado utilizando a função `altas.getImageTemplate` e usado como o conteúdo de um marcador HTML. O modelo pode ser passado para a opção `htmlContent` do marcador e, em seguida, personalizado usando as opções `color`, `secondaryColor`e `text`.

A amostra seguinte usa o modelo de `marker-arrow` com uma cor primária vermelha, uma cor secundária cor-de-rosa e um valor de texto de "00".

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Marcador HTML com modelo de ícone incorporado" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o marcador Pen <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>HTML com o modelo de ícone incorporado</a> pelo Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-custom-reusable-templates"></a>Criar modelos reutilizáveis personalizados

Se a sua aplicação utilizar o mesmo ícone com ícones diferentes ou se estiver a criar um módulo que adiciona modelos de imagem adicionais, pode facilmente adicionar e recuperar estes ícones do Web SDK do Azure Maps. Utilize as seguintes funções estáticas no espaço de `atlas` nome.

| Nome | Tipo de devolução | Descrição | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Adiciona um modelo de imagem SVG personalizado ao espaço de nome do atlas. |
|  `getImageTemplate(templateName: string, scale?: number)`| string | Recupera um modelo SVG pelo nome. |
| `getAllImageTemplateNames()` | string[] |  Recupera um modelo SVG pelo nome. |

Os modelos de imagem SVG suportam os seguintes valores do espaço reservado:

| Marcador de posição | Descrição |
|-|-|
| `{color}` | A cor primária. | 
| `{secondaryColor}` | A cor secundária. | 
| `{scale}` | A imagem SVG é convertida para uma imagem de png quando adicionada ao sprite de imagem do mapa. Este espaço reservado pode ser utilizado para escalar um modelo antes de ser convertido para garantir que se torna claramente. | 
| `{text}` | A localização para renderizar texto quando utilizada com um marcador HTML. |

O exemplo seguinte mostra como pegar num modelo SVG, e adicioná-lo ao Web SDK do Azure Maps como um modelo de ícone reutilizável. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Adicione o modelo de ícone personalizado ao espaço de nome do atlas" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o modelo de ícone personalizado Pen <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>Add para atlas espaço</a> de nome por Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="list-of-image-templates"></a>Lista de modelos de imagem

Esta tabela lista todos os modelos de imagem atualmente disponíveis dentro do Web SDK do Azure Maps. O nome do modelo está acima de cada imagem. Por padrão, a cor primária é azul e a cor secundária é branca. Para tornar a cor secundária mais fácil de ver em um fundo branco, as seguintes imagens têm a cor secundária definida para preto.

**Modelos de ícone de símbolo**

|||||
|:-:|:-:|:-:|:-:|
| marcador | marcador-grosso | marcador-círculo | marcador plano |
|![ícone do marcador](./media/image-templates/marker.png)|![ícone de marcador grosso](./media/image-templates/marker-thick.png)|![ícone do círculo do marcador](./media/image-templates/marker-circle.png)|![ícone plana marcador](./media/image-templates/marker-flat.png)|
||||
| marcador-quadrado | marcador-quadrado-cluster | seta marcador | marcador-bola-pin | 
|![ícone marcador-quadrado](./media/image-templates/marker-square.png)|![ícone de aglomerado de marcadorquadrado](./media/image-templates/marker-square-cluster.png)|![ícone de seta de marcação](./media/image-templates/marker-arrow.png)|![ícone marcador-bola-pin](./media/image-templates/marker-ball-pin.png)|
||||
| marcador-quadrado-arredondado | marcador-quadrado-aglomerado arredondado | bandeira | triângulo de bandeira |
| ![ícone arredondado marcador-quadrado](./media/image-templates/marker-square-rounded.png) | ![ícone de aglomerado arredondado-quadrado de marcador](./media/image-templates/marker-square-rounded-cluster.png) | ![ícone de bandeira](./media/image-templates/flag.png) | ![ícone do triângulo da bandeira](./media/image-templates/flag-triangle.png) |
||||
| triângulo | triângulo-grosso | triângulo-seta-up | triângulo-seta-esquerda |
| ![ícone triângulo](./media/image-templates/triangle.png) | ![ícone triângulo-grosso](./media/image-templates/triangle-thick.png) | ![ícone triângulo-seta-up](./media/image-templates/triangle-arrow-up.png) | ![ícone de seta-triângulo-esquerda](./media/image-templates/triangle-arrow-left.png) |
||||
| hexágono | hexágono-grosso | hexágono arredondado | hexágono-arredondado-grosso |
| ![ícone hexagon](./media/image-templates/hexagon.png) | ![ícone hexagono-grosso](./media/image-templates/hexagon-thick.png) | ![ícone arredondado hexagon](./media/image-templates/hexagon-rounded.png) | ![ícone hexagono-arredondado-grosso](./media/image-templates/hexagon-rounded-thick.png) |
||||
| pin | pin-round | quadrado arredondado | arredondado-grosso |
| ![ícone pin](./media/image-templates/pin.png) | ![ícone pin-round](./media/image-templates/pin-round.png) | ![ícone arredondado-quadrado](./media/image-templates/rounded-square.png) | ![ícone arredondado quadrado-grosso](./media/image-templates/rounded-square-thick.png) |
||||
| seta-up | seta-up-fino | carro ||
| ![ícone seta-up](./media/image-templates/arrow-up.png) | ![seta-up-fino ícone](./media/image-templates/arrow-up-thin.png) | ![ícone do carro](./media/image-templates/car.png) | |

**Modelos de padrão de preenchimento de polígono**

|||||
|:-:|:-:|:-:|:-:|
| verificador | rotativa de verificador | círculos | círculos espaçados |
| ![ícone de verificador](./media/image-templates/checker.png) | ![Ícone rotativo de verificador](./media/image-templates/checker-rotated.png) | ![ícone círculos](./media/image-templates/circles.png) | ![ícone espaçado círculos](./media/image-templates/circles-spaced.png) |
|||||
| diagonal-line-up | diagonal-linhas-down | diagonal-listras-up | diagonal-listras-down |
| ![ícone diagonal-line-up](./media/image-templates/diagonal-lines-up.png) | ![ícone diagonal-lines-down](./media/image-templates/diagonal-lines-down.png) | ![ícone diagonal-listras-up](./media/image-templates/diagonal-stripes-up.png) | ![ícone diagonal-listras-down](./media/image-templates/diagonal-stripes-down.png) |
|||||
| linhas de grelha | linhas rotativas de grelha | roted-grid-listras | x-fill |
| ![ícone de linhas de grelha](./media/image-templates/grid-lines.png) | ![ícone de linhas de grelha rotativa](./media/image-templates/rotated-grid-lines.png) | ![ícone de listras de grelha rotativa](./media/image-templates/rotated-grid-stripes.png) | ![ícone de x-fill](./media/image-templates/x-fill.png) |
|||||
| zig-zag | zig-zag-vertical | pontos |  |
| ![ícone zig-zag](./media/image-templates/zig-zag.png) | ![ícone zig-zag-vertical](./media/image-templates/zig-zag-vertical.png) | ![ícone de pontos](./media/image-templates/dots.png) | |

## <a name="try-it-now-tool"></a>Experimente agora ferramenta

Com a seguinte ferramenta, pode render os diferentes modelos de imagem incorporados de várias maneiras e personalizar as cores e escala primárias e secundárias.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Opções de modelo de ícone" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte as <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>opções</a> do modelo de ícone pen por Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [ImageSpriteManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [atlas espaço de nome](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-maps-typescript-latest#functions
)

Consulte os seguintes artigos para obter mais amostras de código onde os modelos de imagem podem ser utilizados:

> [!div class="nextstepaction"]
> [Adicione uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicione uma camada de linha](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicione uma camada de polígono](map-add-shape.md)

> [!div class="nextstepaction"]
> [Adicionar fabricantes html](map-add-bubble-layer.md)
