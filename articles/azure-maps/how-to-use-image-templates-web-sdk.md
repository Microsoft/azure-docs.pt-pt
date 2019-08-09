---
title: Modelos de imagem no SDK da Web do Azure Maps | Microsoft Docs
description: Como usar modelos de imagem no SDK da Web do Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 1ca29fafae1269b21f4e39a9d2594f84af035d25
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856692"
---
# <a name="how-to-use-image-templates"></a>Como usar modelos de imagem

As imagens podem ser usadas como marcadores de HTML e várias camadas no SDK da Web do Azure Maps:

 - As camadas de símbolo podem renderizar pontos no mapa com um ícone de imagem. Os símbolos também podem ser renderizados ao longo de um caminho de linhas.
 - Camadas de polígono podem ser renderizadas com uma imagem de padrão de preenchimento. 
 - Marcadores de HTML podem renderizar pontos usando imagens e outros elementos HTML.

Para garantir um bom desempenho com camadas, essas imagens precisam ser carregadas no recurso mapear imagem Sprite antes da renderização. O [íconeoptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions) da SymbolLayer sobrecarrega algumas imagens de marcador em algumas cores na imagem de mapa Sprite por padrão. Essas mesmas imagens de marcador e mais estão disponíveis como modelos SVG e podem ser usadas para criar imagens com escalas personalizadas, bem como uma cor primária e secundária do cliente. No total, há 42 modelos de imagem fornecidos; 27 ícones de símbolo e 15 padrões de preenchimento de polígono.

Os modelos de imagem podem ser adicionados à imagem do mapa recursos de Sprite `map.imageSprite.createFromTemplate` usando a função. Essa função permite que até cinco parâmetros sejam passados;

```javascript
createFromTemplate(id: string, templateName: string, color?: string, secondaryColor?: string, scale?: number): Promise<void>
```

em que `id` é um identificador exclusivo que você cria, que é atribuído à imagem quando ele é adicionado à imagem de mapas Sprite. Use esse identificador nas camadas para especificar qual recurso de imagem renderizar. `templateName` Especifica qual modelo de imagem usar. A `color` opção define a cor primária da imagem e as `secondaryColor` opções define a cor secundária da imagem. A `scale` opção dimensiona o modelo de imagem antes de aplicá-lo à imagem Sprite. Quando a imagem é aplicada à imagem Sprite, ela é convertida em um PNG. Para garantir a renderização nítida, é melhor dimensionar o modelo de imagem para cima antes de adicioná-lo ao Sprite do que redimensioná-lo em uma camada.

Essa função carrega a imagem de forma assíncrona na imagem Sprite e, portanto, retorna uma promessa que você pode aguardar até que essa função seja concluída.

O código a seguir mostra como criar uma imagem de um dos modelos internos e usá-lo com uma camada de símbolo.

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

## <a name="use-an-image-template-with-a-symbol-layer"></a>Usar um modelo de imagem com uma camada de símbolo

Depois que um modelo de imagem é carregado na imagem de mapa Sprite, ele pode ser renderizado como um símbolo em uma camada de símbolo referenciando a ID `image` de recurso `iconOptions`de imagem na opção do.

O exemplo a seguir renderiza uma camada de símbolo usando `marker-flat` o modelo de imagem com uma cor primária azul-petróleo e uma cor secundária branca. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Camada de símbolo com o modelo de ícone interno" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a camada de símbolo de caneta <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>com o modelo de ícone interno</a> pelo Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Usar um modelo de imagem ao longo de um caminho de linhas

Depois que um modelo de imagem é carregado na imagem de mapa Sprite, ele pode ser renderizado ao longo do caminho de uma linha adicionando um LineString a uma fonte de dados e usando uma `lineSpacing`camada de símbolo com uma opção e referenciando a ID do `image` recurso de imagem na opção o f th `iconOptions`. 

O exemplo a seguir renderiza uma linha rosa no mapa e usa uma camada de símbolo usando o `car` modelo de imagem com uma cor primária azul-Anil e uma cor secundária branca. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Camada de linha com modelo de ícone interno" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja a camada de linha de caneta <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>com o modelo de ícone interno</a> pelo Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Se o modelo de imagem estiver apontando para cima `rotation` , defina a opção icon da camada de símbolo como 90 se desejar que ela aponte para a mesma direção que a linha.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Usar um modelo de imagem com uma camada de polígono

