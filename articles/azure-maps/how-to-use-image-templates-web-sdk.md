---
title: Modelos de imagem no Azure Maps Web SDK | Microsoft Azure Maps
description: Aprenda a adicionar ícones de imagem e polígonos cheios de padrões aos mapas utilizando o Azure Maps Web SDK. Ver modelos de padrão de imagem e preenchimento disponíveis.
author: rbrundritt
ms.author: richbrun
ms.date: 8/6/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 5f455a1132a0f63e1ba3eb5d2a57a1f9bfa9a867
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895686"
---
# <a name="how-to-use-image-templates"></a>Como utilizar modelos de imagem

As imagens podem ser usadas com marcadores HTML e várias camadas dentro da web SDK do Azure Maps:

 - As camadas de símbolo podem renderizar pontos no mapa com um ícone de imagem. Os símbolos também podem ser renderizados ao longo de um caminho de linhas.
 - As camadas de polígono podem ser renderizadas com uma imagem de padrão de preenchimento. 
 - Os marcadores HTML podem renderizar pontos usando imagens e outros elementos HTML.

Para garantir um bom desempenho com camadas, carregue as imagens no recurso de imagem do mapa antes de renderizar. O [IconOptions](/javascript/api/azure-maps-control/atlas.iconoptions), do SymbolLayer, pré-load um par de imagens de marcador num punhado de cores no sprite de imagem do mapa, por padrão. Estas imagens de marcador e muito mais estão disponíveis como modelos SVG. Podem ser usados para criar imagens com escalas personalizadas, ou usadas como cor primária e secundária do cliente. No total existem 42 modelos de imagem fornecidos: 27 ícones de símbolo e 15 padrões de enchimento de polígono.

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

<iframe height="500" style="width: 100%;" scrolling="no" title="Camada de símbolo com modelo de ícone incorporado" src="//codepen.io/azuremaps/embed/VoQMPp/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte a camada de símbolo de caneta <a href='https://codepen.io/azuremaps/pen/VoQMPp/'>com o modelo de ícone incorporado</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-an-image-template-along-a-lines-path"></a>Use um modelo de imagem ao longo de um caminho de linhas

Uma vez que um modelo de imagem é carregado no sprite de imagem do mapa, ele pode ser renderizado ao longo do caminho de uma linha adicionando um LineString a uma fonte de dados e usando uma camada de símbolo com uma `lineSpacing` opção e referindo o ID do recurso de imagem na `image` opção de th `iconOptions` . 

A amostra seguinte torna uma linha rosa no mapa e usa uma camada de símbolo usando o modelo de `car` imagem com uma cor primária azul dodger e uma cor secundária branca. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Camada de linha com modelo de ícone incorporado" src="//codepen.io/azuremaps/embed/KOQvJe/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte a camada da Linha pen <a href='https://codepen.io/azuremaps/pen/KOQvJe/'>com o modelo de ícone incorporado</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Se o modelo de imagem apontar, deite `rotation` a opção de ícone da camada de símbolo para 90 se quiser que aponte na mesma direção que a linha.

## <a name="use-an-image-template-with-a-polygon-layer"></a>Use um modelo de imagem com uma camada de polígono

Uma vez que um modelo de imagem é carregado no sprite de imagem do mapa, ele pode ser renderizado como um padrão de preenchimento em uma camada de polígono, referindo o ID do recurso de imagem na `fillPattern` opção da camada.

A amostra a seguir torna uma camada de polígono usando o `dot` modelo de imagem com uma cor primária vermelha e uma cor secundária transparente.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Preencha o polígono com o modelo de ícone incorporado" src="//codepen.io/azuremaps/embed/WVMEmz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/WVMEmz/'>polígono</a> Pen Fill com o modelo de ícone incorporado por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Definir a cor secundária dos padrões de preenchimento torna mais fácil ver o mapa subjacente ainda fornecer o padrão primário. 

## <a name="use-an-image-template-with-an-html-marker"></a>Use um modelo de imagem com um marcador HTML

Um modelo de imagem pode ser recuperado usando a `altas.getImageTemplate` função e usado como o conteúdo de um marcador HTML. O modelo pode ser passado para a `htmlContent` opção do marcador, e depois personalizado usando o `color` , e `secondaryColor` `text` opções.

A amostra a seguir utiliza o `marker-arrow` modelo com uma cor primária vermelha, uma cor secundária cor-de-rosa e um valor de texto de "00".

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Marcador HTML com modelo de ícone incorporado" src="//codepen.io/azuremaps/embed/EqQvzq/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
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

