---
title: Criando um aplicativo acessível com o Azure Maps | Microsoft Docs
description: Como criar um aplicativo acessível usando mapas do Azure
services: azure-maps
author: chgennar
ms.author: chgennar
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
manager: timlt
ms.openlocfilehash: 8865027c895be09150797608e43184f1fdefb267
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638783"
---
# <a name="building-an-accessible-application"></a>Criando um aplicativo acessível

Este artigo mostra como criar um aplicativo de mapa que pode ser usado por um leitor de tela.

## <a name="understand-the-code"></a>Compreender o código

<iframe height='500' scrolling='no' title='Tornar um aplicativo acessível' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a caneta <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>tornar um aplicativo acessível</a> pelo mapas do Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>() em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O mapa vem predefinido com os recursos de acessibilidade. Os usuários podem navegar pelo mapa usando o teclado. Se um leitor de tela estiver em execução, o mapa notificará o usuário sobre as alterações em seu estado.
Por exemplo, os usuários são notificados sobre alterações de mapa quando o mapa é panorâmico ou ampliado. Todas as informações adicionais que são colocadas no mapa base devem ter informações textuais correspondentes para os usuários do leitor de tela.

Usar o [pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) é uma maneira de conseguir isso. No exemplo de pesquisa acima, um pop-up com informações textuais é adicionado ao mapa para cada PIN que é colocado no mapa. O uso do método [Attach](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#attach) [do Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) permite que o pop-up seja visto por um leitor de tela sem exibir visualmente o popup no mapa.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre a classe popup e seus métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Confira mais exemplos de código:

> [!div class="nextstepaction"]
> [Página de exemplo de código](https://aka.ms/AzureMapsSamples)
