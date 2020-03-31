---
title: 'Quickstart: Criar uma aplicação xamarin iOS'
description: Neste arranque rápido, aprende-se a construir uma aplicação iOS com o Xamarin usando Âncoras Espaciais.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: c5e217a33c8b461a438e2d0209fe6733850634e6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "75465142"
---
# <a name="quickstart-create-a-xamarin-ios-app-with-azure-spatial-anchors"></a>Quickstart: Crie uma app Xamarin iOS com âncoras espaciais Azure

Este quickstart cobre como criar uma aplicação iOS com Xamarin usando [âncoras espaciais Azure](../overview.md). O Azure Spatial Anchors é um serviço de desenvolvimento de plataformas cruzadas que permite criar experiências de realidade mista utilizando objetos que persistem a sua localização através de dispositivos ao longo do tempo. Quando terminar, terá uma aplicação iOS que pode guardar e recordar uma âncora espacial.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de Âncoras Espaciais
> * Configure o identificador de conta Spatial Anchors e a chave de conta
> * Implementar e executar num dispositivo iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:
- Um Mac que executa macOS High Sierra (10.13) ou superior com:
  - A versão mais recente do Xcode e iOS SDK instalada a partir da [App Store.](https://itunes.apple.com/us/app/xcode/id497799835?mt=12)
  - Uma versão atualizada do <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio para Mac 8.1+</a>.
  - <a href="https://git-scm.com/download/mac" target="_blank">Git para macOS</a>.
  - <a href="https://git-lfs.github.com/">Git LFS</a>.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Abra o projeto da amostra

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Aberto `Xamarin/SampleXamarin.sln` no Estúdio Visual.

## <a name="configure-account-identifier-and-key"></a>Configurar identificador de conta e chave

O próximo passo é configurar a aplicação para utilizar o identificador de conta e a chave de conta. Copiou-os para um editor de texto ao [criar o recurso Spatial Anchors.](#create-a-spatial-anchors-resource)

Abra `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Localize `SpatialAnchorsAccountKey` o `Set me` campo e substitua-o pela chave da conta.

Localize `SpatialAnchorsAccountId` o `Set me` campo e substitua-o pelo identificador de conta.

## <a name="deploy-the-app-to-your-ios-device"></a>Implemente a aplicação para o seu dispositivo iOS

Ligue no dispositivo iOS, inscreva-se e ligue-o ao computador utilizando um cabo USB.

Detete o projeto de arranque para **SampleXamarin.iOS,** altere a Configuração de **Solução** para **O Lançamento**e selecione o dispositivo que pretende implementar no seletor do dispositivo.

![Configuração do estúdio visual](./media/get-started-xamarin-iOS/visual-studio-macos-configuration.jpg)

Selecione **Executar** > **Iniciar Sem Debugging** para implementar e iniciar a sua aplicação.

Na aplicação, selecione **Basic** para executar a demonstração e siga as instruções para colocar e recordar uma âncora.

> ![Screenshot](./media/get-started-xamarin-ios/screenshot-1.jpg)
> ![1](./media/get-started-xamarin-ios/screenshot-2.jpg)
> ![Screenshot 2 Screenshot 3](./media/get-started-xamarin-ios/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Partilhar âncoras espaciais em dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
