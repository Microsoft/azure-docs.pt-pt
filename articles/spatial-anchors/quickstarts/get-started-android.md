---
title: 'Quickstart: Criar uma aplicação Android'
description: Neste arranque rápido, aprende-se a construir uma aplicação Android utilizando Âncoras Espaciais.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: b7be4e257fc884c655fe380f69b08797a907fbbb
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2020
ms.locfileid: "96022654"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Quickstart: Criar uma aplicação Android com âncoras espaciais Azure

Este quickstart abrange como criar uma aplicação Android usando [âncoras espaciais Azure](../overview.md) em Java ou C++/NDK. Azure Spatial Anchors é um serviço de desenvolvedor de plataformas cruzadas que permite criar experiências de realidade mista usando objetos que persistem a sua localização através dos dispositivos ao longo do tempo. Quando terminar, terá uma aplicação ARCore Android que pode guardar e recordar uma âncora espacial.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de Âncoras Espaciais
> * Configure o identificador de conta Spatial Anchors e a chave de conta
> * Implementar e executar num dispositivo Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:

- Uma máquina Windows ou macOS com <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4+</a>.
  - Se estiver a funcionar no Windows, também necessitará de <a href="https://git-scm.com/download/win" target="_blank">Git para Windows</a> e Git <a href="https://git-lfs.github.com/">LFS</a>.
  - Se estiver a funcionar com macOS, instale o Git via HomeBrew. Introduza o seguinte comando numa única linha do Terminal: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` . Então, corra `brew install git` `brew install git-lfs` e.
  - Para construir a amostra NDK, também terá de instalar as Ferramentas NDK e CMake 3.6 ou maiores no Android Studio.
- Um dispositivo Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">habilitado para o desenvolvedor</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">capaz de ARCore.</a>
  - Podem ser necessários controladores adicionais para que o computador comunique com o seu dispositivo Android. Consulte [aqui](https://developer.android.com/studio/run/device.html) informações e instruções adicionais.
- A sua aplicação tem como alvo ARCore **1.11.0**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Abra o projeto de amostra

# <a name="java"></a>[Java](#tab/openproject-java)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Faça o download `arcore_c_api.h` [a](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.11.0/libraries/include/arcore_c_api.h) partir daqui e coloque-o em `Android\NDK\libraries\include` .

A partir do repositório recentemente clonado, inicialize submodulos executando o seguinte comando:

```console
git submodule update --init --recursive
```

---

Abra o Android Studio.

# <a name="java"></a>[Java](#tab/openproject-java)

Selecione **Abrir um projeto Android Studio existente** e selecione o projeto localizado em `Android/Java/` .

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

Selecione **Abrir um projeto Android Studio existente** e selecione o projeto localizado em `Android/NDK/` .

---

## <a name="configure-account-identifier-and-key"></a>Configure identificador de conta e chave

O próximo passo é configurar a app para usar o identificador de conta e a chave da conta. Copiou-os para um editor de texto ao [configurar o recurso Spatial Anchors](#create-a-spatial-anchors-resource).

# <a name="java"></a>[Java](#tab/openproject-java)

Abra `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsManager.java`.

Localize o `SpatialAnchorsAccountKey` campo e `Set me` substitua-o pela chave de conta.

Localize o `SpatialAnchorsAccountId` campo e `Set me` substitua-o pelo identificador de conta.

Localize o `SpatialAnchorsAccountDomain` campo e `Set me` substitua-o pelo domínio da conta.

# <a name="ndk"></a>[NDK](#tab/openproject-ndk)

Abra `Android/NDK/app/src/main/cpp/AzureSpatialAnchorsApplication.cpp`.

Localize o `SpatialAnchorsAccountKey` campo e `Set me` substitua-o pela chave de conta.

Localize o `SpatialAnchorsAccountId` campo e `Set me` substitua-o pelo identificador de conta.

Localize o `SpatialAnchorsAccountDomain` campo e `Set me` substitua-o pelo domínio da conta.

---

## <a name="deploy-the-app-to-your-android-device"></a>Implemente a aplicação para o seu dispositivo Android

Ligue o dispositivo Android, inscreva-se e conecte-o ao PC utilizando um cabo USB.

Selecione **Executar** a partir da barra de ferramentas Do Android Studio.

![Implementação e execução de estúdio Android](./media/get-started-android/android-studio-deploy-run.png)

Selecione o dispositivo Android no diálogo **Select Deployment Target** e selecione **OK** para executar a aplicação no dispositivo Android.

Siga as instruções na aplicação para colocar e lembre-se de uma âncora.

Pare a aplicação selecionando **Stop** a partir da barra de ferramentas Android Studio.

![Paragem do estúdio Android](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Partilhar Âncoras Espaciais através de dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