Depois que um modelo de imagem é carregado na imagem de mapa Sprite, ele pode ser renderizado como um padrão de preenchimento em uma camada de polígono referenciando a `fillPattern` ID de recurso de imagem na opção da camada.

O exemplo a seguir renderiza uma camada de polígono usando `dot` o modelo de imagem com uma cor primária vermelha e uma cor secundária transparente.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Preencher polígono com o modelo de ícone interno" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>polígono preenchimento da caneta com o modelo de ícone interno</a> pelo Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Definir a cor secundária dos padrões de preenchimento torna mais fácil ver o mapa subjacente ainda fornecerá o padrão primário. 

## <a name="use-an-image-template-with-an-html-marker"></a>Usar um modelo de imagem com um marcador HTML

Um modelo de imagem pode ser recuperado usando `altas.getImageTemplate` a função e usado como o conteúdo de um marcador HTML. O modelo pode ser passado para a `htmlContent` opção do marcador e, em seguida, personalizado usando `color`as `secondaryColor`opções, `text` e.

O exemplo a seguir usa `marker-arrow` o modelo com uma cor primária vermelha, uma cor secundária rosa e um valor de texto de "00".

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Marcador HTML com modelo de ícone interno" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/EqQvzq/'>marcador HTML de caneta com o modelo de ícone interno</a> pelo Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="create-custom-reusable-templates"></a>Criar modelos reutilizáveis personalizados

Se seu aplicativo usar o mesmo ícone com ícones diferentes ou se você estiver criando um módulo que adiciona modelos de imagem adicionais, você poderá adicionar e recuperar facilmente esses ícones do SDK da Web do Azure Maps usando as seguintes funções estáticas `atlas` no namespace.

| Nome | Tipo de Retorno | Descrição | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Adiciona um modelo de imagem SVG personalizado ao namespace do Atlas. |
|  `getImageTemplate(templateName: string, scale?: number)`| cadeia | Recupera um modelo SVG por nome. |
| `getAllImageTemplateNames()` | string[] |  Recupera um modelo SVG por nome. |

Os modelos de imagem SVG dão suporte aos seguintes valores de espaço reservado:

| Marcador de posição | Descrição |
|-|-|
| `{color}` | A cor primária. | 
| `{secondaryColor}` | A cor secundária. | 
| `{scale}` | A imagem SVG é convertida em uma imagem png quando adicionada à imagem de mapa Sprite. Esse espaço reservado pode ser usado para dimensionar um modelo antes que ele seja convertido para garantir que ele seja processado claramente. | 
| `{text}` | O local para renderizar o texto quando usado com um marcador HTML. |

O exemplo a seguir mostra como pegar um modelo SVG e adicioná-lo ao SDK da Web do Azure Maps como um modelo de ícone reutilizável. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Adicionar modelo de ícone personalizado ao namespace do Atlas" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>Adicionar modelo de ícone personalizado ao namespace do Atlas</a> pelo Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="list-of-image-templates"></a>Lista de modelos de imagem

A tabela a seguir lista todos os modelos de imagem disponíveis atualmente no SDK da Web do Azure Maps com o nome do modelo acima de cada imagem. Por padrão, a cor primária é azul e a cor secundária é branca. Para facilitar a visualização da cor secundária em um plano de fundo branco, as imagens a seguir têm a cor secundária definida como preto.

**Modelos de ícone de símbolo**

