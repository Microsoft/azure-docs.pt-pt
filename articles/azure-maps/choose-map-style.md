---
title: Funcionalidades de estilo de mapa / Microsoft Azure Maps
description: Neste artigo, você vai aprender sobre funcionalidades relacionadas com o estilo disponíveis no Microsoft Azure Maps web SDK.
author: farah-alyasari
ms.author: v-faalya
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b793a0fc977d3da4b4b6753877e838441e70e800
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210094"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Escolha um estilo de mapa em Mapas Azure

Muitos dos estilos de [mapas suportados no Azure Maps](./supported-map-styles.md) estão disponíveis no Web SDK. Este artigo mostra como usar as funcionalidades relacionadas com o estilo. Aprenda a definir um estilo ao carregar um mapa e aprenda a definir um novo estilo de mapa usando o controlo de picker de estilo.

## <a name="set-style-on-map-load"></a>Definir o estilo na carga do mapa

No código seguinte, a opção `style` do mapa está definida para `grayscale_dark` na inicialização.

<br/>

<iframe height='500' scrolling='no' title='Definindo o estilo na carga do mapa' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a caneta <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Definindo o estilo na carga do mapa</a> por Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="update-the-style"></a>Atualizar o estilo

No código seguinte, depois de uma instância de mapa ser carregada, o estilo do mapa é atualizado de `road` para `satellite` utilizando a função [setStyle.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

<br/>

<iframe height='500' scrolling='no' title='Atualizar o estilo' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Atualizando o estilo</a> por Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-the-style-picker"></a>Adicione o picker de estilo

O código seguinte adiciona um [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) ao mapa, para que o utilizador possa facilmente alternar entre os diferentes estilos do mapa. Alternar o estilo do mapa usando o controlo do estilo do mapa perto do canto superior direito.

<br/>

<iframe height='500' scrolling='no' title='Adicionando o picker de estilo' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Adicionando o apanhador</a> de estilo por Azure Maps<a href='https://codepen.io/azuremaps'> (@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Por padrão, ao utilizar o nível de preços S0 do Azure Maps, o controlo do picker de estilo lista todos os estilos disponíveis. Se quiser reduzir o número de estilos nesta lista, passe uma série de estilos que pretende aparecer na lista na opção `mapStyle` do selecionador de estilo. Se estiver a utilizar o S1 e quiser mostrar todos os estilos disponíveis, detete a opção `mapStyles` do selecionador de estilo para `"all"`.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as aulas e métodos utilizados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Adicione controlos aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar controlos de mapas](map-add-controls.md)

> [!div class="nextstepaction"]
> [Adicione um pino](map-add-pin.md)
