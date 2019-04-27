---
title: Tornar uma aplicação acessível com o Azure Maps | Documentos da Microsoft
description: Como criar uma aplicação acessível com o Azure Maps
services: azure-maps
keywords: ''
author: chgennar
ms.author: chgennar
ms.date: 09/17/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.openlocfilehash: ef948b4dca3d3800a81ac52f3a73beee2558d21c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769707"
---
# <a name="building-an-accessible-application"></a>Criando um aplicativo acessível

Este artigo mostra-lhe como criar um aplicativo de mapa que pode ser utilizado por um leitor de ecrã.

## <a name="understand-the-code"></a>Compreender o código

<iframe height='500' scrolling='no' title='Tornar uma aplicação acessível' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>tornar uma aplicação acessível</a> ao Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) na <a href='https://codepen.io'>CodePen</a>.
</iframe>

O mapa vem criados previamente com funcionalidades de acessibilidade. Os usuários podem navegar o mapa com o teclado. Se um leitor de tela está em execução, o mapa notificará o usuário das alterações ao seu estado.
Por exemplo, os utilizadores são notificados de alterações do mapa quando o mapa é estendido ou ampliado. Qualquer informação adicional que é colocada no mapa base deve ter informações textuais correspondentes para os utilizadores de leitor de ecrã.

Usando [pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) é uma forma de atingir esse objetivo. No exemplo de pesquisa acima, um pop-up com informações textuais é adicionado ao mapa para cada pin que é colocado no mapa. Utilizar o [do pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) [anexar](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#attach) método permite que os pop-up ser visto por um leitor de ecrã sem apresentar visualmente o pop-up no mapa.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre a classe de pop-up e seus métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Veja mais exemplos de código:

> [!div class="nextstepaction"]
> [Página de exemplo de código](https://aka.ms/AzureMapsSamples)
