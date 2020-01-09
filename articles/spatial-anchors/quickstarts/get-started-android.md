---
title: 'Início rápido: criar um aplicativo Android'
description: Neste guia de início rápido, você aprende a criar um aplicativo do Android usando âncoras espaciais.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 0501c8bb1d71c6cff6033fc937cda019c8890056
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75376465"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Início rápido: criar um aplicativo Android com âncoras espaciais do Azure

Este guia de início rápido aborda como criar um aplicativo Android usando [âncoras espaciais do Azure](../overview.md) em Java ou C++/NDK. As âncoras espaciais do Azure são um serviço de desenvolvedor de plataforma cruzada que permite que você crie experiências de realidade mista usando objetos que persistem seu local entre dispositivos ao longo do tempo. Quando tiver terminado, você terá um aplicativo ARCore Android que pode salvar e recuperar uma âncora espacial.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de âncoras espaciais
> * Configurar o identificador de conta de âncoras espaciais e a chave de conta
> * Implantar e executar em um dispositivo Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:

- Um computador com Windows ou macOS com o <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4 +</a>.
  - Se estiver em execução no Windows, você também precisará <a href="https://git-scm.com/download/win" target="_blank">do git para Windows</a> e <a href="https://git-lfs.github.com/">git LFS</a>.
  - Se estiver em execução no macOS, obtenha o Git instalado via HomeBrew. Digite o seguinte comando em uma única linha do terminal: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Em seguida, execute `brew install git` e `brew install git-lfs`.
  - Para criar o exemplo de NDK, você também precisará instalar o NDK e CMake 3,6 ou superior SDK Tools em Android Studio.
- Um dispositivo Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">habilitado por desenvolvedor</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">com ARCore</a> .
  - Drivers de dispositivo adicionais podem ser necessários para que seu computador se comunique com seu dispositivo Android. Consulte [aqui](https://developer.android.com/studio/run/device.html) para obter informações e instruções adicionais.
- Seu aplicativo deve ter como destino ARCore **1.11.0**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Abrir o projeto de exemplo

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Baixe `arcore_c_api.h` [aqui](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.11.0/libraries/include/arcore_c_api.h) e coloque-o em `Android\NDK\libraries\include`.

No repositório recém clonado, inicialize os submódulos executando o seguinte comando:

```console
git submodule update --init --recursive
```

---

Abra o Android Studio.

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Selecione **abrir um projeto de Android Studio existente** e selecione o projeto localizado em `Android/Java/`.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Selecione **abrir um projeto de Android Studio existente** e selecione o projeto localizado em `Android/NDK/`.

---

## <a name="configure-account-identifier-and-key"></a>Configurar o identificador e a chave da conta

A próxima etapa é configurar o aplicativo para usar o identificador de conta e a chave de conta. Você os copiou em um editor de texto ao [Configurar o recurso âncoras espaciais](#create-a-spatial-anchors-resource).

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Abra `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsManager.java`.

Localize o campo `SpatialAnchorsAccountKey` e substitua `Set me` pela chave de conta.

Localize o campo `SpatialAnchorsAccountId` e substitua `Set me` pelo identificador da conta.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Abra `Android/NDK/app/src/main/cpp/AzureSpatialAnchorsApplication.cpp`.

Localize o campo `SpatialAnchorsAccountKey` e substitua `Set me` pela chave de conta.

Localize o campo `SpatialAnchorsAccountId` e substitua `Set me` pelo identificador da conta.

---

## <a name="deploy-the-app-to-your-android-device"></a>Implantar o aplicativo em seu dispositivo Android

Ligue o dispositivo Android, entre e conecte-o ao PC usando um cabo USB.

Selecione **executar** na barra de ferramentas Android Studio.

![Android Studio implantar e executar](./media/get-started-android/android-studio-deploy-run.png)

Selecione o dispositivo Android na caixa de diálogo **Selecionar destino de implantação** e selecione **OK** para executar o aplicativo no dispositivo Android.

Siga as instruções no aplicativo para colocá-lo e relembrar uma âncora.

Pare o aplicativo selecionando **parar** na barra de ferramentas Android Studio.

![Android Studio parar](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: compartilhar âncoras espaciais entre dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
