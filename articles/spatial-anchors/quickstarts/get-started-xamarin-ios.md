---
title: 'Quickstart: Criar uma aplicação Xamarin iOS'
description: Neste quickstart, você aprende a construir uma aplicação iOS com Xamarin usando Âncoras Espaciais.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 610112f9374f2ca39a8df4b849aeda4abf965e57
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92097427"
---
# <a name="quickstart-create-a-xamarin-ios-app-with-azure-spatial-anchors"></a>Quickstart: Criar uma aplicação Xamarin iOS com âncoras espaciais Azure

Este quickstart cobre como criar uma aplicação iOS com Xamarin usando [Âncoras Espaciais Azure](../overview.md). Azure Spatial Anchors é um serviço de desenvolvedor de plataformas cruzadas que permite criar experiências de realidade mista usando objetos que persistem a sua localização através dos dispositivos ao longo do tempo. Quando terminar, terá uma aplicação para iOS que pode guardar e recordar uma âncora espacial.

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
  - Uma versão atualizada do <a href="/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio para Mac 8.1+</a>.
  - <a href="https://git-scm.com/download/mac" target="_blank">Git para macOS</a>.
  - <a href="https://git-lfs.github.com/">Git LFS.</a>

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Abra o projeto de amostra

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Aberto `Xamarin/SampleXamarin.sln` no Estúdio Visual.

## <a name="configure-account-identifier-and-key"></a>Configure identificador de conta e chave

O próximo passo é configurar a app para usar o identificador de conta e a chave da conta. Copiou-os para um editor de texto ao [configurar o recurso Spatial Anchors](#create-a-spatial-anchors-resource).

Abra `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Localize o `SpatialAnchorsAccountKey` campo e `Set me` substitua-o pela chave de conta.

Localize o `SpatialAnchorsAccountId` campo e `Set me` substitua-o pelo identificador de conta.

Localize o `SpatialAnchorsAccountDomain` campo e `Set me` substitua-o pelo domínio da conta.

## <a name="deploy-the-app-to-your-ios-device"></a>Implemente a aplicação para o seu dispositivo iOS

Ligue o dispositivo iOS, inscreva-se e conecte-o ao computador utilizando um cabo USB.

Defina o projeto de arranque para **SampleXamarin.iOS,** altere a **Configuração de Solução** para **Ser Desbloqueando**e selecione o dispositivo a que pretende implementar no seletor do dispositivo.

![Configuração visual do estúdio](./media/get-started-xamarin-iOS/visual-studio-macos-configuration.jpg)

Selecione **Executar**  >  **Start Sem Depurar** para implementar e iniciar a sua aplicação.

Na aplicação, selecione **Basic** para executar a demonstração e siga as instruções para colocar e recordar uma âncora.

> ![Screenshot 1 ](./media/get-started-xamarin-ios/screenshot-1.jpg)
>  ![ Screenshot 2 ](./media/get-started-xamarin-ios/screenshot-2.jpg)
>  ![ Screenshot 3](./media/get-started-xamarin-ios/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Partilhar Âncoras Espaciais através de dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)