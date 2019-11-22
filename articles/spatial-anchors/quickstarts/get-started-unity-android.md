---
title: 'Início rápido: criar um aplicativo do Unity para Android'
description: Neste guia de início rápido, você aprende a criar um aplicativo Android com o Unity usando âncoras espaciais.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 213cf9b64f2c43274192c22efa2fa5a7dfbce5e5
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277068"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Início rápido: criar um aplicativo do Unity para Android com âncoras espaciais do Azure

Este guia de início rápido aborda como criar um aplicativo Android [do Unity usando âncoras espaciais do Azure](../overview.md). As âncoras espaciais do Azure são um serviço de desenvolvedor de plataforma cruzada que permite que você crie experiências de realidade mista usando objetos que persistem seu local entre dispositivos ao longo do tempo. Quando tiver terminado, você terá um aplicativo ARCore Android criado com o Unity que pode salvar e recuperar uma âncora espacial.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de âncoras espaciais
> * Preparar configurações de compilação do Unity
> * Configurar o identificador de conta de âncoras espaciais e a chave de conta
> * Exportar o projeto de Android Studio
> * Implantar e executar em um dispositivo Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:

- Um computador com Windows ou macOS com o <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1 +</a> incluindo o suporte de compilação do Android e SDK do Android & módulos de ferramentas NDK.
  - Se estiver executando no Windows, você também precisará <a href="https://git-scm.com/download/win" target="_blank">do git para Windows</a>.
  - Se estiver em execução no macOS, obtenha o Git instalado via HomeBrew. Digite o seguinte comando em uma única linha do terminal: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Em seguida, execute `brew install git`.
- Um dispositivo Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">habilitado por desenvolvedor</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">com ARCore</a> .
  - Drivers de dispositivo adicionais podem ser necessários para que seu computador se comunique com seu dispositivo Android. Consulte [aqui](https://developer.android.com/studio/run/device.html) para obter informações e instruções adicionais.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Baixar e abrir o projeto de exemplo do Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Configurar o identificador e a chave da conta

No painel **projeto** , navegue até `Assets/AzureSpatialAnchors.Examples/Scenes` e abra o `AzureSpatialAnchorsBasicDemo.unity` arquivo de cena.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Salve a cena selecionando **arquivo** -> **salvar**.

## <a name="export-the-android-studio-project"></a>Exportar o projeto de Android Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Selecione seu dispositivo em **executar dispositivo** e clique em **Compilar e executar**. Você será solicitado a salvar um arquivo de `.apk` para o qual pode escolher qualquer nome.

Siga as instruções no aplicativo para colocá-lo e relembrar uma âncora.

## <a name="troubleshooting"></a>Resolução de problemas

Ao executar o aplicativo, se você não vir a câmera como segundo plano (por exemplo, você vê uma textura em branco, azul ou outras), provavelmente precisará importar novamente os ativos no Unity. Pare a aplicação. No menu superior no Unity, escolha **ativos-> reimportar tudo**. Em seguida, execute o aplicativo novamente.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: compartilhar âncoras espaciais entre dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
