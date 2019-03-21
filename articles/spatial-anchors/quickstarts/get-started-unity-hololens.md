---
title: Início rápido - criar uma aplicação HoloLens Unity com âncoras espaciais do Azure | Documentos da Microsoft
description: Neste guia de introdução, saiba como criar uma aplicação de HoloLens com o Unity usando âncoras espaciais.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 2c29d9860f1c8fc4f0f6d9f4d84c06e8ade8dee5
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286980"
---
# <a name="quickstart-create-a-hololens-unity-app-that-uses-azure-spatial-anchors"></a>Início rápido: Criar uma aplicação HoloLens Unity que utiliza as âncoras espaciais do Azure

Neste início rápido, irá criar uma aplicação HoloLens Unity que utiliza [âncoras geográficos de Azure](../overview.md). Âncoras espaciais é um serviço de programador para várias plataformas que permite-lhe criar experiências de realidade mista com objetos que manter a respetiva localização em todos os dispositivos ao longo do tempo. Quando tiver terminado, terá uma aplicação de HoloLens criada com o Unity, que pode guardar e lembre-se de uma âncora de espacial.

Vai aprender a:

- Crie uma conta de âncoras espaciais.
- Prepare-se as definições de compilação do Unity.
- Configure a chave de conta e o identificador da conta de âncoras espaciais.
- Exporte o projeto do Visual Studio de HoloLens.
- Implementar a aplicação e executá-lo num dispositivo HoloLens.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:


- Precisa de um computador Windows, no qual <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3</a> ou posterior e <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017</a> ou posterior está instalado. Instalação do Visual Studio tem de incluir o **desenvolvimento de plataforma Universal do Windows** carga de trabalho. Deve instalar também <a href="https://git-scm.com/download/win" target="_blank">Git para Windows</a>.
- Terá de dispositivo em que HoloLens [modo de programador](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) ativada. [Atualização do Windows 10 de Outubro de 2018](https://docs.microsoft.com/en-us/windows/mixed-reality/release-notes-october-2018) (também conhecido como RS5) tem de estar instalado no dispositivo. Para atualizar para a versão mais recente no HoloLens, abra a **definições** aplicação, aceda à **atualização e segurança**e, em seguida, selecione **procurar atualizações**.
- Na sua aplicação, tem de ativar a **SpatialPerception** capacidade. Esta definição está em **definições de criação** > **definições de leitor** > **definições de publicação**  >   **Capacidades**.
- Na sua aplicação, tem de ativar **suportado de realidade Virtual** com **SDK de realidade mista do Windows**. Esta definição está em **definições de criação** > **definições de leitor** > **XR definições**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Abra o projeto de exemplo no Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

No Unity, abra o projeto na pasta Unity.

Open **definições de criação** ao selecionar **ficheiro** > **definições de criação**.

Na **plataforma** secção, selecione **plataforma Universal do Windows**. Alteração da **dispositivo de destino** ao **HoloLens**.

Selecione **plataforma de comutador** para alterar a plataforma para **plataforma Universal do Windows**. Unity poderá pedir-lhe para instalar os componentes de suporte do UWP se estiverem em falta.

   ![Janela de definições de criação do Unity](./media/get-started-unity-hololens/unity-build-settings.png)

Fechar o **definições de criação** janela.

## <a name="configure-the-account-identifier-and-key"></a>Configurar o identificador de conta e a chave

Na **projeto** painel, aceda à `Assets/AzureSpatialAnchorsPlugin/Examples` e abra o `AzureSpatialAnchorsBasicDemo.unity` ficheiro de cena.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Salve a cena selecionando **arquivo** > **guardar**.

## <a name="export-the-hololens-visual-studio-project"></a>Exportar o projeto do Visual Studio do HoloLens

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Selecione **criar**. Na caixa de diálogo, selecione uma pasta na qual pretende exportar o projeto do Visual Studio de HoloLens.

Quando a exportação for concluída, será apresentada uma pasta que contém o projeto de HoloLens exportado.

## <a name="deploy-the-hololens-application"></a>Implementar a aplicação de HoloLens

Na pasta, faça duplo clique em **HelloAR U3D.sln** para abrir o projeto no Visual Studio.

Alterar o **configuração da solução** ao **versão**, alterar o **plataforma de solução** para **x86**e selecione **dispositivo**  entre as opções de destino da implementação.

   ![Configuração do Visual Studio](./media/get-started-unity-hololens/visual-studio-configuration.png)

Ativar o dispositivo HoloLens, inicie sessão e ligue o dispositivo no PC com um cabo USB.

Selecione **depurar** > **iniciar a depuração** para implementar a sua aplicação e iniciar a depuração.

Siga as instruções na aplicação para colocar e lembre-se de uma âncora.

No Visual Studio, pare a aplicação ao selecionar qualquer um **parar depuração** ou Shift + F5.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Partilhar as âncoras geográficos em todos os dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
