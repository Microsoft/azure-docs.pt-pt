---
title: Adicionar controles de mapa no Azure Maps | Microsoft Docs
description: Como adicionar controle de zoom, controle de densidade, controle de rotação e um seletor de estilo a um mapa no Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 7a504b8df199a3a461d5eb4e5b7238462b4c438f
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638769"
---
# <a name="add-map-controls-to-azure-maps"></a>Adicionar controles de mapa ao Azure Maps

Este artigo mostra como adicionar controles de mapa a um mapa. Você também aprenderá a criar um mapa com todos os controles e um [seletor de estilo](https://docs.microsoft.com/azure/azure-maps/choose-map-style).

## <a name="add-zoom-control"></a>Adicionar controle de zoom

<iframe height='500' scrolling='no' title='Adicionando um controle de zoom' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>adicionando um controle de zoom</a> pelo mapas do<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código cria um objeto de mapa usando o mecanismo de autenticação anônima. Consulte [criar um mapa](./map-create.md) para obter instruções sobre como criar um mapa.

O controle de zoom adiciona a capacidade de ampliar e reduzir o mapa. O segundo bloco de código cria um objeto de controle de zoom usando o [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) do Atlas e o adiciona ao mapa usando o método Controls do mapa [. Add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) . O controle de zoom está dentro do **ouvinte de evento** de mapa para garantir que ele seja carregado depois que o mapa for totalmente carregado.

## <a name="add-pitch-control"></a>Adicionar controle de densidade

<iframe height='500' scrolling='no' title='Adicionando um controle de timbre' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>adicionando um controle de pitch</a> pelo Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código cria um objeto de mapa usando o mecanismo de autenticação anônima. Consulte [criar um mapa](./map-create.md) para obter instruções sobre como criar um mapa.

Controle de densidade adiciona a capacidade de alterar o timbre do mapa. O segundo bloco de código cria um objeto de controle de timbre usando o [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) do Atlas e o adiciona ao mapa usando o método Controls do mapa [. Add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) . O controle de densidade está dentro do **ouvinte de evento** de mapa para garantir que ele seja carregado depois que o mapa for totalmente carregado.

## <a name="add-compass-control"></a>Adicionar controle de bússola

<iframe height='500' scrolling='no' title='Adicionando um controle de rotação' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>adicionando um controle de giro</a> pelo Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código cria um objeto de mapa usando o mecanismo de autenticação anônima. Consulte [criar um mapa](./map-create.md) para obter instruções sobre como criar um mapa.

O segundo bloco de código cria um objeto de controle de bússola usando o [controle de bússola](/javascript/api/azure-maps-control/atlas.control.compasscontrol)do Atlas. Ele também adiciona o controle Compass ao mapa usando o método [Controls. Add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) do mapa. O controle Compass está dentro do ouvinte de **eventos** de mapa para garantir que ele seja carregado depois que o mapa for totalmente carregado.

## <a name="a-map-with-all-controls"></a>Um mapa com todos os controles

<iframe height='500' scrolling='no' title='Um mapa com todos os controles' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Confira a caneta a <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>um mapa com todos os controles</a> do Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código cria um objeto de mapa usando o mecanismo de autenticação anônima. Consulte [criar um mapa](./map-create.md) para obter instruções sobre como criar um mapa.

O segundo bloco de código cria um objeto de controle Compass usando o [CompassControl](/javascript/api/azure-maps-control/atlas.control.compasscontrol) do Atlas e o adiciona ao mapa usando o método Controls do mapa [. Add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) .

O terceiro bloco de código cria um objeto de controle de zoom usando o [ZoomControl](/javascript/api/azure-maps-control/atlas.control.zoomcontrol) do Atlas e o adiciona ao mapa usando o método [Controls. Add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) do mapa.

O quarto bloco de código cria um objeto de controle de timbre usando o [PitchControl](/javascript/api/azure-maps-control/atlas.control.pitchcontrol) do Atlas e o adiciona ao mapa usando o método Controls do mapa [. Add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) .

O último bloco de código cria um objeto de seletor de estilo usando o [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) do Atlas e o adiciona ao mapa usando o método Controls do mapa [. Add](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) . Todos os objetos de controle são adicionados dentro do ouvinte de **eventos** de mapa para garantir que eles sejam carregados depois que o mapa for totalmente carregado.

A ordem dos objetos de controle no script determina a ordem na qual eles aparecem no mapa. Para alterar a ordem dos controles no mapa, você pode alterar sua ordem no script.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapeada](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)

Consulte os artigos a seguir para obter o código completo:

> [!div class="nextstepaction"]
> [Adicionar um PIN](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar um pop-up](./map-add-popup.md)