| Name | Tipo de retorno | Description | 
|-|-|-|
| `addImageTemplate(templateName: string, template: string, override: boolean)` | | Adiciona um modelo de imagem SVG personalizado ao espaço de nome atlas. |
|  `getImageTemplate(templateName: string, scale?: number)`| string | Recupera um modelo SVG pelo nome. |
| `getAllImageTemplateNames()` | corda[] |  Recupera um modelo SVG pelo nome. |

Os modelos de imagem SVG suportam os seguintes valores de espaço reservado:

| Marcador de posição | Description |
|-|-|
| `{color}` | A cor primária. | 
| `{secondaryColor}` | A cor secundária. | 
| `{scale}` | A imagem SVG é convertida para uma imagem de png quando adicionada ao sprite de imagem do mapa. Este espaço reservado pode ser usado para escalar um modelo antes de ser convertido para garantir que torna claramente. | 
| `{text}` | A localização para renderizar texto quando utilizado com um marcador HTML. |

O exemplo a seguir mostra como pegar um modelo SVG e adicioná-lo à web SDK do Azure Maps como um modelo de ícone reutilizável. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Adicione modelo de ícone personalizado ao espaço de nome do atlas" src="//codepen.io/azuremaps/embed/NQyvEX/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte o modelo de ícone personalizado Pen <a href='https://codepen.io/azuremaps/pen/NQyvEX/'>Add para atlas namespace</a> by Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="list-of-image-templates"></a>Lista de modelos de imagem

Esta tabela lista todos os modelos de imagem atualmente disponíveis dentro da web SDK do Azure Maps. O nome do modelo está acima de cada imagem. Por padrão, a cor primária é azul e a cor secundária é branca. Para tornar a cor secundária mais fácil de ver num fundo branco, as seguintes imagens têm a cor secundária definida para preto.

**Modelos de ícone de símbolo**

