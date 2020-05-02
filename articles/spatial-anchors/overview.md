---
title: Visão geral das Âncoras Espaciais Azure
description: Saiba como a Azure Spatial Anchors o ajuda a desenvolver experiências de realidade mista de plataformas cruzadas.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 5ebd29b5fb6fdedcdfbc434209b350512c4cd5dc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77657312"
---
# <a name="azure-spatial-anchors-overview"></a>Visão geral das Âncoras Espaciais Azure

Bem-vindos às Âncoras Espaciais Azure. A Azure Spatial Anchors capacita os desenvolvedores com capacidades essenciais para construir aplicações de realidade mista espacialmente conscientes. Estas aplicações podem suportar o Microsoft HoloLens, dispositivos baseados no iOS que suportam arKit e dispositivos baseados no Android que suportam arCore. O Azure Spatial Anchors permite que os programadores trabalhem com plataformas de realidade mista para reconhecer espaços, designar pontos de interesse precisos e revocar esses pontos de interesse de dispositivos suportados.
Estes pontos de interesse precisos são referidos como Âncoras Espaciais.

![Plataforma Transversal](./media/cross-platform.png)

## <a name="examples"></a>Exemplos

Alguns casos de utilização de exemplo saem:

- [Experiências multiutilizadores.](tutorials/tutorial-share-anchors-across-devices.md) As Âncoras Espaciais facilitam a participação de aplicações de realidade mista multiutilizador. Por exemplo, duas pessoas podem começar um jogo de xadrez de realidade mista colocando um tabuleiro de xadrez virtual sobre uma mesa. Depois, apontando o seu dispositivo para a mesa, podem ver e interagir com o tabuleiro de xadrez virtual juntos.

- [Encontrar caminhos.](concepts/anchor-relationships-way-finding.md) Os desenvolvedores também podem ligar âncoras espaciais, criando relações entre eles. Por exemplo, uma aplicação pode incluir uma experiência que tenha dois ou mais pontos de interesse com os quais um utilizador deve interagir para completar uma tarefa. Esses pontos de interesse podem ser criados de forma conectada. Mais tarde, quando o utilizador estiver a completar a tarefa em várias etapas, a aplicação pode pedir âncoras próximas da atual para direcionar o utilizador para o próximo passo na tarefa.

- [Persistir em conteúdos virtuais no mundo real.](how-tos/create-locate-anchors-unity.md#create-a-cloud-spatial-anchor) Uma aplicação pode permitir que um utilizador coloque um calendário virtual numa parede de sala de conferências, que as pessoas possam ver usando uma aplicação telefónica ou um dispositivo HoloLens. Numa configuração industrial, um utilizador pode receber informações contextuais sobre um computador ao apontar para uma câmara de dispositivo suportada.

O Azure Spatial Anchors é composto por um serviço gerido e por SDKs de cliente para plataformas de dispositivos suportadas. As seguintes secções fornecem informações sobre começar com aplicações de construção usando âncoras espaciais Azure.

## <a name="next-steps"></a>Passos seguintes

Crie a sua primeira aplicação com Âncoras Espaciais.

> [!div class="nextstepaction"]
> [Unidade (Hololens)](quickstarts/get-started-unity-hololens.md)

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
