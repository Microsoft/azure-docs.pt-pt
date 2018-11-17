---
title: Adicionar controles de mapa do Azure Maps | Documentos da Microsoft
description: Como adicionar o controle de zoom, controle de argumento de venda, girar controle e um Seletor de estilo para um mapa do Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 29737bbe244f9a6fb6799c4ce770a3d2615342aa
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2018
ms.locfileid: "51820618"
---
# <a name="add-map-controls-to-azure-maps"></a>Adicionar controles de mapa do Azure Maps

Este artigo mostra-lhe como adicionar controles de mapa para um mapa. Também aprenderá como criar um mapa com todos os controles e um [Seletor de estilo](https://docs.microsoft.com/azure/azure-maps/choose-map-style#adding-the-style-picker).

## <a name="add-zoom-control"></a>Adicionar o controle de zoom

<iframe height='500' scrolling='no' title='Adição de um controle de zoom' src='//codepen.io/azuremaps/embed/WKOQyN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/WKOQyN/'>adicionando um controle de zoom</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código define a chave de subscrição e cria um objeto de mapa sem previamente definir o estilo. Ver [criar um mapa](./map-create.md) para obter instruções sobre como criar um mapa.

Controle de zoom acrescenta a capacidade de aplicar zoom dentro e fora do mapa. O segundo bloco de código cria um objeto de controle de Zoom, usando o atlas [ZoomControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.zoomcontrol?view=azure-iot-typescript-latest) e adiciona-o para o mapa através do mapa [Controls](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) método. O controle de Zoom está dentro do mapa **serviço de escuta de eventos** para garantir que ele carrega depois que o mapa for totalmente carregada.

## <a name="add-pitch-control"></a>Adicionar controlo de argumento de venda

<iframe height='500' scrolling='no' title='Adição de um controle de argumento de venda' src='//codepen.io/azuremaps/embed/xJrwaP/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/xJrwaP/'>adicionando um controle de argumento de venda</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código define a chave de subscrição e cria um objeto de mapa sem previamente definir o estilo. Ver [criar um mapa](./map-create.md) para obter instruções sobre como criar um mapa.

Controlo de argumento de venda adiciona a capacidade de alterar o argumento de venda do mapa. O segundo bloco de código cria um objeto de argumento de venda controle usando o atlas [PitchControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.pitchcontrol?view=azure-iot-typescript-latest) e adiciona-o para o mapa através do mapa [Controls](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) método. O controle de argumento de venda é no mapa **serviço de escuta de eventos** para garantir que ele carrega depois que o mapa for totalmente carregada.

## <a name="add-compass-control"></a>Adicionar controlo de bússola

<iframe height='500' scrolling='no' title='Adição de um controle de rotação' src='//codepen.io/azuremaps/embed/GBEoRb/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/GBEoRb/'>adicionando um controle rodar</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código define a chave de subscrição e cria um objeto de mapa sem previamente definir o estilo. Ver [criar um mapa](./map-create.md) para obter instruções sobre como criar um mapa.

O segundo bloco de código cria um objeto de controle de bússola usando o atlas [controle de bússola](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.compasscontrol?view=azure-iot-typescript-latest#compasscontrol). Ele também adiciona o controle de bússola para o mapa através do mapa [Controls](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) método. O controle de bússola está dentro do mapa **serviço de escuta de eventos** para garantir que ele carrega depois que o mapa for totalmente carregada.

## <a name="a-map-with-all-controls"></a>Um mapa com todos os controles

<iframe height='500' scrolling='no' title='Um mapa com todos os controles' src='//codepen.io/azuremaps/embed/qyjbOM/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/qyjbOM/'>mapa com todos os controles</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

O primeiro bloco de código define a chave de subscrição e cria um objeto de mapa sem previamente definir o estilo. Ver [criar um mapa](./map-create.md) para obter instruções sobre como criar um mapa.

O segundo bloco de código cria um objeto de controle de bússola usando o atlas [CompassControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.compasscontrol?view=azure-iot-typescript-latest#compasscontrol) e adiciona-o para o mapa através do mapa [Controls](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) método.

O terceiro bloco de código cria um objeto de controle de Zoom, usando o atlas [ZoomControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.zoomcontrol?view=azure-iot-typescript-latest) e adiciona-o para o mapa através do mapa [Controls](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) método.

O quarto bloco de código cria um objeto de argumento de venda controle usando o atlas [PitchControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.pitchcontrol?view=azure-iot-typescript-latest) e adiciona-o para o mapa através do mapa [Controls](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) método.

O último bloco de código cria um objeto de estilo Seletor com o atlas [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol) e adiciona-o para o mapa através do mapa [Controls](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol) método. Todos os objetos de controle são adicionados no mapa **serviço de escuta de eventos** para garantir a carregar depois que o mapa for totalmente carregada.

A ordem dos objetos de controle no script determina a ordem em que aparecem no mapa. Para alterar a ordem dos controles no mapa, pode alterar a sua ordem no script.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Atlas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest)

Veja os artigos seguintes para o código completo:

> [!div class="nextstepaction"]
> [Adicionar um pin](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar um pop-up](./map-add-popup.md)