|||||
|:-:|:-:|:-:|:-:|
| Marker | marcador-espesso | marcador-círculo | marcador-simples |
|![ícone de marcador](./media/image-templates/marker.png)|![marcador-espesso, ícone](./media/image-templates/marker-thick.png)|![marcador-ícone de círculo](./media/image-templates/marker-circle.png)|![marcador-ícone simples](./media/image-templates/marker-flat.png)|
||||
| marcador-quadrado | marcador-quadrado-cluster | marcador-seta | marcador-bola-PIN | 
|![marcador-ícone quadrado](./media/image-templates/marker-square.png)|![marcador-ícone de cluster quadrado](./media/image-templates/marker-square-cluster.png)|![marcador-ícone de seta](./media/image-templates/marker-arrow.png)|![marcador-ícone de pino de bola](./media/image-templates/marker-ball-pin.png)|
||||
| marcador-quadrado arredondado | marcador-quadrado-arredondado-cluster | identificar | Flag-triângulo |
| ![marcador-ícone arredondado quadrado](./media/image-templates/marker-square-rounded.png) | ![marcador-quadrado-arredondado-ícone de cluster](./media/image-templates/marker-square-rounded-cluster.png) | ![ícone de sinalizador](./media/image-templates/flag.png) | ![ícone sinalizador-triângulo](./media/image-templates/flag-triangle.png) |
||||
| ângulo | triângulo espesso | triângulo – seta para cima | triângulo – seta para a esquerda |
| ![ícone de triângulo](./media/image-templates/triangle.png) | ![ícone de triângulo espesso](./media/image-templates/triangle-thick.png) | ![triângulo – ícone de seta para cima](./media/image-templates/triangle-arrow-up.png) | ![triângulo – ícone de seta para a esquerda](./media/image-templates/triangle-arrow-left.png) |
||||
| hexágono | hexágono-espesso | hexágono-arredondado | hexágono-arredondado-espesso |
| ![ícone de hexágono](./media/image-templates/hexagon.png) | ![ícone de hexágono-espesso](./media/image-templates/hexagon-thick.png) | ![hexágono – ícone arredondado](./media/image-templates/hexagon-rounded.png) | ![hexágono-ícone arredondado espesso](./media/image-templates/hexagon-rounded-thick.png) |
||||
| afixar | pino arredondado | quadrado arredondado | arredondado-quadrado espesso |
| ![ícone de pino](./media/image-templates/pin.png) | ![ícone de arredondamento de PIN](./media/image-templates/pin-round.png) | ![ícone de quadrado arredondado](./media/image-templates/rounded-square.png) | ![ícone arredondado-quadrado espesso](./media/image-templates/rounded-square-thick.png) |
||||
| seta para cima | seta para cima – fina | carro ||
| ![ícone de seta para cima](./media/image-templates/arrow-up.png) | ![seta para cima – ícone fino](./media/image-templates/arrow-up-thin.png) | ![ícone de carro](./media/image-templates/car.png) | |

**Modelos de padrão de preenchimento de polígono**

|||||
|:-:|:-:|:-:|:-:|
| verificador | verificador-girado | indicam | círculos-espaçados |
| ![ícone do verificador](./media/image-templates/checker.png) | ![verificador-ícone girado](./media/image-templates/checker-rotated.png) | ![ícone de círculos](./media/image-templates/circles.png) | ![círculos – ícone com espaçamento](./media/image-templates/circles-spaced.png) |
|||||
| diagonal-linhas para cima | diagonal-linhas para baixo | diagonal-listras-verticalmente | diagonal-listras-verticalmente |
| ![diagonal – ícone de linhas para cima](./media/image-templates/diagonal-lines-up.png) | ![diagonal – ícone de linhas para baixo](./media/image-templates/diagonal-lines-down.png) | ![diagonal – ícone de listras](./media/image-templates/diagonal-stripes-up.png) | ![diagonal-listras-ícone para baixo](./media/image-templates/diagonal-stripes-down.png) |
|||||
| linhas de grade | girado – linhas de grade | girado-grade-listras | x-preenchimento |
| ![ícone de linhas de grade](./media/image-templates/grid-lines.png) | ![girado – ícone de linhas de grade](./media/image-templates/rotated-grid-lines.png) | ![girar-ícone de listras de grade](./media/image-templates/rotated-grid-stripes.png) | ![ícone de preenchimento x](./media/image-templates/x-fill.png) |
|||||
| ziguezague-ziguezague | ziguezague-ziguezague-vertical | pontilha |  |
| ![ícone de ziguezague-ziguezague](./media/image-templates/zig-zag.png) | ![ziguezague-ziguezague-ícone vertical](./media/image-templates/zig-zag-vertical.png) | ![ícone de pontos](./media/image-templates/dots.png) | |

## <a name="try-it-now-tool"></a>Ferramenta experimentar agora

Com a ferramenta a seguir, você pode renderizar os diferentes modelos de imagem internos de várias maneiras e personalizar as cores primárias e secundárias e a escala.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Opções de modelo de ícone" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte as <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>Opções de modelo de ícone</a> de caneta por<a href='https://codepen.io/azuremaps'>@azuremaps</a>mapas do Azure () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [ImageSpriteManager](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [namespace do Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-maps-typescript-latest#functions
)

Consulte os artigos a seguir para obter mais exemplos de código em que modelos de imagem podem ser usados:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar uma forma](map-add-shape.md)

> [!div class="nextstepaction"]
> [Adicionar makes HTML](map-add-bubble-layer.md)
