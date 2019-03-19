---
title: Início rápido - criar aplicação de HoloLens com o Azure âncoras geográficos | Documentos da Microsoft
description: Neste guia de introdução, saiba como criar uma aplicação de HoloLens com âncoras espaciais.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 42528038b5744f001422620031a1e5300bb4146d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57898266"
---
# <a name="quickstart-create-a-hololens-app-with-azure-spatial-anchors-in-cwinrt-and-directx"></a>Início rápido: Criar uma aplicação de HoloLens com o Azure âncoras espaciais, em C++ /CLI WinRT e DirectX

Este guia de introdução mostra como criar uma aplicação de HoloLens com [âncoras geográficos de Azure](../overview.md) em C++ c++ /CLI WinRT e DirectX. Âncoras espaciais do Azure é um serviço de programador para várias plataformas que permite-lhe criar experiências de realidade mista usando objetos que manter a respetiva localização em todos os dispositivos ao longo do tempo. Quando tiver terminado, terá uma aplicação de HoloLens que pode guardar e lembre-se de uma âncora de geográfica.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de âncoras espaciais
> * Configurar a chave de conta e o identificador da conta de âncoras espaciais
> * Implementar e executar num dispositivo HoloLens

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:

- Uma máquina Windows com <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 +</a> instalada com o **desenvolvimento da plataforma Universal do Windows** carga de trabalho e o **Windows 10 SDK (10.0.17763.0 ou mais recente)** componente, e <a href="https://git-scm.com/download/win" target="_blank">Git para Windows</a>.
- O [C++ c++ /CLI WinRT Visual Studio extensão (VSIX)](https://aka.ms/cppwinrt/vsix) para o Visual Studio deve ser instalada a partir do [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
- Um dispositivo de HoloLens com [modo de programador](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) ativada. Este artigo requer um dispositivo de HoloLens com o [atualização do Windows 10 de Outubro de 2018](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (também conhecido como RS5). Para atualizar para a versão mais recente no HoloLens, abra a **definições** aplicação, aceda à **atualização e segurança**, em seguida, selecione o **procurar atualizações** botão.
- A aplicação tem de definir o **spatialPerception** capacidade no seu manifesto AppX.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Abra o projeto de exemplo

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Abra `HoloLens\DirectX\SampleHoloLens.sln` no Visual Studio.

## <a name="configure-account-identifier-and-key"></a>Configurar o identificador de conta e chave

A próxima etapa é configurar a aplicação para utilizar o seu identificador de conta e chave da conta. Copiou num editor de texto quando [como configurar o recurso de âncoras geográficos](#create-a-spatial-anchors-resource).

Abra `HoloLens\DirectX\SampleHoloLens\ViewController.cpp`.

Localize a `SpatialAnchorsAccountKey` campo e substitua `Set me` com a chave de conta.

Localize a `SpatialAnchorsAccountId` campo e substitua `Set me` com o identificador de conta.

## <a name="deploy-the-app-to-your-hololens"></a>Implementar a aplicação no seu HoloLens

Alterar o **configuração da solução** ao **versão**, alterar **plataforma de solução** para **x86**e selecione **dispositivo**  entre as opções de destino da implementação.

![Configuração do Visual Studio](./media/get-started-hololens/visual-studio-configuration.png)

Ligam os dispositivos HoloLens, inicie sessão e ligá-la no PC com um cabo USB.

Selecione **depurar** > **iniciar a depuração** para implementar a sua aplicação e iniciar a depuração.

Siga as instruções na aplicação para colocar e lembre-se de uma âncora.

No Visual Studio, pare a aplicação, selecionando **parar depuração** ou premir **Shift + F5**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Partilha âncoras geográficos em todos os dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
