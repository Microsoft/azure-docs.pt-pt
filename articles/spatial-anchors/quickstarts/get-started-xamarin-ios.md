---
title: 'Início rápido: criar um aplicativo Xamarin iOS'
description: Neste guia de início rápido, você aprende a criar um aplicativo iOS com o Xamarin usando âncoras espaciais.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: c5e217a33c8b461a438e2d0209fe6733850634e6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465142"
---
# <a name="quickstart-create-a-xamarin-ios-app-with-azure-spatial-anchors"></a>Início rápido: criar um aplicativo do Xamarin iOS com âncoras espaciais do Azure

Este guia de início rápido aborda como criar um aplicativo iOS com o Xamarin usando [âncoras espaciais do Azure](../overview.md). As âncoras espaciais do Azure são um serviço de desenvolvedor de plataforma cruzada que permite que você crie experiências de realidade mista usando objetos que persistem seu local entre dispositivos ao longo do tempo. Quando tiver terminado, você terá um aplicativo iOS que pode salvar e lembrar uma âncora espacial.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de âncoras espaciais
> * Configurar o identificador de conta de âncoras espaciais e a chave de conta
> * Implantar e executar em um dispositivo iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:
- Um Mac que executa o macOS High Sierra (10,13) ou superior com:
  - A versão mais recente do Xcode e do SDK do iOS instalada da [loja de aplicativos](https://itunes.apple.com/us/app/xcode/id497799835?mt=12).
  - Uma versão atualizada do <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio para Mac 8.1 +</a>.
  - <a href="https://git-scm.com/download/mac" target="_blank">Git para MacOS</a>.
  - <a href="https://git-lfs.github.com/">LFS git</a>.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Abrir o projeto de exemplo

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Abra `Xamarin/SampleXamarin.sln` no Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Configurar o identificador e a chave da conta

A próxima etapa é configurar o aplicativo para usar o identificador de conta e a chave de conta. Você os copiou em um editor de texto ao [Configurar o recurso âncoras espaciais](#create-a-spatial-anchors-resource).

Abra `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Localize o campo `SpatialAnchorsAccountKey` e substitua `Set me` pela chave de conta.

Localize o campo `SpatialAnchorsAccountId` e substitua `Set me` pelo identificador da conta.

## <a name="deploy-the-app-to-your-ios-device"></a>Implantar o aplicativo em seu dispositivo iOS

Ligue o dispositivo iOS, entre e conecte-o ao computador usando um cabo USB.

Defina o projeto de inicialização como **SampleXamarin. Ios**, altere a **configuração da solução** para **liberação**e selecione o dispositivo no qual você deseja implantar na lista suspensa seletor de dispositivo.

![Configuração do Visual Studio](./media/get-started-xamarin-iOS/visual-studio-macos-configuration.jpg)

Selecione **executar** > **Iniciar sem Depurar** para implantar e iniciar o aplicativo.

No aplicativo, selecione **básico** para executar a demonstração e siga as instruções para colocá-la e relembrar uma âncora.

> ![captura de tela 1](./media/get-started-xamarin-ios/screenshot-1.jpg)
> ![captura de tela 2](./media/get-started-xamarin-ios/screenshot-2.jpg)
> ![captura de tela 3](./media/get-started-xamarin-ios/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: compartilhar âncoras espaciais entre dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
