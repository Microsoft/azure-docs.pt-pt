---
title: Mapear as funcionalidades de estilo no Azure Maps | Documentos da Microsoft
description: Saiba mais sobre o Azure Maps funcionalidades relacionados com o estilo.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 69cf0815a8418df4c8a36adf99770640df327d49
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57568860"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Escolha um estilo de mapa do Azure Maps

Mapas do Azure tem quatro estilos de mapas diferentes à sua escolha. Para mais informações sobre os estilos de mapa, consulte [suportado estilos de mapa do Azure Maps](./supported-map-styles.md). Este artigo mostra como utilizar as funcionalidades relacionadas ao estilo para definir um estilo na carga de mapa, definir um novo estilo e utilizar o controlo de Seletor de estilo.

## <a name="set-style-on-map-load"></a>Estilo de conjunto na carga de mapa

<iframe height='500' scrolling='no' title='Definir o estilo na carga de mapa' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>definindo o estilo na carga de mapa</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

O bloco de código acima define a chave de subscrição e cria um objeto de mapa, com o estilo definido como grayscale_dark. Ver [criar um mapa](./map-create.md) para obter instruções sobre como criar um mapa.

## <a name="update-the-style"></a>Atualizar o estilo

<iframe height='500' scrolling='no' title='A atualizar o estilo' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>atualizar o estilo</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

O bloco de código acima define a chave de subscrição e cria um objeto de mapa sem previamente definir o estilo. Ver [criar um mapa](./map-create.md) para obter instruções sobre como criar um mapa.

O segundo bloco de código utiliza o mapa [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) método para definir o estilo de mapa de satélite.

## <a name="add-the-style-picker"></a>Adicionar o Seletor de estilo

<iframe height='500' scrolling='no' title='Adicionar o Seletor de estilo' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>adicionando o Seletor de estilo</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código no código anterior define a chave de subscrição e cria um objeto de mapa, o estilo de mapa previamente está definido como grayscale_dark. Ver [criar um mapa](./map-create.md) para obter instruções sobre como criar um mapa.

O segundo bloco de código constrói um Seletor de estilo usando o atlas [StyleControl](/javascript/api/azure-maps-control/atlas.controls.stylecontrol) construtor.

Um Seletor de estilo permite a seleção de estilo para o mapa. O terceiro bloco de código adiciona o Seletor de estilo para o mapa através do mapa [Controls](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) método. O Seletor de estilo é no mapa **serviço de escuta de eventos** para se certificar de que carrega depois que o mapa for totalmente carregada.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Adicione controlo a seus mapas:

> [!div class="nextstepaction"]
> [Adicionar controles de mapa](./map-add-controls.md)

Adicione um pin de mapa:

> [!div class="nextstepaction"]
> [Adicionar um pin](./map-add-pin.md)
