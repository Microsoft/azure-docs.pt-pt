---
title: Mude o estilo do mapa no Azure Maps Microsoft Azure Maps
description: Neste artigo, você vai aprender sobre funcionalidades relacionadas com o estilo disponíveis no Microsoft Azure Maps web SDK.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b1c5d9b5cd2b6b9bfecf8a0af79699061003eec1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335690"
---
# <a name="change-the-style-of-the-map"></a>Alterar o estilo do mapa

O mapa suporta várias [opções](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) de estilo diferentes que podem ser `setStyle` definidas quando o mapa está sendo inicializado ou mais tarde usando a função de mapas. Este artigo mostra como usar estas opções de estilo para personalizar a aparência dos mapas. Aprenda a definir um estilo ao carregar um mapa e aprenda a definir um novo estilo de mapa usando o controlo de picker de estilo.

## <a name="set-the-style-options"></a>Definir as opções de estilo 

As opções de estilo podem ser passadas para o `setStyle` mapa quando são inicializadas ou atualizadas mais tarde usando a função de mapas.

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: true

    //Additional map options.
};

//Update the style options at anytime using setStyle function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: false
});
```

A seguinte ferramenta mostra como as diferentes opções de estilo mudam a forma como o mapa é renderizado. Para ver os edifícios 3D, aproxime-se de uma grande cidade. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opções de estilo de mapa" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte as <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>opções</a> de estilo<a href='https://codepen.io/azuremaps'>@azuremaps</a>Pen Map por Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="choose-a-base-map-style"></a>Escolha um estilo de mapa base

Uma das opções de estilo de mapa mais comuns é usada para alterar o estilo do mapa base que é modelado. Muitos dos estilos de [mapas suportados no Azure Maps](supported-map-styles.md) estão disponíveis no Web SDK. 

### <a name="set-base-map-style-on-map-load"></a>Definir o estilo do mapa base na carga do mapa


O estilo do mapa pode ser especificado `style` ao inicializar o mapa definindo a opção. No código seguinte, `style` a opção do `grayscale_dark` mapa está definida para a inicialização.

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additiona map options
);
```

<br/>

<iframe height='500' scrolling='no' title='Definindo o estilo na carga do mapa' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a caneta <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Definindo o estilo na carga do mapa</a> por Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="update-the-base-map-style"></a>Atualizar o estilo do mapa base

 O estilo do mapa `setStyle` pode ser `style` atualizado usando a função e definindo a opção para o estilo de mapa desejado.

```javascript
map.setStyle({ style: 'satellite' });
```

No código seguinte, depois de uma instância de mapa `road` `satellite` ser carregada, o estilo do mapa é atualizado de usar a função [setStyle.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-)

<br/>

<iframe height='500' scrolling='no' title='Atualizar o estilo' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Atualizando o</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>estilo por Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-the-style-picker"></a>Adicione o picker de estilo

O comando do picker de estilo fornece um botão fácil de usar com painel flyout que pode ser usado pelo utilizador final para alterar o estilo do mapa. O selecionador de estilo tem duas opções de layout diferentes. Por padrão, o apanhador `icons` de estilo usa o layout e exibe todo o estilo do mapa como uma linha horizontal de ícones. 

<center>

![Layout de ícone de picker de estilo](media/choose-map-style/style-picker-icon-layout.png)</center>

A segunda opção `list` de layout é chamada e exibe uma lista de estilos de mapa perlocáveis.  

<center>

![Layout de lista de escolha de estilo](media/choose-map-style/style-picker-list-layout.png)</center>


O código que se segue mostra como criar uma instância do controlo do picker de estilo e adicioná-lo ao canto superior direito do mapa. O picker de estilo está definido para ter um estilo escuro e mostrar alguns estilos de mapa selecionados usando a camada da lista.

```javascript
map.controls.add(new atlas.control.StyleControl({
    mapStyles: ['road', 'night', 'grayscale_dark', 'grayscale_light'],
    layout: 'list',
    style: 'dark'
}), {
    position: 'top-right'
}); 
```

O código seguinte adiciona um controlo de picker de estilo com as suas definições padrão no mapa, para que o utilizador possa facilmente alternar entre os diferentes estilos do mapa. Alternar o estilo do mapa usando o controlo do estilo do mapa perto do canto superior direito.

<br/>

<iframe height='500' scrolling='no' title='Adicionando o picker de estilo' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Adicionando o apanhador</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>de estilo por Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Por padrão, ao utilizar o nível de preços S0 do Azure Maps, o controlo do picker de estilo lista todos os estilos disponíveis. Se quiser reduzir o número de estilos nesta lista, passe uma série de `mapStyle` estilos que pretende aparecer na lista na opção do selecionador de estilo. Se estiver a utilizar o S1 e quiser `mapStyles` mostrar todos os estilos disponíveis, detete a opção do selecionador de estilo para `"all"`.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Adicione controlos aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar controlos de mapa](map-add-controls.md)

> [!div class="nextstepaction"]
> [Adicionar um pin](map-add-pin.md)
