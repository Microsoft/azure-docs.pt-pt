---
title: 'Início rápido: criar um aplicativo do HoloLens com DirectX'
description: Neste guia de início rápido, você aprende a criar um aplicativo do HoloLens usando âncoras espaciais.
author: craigktreasure
manager: virivera
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3814a12657b2634ede02eda83c7e7bcdf78cd7e2
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277105"
---
# <a name="quickstart-create-a-hololens-app-with-azure-spatial-anchors-in-cwinrt-and-directx"></a>Início rápido: criar um aplicativo do HoloLens com âncoras espaciais C++do Azure, no/WinRT e no DirectX

Este guia de início rápido aborda como criar um aplicativo do HoloLens usando [âncoras espaciais do Azure](../overview.md) no C++/WinRT e no DirectX. As âncoras espaciais do Azure são um serviço de desenvolvedor de plataforma cruzada que permite que você crie experiências de realidade mista usando objetos que persistem seu local entre dispositivos ao longo do tempo. Quando tiver terminado, você terá um aplicativo de HoloLens que pode salvar e recuperar uma âncora espacial.

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

Abra `HoloLens\DirectX\SampleHoloLens\ViewController.cpp`.

Localize o campo `SpatialAnchorsAccountKey` e substitua `Set me` pela chave de conta.

Localize o campo `SpatialAnchorsAccountId` e substitua `Set me` pelo identificador da conta.

## <a name="deploy-the-app-to-your-hololens"></a>Implantar o aplicativo em seu HoloLens

Altere a **configuração da solução** para **liberar**, altere **plataforma de solução** para **x86**e selecione **dispositivo** nas opções de destino de implantação.

Se estiver usando o HoloLens 2, use **ARM64** como a **plataforma da solução**, em vez de **x86**.

![Configuração do Visual Studio](./media/get-started-hololens/visual-studio-configuration.png)

Ligue o dispositivo do HoloLens, entre e conecte-o ao PC usando um cabo USB.

Selecione **depurar** > **iniciar a depuração** para implantar seu aplicativo e iniciar a depuração.

Siga as instruções no aplicativo para colocá-lo e relembrar uma âncora.

No Visual Studio, interrompa o aplicativo selecionando **parar depuração** ou pressionando **Shift + F5**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: compartilhar âncoras espaciais entre dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
