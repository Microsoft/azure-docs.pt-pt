---
title: Adicionar um pop-up com o Azure Maps | Documentos da Microsoft
description: Como adicionar um pop-up para o mapa de Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 11/09/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: a6c8a8aa954379036ce566a205b8cb4e97952727
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60769554"
---
# <a name="add-a-popup-to-the-map"></a>Adicionar um pop-up ao mapa

Este artigo mostra-lhe como adicionar um pop-up para um ponto com um mapa.

## <a name="understand-the-code"></a>Compreender o código

<a id="addAPopup"></a>

<iframe height='500' scrolling='no' title='Adicionar um preenchimento de cópia de segurança com o Azure Maps' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>adicionar um preenchimento de cópia de segurança com o Azure Maps</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código constrói um objeto de mapa. Pode ver [criar um mapa](./map-create.md) para obter instruções. Ele também cria HTML conteúdo a serem exibidas na pop-up.

O segundo bloco de código cria uma origem de dados objeto utilizando a [origem de dados](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) classe. É um ponto de um [funcionalidade](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) da [ponto](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) classe. Um objeto point com propriedades de um nome e uma descrição, em seguida, é criado e adicionado à origem de dados.

Uma [camada de símbolo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest) usa ou ícones de texto para processar dados com base no ponto encapsulados no [DataSource](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.source.datasource?view=azure-iot-typescript-latest) como símbolos no mapa.  Uma camada de símbolo é criada no terceiro bloco de código. A origem de dados é adicionada para a camada de símbolo, o que é então adicionada ao mapa.

O quarto bloco de código cria um [objeto de pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) via `new atlas.Popup()`. Propriedades de pop-up, como a posição e pixelOffset fazem parte da [PopupOptions](/javascript/api/azure-maps-control/atlas.popupoptions). PopupOptions podem ser definidos no construtor de pop-up ou via [setOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#setoptions-popupoptions-) função da classe pop-up. A `mouseover` , em seguida, é criado o serviço de escuta de eventos para a camada de símbolo.

O último bloco de código cria uma função que é acionada pelo `mouseover` escuta de eventos. Ele define o conteúdo e propriedades de pop-up e adiciona o objeto de pop-up ao mapa.

## <a name="reusing-a-popup-with-multiple-points"></a>Reutilizar um pop-up com vários pontos

Quando tiver muitos pontos e só queira mostrar uma janela de pop-up ao mesmo tempo, a melhor abordagem é criar uma janela de pop-up e reutilizá-lo em vez de criar um pop-up para cada funcionalidade de ponto. Ao fazer isso, o número de elementos DOM criados pela aplicação é significativamente reduzido que pode fornecer um melhor desempenho. Este exemplo cria os recursos de ponto de 3. Se clicar em qualquer um deles, será apresentado um pop-up com o conteúdo para essa funcionalidade de ponto.

<br/>

<iframe height='500' scrolling='no' title='Reutilização de pop-up com vários Pins' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>reutilizar o pop-up com vários Pins</a> através do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

Consulte os seguintes artigos excelentes exemplos de código completo:

> [!div class="nextstepaction"]
> [Adicionar uma camada de símbolo](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar um marcador HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Adicionar uma forma](./map-add-shape.md)