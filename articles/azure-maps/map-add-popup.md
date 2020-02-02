---
title: Adicionar um pop-up a um ponto em um mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a adicionar um pop-up a um ponto usando o SDK da Web do Microsoft Azure Maps.
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 45d210725f7f09663b126528479655d7f4d9c19f
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76933359"
---
# <a name="add-a-popup-to-the-map"></a>Adicionar um pop-up ao mapa

Este artigo mostra como adicionar um pop-up a um ponto em um mapa.

## <a name="understand-the-code"></a>Compreender o código

O código a seguir adiciona um recurso de ponto, que tem `name` e `description` Propriedades, ao mapa usando uma camada de símbolo. Uma instância da [classe Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) é criada, mas não exibida. Os eventos do rato são adicionados à camada de símbolo para desencadear a abertura e o fecho do popup. Quando o símbolo do marcador está pairado, a propriedade `position` do popup é atualizada com a posição do marcador, e a opção `content` é atualizada com algum HTML que envolve as propriedades `name` e `description` da característica do ponto a pairar. Em seguida, o popup é exibido no mapa usando sua função `open`.

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

Abaixo está o exemplo de código completo em execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Adicionar um pop-up usando mapas do Azure' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Adicionar um pop-up usando mapas do Azure</a> pelo Azure maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Reutilizando um popup com vários pontos

Quando se tem um grande número de pontos e só quer mostrar um pop-up de cada vez, a melhor abordagem é criar um popup e reutilizar. Ao reutilizar o pop-up, o número de elementos DOM criados pelo aplicativo é bastante reduzido, o que pode fornecer um melhor desempenho. O exemplo a seguir cria recursos de 3 pontos. Se você clicar em qualquer um deles, um pop-up será exibido com o conteúdo para esse recurso de ponto.

<br/>

<iframe height='500' scrolling='no' title='Reutilizando o popup com vários Pins' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>reutilizando o popup com vários Pins</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Personalizando um pop-up

Por padrão, o pop-up tem um plano de fundo branco, uma seta de ponteiro na parte inferior e um botão fechar no canto superior direito. O exemplo a seguir altera a cor do plano de fundo para preto usando a opção `fillColor` do pop-up. O botão de fecho é removido definindo a opção `CloseButton` para falsa. O conteúdo HTML do popup utiliza acolchoados 10 pixels das bordas do popup. O texto é feito branco, por isso aparece bem no fundo preto.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Pop-up personalizado" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>Popup personalizado</a> da caneta pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Eventos pop-up

Os pop-ups podem ser abertos, fechados e arrastados. A classe popup fornece eventos para ajudar os desenvolvedores a reagir a estes eventos. A amostra seguinte realça quais os eventos que disparam quando o utilizador abre, fecha ou arrasta o popup. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Eventos pop-up" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte os <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>eventos de pop-up</a> de caneta pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Pop-ups](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

Consulte os ótimos artigos a seguir para obter exemplos de código completos:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar um marcador HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linha](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](map-add-shape.md)
