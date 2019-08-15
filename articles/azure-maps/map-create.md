---
title: Criar um mapa com mapas do Azure | Microsoft Docs
description: Como criar um mapa com o SDK da Web do Azure Maps.
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 932c244ff41e757413a05cde019ee7ee1a82232d
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976129"
---
# <a name="create-a-map"></a>Criar um mapa

Este artigo mostra maneiras de criar um mapa e animar um mapa.  

## <a name="loading-a-map"></a>Carregando um mapa

Para carregar um mapa, crie uma nova instância da [classe Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). Ao inicializar o mapa, uma ID do elemento DIV para renderizar o mapa e um conjunto de opções a serem usadas durante o carregamento do mapa são transmitidas. Se as informações de autenticação padrão não forem `atlas` especificadas no namespace, essas informações precisarão ser especificadas nas opções de mapa ao carregar o mapa. O mapa carrega vários recursos de forma assíncrona para o desempenho. Assim, depois de criar a instância de mapa, anexe `ready` um `load` evento ou ao mapa e, em seguida, adicione qualquer código adicional que interaja com o mapa nesse manipulador de eventos. O `ready` evento é acionado assim que o mapa tem recursos suficientes carregados para interagir com programaticamente. O `load` evento é acionado depois que o modo de exibição de mapa inicial termina completamente de ser carregado. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Carregamento de mapa básico" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>mapa básico</a> da caneta pelo mapas do Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>() em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Vários mapas podem ser carregados na mesma página e cada um deles pode usar a mesma configuração e configurações de idioma e autenticação diferentes.

## <a name="show-a-single-copy-of-the-world"></a>Mostrar uma única cópia do mundo

Quando o mapa for ampliado em uma tela larga, várias cópias do mundo aparecerão horizontalmente. Isso é ótimo para a maioria dos cenários, mas alguns para alguns aplicativos, pode ser desejável ver apenas uma única cópia do mundo. Isso pode ser feito definindo a opção Maps `renderWorldCopies` como. `false`

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Confira a caneta <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> pelo Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="controlling-the-map-camera"></a>Controlando a câmera do mapa

Há duas maneiras de definir a área exibida do mapa usando a câmera. Você pode definir opções de câmera como Center e zoom, ao carregar o mapa ou chamar a `setCamera` opção a qualquer momento depois que o mapa for carregado para atualizar programaticamente a exibição do mapa.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Definir a câmera

No código a seguir, um [objeto MAP](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) é criado e as opções Center e zoom são definidas. As propriedades do mapa, como centro, e nível de zoom fazem [](/javascript/api/azure-maps-control/atlas.cameraoptions)parte das câmerasoptions.

<br/>

<iframe height='500' scrolling='no' title='Criar um mapa por meio de CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>criar um mapa por `CameraOptions` meio </a>dos serviços do Azure baseados<a href='https://codepen.io/azuremaps'>@azuremaps</a>na localização () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Definir os limites da câmera

No código a seguir, um [objeto de mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) é construído `new atlas.Map()`por meio de. As propriedades do mapa `CameraBoundsOptions` , como, podem ser definidas por meio da função setcamera da classe Map. [](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) Os limites e as propriedades de preenchimento são `setCamera`definidos usando.

<br/>

<iframe height='500' scrolling='no' title='Criar um mapa por meio de CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>criar um mapa por `CameraBoundsOptions` meio </a>do Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() em <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Animar exibição de mapa

No código a seguir, o primeiro bloco de código cria um mapa e define o estilo de mapa, o centro e os valores de zoom. No segundo bloco de código, um manipulador de eventos de clique é criado para o botão de animação. Quando esse botão é clicado, a função setcamera é chamada com alguns valores aleatórios para [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions), [animationoptions](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Animar exibição de mapa' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a <a href='https://codepen.io/azuremaps/pen/WayvbO/'>exibição de mapa de animação</a> de caneta por<a href='https://codepen.io/azuremaps'>@azuremaps</a>mapas do Azure () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Experimente o código

Dê uma olhada no código de exemplo acima. Você pode editar o código JavaScript na **guia js** à esquerda e ver a exibição do mapa é alterada na **guia resultado** à direita. Você também pode clicar no botão **Editar no CodePen** e editar o código em CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapeada](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Câmara de câmera](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [Animaçãooptions](/javascript/api/azure-maps-control/atlas.animationoptions)

Consulte exemplos de código para adicionar funcionalidade ao seu aplicativo:

> [!div class="nextstepaction"]
> [Alterar o estilo do mapa](choose-map-style.md)

> [!div class="nextstepaction"]
> [Adicionar controles ao mapa](map-add-controls.md)

> [!div class="nextstepaction"]
> [Exemplos de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)