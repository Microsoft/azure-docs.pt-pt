---
title: Visão geral das âncoras espaciais do Azure
description: Saiba como as âncoras espaciais do Azure ajudam você a desenvolver experiências de realidade mista entre plataformas.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 503004cba0d4109adcfee62f7acd108cbcb73eb6
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277143"
---
# <a name="azure-spatial-anchors-overview"></a>Visão geral das âncoras espaciais do Azure

Bem-vindo às âncoras espaciais do Azure. As âncoras espaciais do Azure capacitam os desenvolvedores com recursos essenciais para criar aplicativos de realidade misturados com consciência espacial. Esses aplicativos podem dar suporte ao Microsoft HoloLens, dispositivos baseados em iOS com suporte a ARKit e dispositivos baseados em Android que dão suporte a ARCore. O Azure Spatial Anchors permite que os programadores trabalhem com plataformas de realidade mista para reconhecer espaços, designar pontos de interesse precisos e revocar esses pontos de interesse de dispositivos suportados.
Esses pontos de interesse precisos são chamados de âncoras espaciais.

![Para várias plataformas](./media/cross-platform.png)

## <a name="examples"></a>Exemplos

Alguns casos de uso de exemplo habilitados por âncoras espaciais incluem:

- [Experiências de vários usuários](tutorials/tutorial-share-anchors-across-devices.md). As âncoras espaciais facilitam para as pessoas no mesmo lugar participarem de aplicativos de realidade misturada de vários usuários. Por exemplo, duas pessoas podem iniciar um jogo de xadrez de realidade misturada colocando um tabuleiro de xadrez virtual em uma tabela. Em seguida, apontando seu dispositivo na tabela, eles podem exibir e interagir com o painel de xadrez virtual juntos.

- [Como encontrar](concepts/anchor-relationships-way-finding.md). Os desenvolvedores também podem conectar âncoras espaciais, criando relações entre elas. Por exemplo, um aplicativo pode incluir uma experiência que tenha dois ou mais pontos de interesse com os quais um usuário deve interagir para concluir uma tarefa. Esses pontos de interesse podem ser criados de maneira conectada. Posteriormente, quando o usuário estiver concluindo a tarefa de várias etapas, o aplicativo poderá solicitar âncoras que estejam próximas da atual para direcionar o usuário para a próxima etapa na tarefa.

- [Persistência de conteúdo virtual no mundo real](how-tos/create-locate-anchors-unity.md#create-a-cloud-spatial-anchor). Um aplicativo pode permitir que um usuário Coloque um calendário virtual em uma parede da sala de conferência, que as pessoas possam ver usando um aplicativo de telefone ou um dispositivo HoloLens. Numa configuração industrial, um utilizador pode receber informações contextuais sobre um computador ao apontar para uma câmara de dispositivo suportada.

O Azure Spatial Anchors é composto por um serviço gerido e por SDKs de cliente para plataformas de dispositivos suportadas. As seções a seguir fornecem informações sobre como começar a criar aplicativos usando âncoras espaciais do Azure.

## <a name="next-steps"></a>Passos seguintes

Crie seu primeiro aplicativo com âncoras espaciais.

> [!div class="nextstepaction"]
> [Unity](unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](quickstarts/get-started-hololens.md)
