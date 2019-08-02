---
title: Criar um mapa com mapas do Azure | Microsoft Docs
description: Como criar um mapa JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 8a4f67290e93d8b296added9023fe9b6947ba02c
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638675"
---
# <a name="create-a-map"></a>Criar um mapa

Este artigo mostra maneiras de criar um mapa e animar um mapa.  

## <a name="understand-the-code"></a>Compreender o código

Há duas maneiras de construir um mapa. Você pode definir a câmera do mapa especificando o ponto central e o nível de zoom ou pode definir os limites da câmera do mapa especificando os pontos delimitadores do sudoeste e do nordeste.

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Definir a câmera

<iframe height='500' scrolling='no' title='Criar um mapa por meio de CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>criar um mapa por `CameraOptions` meio </a>dos serviços do Azure baseados<a href='https://codepen.io/azuremaps'>@azuremaps</a>na localização () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, um [objeto de mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) é criado por `new atlas.Map()` meio do e o centro e o zoom são definidos. As propriedades do mapa, como o centro e o nível [](/javascript/api/azure-maps-control/atlas.cameraoptions)de zoom, fazem parte das câmerasoptions.

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Definir os limites da câmera

<iframe height='500' scrolling='no' title='Criar um mapa por meio de CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>criar um mapa por `CameraBoundsOptions` meio </a>do Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, um [objeto de mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) é construído por `new atlas.Map()`meio de. As propriedades do mapa `CameraBoundsOptions` , como, podem ser definidas por meio da função setcamera da classe Map. [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) Os limites e as propriedades de preenchimento são `setCamera`definidos usando.

### <a name="animate-map-view"></a>Animar exibição de mapa

<iframe height='500' scrolling='no' title='Animar exibição de mapa' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a <a href='https://codepen.io/azuremaps/pen/WayvbO/'>exibição de mapa de animação</a> de caneta por<a href='https://codepen.io/azuremaps'>@azuremaps</a>mapas do Azure () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código cria um [objeto](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) de mapa `new atlas.Map()`por meio de. As propriedades do mapa, como o centro e o nível [](/javascript/api/azure-maps-control/atlas.cameraoptions)de zoom, fazem parte das câmerasoptions. `CameraOptions`pode ser definido no Construtor MAP ou por meio [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) da função setcamera da classe Map. O [estilo do mapa](https://docs.microsoft.com/azure/azure-maps/supported-map-styles) é definido `road`como.

O segundo bloco de código cria uma função de mapa de animação, que anima a alteração no modo [](/javascript/api/azure-maps-control/atlas.animationoptions) de exibição de mapa definindo animaçõesoptions por meio da função setcamera. [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) A função é disparada pelo botão ' animar mapa ' para gerar um nível de zoom aleatório após cada clique.

## <a name="try-out-the-code"></a>Experimente o código

Dê uma olhada no código de exemplo acima. Você pode editar o código JavaScript na **guia js** à esquerda e ver a exibição do mapa é alterada na **guia resultado** à direita. Você também pode clicar no botão **Editar no CodePen** e editar o código em CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapeada](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Consulte exemplos de código para adicionar funcionalidade ao seu aplicativo:

> [!div class="nextstepaction"]
> [Escolher um estilo de mapa](choose-map-style.md)

> [!div class="nextstepaction"]
> [Adicionar controles de mapa](map-add-controls.md)
