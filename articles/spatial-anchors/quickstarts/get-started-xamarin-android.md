---
title: Início rápido-criar um aplicativo Xamarin Android com âncoras espaciais do Azure | Microsoft Docs
description: Neste guia de início rápido, você aprende a criar um aplicativo do Android com o Xamarin usando âncoras espaciais.
author: craigktreasure
manager: virivera
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 0958961f1e66be130dd2be816f6002dd34465dc6
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68931659"
---
# <a name="quickstart-create-a-xamarin-android-app-with-azure-spatial-anchors"></a>Início rápido: Criar um aplicativo Xamarin Android com âncoras espaciais do Azure

Este guia de início rápido aborda como criar um aplicativo Android com o Xamarin usando [âncoras espaciais do Azure](../overview.md). As âncoras espaciais do Azure são um serviço de desenvolvedor de plataforma cruzada que permite que você crie experiências de realidade mista usando objetos que persistem seu local entre dispositivos ao longo do tempo. Quando tiver terminado, você terá um aplicativo Android que pode salvar e recuperar uma âncora espacial.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de âncoras espaciais
> * Configurar o identificador de conta de âncoras espaciais e a chave de conta
> * Implantar e executar em um dispositivo Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:
- Um computador com Windows ou macOS:
  - Se estiver usando o Windows:
    - Uma versão atualizada do <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 16.2 +</a>.
    - <a href="https://git-scm.com/download/win" target="_blank">Git para Windows</a>
  - Se estiver usando o macOS:
    - Uma versão atualizada do <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio para Mac 8.1 +</a>.
    - <a href="https://git-scm.com/download/mac" target="_blank">Git para macOS</a>
- A versão mais recente do Xamarin. Android instalada e em execução na sua plataforma de sua escolha. Para obter um guia para instalar o Xamarin. Android, consulte os guias de [instalação do xamarin. Android](https://docs.microsoft.com/xamarin/android/get-started/installation/index) .
- Um dispositivo Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">habilitado por desenvolvedor</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">com ARCore</a> .
  - Drivers de dispositivo adicionais podem ser necessários para que seu computador se comunique com seu dispositivo Android. Para obter mais informações, consulte [aqui](https://developer.android.com/studio/run/device.html).
- Seu aplicativo deve ter como destino o ARCore **1,8**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Abrir o projeto de exemplo

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Abra `Xamarin/SampleXamarin.sln` no Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Configurar o identificador e a chave da conta

A próxima etapa é configurar o aplicativo para usar o identificador de conta e a chave de conta. Você os copiou em um editor de texto ao [Configurar o recurso âncoras espaciais](#create-a-spatial-anchors-resource).

Abra `Xamarin/SampleXamarin.Common/AccountDetails.cs`o.

Localize o `SpatialAnchorsAccountKey` campo e substitua `Set me` pela chave da conta.

Localize o `SpatialAnchorsAccountId` campo e substitua `Set me` pelo identificador da conta.

## <a name="deploy-the-app-to-your-android-device"></a>Implantar o aplicativo em seu dispositivo Android

Ligue o dispositivo Android, entre e conecte-o ao computador usando um cabo USB.

Defina o projeto de inicialização como **SampleXamarin. Android**, altere a **configuração da solução** para **liberação**e selecione o dispositivo no qual você deseja implantar na lista suspensa seletor de dispositivo.

# <a name="windowstabdeploy-windows"></a>[Windows](#tab/deploy-windows)

![Configuração do Visual Studio](./media/get-started-xamarin-android/visual-studio-windows-configuration.png)

Selecione **depurar** > **Iniciar Depuração** para implantar e iniciar seu aplicativo.

# <a name="macostabdeploy-macos"></a>[macOS](#tab/deploy-macos)

![Configuração do Visual Studio](./media/get-started-xamarin-android/visual-studio-macos-configuration.jpg)

Selecione **executar** > **Iniciar sem depuração** para implantar e iniciar o aplicativo.

---

No aplicativo, selecione **básico** para executar a demonstração e siga as instruções para colocá-la e relembrar uma âncora.

> ![Captura de](./media/get-started-xamarin-android/screenshot-1.jpg)
> tela 1![captura de tela 2](./media/get-started-xamarin-android/screenshot-2.jpg)
> ![captura 3](./media/get-started-xamarin-android/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Compartilhar âncoras espaciais entre dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
