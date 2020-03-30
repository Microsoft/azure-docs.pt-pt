---
title: 'Quickstart: Criar uma aplicação Xamarin Android'
description: Neste arranque rápido, aprende-se a construir uma aplicação Android com o Xamarin usando Âncoras Espaciais.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 94906d645ec38a24d54536ee8aa93e7418c8dc35
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "75465175"
---
# <a name="quickstart-create-a-xamarin-android-app-with-azure-spatial-anchors"></a>Quickstart: Crie uma aplicação Xamarin Android com Âncoras Espaciais Azure

Este quickstart cobre como criar uma aplicação Android com Xamarin usando [Âncoras Espaciais Azure](../overview.md). O Azure Spatial Anchors é um serviço de desenvolvimento de plataformas cruzadas que permite criar experiências de realidade mista utilizando objetos que persistem a sua localização através de dispositivos ao longo do tempo. Quando terminar, terá uma aplicação Android que pode guardar e recordar uma âncora espacial.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de Âncoras Espaciais
> * Configure o identificador de conta Spatial Anchors e a chave de conta
> * Implementar e executar num dispositivo Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:
- Um computador Windows ou macOS:
  - Se utilizar o Windows:
    - Uma versão atualizada do <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 16.2+</a>.
    - <a href="https://git-scm.com/download/win" target="_blank">Git para Windows</a>.
    - <a href="https://git-lfs.github.com/">Git LFS</a>.
  - Se utilizar o macOS:
    - Uma versão atualizada do <a href="https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio para Mac 8.1+</a>.
    - <a href="https://git-scm.com/download/mac" target="_blank">Git para macOS</a>.
    - <a href="https://git-lfs.github.com/">Git LFS</a>.
- A versão mais recente do Xamarin.Android instalada e a funcionar na sua plataforma de eleição. Para um guia para instalar Xamarin.Android, consulte os guias de [instalação Xamarin.Android.](https://docs.microsoft.com/xamarin/android/get-started/installation/index)
- Um <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">desenvolvedor ativado</a> e dispositivo Android <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">capaz de ARCore.</a>
  - Podem ser necessários controladores adicionais para que o seu computador se comunique com o seu dispositivo Android. Para mais informações, consulte [aqui.](https://developer.android.com/studio/run/device.html)
- A sua aplicação deve ter como alvo ARCore **1.8**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Abra o projeto da amostra

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Aberto `Xamarin/SampleXamarin.sln` no Estúdio Visual.

## <a name="configure-account-identifier-and-key"></a>Configurar identificador de conta e chave

O próximo passo é configurar a aplicação para utilizar o identificador de conta e a chave de conta. Copiou-os para um editor de texto ao [criar o recurso Spatial Anchors.](#create-a-spatial-anchors-resource)

Abra `Xamarin/SampleXamarin.Common/AccountDetails.cs`.

Localize `SpatialAnchorsAccountKey` o `Set me` campo e substitua-o pela chave da conta.

Localize `SpatialAnchorsAccountId` o `Set me` campo e substitua-o pelo identificador de conta.

## <a name="deploy-the-app-to-your-android-device"></a>Implemente a aplicação para o seu dispositivo Android

Ligue no dispositivo Android, inscreva-se e conecte-o ao computador utilizando um cabo USB.

Detete o projeto de arranque para **SampleXamarin.Android,** altere a Configuração de **Solução** para **O Lançamento**, e selecione o dispositivo para o que pretende implementar no seletor do dispositivo.

# <a name="windows"></a>[Windows](#tab/deploy-windows)

![Configuração do estúdio visual](./media/get-started-xamarin-android/visual-studio-windows-configuration.png)

Selecione **Debug** > **Comece a depurar** para implementar e inicie a sua aplicação.

# <a name="macos"></a>[macOS](#tab/deploy-macos)

![Configuração do estúdio visual](./media/get-started-xamarin-android/visual-studio-macos-configuration.jpg)

Selecione **Executar** > **Iniciar Sem Debugging** para implementar e iniciar a sua aplicação.

---

Na aplicação, selecione **Basic** para executar a demonstração e siga as instruções para colocar e recordar uma âncora.

> ![Screenshot](./media/get-started-xamarin-android/screenshot-1.jpg)
> ![1](./media/get-started-xamarin-android/screenshot-2.jpg)
> ![Screenshot 2 Screenshot 3](./media/get-started-xamarin-android/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Partilhar âncoras espaciais em dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
