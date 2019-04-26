---
title: Início rápido - criar aplicação Android com âncoras espaciais do Azure | Documentos da Microsoft
description: Neste guia de introdução, saiba como criar um aplicativo Android usando âncoras espaciais.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 003e9beb3a319802754397efae3b636f51de9e52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60234345"
---
# <a name="quickstart-create-an-android-app-with-azure-spatial-anchors"></a>Início rápido: Criar uma aplicação Android com o Azure âncoras espaciais

Este guia de introdução mostra como criar uma aplicação Android com [âncoras geográficos de Azure](../overview.md) em Java ou C + c++ /CLI NDK. Âncoras espaciais do Azure é um serviço de programador para várias plataformas que permite-lhe criar experiências de realidade mista usando objetos que manter a respetiva localização em todos os dispositivos ao longo do tempo. Quando tiver terminado, terá uma aplicação Android de ARCore que pode guardar e lembre-se de uma âncora de espacial.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de âncoras espaciais
> * Configurar a chave de conta e o identificador da conta de âncoras espaciais
> * Implementar e executar num dispositivo Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:

- Um Windows ou macOS do computador com <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3 +</a>.
  - Se executar no Windows, também precisará <a href="https://git-scm.com/download/win" target="_blank">Git para Windows</a>.
  - Se executar no macOS, obtenha o Git instalado através do HomeBrew. Introduza o seguinte comando numa única linha de Terminal: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Em seguida, execute `brew install git`.
  - Para criar o exemplo NDK, também terá de instalar o NDK e as ferramentas de SDK do CMake 3.6 no Android Studio.
- R <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">desenvolvedor ativada</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore com capacidade</a> dispositivo Android.
- Seu aplicativo deve ter ARCore 1.7.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Abra o projeto de exemplo

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Se estiver a criar o exemplo de Android NDK, precisará baixar `arcore_c_api.h` partir [aqui](https://raw.githubusercontent.com/google-ar/arcore-android-sdk/v1.7.0/libraries/include/arcore_c_api.h) e colocá-lo no `Android\NDK\libraries\include`.

Abra o Android Studio.

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Selecione **abrir um projeto existente do Android Studio** e selecione o projeto localizado em `Android/Java/`.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Selecione **abrir um projeto existente do Android Studio** e selecione o projeto localizado em `Android/NDK/`.

***

## <a name="configure-account-identifier-and-key"></a>Configurar o identificador de conta e chave

A próxima etapa é configurar a aplicação para utilizar o seu identificador de conta e chave da conta. Copiou num editor de texto quando [como configurar o recurso de âncoras geográficos](#create-a-spatial-anchors-resource).

# <a name="javatabopenproject-java"></a>[Java](#tab/openproject-java)

Abra `Android/Java/app/src/main/java/com/microsoft/sampleandroid/AzureSpatialAnchorsActivity.java`.

Localize a `SpatialAnchorsAccountKey` campo e substitua `Set me` com a chave de conta.

Localize a `SpatialAnchorsAccountId` campo e substitua `Set me` com o identificador de conta.

# <a name="ndktabopenproject-ndk"></a>[NDK](#tab/openproject-ndk)

Abra `Android/NDK/app/src/main/cpp/spatial_services_application.cc`.

Localize a `SpatialAnchorsAccountKey` campo e substitua `Set me` com a chave de conta.

Localize a `SpatialAnchorsAccountId` campo e substitua `Set me` com o identificador de conta.

***

## <a name="deploy-the-app-to-your-android-device"></a>Implementar a aplicação no seu dispositivo Android

Ligar o dispositivo Android, inicie sessão e ligá-la no PC com um cabo USB.

Selecione **executar** da barra de ferramentas do Android Studio.

![Android Studio, implementar e executar](./media/get-started-android/android-studio-deploy-run.png)

Selecione o dispositivo Android no **selecione o destino de implementação** caixa de diálogo e selecione **OK** para executar a aplicação no dispositivo Android.

Siga as instruções na aplicação para colocar e lembre-se de uma âncora.

Pare a aplicação, selecionando **parar** da barra de ferramentas do Android Studio.

![Parar de Android Studio](./media/get-started-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Partilha âncoras geográficos em todos os dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
