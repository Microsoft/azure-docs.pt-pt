---
title: Alterar o estilo do Controlo do Mapa Web do Azure Maps
description: Saiba como mudar o estilo e as opções de um mapa. Veja como adicionar um controlo de picker de estilo a um mapa no Azure Maps para que os utilizadores possam alternar entre diferentes estilos.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: e993e3086ac63a6e9d5b8372327e35016b36239f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91285167"
---
# <a name="change-the-style-of-the-map"></a>Alterar o estilo do mapa

O controlo do mapa suporta várias [opções](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) diferentes de estilo de mapa e [estilos de mapa base.](supported-map-styles.md) Todos os estilos podem ser definidos quando o controlo do mapa está a ser inicializado. Ou, pode definir estilos usando a função do controlo do `setStyle` mapa. Este artigo mostra-lhe como usar estas opções de estilo para personalizar a aparência do mapa. Além disso, você vai aprender a implementar o controlo de picker de estilo no seu mapa. O controlo do picker de estilo permite ao utilizador alternar entre diferentes estilos base.

## <a name="set-map-style-options"></a>Definir opções de estilo de mapa

As opções de estilo podem ser definidas durante a inicialização do controlo web. Ou, pode atualizar opções de estilo chamando a função do controlo do `setStyle` mapa. Para ver todas as opções de estilo disponíveis, consulte [as opções de estilo.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: false,
    showLogo = true,
    showFeedbackLink = true,
    style='road'

    //Additional map options.
};

//Update the style options at anytime using `setStyle` function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: true,
    showLogo = false,
    showFeedbackLink = false
});
```

A ferramenta que se segue mostra como as diferentes opções de estilo mudam a forma como o mapa é renderizado. Para ver os edifícios 3D, faça zoom perto de uma grande cidade.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opções de estilo de mapa" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte as <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>opções</a> de estilo Pen Map by Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="set-a-base-map-style"></a>Definir um estilo de mapa base

Também pode inicializar o controlo do mapa com um dos [estilos](supported-map-styles.md) de mapa base que estão disponíveis na Web SDK. Em seguida, pode utilizar a `setStyle` função para atualizar o estilo base com um estilo de mapa diferente.

### <a name="set-a-base-map-style-on-initialization"></a>Desa parte um estilo de mapa base na inicialização

Os estilos base do controlo do mapa podem ser definidos durante a inicialização. No código seguinte, a `style` opção do controlo do mapa é definida para o [ `grayscale_dark` estilo do mapa base.](supported-map-styles.md#grayscale_dark)  

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additional map options
);
```

<br/>

<iframe height='500' scrolling='no' title='Definindo o estilo na carga do mapa' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a pen <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Definição do estilo na carga do mapa</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="update-the-base-map-style"></a>Atualize o estilo do mapa base

O estilo do mapa base pode ser atualizado usando a `setStyle` função e definindo a `style` opção de alterar para um estilo de mapa base diferente ou adicionar opções de estilo adicionais.

```javascript
map.setStyle({ style: 'satellite' });
```

No código seguinte, depois de uma instância de mapa ser carregada, o estilo do mapa é atualizado a partir da `grayscale_dark` `satellite` função [setStyle.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map#setstyle-styleoptions-)

<br/>

<iframe height='500' scrolling='no' title='Atualizar o estilo' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Atualização do estilo</a> por Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-the-style-picker-control"></a>Adicione o controlo de picker de estilo

O controlo de picker de estilo proporciona um botão fácil de usar com painel flyout que pode ser usado pelo utilizador final para alternar entre os estilos base.

O picker de estilo tem duas opções de layout diferentes: `icon` e `list` . Além disso, o picker de estilo permite-lhe escolher duas opções de controlo de picker de estilo `style` diferentes: `light` e `dark` . Neste exemplo, o picker de estilo usa o `icon` layout e apresenta uma lista selecionada de estilos de mapa base na forma de ícones. O selecionador de controlo de estilo inclui o seguinte conjunto de estilos base: `["road", "grayscale_light", "grayscale_dark", "night", "road_shaded_relief"]` . Para obter mais informações sobre as opções de controlo do picker de estilo, consulte [opções de controlo de estilo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions).

A imagem abaixo mostra o controlo do picker de estilo exibido no `icon` layout.

:::image type="content" source="./media/choose-map-style/style-picker-icon-layout.png" alt-text="Layout de ícone de picker de estilo":::

A imagem abaixo mostra o controlo do picker de estilo exibido no `list` layout.

:::image type="content" source="./media/choose-map-style/style-picker-list-layout.png" alt-text="Layout de ícone de picker de estilo":::

> [!IMPORTANT]
> Por padrão, o controlo do picker de estilo lista todos os estilos disponíveis sob o nível de preços S0 do Azure Maps. Se quiser reduzir o número de estilos desta lista, passe uma série de estilos que pretende aparecer na lista para a `mapStyle` opção do picker de estilo. Se estiver a utilizar o S1 e quiser mostrar todos os estilos disponíveis, desaponsee a `mapStyles` opção do picker de estilo para `"all"` .

O código que se segue mostra-lhe como anular a lista de estilo base predefinido. `mapStyles` Neste exemplo, estamos a definir `mapStyles` a opção de listar quais os estilos base que queremos ser exibidos pelo controlo de picker de estilo.

<br/>

<iframe height='500' scrolling='no' title='Adicionando o picker de estilo' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Ver a caneta <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Adicionar o picker de estilo</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Opções de Estilo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Adicione controlos aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar controlos de mapa](map-add-controls.md)

> [!div class="nextstepaction"]
> [Adicionar um pin](map-add-pin.md)
