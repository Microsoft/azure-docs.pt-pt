---
title: Início rápido-criar um aplicativo do Unity iOS com âncoras espaciais do Azure | Microsoft Docs
description: Neste guia de início rápido, você aprende a criar um aplicativo iOS com o Unity usando âncoras espaciais.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: be478a3f77996276f248d9b385954af813ac0397
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562476"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>Início rápido: Criar um aplicativo do Unity iOS com âncoras espaciais do Azure

Este guia de início rápido aborda como criar um aplicativo do iOS do Unity usando [âncoras espaciais do Azure](../overview.md). As âncoras espaciais do Azure são um serviço de desenvolvedor de plataforma cruzada que permite que você crie experiências de realidade mista usando objetos que persistem seu local entre dispositivos ao longo do tempo. Quando tiver terminado, você terá um aplicativo ARKit iOS criado com o Unity que pode salvar e recuperar uma âncora espacial.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de âncoras espaciais
> * Preparar configurações de compilação do Unity
> * Configurar o identificador de conta de âncoras espaciais e a chave de conta
> * Exportar o projeto do Xcode
> * Implantar e executar em um dispositivo iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:

- Uma máquina macOS com o <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1 +</a>, o <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10</a>e o <a href="https://cocoapods.org" target="_blank">CocoaPods</a> instalados.
- Git instalado via HomeBrew. Digite o seguinte comando em uma única linha do terminal: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Em seguida, `brew install git`execute.
- Um dispositivo IOS <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">compatível com o ARKit</a> habilitado para desenvolvedor.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Baixar e abrir o projeto de exemplo do Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Configurar o identificador e a chave da conta

No painel **projeto** , navegue até `Assets/AzureSpatialAnchors.Examples/Scenes` e abra o arquivo `AzureSpatialAnchorsBasicDemo.unity` de cena.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Salve a cena selecionando **arquivo** -> **salvar**.

## <a name="export-the-xcode-project"></a>Exportar o projeto do Xcode

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

Siga as instruções no aplicativo para colocá-lo e relembrar uma âncora.

Quando terminar, pare o aplicativo pressionando **parar** no Xcode.

## <a name="troubleshooting"></a>Resolução de problemas

Ao executar o aplicativo, se você não vir a câmera como segundo plano (por exemplo, você vê uma textura em branco, azul ou outras), provavelmente precisará importar novamente os ativos no Unity. Pare a aplicação. No menu superior no Unity, escolha **ativos-> reimportar tudo**. Em seguida, execute o aplicativo novamente.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Compartilhar âncoras espaciais entre dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
