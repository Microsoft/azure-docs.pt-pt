---
title: Visão geral das âncoras espaciais de Azure
description: Saiba como a Azure Spatial Anchors ajuda a desenvolver experiências de realidade mistas entre plataformas.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: a422371bacddf049365562fce9af7e61f35089a1
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95487389"
---
# <a name="azure-spatial-anchors-overview"></a>Visão geral das âncoras espaciais de Azure

Bem-vindos ao Azure Spatial Anchors. A Azure Spatial Anchors capacita os desenvolvedores com capacidades essenciais para construir aplicações de realidade mista conscientes espacialmente. Estas aplicações podem suportar o Microsoft HoloLens, dispositivos baseados em iOS que suportam ARKit e dispositivos baseados no Android que suportam o ARCore. O Azure Spatial Anchors permite que os programadores trabalhem com plataformas de realidade mista para reconhecer espaços, designar pontos de interesse precisos e revocar esses pontos de interesse de dispositivos suportados.
Estes pontos de interesse precisos são referidos como Âncoras Espaciais.

![Plataforma Transversal](./media/cross-platform.png)

## <a name="examples"></a>Exemplos

Alguns casos de utilização de exemplos habilitados por Âncoras Espaciais incluem:

- [Experiências multiutilizadores.](tutorials/tutorial-share-anchors-across-devices.md) As Âncoras Espaciais Azure facilitam a participação de aplicações de realidade mista multiutilizador. Por exemplo, duas pessoas podem começar um jogo de xadrez de realidade mista colocando um tabuleiro de xadrez virtual em uma mesa. Depois, apontando o dispositivo para a mesa, podem ver e interagir com o tabuleiro de xadrez virtual juntos.

- [Descoberta de caminho.](concepts/anchor-relationships-way-finding.md) Os desenvolvedores também podem ligar Âncoras Espaciais juntas criando relações entre eles. Por exemplo, uma aplicação pode incluir uma experiência que tenha dois ou mais pontos de interesse com os quais um utilizador deve interagir para completar uma tarefa. Esses pontos de interesse podem ser criados de forma conectada. Mais tarde, quando o utilizador estiver a completar a tarefa em várias etapas, a aplicação pode pedir âncoras que se encontrem nas proximidades da atual para direcionar o utilizador para o próximo passo na tarefa.

- [Persistindo de conteúdo virtual no mundo real.](how-tos/create-locate-anchors-unity.md#create-a-cloud-spatial-anchor) Uma aplicação pode permitir que um utilizador coloque um calendário virtual na parede de uma sala de conferências, que as pessoas possam ver usando uma aplicação de telefone ou um dispositivo HoloLens. Numa configuração industrial, um utilizador pode receber informações contextuais sobre um computador ao apontar para uma câmara de dispositivo suportada.

O Azure Spatial Anchors é composto por um serviço gerido e por SDKs de cliente para plataformas de dispositivos suportadas. As secções seguintes fornecem informações sobre começar com aplicações de construção usando âncoras espaciais Azure.

## <a name="next-steps"></a>Passos seguintes

Crie a sua primeira aplicação com âncoras espaciais Azure.

> [!div class="nextstepaction"]
> [Unidade (HoloLens)](quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unidade (iOS)](quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unidade (Android)](quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](quickstarts/get-started-xamarin-ios.md)
