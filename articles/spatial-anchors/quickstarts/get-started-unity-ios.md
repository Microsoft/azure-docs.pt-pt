---
title: Início rápido - criar uma aplicação do Unity iOS com as âncoras espaciais do Azure | Documentos da Microsoft
description: Neste guia de introdução, saiba como criar uma aplicação iOS com o Unity usando âncoras espaciais.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 37856c0833ecde1478d4bd588b8e3122e8eac0ca
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135249"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>Início rápido: Criar uma aplicação do Unity iOS com as âncoras espaciais do Azure

Este início rápido mostra como criar um através de aplicações de iOS do Unity [âncoras geográficos de Azure](../overview.md). Âncoras espaciais do Azure é um serviço de programador para várias plataformas que permite-lhe criar experiências de realidade mista usando objetos que manter a respetiva localização em todos os dispositivos ao longo do tempo. Quando tiver terminado, terá uma aplicação do iOS de ARKit criada com o Unity, que pode guardar e lembre-se de uma âncora de espacial.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de âncoras espaciais
> * Preparar as definições de compilação do Unity
> * Transferir e importar o plug-in do Unity ARKit
> * Configurar a chave de conta e o identificador da conta de âncoras espaciais
> * Exportar o projeto Xcode
> * Implementar e executar num dispositivo iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:

- Um computador macOS com <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3 +</a>, <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10</a>, e <a href="https://cocoapods.org" target="_blank">CocoaPods</a> instalado.
- Git instalado através do HomeBrew. Introduza o seguinte comando numa única linha de Terminal: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Em seguida, execute `brew install git`.
- Um desenvolvedor ativado <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">compatível com o ARKit</a> dispositivo iOS.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Transfira e abra o projeto de exemplo do Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Configurar o identificador de conta e chave

Na **projeto** painel, navegue até à `Assets/AzureSpatialAnchorsPlugin/Examples` e abra o `AzureSpatialAnchorsBasicDemo.unity` ficheiro de cena.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Salve a cena selecionando **arquivo** -> **guardar**.

## <a name="export-the-xcode-project"></a>Exportar o projeto Xcode

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

Siga as instruções na aplicação para colocar e lembre-se de uma âncora.

> [!NOTE]
> Ao executar a aplicação, se não vir a câmera como plano de fundo (para a instância em vez disso, verá um espaço em branco, azul ou outras texturas), em seguida, provavelmente terá de voltar a importar ativos no Unity. Pare a aplicação. No menu superior no Unity, escolha **elementos -> importe novamente todos os**. Em seguida, execute novamente a aplicação.

No Xcode, pare a aplicação ao premir **parar**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Partilha âncoras geográficos em todos os dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
