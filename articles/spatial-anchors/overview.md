---
title: Descrição geral de âncoras espaciais do Azure | Documentos da Microsoft
description: Saiba como as âncoras espaciais do Azure ajuda-o a desenvolver experiências de realidade mista de várias plataformas.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 24a35b387a8b47d44f742303ddde0a0e8fb47fe6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57833803"
---
# <a name="azure-spatial-anchors-overview"></a>Descrição geral de âncoras espaciais do Azure

Bem-vindo ao Azure âncoras espaciais. Âncoras geográficos Azure capacita os desenvolvedores com funcionalidades essenciais para criar aplicações de realidade mista spatially ciente. Esses aplicativos podem suportar Microsoft HoloLens, os dispositivos baseados no iOS ARKit de suporte e dispositivos baseados em Android ARCore de suporte. Âncoras espaciais do Azure permite aos programadores funciona com plataformas de realidade mista, poderá ver espaços, designar precisos pontos de interesse e recuperar esses pontos de interesse de dispositivos suportados.
Esses pontos precisos de interesse são referidos como âncoras espaciais.

![Para várias plataformas](./media/cross-platform.png)

## <a name="examples"></a>Exemplos

Alguns casos de utilização de exemplo ativados por âncoras geográficos incluem:

- [Experiências de vários utilizadores](tutorials/tutorial-share-anchors-across-devices.md). Âncoras geográficos torna mais fácil para as pessoas no mesmo local participar em aplicações de realidade mista de vários utilizadores. Por exemplo, duas pessoas podem iniciar um jogo de chess de realidade mista, colocando um quadro de virtual chess numa tabela. Em seguida, ao apontar o seu dispositivo na mesa, podem ver e interagir com o quadro de virtual chess em conjunto.

- [Way-finding](concepts/anchor-relationships-way-finding.md). Os programadores também podem ligar âncoras geográficos em conjunto, criar relações entre elas. Por exemplo, uma aplicação pode incluir uma experiência que tem dois ou mais pontos de interesse que um utilizador tem de interagir com para concluir uma tarefa. Esses pontos de interesse podem ser criados de maneira conectada. Mais tarde, quando o utilizador está a concluir a tarefa de várias etapa, a aplicação pode pedir as âncoras de que estão próximos aquele atual para direcionar o utilizador para a próxima etapa na tarefa.

- [Conteúdo virtual persistente do mundo real](concepts/create-locate-anchors-unity.md#create-a-cloud-spatial-anchor). Uma aplicação pode permitir que um usuário colocar um calendário virtual na parede de sala de conferência, que as pessoas possam ver utiliza uma aplicação de telefone ou um dispositivo de HoloLens. Numa definição industrial, um utilizador pode receber informações contextuais sobre uma máquina ao apontar uma câmara de dispositivo com suporte para ele.

Âncoras espaciais do Azure é composta por um serviço gerido e SDKs de cliente para plataformas de dispositivos suportados. As secções seguintes fornecem informações sobre como começar a criar aplicações com o âncoras espaciais do Azure.

## <a name="next-steps"></a>Passos Seguintes

Crie seu primeiro aplicativo com âncoras espaciais.

> [!div class="nextstepaction"]
> [Unity](unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](quickstarts/get-started-hololens.md)