:::row:::
   :::column span="":::
      marcador
   :::column-end:::
   :::column span="":::
      marcador de espessura
   :::column-end:::
   :::column span="":::
      marcador-círculo
   :::column-end:::
   :::column span="":::
      marcador-plano
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ícone marcador](./media/image-templates/marker.png)
   :::column-end:::
   :::column span="":::
      ![ícone de espessura marcador](./media/image-templates/marker-thick.png)
   :::column-end:::
   :::column span="":::
      ![ícone marcador-círculo](./media/image-templates/marker-circle.png)
   :::column-end:::
   :::column span="":::
      ![ícone marcador-plano](./media/image-templates/marker-flat.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      marcador quadrado
   :::column-end:::
   :::column span="":::
      marcador-quadrado-cluster
   :::column-end:::
   :::column span="":::
      marcador-seta
   :::column-end:::
   :::column span="":::
      marcador-bola-pin
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ícone marcador quadrado](./media/image-templates/marker-square.png)
   :::column-end:::
   :::column span="":::
      ![ícone de marcador-quadrado cluster](./media/image-templates/marker-square-cluster.png)
   :::column-end:::
   :::column span="":::
      ![ícone marcador-seta](./media/image-templates/marker-arrow.png)
   :::column-end:::
   :::column span="":::
      ![ícone marcador-bola-pin](./media/image-templates/marker-ball-pin.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      marcador-quadrado arredondado
   :::column-end:::
   :::column span="":::
      marcador-quadrado arredondado-aglomerado
   :::column-end:::
   :::column span="":::
      flag
   :::column-end:::
   :::column span="":::
      triângulo bandeira
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ícone de marcador quadrado arredondado](./media/image-templates/marker-square-rounded.png)
   :::column-end:::
   :::column span="":::
      ![marcador-quadrado-arredondado-cluster ícone](./media/image-templates/marker-square-rounded-cluster.png)
   :::column-end:::
   :::column span="":::
      ![ícone de bandeira](./media/image-templates/flag.png)
   :::column-end:::
   :::column span="":::
      ![ícone triângulo bandeira](./media/image-templates/flag-triangle.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      triângulo
   :::column-end:::
   :::column span="":::
      triângulo-grosso
   :::column-end:::
   :::column span="":::
      triângulo-seta-se
   :::column-end:::
   :::column span="":::
      triângulo-seta-esquerda
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ícone triângulo](./media/image-templates/triangle.png)
   :::column-end:::
   :::column span="":::
      ![ícone triângulo-grosso](./media/image-templates/triangle-thick.png)
   :::column-end:::
   :::column span="":::
      ![triângulo-seta-up ícone](./media/image-templates/triangle-arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![triângulo-seta-esquerda ícone](./media/image-templates/triangle-arrow-left.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      hexágono
   :::column-end:::
   :::column span="":::
      hexágono-grosso
   :::column-end:::
   :::column span="":::
      hexágono arredondado
   :::column-end:::
   :::column span="":::
      hexágono arredondado de espessura
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ícone hexágono](./media/image-templates/hexagon.png)
   :::column-end:::
   :::column span="":::
      ![ícone hexágono-grosso](./media/image-templates/hexagon-thick.png)
   :::column-end:::
   :::column span="":::
      ![ícone arredondado hexágono](./media/image-templates/hexagon-rounded.png)
   :::column-end:::
   :::column span="":::
      ![ícone hexágono arredondado de espessura](./media/image-templates/hexagon-rounded-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      afixar
   :::column-end:::
   :::column span="":::
      pin-round
   :::column-end:::
   :::column span="":::
      arredondado quadrado
   :::column-end:::
   :::column span="":::
      arredondado quadrado-grosso
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ícone afixar](./media/image-templates/pin.png)
   :::column-end:::
   :::column span="":::
      ![ícone redondo pin-round](./media/image-templates/pin-round.png)
   :::column-end:::
   :::column span="":::
      ![ícone quadrado arredondado](./media/image-templates/rounded-square.png)
   :::column-end:::
   :::column span="":::
      ![ícone arredondado de espessura quadrada](./media/image-templates/rounded-square-thick.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      seta-se
   :::column-end:::
   :::column span="":::
      seta-para-cima-fino
   :::column-end:::
   :::column span="":::
      car
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ícone de seta-up](./media/image-templates/arrow-up.png)
   :::column-end:::
   :::column span="":::
      ![seta-up-thin ícone](./media/image-templates/arrow-up-thin.png)
   :::column-end:::
   :::column span="":::
      ![ícone de carro](./media/image-templates/car.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::


**Modelos de padrões de preenchimento de polígono**

:::row:::
   :::column span="":::
      verificador
   :::column-end:::
   :::column span="":::
      verificador-rodado
   :::column-end:::
   :::column span="":::
      círculos
   :::column-end:::
   :::column span="":::
      círculos espaçados
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ícone de checker](./media/image-templates/checker.png)
   :::column-end:::
   :::column span="":::
      ![ícone rotativo de checker](./media/image-templates/checker-rotated.png)
   :::column-end:::
   :::column span="":::
      ![ícone de círculos](./media/image-templates/circles.png)
   :::column-end:::
   :::column span="":::
      ![ícone espaçado de círculos](./media/image-templates/circles-spaced.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      diagonal-line-up
   :::column-end:::
   :::column span="":::
      diagonal-linhas para baixo
   :::column-end:::
   :::column span="":::
      diagonal-listras-up
   :::column-end:::
   :::column span="":::
      diagonal-listras para baixo
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![diagonal-line-up ícone](./media/image-templates/diagonal-lines-up.png)
   :::column-end:::
   :::column span="":::
      ![diagonal-line-down ícone](./media/image-templates/diagonal-lines-down.png)
   :::column-end:::
   :::column span="":::
      ![diagonal-listras-up ícone](./media/image-templates/diagonal-stripes-up.png)
   :::column-end:::
   :::column span="":::
      ![diagonal-listras-para baixo ícone](./media/image-templates/diagonal-stripes-down.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      linhas de grelha
   :::column-end:::
   :::column span="":::
      linhas de grelha rotativas
   :::column-end:::
   :::column span="":::
      listras rotativas
   :::column-end:::
   :::column span="":::
      x-fill
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ícone de linhas de grelha](./media/image-templates/grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![ícone de linhas de grelha rotativa](./media/image-templates/rotated-grid-lines.png)
   :::column-end:::
   :::column span="":::
      ![ícone de riscas de grelha rotada](./media/image-templates/rotated-grid-stripes.png)
   :::column-end:::
   :::column span="":::
      ![ícone x-preenchimento](./media/image-templates/x-fill.png)
   :::column-end:::
:::row-end:::
<br>

:::row:::
   :::column span="":::
      zig-zag
   :::column-end:::
   :::column span="":::
      zig-zag-vertical
   :::column-end:::
   :::column span="":::
      pontos
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      ![ícone zig-zag](./media/image-templates/zig-zag.png)
   :::column-end:::
   :::column span="":::
      ![zig-zag-vertical ícone](./media/image-templates/zig-zag-vertical.png)
   :::column-end:::
   :::column span="":::
      ![ícone de pontos](./media/image-templates/dots.png)
   :::column-end:::
   :::column span="":::
      &nbsp;
   :::column-end:::
:::row-end:::
<br>

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

<iframe height="500" style="width: 100%;" scrolling="no" title="Opções de modelo de ícone" src="//codepen.io/azuremaps/embed/NQyaaO/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Consulte as <a href='https://codepen.io/azuremaps/pen/NQyaaO/'>opções do modelo do ícone</a> da caneta por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [ImageSpriteManager](/javascript/api/azure-maps-control/atlas.imagespritemanager)

> [!div class="nextstepaction"]
> [espaço de nome atlas](/javascript/api/azure-maps-control/atlas#functions
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