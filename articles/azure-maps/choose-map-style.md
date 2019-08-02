---
title: Funcionalidades de estilo de mapa no Azure Maps | Microsoft Docs
description: Saiba mais sobre as funcionalidades relacionadas ao estilo do Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 52936b14264bd4fe1846ae365e1de447d594b612
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639055"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Escolher um estilo de mapa no Azure Maps

O mapas do Azure tem quatro estilos de mapas diferentes para escolher. Para saber mais sobre os estilos de mapa, confira [estilos de mapa com suporte no Azure Maps](./supported-map-styles.md). Este artigo mostra como usar as funcionalidades relacionadas ao estilo para definir um estilo na carga do mapa, definir um novo estilo e usar o controle do seletor de estilo.

## <a name="set-style-on-map-load"></a>Definir estilo na carga do mapa

<iframe height='500' scrolling='no' title='Definindo o estilo na carga do mapa' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Configurando o estilo na carga do mapa</a> pelo<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O bloco de código acima define a chave de assinatura e cria um objeto de mapa com o estilo definido como grayscale_dark. Consulte [criar um mapa](./map-create.md) para obter instruções sobre como criar um mapa.

## <a name="update-the-style"></a>Atualizar o estilo

<iframe height='500' scrolling='no' title='Atualizando o estilo' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>atualizando o estilo</a> pelo Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O bloco de código acima define a chave de assinatura e cria um objeto de mapa sem antes definir o estilo. Consulte [criar um mapa](./map-create.md) para obter instruções sobre como criar um mapa.

O segundo bloco de código usa o método [SetStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) do mapa para definir o estilo do mapa como satélite.

## <a name="add-the-style-picker"></a>Adicionar o seletor de estilo

<iframe height='500' scrolling='no' title='Adicionando o seletor de estilo' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>adicionando o seletor de estilo</a> pelo Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código no código acima define a chave de assinatura e cria um objeto de mapa, o estilo de mapa é predefinido como grayscale_dark. Consulte [criar um mapa](./map-create.md) para obter instruções sobre como criar um mapa.

O segundo bloco de código constrói um seletor de estilo usando o construtor [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) do Atlas.

Um seletor de estilo habilita a seleção de estilo para o mapa. O terceiro bloco de código adiciona o seletor de estilo ao mapa usando o método [Controls. Add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) do mapa. O seletor de estilo está dentro do ouvinte de **evento** de mapa para garantir que ele seja carregado depois que o mapa for totalmente carregado.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapeada](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Adicione controle aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar controles de mapa](./map-add-controls.md)

Adicionar um PIN de mapa:

> [!div class="nextstepaction"]
> [Adicionar um PIN](./map-add-pin.md)
