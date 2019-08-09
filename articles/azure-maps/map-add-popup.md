---
title: Adicionar um pop-up com o Azure Maps | Microsoft Docs
description: Como adicionar um pop-up ao mapa do JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: caf661faf00d1d32664b7958a14a8719a37ab36e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882105"
---
# <a name="add-a-popup-to-the-map"></a>Adicionar um pop-up ao mapa

Este artigo mostra como adicionar um pop-up a um ponto em um mapa.

## <a name="understand-the-code"></a>Compreender o código

<a id="addAPopup"></a>

O código a seguir adiciona um recurso de ponto, `name` que `description` tem e propriedades, ao mapa usando uma camada de símbolo. Uma instância da [classe Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) é criada, mas não exibida. Os eventos do mouse são adicionados à camada de símbolo para disparar abertura e fechamento do Popup quando o mouse passa sobre o marcador de símbolo. Quando o símbolo de marcador é focalizado, a propriedade `position` do popup é atualizada com a posição do marcador e `content` a opção é atualizada com algum HTML que encapsula as `name` propriedades `description` e do recurso de ponto que está sendo focalizado. Em seguida, o popup é exibido no mapa usando `open` sua função.

<br/>

<iframe height='500' scrolling='no' title='Adicionar um pop-up usando mapas do Azure' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Adicionar um pop-up usando mapas do Azure</a> pelo Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Reutilizando um popup com vários pontos

Quando você tem um grande número de pontos e quer apenas mostrar um popup de cada vez, a melhor abordagem é criar um popup e reutilizá-lo em vez de criar um popup para cada recurso de ponto. Ao reutilizar o pop-up, o número de elementos DOM criados pelo aplicativo é bastante reduzido, o que pode fornecer um melhor desempenho. O exemplo a seguir cria 3 recursos de ponto. Se você clicar em qualquer um deles, um pop-up será exibido com o conteúdo para esse recurso de ponto.

<br/>

<iframe height='500' scrolling='no' title='Reutilizando o popup com vários Pins' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>reutilizando o popup com vários Pins</a> pelo Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Personalizando um pop-up

Por padrão, o pop-up tem um plano de fundo branco, uma seta de ponteiro na parte inferior e um botão fechar no canto superior direito. O exemplo a seguir altera a cor do plano de fundo `fillColor` para preto usando a opção do pop-up. O botão fechar é removido definindo a `shoCloseButton` opção como false. O conteúdo HTML do pop-up usa preenchido 10 pixels das bordas do pop-up e o texto é tornado branco para que ele apareça bem no plano de fundo preto.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Pop-up personalizado" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>Popup personalizado</a> da caneta pelo Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Eventos pop-up

Os pop-ups podem ser abertos, fechados e arrastados. A classe Popup fornece eventos para os desenvolvedores de ajuda reagir a essas ações. O exemplo a seguir realça os eventos que estão sendo acionados quando você abre, fecha ou arrasta o pop-up. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Eventos pop-up" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte os <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>eventos de pop-up</a> de caneta<a href='https://codepen.io/azuremaps'>@azuremaps</a>pelo Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Pop-ups](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

Consulte os ótimos artigos a seguir para obter exemplos de código completos:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar um marcador HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Adicionar uma forma](./map-add-shape.md)