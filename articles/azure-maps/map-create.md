---
title: Criar um mapa com o Azure Maps | Documentos da Microsoft
description: Como criar um mapa de Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 10/30/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 222fc5e9083c03ff0d4e31927363c5f517cf32a9
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108604"
---
# <a name="create-a-map"></a>Criar um mapa

Este artigo mostra-lhe formas de criar um mapa e animar um mapa.  

## <a name="understand-the-code"></a>Compreender o código

Existem duas formas, pode construir um mapa. Pode definir a câmera do mapa, especificando o ponto central e nível de zoom, ou pode definir os limites de câmera do mapa especificando Sudoeste e nordeste delimitação de pontos.

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Definir a câmara

<iframe height='500' scrolling='no' title='Criar um mapa via CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>criar um mapa via `CameraOptions` </a>pelo Azure Location Based Services (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, uma [objeto de mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) é criado através de `new atlas.Map()` e o centro e zoom estão definidos. As propriedades do mapa, como o nível de centro e zoom fazem parte da [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions).

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Defina os limites de câmara

<iframe height='500' scrolling='no' title='Criar um mapa via CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>criar um mapa via `CameraBoundsOptions` </a>pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, uma [objeto de mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) é construída por meio de `new atlas.Map()`. Propriedades do mapa, tal como `CameraBoundsOptions` pode ser definida [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) função da classe Map. As propriedades de preenchimento e de limites são definidas usando `setCamera`.

### <a name="animate-map-view"></a>Animar a vista do mapa

<iframe height='500' scrolling='no' title='Animar a vista do mapa' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/WayvbO/'>animar a vista do mapa</a> através do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

No código acima, o primeiro bloco de código cria um [objeto de mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) via `new atlas.Map()`. As propriedades do mapa, como o nível de centro e zoom fazem parte da [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions). `CameraOptions` podem ser definidos no construtor de mapa ou via [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) função da classe Map. O [estilo de mapa](https://docs.microsoft.com/azure/azure-maps/supported-map-styles) está definida como `road`.

O segundo bloco de código cria uma função de mapa animar, que anima a alteração na vista de mapa definindo [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions) via [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) função. A função é acionada pelo botão "Animar o mapa" para gerar um nível de zoom aleatório após cada clique.

## <a name="try-out-the-code"></a>Experimente o código

Dar uma olhada no código de exemplo acima. Pode editar o código JavaScript no **separador JS** à esquerda e consulte a vista do mapa alterações na **separador de resultado** à direita. Também pode clicar no **editar no CodePen** botão e editar o código na CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Veja exemplos de código para adicionar funcionalidades à sua aplicação:

> [!div class="nextstepaction"]
> [Escolha um estilo de mapa](choose-map-style.md)

> [!div class="nextstepaction"]
> [Adicionar controles de mapa](map-add-controls.md)
