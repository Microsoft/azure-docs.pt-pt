---
title: Início rápido-criar aplicativo do HoloLens com âncoras espaciais do Azure | Microsoft Docs
description: Neste guia de início rápido, você aprende a criar um aplicativo do HoloLens usando âncoras espaciais.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 94cae186cee099618772f53d2b820e12f20cad64
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562407"
---
# <a name="quickstart-create-a-hololens-app-with-azure-spatial-anchors-in-cwinrt-and-directx"></a>Início rápido: Criar um aplicativo do HoloLens com âncoras espaciais do C++Azure, no/WinRT e no DirectX

Este guia de início rápido aborda como criar um aplicativo do HoloLens usando âncoras C++espaciais [do Azure](../overview.md) no/WinRT e no DirectX. As âncoras espaciais do Azure são um serviço de desenvolvedor de plataforma cruzada que permite que você crie experiências de realidade mista usando objetos que persistem seu local entre dispositivos ao longo do tempo. Quando tiver terminado, você terá um aplicativo de HoloLens que pode salvar e recuperar uma âncora espacial.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de âncoras espaciais
> * Configurar o identificador de conta de âncoras espaciais e a chave de conta
> * Implantar e executar em um dispositivo HoloLens

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:
- Um computador Windows com o <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> instalado com a carga de trabalho de **desenvolvimento plataforma universal do Windows** e o componente **SDK do Windows 10 (10.0.18362.0 ou mais recente)** e <a href="https://git-scm.com/download/win" target="_blank">git para Windows</a>.
- A [ C++extensão do/WinRT Visual Studio (VSIX)](https://aka.ms/cppwinrt/vsix) para Visual Studio deve ser instalada no [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
- Um dispositivo HoloLens com o [modo de desenvolvedor](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) habilitado. Este artigo requer um dispositivo HoloLens com a [atualização de outubro de 2018 do Windows 10](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (também conhecida como RS5). Para atualizar para a versão mais recente no HoloLens, abra o aplicativo **configurações** , acesse **Atualizar & segurança**e, em seguida, selecione o botão **verificar atualizações** .
- Seu aplicativo deve definir o recurso **spatialPerception** em seu manifesto Appx.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Abrir o projeto de exemplo

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Abra `HoloLens\DirectX\SampleHoloLens.sln` no Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Configurar o identificador e a chave da conta

A próxima etapa é configurar o aplicativo para usar o identificador de conta e a chave de conta. Você os copiou em um editor de texto ao [Configurar o recurso âncoras espaciais](#create-a-spatial-anchors-resource).

Abra `HoloLens\DirectX\SampleHoloLens\ViewController.cpp`o.

Localize o `SpatialAnchorsAccountKey` campo e substitua `Set me` pela chave da conta.

Localize o `SpatialAnchorsAccountId` campo e substitua `Set me` pelo identificador da conta.

## <a name="deploy-the-app-to-your-hololens"></a>Implantar o aplicativo em seu HoloLens

Altere a **configuração da solução** para **liberar**, altere **plataforma de solução** para **x86**e selecione **dispositivo** nas opções de destino de implantação.

Se estiver usando o HoloLens 2, use o **ARM** como **plataforma de solução**, em vez de **x86**.

![Configuração do Visual Studio](./media/get-started-hololens/visual-studio-configuration.png)

Ligue o dispositivo do HoloLens, entre e conecte-o ao PC usando um cabo USB.

Selecione **depurar** > **Iniciar Depuração** para implantar seu aplicativo e iniciar a depuração.

Siga as instruções no aplicativo para colocá-lo e relembrar uma âncora.

No Visual Studio, interrompa o aplicativo selecionando **parar depuração** ou pressionando **Shift + F5**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Compartilhar âncoras espaciais entre dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
