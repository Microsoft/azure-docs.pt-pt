---
title: Criar um mapa com mapas Azure Microsoft Azure Maps
description: Neste artigo, você aprenderá a renderizar um mapa numa página web usando o Microsoft Azure Maps Web SDK.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b4d3784bda1ba5a94dad1cae45513120b3686869
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87130499"
---
# <a name="create-a-map"></a>Criar um mapa

Este artigo mostra-lhe formas de criar um mapa e animar um mapa.  

## <a name="loading-a-map"></a>Carregando um mapa

Para carregar um mapa, crie uma nova instância da [classe Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map). Ao rubricar o mapa, passe um ID de elemento DIV para renderizar o mapa e passe um conjunto de opções a utilizar ao carregar o mapa. Se as informações de autenticação predefinida não forem especificadas no espaço de `atlas` nomes, esta informação terá de ser especificada nas opções do mapa ao carregar o mapa. O mapa carrega vários recursos assíncronos para o desempenho. Como tal, depois de criar a instância do mapa, anexe um `ready` ou evento ao mapa `load` e, em seguida, adicione qualquer código adicional que interaja com o mapa ao manipulador de eventos. O `ready` evento dispara assim que o mapa tiver recursos suficientes para ser interagido programáticamente. O `load` evento dispara após a vista inicial do mapa ter terminado completamente de carregar. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Carga de mapa básico" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>carga do mapa básico</a> da Pen por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Pode carregar vários mapas na mesma página. Vários mapas na mesma página podem usar as mesmas ou diferentes configurações de autenticação e idioma.

## <a name="show-a-single-copy-of-the-world"></a>Mostre uma única cópia do mundo

Quando o mapa é ampliado num ecrã largo, várias cópias do mundo aparecerão horizontalmente. Esta opção é ótima para alguns cenários, mas para outras aplicações é desejável ver uma única cópia do mundo. Este comportamento é implementado definindo a opção de mapas `renderWorldCopies` para `false` .

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = falso" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a Pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = falso</a> por Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="map-options"></a>Opções de mapa

Ao criar um mapa lá, existem vários tipos diferentes de opções que podem ser passadas para personalizar como o mapa funciona como listado abaixo.

- [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions) e [CameraBoundOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions) são usados para especificar a área que o mapa deve exibir.
- [As Opções de Serviço](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions) são usadas para especificar como o mapa deve interagir com serviços que alimentam o mapa.
- [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) são usados para especificar que o mapa deve ser modelado e renderizado.
- [As Opções de Utilização](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions) são utilizadas para especificar como o mapa deve chegar quando o utilizador está a interagir com o mapa. 

Estas opções também podem ser atualizadas depois de o mapa ter sido carregado usando as `setCamera` `setServiceOptions` `setStyle` funções , e `setUserInteraction` funções. 

## <a name="controlling-the-map-camera"></a>Controlando a câmara do mapa

Há duas maneiras de definir a área exibida do mapa usando a câmara de um mapa. Pode definir as opções da câmara ao carregar o mapa. Ou, pode ligar para a `setCamera` opção a qualquer momento depois de o mapa ter sido carregado para atualizar programáticamente a vista do mapa.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Definir a câmara

A câmara do mapa controla o que é exibido no ecrã da tela do mapa. As opções da câmara podem ser passadas nas opções do mapa quando são inicializadas ou passadas na função de `setCamera` mapas.

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

No código seguinte, é criado um [objeto mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) e as opções de centro e zoom são definidas. As propriedades do mapa, tais como o nível de centro e zoom, fazem parte das [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Criar um mapa através de CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Ver a Caneta <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Criar `CameraOptions` um mapa através de </a>Azure Location Based Services <a href='https://codepen.io/azuremaps'>@azuremaps</a> () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Definir os limites da câmara

Uma caixa de limites pode ser usada para atualizar a câmara do mapa. Se a caixa de delimitação foi calculada a partir de dados de ponto, muitas vezes é útil especificar também um valor de enchimento de pixels nas opções da câmara para responder ao tamanho do ícone. Isto ajudará a garantir que os pontos não caiam fora da borda do porto do mapa.

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

No seguinte código, um [objeto mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) é construído via `new atlas.Map()` . Propriedades de mapa como `CameraBoundsOptions` podem ser definidas através da função [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) da classe Mapa. Os limites e as propriedades de enchimento são definidos usando `setCamera` .

<br/>

<iframe height='500' scrolling='no' title='Criar um mapa através de CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Ver a Caneta <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Criar `CameraBoundsOptions` um mapa via </a>Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Vista de mapa animado

Ao definir as opções da câmara do mapa, [as opções](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.animationoptions) de animação também podem ser definidas. Estas opções especificam o tipo de animação e duração que deve ser necessário para mover a câmara.

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

No código seguinte, o primeiro bloco de código cria um mapa e define os estilos de mapa de entrada e zoom. No segundo bloco de código, é criado um manipulador de eventos de clique para o botão animato. Quando este botão é clicado, a `setCamera` função é chamada com alguns valores aleatórios para as [Opções de Câmara](/javascript/api/azure-maps-control/atlas.cameraoptions) e [Opções de Animação](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Vista do mapa animado' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Vista <a href='https://codepen.io/azuremaps/pen/WayvbO/'>do Mapa Da Caneta Animate</a> by Azure Maps <a href='https://codepen.io/azuremaps'>@azuremaps</a> () no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Experimente o código

Olha para as amostras de código. Pode editar o código JavaScript dentro do **separador JS** e ver as alterações da visualização do mapa no **separador Resultados.** Também pode clicar **em Editar no CodePen,** no canto superior direito, e modificar o código no CodePen.

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
