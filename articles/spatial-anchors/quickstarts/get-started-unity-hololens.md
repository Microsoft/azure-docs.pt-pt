---
title: Início rápido - criar aplicação HoloLens Unity com âncoras espaciais do Azure | Documentos da Microsoft
description: Neste guia de introdução, saiba como criar uma aplicação de HoloLens com o Unity usando âncoras espaciais.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: b2bfec47bc92ebf5db1561d8fca33940dc376866
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752333"
---
# <a name="quickstart-create-a-hololens-unity-app-using-azure-spatial-anchors"></a>Início rápido: Criar uma aplicação de HoloLens Unity com âncoras espaciais do Azure

Este guia de introdução mostra como criar uma aplicação HoloLens Unity com [âncoras geográficos de Azure](../overview.md). Âncoras espaciais do Azure é um serviço de programador para várias plataformas que permite-lhe criar experiências de realidade mista usando objetos que manter a respetiva localização em todos os dispositivos ao longo do tempo. Quando tiver terminado, terá uma aplicação de HoloLens criada com o Unity, que pode guardar e lembre-se de uma âncora de espacial.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de âncoras espaciais
> * Preparar as definições de compilação do Unity
> * Configurar a chave de conta e o identificador da conta de âncoras espaciais
> * Exportar o projeto do Visual Studio do HoloLens
> * Implementar e executar num dispositivo HoloLens

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:

- Uma máquina Windows com <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3 +</a> e <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 +</a> instalado com o **desenvolvimento da plataforma Universal do Windows** carga de trabalho.
- Um dispositivo de HoloLens com [modo de programador](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) ativada.
- A aplicação tem de definir o **SpatialPerception** capacidade sob **definições de criação**->**as definições do leitor**->**publicação As definições**->**capacidades**.
- Tem de ativar a sua aplicação **suportado de realidade Virtual** com **SDK de realidade mista do Windows** sob **definições de criação**->**asdefiniçõesdoleitor** -> **XR definições**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Abra o projeto de exemplo no Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Abra o Unity e abra o projeto no `Unity` pasta.

Open **definições de criação** ao selecionar **ficheiro** -> **definições de criação**.

Na **plataforma** secção, selecione **plataforma Universal do Windows**. Em seguida, altere a **dispositivo de destino** ao **HoloLens**.

Selecione **plataforma de comutador** para alterar a plataforma para **plataforma Universal do Windows**.

![Definições de compilação do Unity](./media/get-started-unity-hololens/unity-build-settings.png)

Fechar o **definições de criação** janela.

## <a name="configure-account-identifier-and-key"></a>Configurar o identificador de conta e chave

Na **projeto** painel, navegue até à `Assets/AzureSpatialAnchorsPlugin/Examples` e abra o `AzureSpatialAnchorsBasicDemo.unity` ficheiro de cena.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Salve a cena selecionando **arquivo** -> **guardar**.

## <a name="export-the-hololens-visual-studio-project"></a>Exportar o projeto do Visual Studio do HoloLens

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Selecione **criar** para abrir uma caixa de diálogo. Em seguida, selecione uma pasta para exportar o projeto do Visual Studio de HoloLens.

Quando a exportação for concluída, uma pasta será apresentada que contém o projeto de HoloLens exportado.

## <a name="deploy-the-hololens-application"></a>Implementar a aplicação de HoloLens

Na pasta, faça duplo clique no `HelloAR U3D.sln` para abrir o projeto no Visual Studio.

Alterar o **configuração da solução** ao **versão**, alterar **plataforma de solução** para **x86**e selecione **dispositivo**  entre as opções de destino da implementação.

![Configuração do Visual Studio](./media/get-started-unity-hololens/visual-studio-configuration.png)

Ligam os dispositivos HoloLens, inicie sessão e ligá-la no PC com um cabo USB.

Selecione **depurar** > **iniciar a depuração** para implementar a sua aplicação e iniciar a depuração.

Siga as instruções na aplicação para colocar e lembre-se de uma âncora.

No Visual Studio, pare a aplicação, selecionando **parar depuração** ou premir **Shift + F5**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Partilha âncoras geográficos em todos os dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
