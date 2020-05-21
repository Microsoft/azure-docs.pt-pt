---
title: 'Quickstart: Criar uma aplicação Android'
description: Neste arranque rápido, aprende-se a construir uma aplicação Android utilizando Âncoras Espaciais.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3f794d1c70baee07b9ff3ed5d8299cf8ad3bf983
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652496"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Quickstart: Criar uma aplicação Android com Âncoras Espaciais Azure

Este quickstart cobre como criar uma aplicação Android usando [âncoras espaciais Azure](../overview.md) em Java ou C++/NDK. O Azure Spatial Anchors é um serviço de desenvolvimento de plataformas cruzadas que permite criar experiências de realidade mista utilizando objetos que persistem a sua localização através de dispositivos ao longo do tempo. Quando terminar, terá uma aplicação ARCore Android que pode guardar e recordar uma âncora espacial.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de Âncoras Espaciais
> * Configure o identificador de conta Spatial Anchors e a chave de conta
> * Implementar e executar num dispositivo Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:

- Uma máquina Windows ou macOS com <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4+</a>.
  - Se estiver a funcionar no Windows, também vai precisar de <a href="https://git-scm.com/download/win" target="_blank">Git para Windows</a> e <a href="https://git-lfs.github.com/">Git LFS</a>.
  - Se funcionar no macOS, instale o Git via HomeBrew. Introduza o seguinte comando numa única linha do Terminal: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` . Então, corra `brew install git` `brew install git-lfs` e.
  - Para construir a amostra NDK, você também precisa instalar as ferramentas NDK e CMake 3.6 ou maiores SDK no Android Studio.
- Um <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">desenvolvedor ativado</a> e dispositivo Android <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">capaz de ARCore.</a>
  - Podem ser necessários controladores adicionais para que o seu computador se comunique com o seu dispositivo Android. Consulte [aqui](https://developer.android.com/studio/run/device.html) informações e instruções adicionais.
- A sua aplicação deve ter como alvo ARCore **1.11.0**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Abra o projeto da amostra

# <a name="java"></a>[Java](#tab/openproject-java)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Faça o download `arcore_c_api.h` [daqui](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.11.0/libraries/include/arcore_c_api.h) e coloque-o em `Android\NDK\libraries\include` .

A partir do repositório recém-clonado, inicializar os submódulos executando o seguinte comando:

```console
git submodule update --init --recursive
```

---

Abra o Android Studio.

# <a name="java"></a>[Java](#tab/openproject-java)

**Selecione Abra um projeto Android Studio existente** e selecione o projeto localizado em `Android/Java/` .

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

**Selecione Abra um projeto Android Studio existente** e selecione o projeto localizado em `Android/NDK/` .

---

## <a name="configure-account-identifier-and-key"></a>Configurar identificador de conta e chave

O próximo passo é configurar a aplicação para utilizar o identificador de conta e a chave de conta. Copiou-os para um editor de texto ao [criar o recurso Spatial Anchors.](#create-a-spatial-anchors-resource)

# <a name="java"></a>[Java](#tab/openproject-java)

Abra `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsManager.java`.

Localize o `SpatialAnchorsAccountKey` campo e `Set me` substitua-o pela chave da conta.

Localize o `SpatialAnchorsAccountId` campo e `Set me` substitua-o pelo identificador de conta.

Localize `public AzureSpatialAnchorsManager(Session arCoreSession)` e adicione a seguinte linha, substituindo no domínio da sua conta anterior: `spatialAnchorsSession.getConfiguration().setAccountDomain("MyAccountDomain");` .

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

Abra `Android/NDK/app/src/main/cpp/AzureSpatialAnchorsApplication.cpp`.

Localize o `SpatialAnchorsAccountKey` campo e `Set me` substitua-o pela chave da conta.

Localize o `SpatialAnchorsAccountId` campo e `Set me` substitua-o pelo identificador de conta.

Localize `AzureSpatialAnchorsApplication::StartCloudSession()` e adicione a seguinte linha, substituindo no domínio da sua conta anterior: `m_cloudSession->Configuration()->AccountDomain("MyAccountDomain");` .

---

## <a name="deploy-the-app-to-your-android-device"></a>Implemente a aplicação para o seu dispositivo Android

Ligue no dispositivo Android, inscreva-se e conecte-o ao PC utilizando um cabo USB.

Selecione **Run** a partir da barra de ferramentas Android Studio.

![Android Studio Deploy and Run](./media/get-started-android/android-studio-deploy-run.png)

Selecione o dispositivo Android no diálogo **Select Deployment Target** e selecione **OK** para executar a aplicação no dispositivo Android.

Siga as instruções na aplicação para colocar e lembre-se de uma âncora.

Pare a aplicação selecionando **Stop** da barra de ferramentas Android Studio.

![Paragem do estúdio Android](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Partilhar âncoras espaciais em dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
