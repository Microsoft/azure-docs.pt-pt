---
title: Criar um mapa com mapas do Azure | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a renderizar um mapa numa página web usando o Microsoft Azure Maps Web SDK.
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 578abae5b206b31674b00b9d27ef34174b93759f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988588"
---
# <a name="create-a-map"></a>Criar um mapa

Este artigo mostra maneiras de criar um mapa e animar um mapa.  

## <a name="loading-a-map"></a>Carregando um mapa

Para carregar um mapa, crie uma nova instância da [classe Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). Ao inicializar o mapa, passe um ID de elemento DIV para renderizar o mapa e passar um conjunto de opções a utilizar ao carregar o mapa. Se as informações de autenticação padrão não forem especificadas no namespace `atlas`, essas informações precisarão ser especificadas nas opções de mapa ao carregar o mapa. O mapa carrega vários recursos de forma assíncrona para o desempenho. Como tal, depois de criar a instância do mapa, prenda um evento `ready` ou `load` ao mapa e, em seguida, adicione qualquer código adicional que interaja com o mapa ao manipulador de eventos. O evento `ready` é acionado assim que o mapa tem recursos suficientes carregados para interagir com programaticamente. O evento `load` é acionado depois que o modo de exibição do mapa inicial termina completamente de ser carregado. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Carregamento de mapa básico" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>mapa básico</a> da caneta pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Pode carregar vários mapas na mesma página. O mapa múltiplo na mesma página pode utilizar as mesmas definições de autenticação ou idioma.

## <a name="show-a-single-copy-of-the-world"></a>Mostrar uma única cópia do mundo

Quando o mapa for ampliado em uma tela larga, várias cópias do mundo aparecerão horizontalmente. Esta opção é ótima para alguns cenários, mas para outras aplicações é desejável ver uma única cópia do mundo. Este comportamento é implementado definindo os mapas `renderWorldCopies` opção para `false`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Confira a caneta <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="controlling-the-map-camera"></a>Controlando a câmera do mapa

Existem duas maneiras de definir a área exibida do mapa usando a câmara de um mapa. Pode definir as opções da câmara ao carregar o mapa. Ou, pode ligar para a opção `setCamera` a qualquer momento depois de o mapa ter carregado para atualizar programáticamente a vista do mapa.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Definir a câmera

No código a seguir, um [objeto MAP](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) é criado e as opções Center e zoom são definidas. As propriedades do mapa, como o nível central e de zoom, fazem parte das [Opções](/javascript/api/azure-maps-control/atlas.cameraoptions)de Câmara .

<br/>

<iframe height='500' scrolling='no' title='Criar um mapa por meio de CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>criar um mapa por meio de `CameraOptions` </a>pelos serviços do Azure baseados na localização (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Definir os limites da câmera

No código a seguir, um [objeto de mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) é construído por meio de `new atlas.Map()`. As propriedades do mapa, como `CameraBoundsOptions`, podem ser definidas por meio da função [setcamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) da classe Map. Os limites e as propriedades de preenchimento são definidos usando `setCamera`.

<br/>

<iframe height='500' scrolling='no' title='Criar um mapa por meio de CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>criar um mapa por meio de `CameraBoundsOptions` </a>pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Animar exibição de mapa

No código seguinte, o primeiro bloco de código cria um mapa e define os estilos de mapa de entrada e zoom. No segundo bloco de código, um manipulador de eventos de clique é criado para o botão de animação. Quando este botão é clicado, a função `setCamera` é chamada com alguns valores aleatórios para as [Opções de Câmara](/javascript/api/azure-maps-control/atlas.cameraoptions) e [Opções de Animação](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Animar exibição de mapa' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a <a href='https://codepen.io/azuremaps/pen/WayvbO/'>exibição de mapa de animação</a> de caneta por mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Experimente o código

Olha para as amostras de código. Pode editar o código JavaScript dentro do **separador JS** e ver as alterações na vista do mapa no **separador Resultado**. Também pode clicar **em Editar no CodePen,** no canto superior direito, e modificar o código em CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

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
