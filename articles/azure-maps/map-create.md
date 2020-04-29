---
title: Crie um mapa com o Azure Maps Microsoft Azure Maps
description: Neste artigo, você aprenderá a renderizar um mapa numa página web usando o Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c85d6078fce7fc8e5a5b5d8485517a8b262044a9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80802336"
---
# <a name="create-a-map"></a>Criar um mapa

Este artigo mostra-lhe formas de criar um mapa e animar um mapa.  

## <a name="loading-a-map"></a>Carregar um mapa

Para carregar um mapa, crie uma nova instância da [classe Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map). Ao inicializar o mapa, passe um ID de elemento DIV para renderizar o mapa e passar um conjunto de opções a utilizar ao carregar o mapa. Se as informações de autenticação `atlas` padrão não forem especificadas no espaço de nome, esta informação terá de ser especificada nas opções do mapa ao carregar o mapa. O mapa carrega vários recursos assincronicamente para o desempenho. Como tal, depois de criar `ready` a `load` instância do mapa, prenda um ou evento ao mapa e, em seguida, adicione qualquer código adicional que interaja com o mapa ao manipulador de eventos. O `ready` evento dispara assim que o mapa tem recursos suficientes carregados para ser interagido programáticamente. O `load` evento dispara depois da vista inicial do mapa ter terminado completamente o carregamento. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Carga básica do mapa" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja a <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>carga do mapa</a> básico<a href='https://codepen.io/azuremaps'>@azuremaps</a>pen por Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Pode carregar vários mapas na mesma página. O mapa múltiplo na mesma página pode utilizar as mesmas definições de autenticação ou idioma.

## <a name="show-a-single-copy-of-the-world"></a>Mostre uma única cópia do mundo

Quando o mapa é ampliado num ecrã largo, várias cópias do mundo aparecerão horizontalmente. Esta opção é ótima para alguns cenários, mas para outras aplicações é desejável ver uma única cópia do mundo. Este comportamento é implementado definindo a opção de mapas `renderWorldCopies` para `false`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopy = falso" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopy =</a> falsa<a href='https://codepen.io/azuremaps'>@azuremaps</a>por Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="map-options"></a>Opções de mapa

Ao criar um mapa lá, existem vários tipos diferentes de opções que podem ser transmitidas para personalizar como o mapa funciona como listado abaixo.

- [As Opções de Câmara](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions) e [as Opções De Câmara](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions) são utilizadas para especificar a área que o mapa deve apresentar.
- [As Opções](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions) de Serviço são utilizadas para especificar como o mapa deve interagir com serviços que alimentam o mapa.
- [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) são usados para especificar que o mapa deve ser modelado e renderizado.
- [UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions) são usados para especificar como o mapa deve chegar quando o utilizador está interagindo com o mapa. 

Estas opções também podem ser atualizadas `setCamera`depois `setServiceOptions` `setStyle`de `setUserInteraction` o mapa ter sido carregado com as funções e funções. 

## <a name="controlling-the-map-camera"></a>Controlando a câmara do mapa

Existem duas maneiras de definir a área exibida do mapa usando a câmara de um mapa. Pode definir as opções da câmara ao carregar o mapa. Ou, pode ligar `setCamera` para a opção a qualquer momento depois de o mapa ter carregado para atualizar programáticamente a vista do mapa.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Definir a câmara

A câmara do mapa controla o que é exibido no viewport da tela do mapa. As opções da câmara podem ser passadas para `setCamera` as opções do mapa quando são inicializadas ou transmitidas para a função de mapas.

```javascript
//Set the camera options when creating the map.
var map = new atlas.Map('map', {
    center: [-122.33, 47.6],
    zoom: 12

    //Additional map options.
};

//Update the map camera at anytime using setCamera function.
map.setCamera({
    center: [-110, 45],
    zoom: 5 
});
```

No código seguinte, é criado um [objeto Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) e as opções central e de zoom são definidas. As propriedades do mapa, como o nível central e de zoom, fazem parte das [Opções](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions)de Câmara .

<br/>

<iframe height='500' scrolling='no' title='Criar um mapa via CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a caneta <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Criar `CameraOptions` um mapa via </a><a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Location Based Services () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Definir os limites da câmara

Uma caixa de delimitação pode ser usada para atualizar a câmara do mapa. Se a caixa de delimitação foi calculada a partir de dados pontuais, é muitas vezes útil também especificar um valor de acolchoamento de pixels nas opções da câmara para explicar o tamanho do ícone. Isto ajudará a garantir que os pontos não caiam da borda do viewport do mapa.

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

No seguinte código, um objeto de `new atlas.Map()` [mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) é construído via . Propriedades do `CameraBoundsOptions` mapa como podem ser definidas através da função [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) da classe Map. Os limites e as `setCamera`propriedades do enchimento são definidos utilizando .

<br/>

<iframe height='500' scrolling='no' title='Criar um mapa via CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a caneta <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Criar `CameraBoundsOptions` um mapa </a><a href='https://codepen.io/azuremaps'>@azuremaps</a>via Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Vista do mapa animado

Ao definir as opções da câmara do mapa, as opções de [animação](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.animationoptions) também podem ser definidas. Estas opções especificam o tipo de animação e duração que deve levar para mover a câmara.

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

No código seguinte, o primeiro bloco de código cria um mapa e define os estilos de mapa de entrada e zoom. No segundo bloco de código, é criado um manipulador de eventos para o botão de animação. Quando este botão é `setCamera` clicado, a função é chamada com alguns valores aleatórios para as [Opções de Câmara](/javascript/api/azure-maps-control/atlas.cameraoptions) e [Opções de Animação](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Vista do mapa animado' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a <a href='https://codepen.io/azuremaps/pen/WayvbO/'>Pen Animate Map</a> View<a href='https://codepen.io/azuremaps'>@azuremaps</a>by Azure Maps () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Experimente o código

Olha para as amostras de código. Pode editar o código JavaScript dentro do **separador JS** e ver as alterações na vista do mapa no **separador Resultado**. Também pode clicar **em Editar no CodePen,** no canto superior direito, e modificar o código em CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Opções de Câmara](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [Opções de Animação](/javascript/api/azure-maps-control/atlas.animationoptions)

Consulte exemplos de código para adicionar funcionalidade à sua aplicação:

> [!div class="nextstepaction"]
> [Alterar o estilo do mapa](choose-map-style.md)

> [!div class="nextstepaction"]
> [Adicionar controlos ao mapa](map-add-controls.md)

> [!div class="nextstepaction"]
> [Amostras de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)
