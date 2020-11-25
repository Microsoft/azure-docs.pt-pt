---
title: 'Quickstart: Criar uma aplicação Xamarin Android'
description: Neste quickstart, você aprende a construir uma aplicação Android com Xamarin usando Âncoras Espaciais.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 1626dfa739df6b7cf971aa570f14d36ac6c532e7
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2020
ms.locfileid: "96022620"
---
# <a name="quickstart-create-a-xamarin-android-app-with-azure-spatial-anchors"></a>Quickstart: Criar uma aplicação Xamarin Android com Âncoras Espaciais Azure

Este quickstart cobre como criar uma aplicação Android com Xamarin usando [Azure Spatial Anchors](../overview.md). Azure Spatial Anchors é um serviço de desenvolvedor de plataformas cruzadas que permite criar experiências de realidade mista usando objetos que persistem a sua localização através dos dispositivos ao longo do tempo. Quando terminar, terá uma aplicação Android que pode economizar e recordar uma âncora espacial.

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
    - <a href="https://git-lfs.github.com/">Git LFS.</a>
  - Se utilizar o macOS:
    - Uma versão atualizada do <a href="/visualstudio/mac/installation?view=vsmac-2019" target="_blank">Visual Studio para Mac 8.1+</a>.
    - <a href="https://git-scm.com/download/mac" target="_blank">Git para macOS</a>.
    - <a href="https://git-lfs.github.com/">Git LFS.</a>
- A mais recente versão do Xamarin.Android instalada e a funcionar na sua plataforma de eleição. Para obter um guia para instalar Xamarin.Android, consulte os guias de [instalação Xamarin.Android.](/xamarin/android/get-started/installation/index)
- Um dispositivo Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">habilitado para o desenvolvedor</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">capaz de ARCore.</a>
  - Podem ser necessários controladores adicionais para que o computador comunique com o seu dispositivo Android. Para mais informações, consulte [aqui.](https://developer.android.com/studio/run/device.html)
- A sua aplicação tem como alvo ARCore **1.8**.

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

## <a name="deploy-the-app-to-your-android-device"></a>Implemente a aplicação para o seu dispositivo Android

Ligue o dispositivo Android, inscreva-se e conecte-o ao computador utilizando um cabo USB.

Defina o projeto de arranque para **SampleXamarin.Android,** altere a **Configuração de Solução** para **Ser Lançada** e selecione o dispositivo para o quais pretende implementar no seletor do dispositivo.

# <a name="windows"></a>[Windows](#tab/deploy-windows)

![Screenshot que mostra o menu para selecionar o projeto e o dispositivo no Windows.](./media/get-started-xamarin-android/visual-studio-windows-configuration.png)

Selecione **Debug**  >  **Comece a depurar** para implementar e iniciar a sua aplicação.

# <a name="macos"></a>[macOS](#tab/deploy-macos)

![Configuração visual do estúdio](./media/get-started-xamarin-android/visual-studio-macos-configuration.jpg)

Selecione **Executar**  >  **Start Sem Depurar** para implementar e iniciar a sua aplicação.

---

Na aplicação, selecione **Basic** para executar a demonstração e siga as instruções para colocar e recordar uma âncora.

> ![Screenshot 1 ](./media/get-started-xamarin-android/screenshot-1.jpg)
>  ![ Screenshot 2 ](./media/get-started-xamarin-android/screenshot-2.jpg)
>  ![ Screenshot 3](./media/get-started-xamarin-android/screenshot-3.jpg)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Partilhar Âncoras Espaciais através de dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)