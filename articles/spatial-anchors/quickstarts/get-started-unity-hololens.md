---
title: Início rápido-criar um aplicativo de HoloLens de Unity com âncoras espaciais do Azure | Microsoft Docs
description: Neste guia de início rápido, você aprende a criar um aplicativo do HoloLens com o Unity usando âncoras espaciais.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 56f9901a1468207a295f8223e990f7a926b2d76e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561445"
---
# <a name="quickstart-create-a-unity-hololens-app-that-uses-azure-spatial-anchors"></a>Início rápido: Criar um aplicativo de HoloLens do Unity que usa âncoras espaciais do Azure

Neste guia de início rápido, você criará um aplicativo de HoloLens do Unity que usa [âncoras espaciais do Azure](../overview.md). As âncoras espaciais são um serviço de desenvolvedor de plataforma cruzada que permite criar experiências de realidade misturada com objetos que persistem seu local entre dispositivos ao longo do tempo. Quando tiver terminado, você terá um aplicativo de HoloLens criado com o Unity que pode salvar e recuperar uma âncora espacial.

Vai aprender a:

- Crie uma conta de âncoras espaciais.
- Prepare as configurações de compilação do Unity.
- Configure o identificador de conta e a chave de conta das âncoras espaciais.
- Exporte o projeto do Visual Studio do HoloLens.
- Implante o aplicativo e execute-o em um dispositivo HoloLens.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

- Você precisa de um computador Windows no qual o <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019,1</a> ou posterior e o <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> ou posterior estejam instalados. A instalação do Visual Studio deve incluir a carga de trabalho de **desenvolvimento plataforma universal do Windows** e o componente **SDK do Windows 10 (10.0.18362.0 ou mais recente)** . Você também deve instalar o <a href="https://git-scm.com/download/win" target="_blank">git para Windows</a>.
- Você precisa de um dispositivo HoloLens no qual o [modo de desenvolvedor](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) esteja habilitado. [Atualização de outubro de 2018 do Windows 10](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018) (também conhecido como RS5) deve ser instalado no dispositivo. Para atualizar para a versão mais recente no HoloLens, abra o aplicativo **configurações** , vá para **Atualizar & segurança**e, em seguida, selecione **verificar se há atualizações**.
- Em seu aplicativo, você precisa habilitar o recurso **SpatialPerception** . Essa configuração está no **Build Settings** > configurações do**Player** > configurações de**publicação** > **recursos**.
- Em seu aplicativo, você precisa habilitar a **realidade virtual com suporte** com o **SDK do Windows Mixed Reality**. Essa configuração está em configurações de **compilação** > configurações do**Player** > **XR Settings**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Baixar e abrir o projeto de exemplo do Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

Abra **configurações de compilação** selecionando **arquivo** > **configurações de compilação**.

Na seção **plataforma** , selecione **plataforma universal do Windows**. Altere o **dispositivo de destino** para o **HoloLens**.

Selecione **alternar plataforma** para alterar a plataforma para **plataforma universal do Windows**. O Unity pode solicitar que você instale componentes de suporte do UWP se eles estiverem ausentes.

![Janela de configurações de Build do Unity](./media/get-started-unity-hololens/unity-build-settings.png)

Feche a janela **configurações de compilação** .

## <a name="configure-the-account-identifier-and-key"></a>Configurar o identificador e a chave da conta

No painel **projeto** , vá para `Assets/AzureSpatialAnchors.Examples/Scenes` e abra o arquivo `AzureSpatialAnchorsBasicDemo.unity` de cena.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Salve a cena selecionando **arquivo** > **salvar**.

## <a name="export-the-hololens-visual-studio-project"></a>Exportar o projeto do Visual Studio do HoloLens

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Selecione **Compilar**. Na caixa de diálogo, selecione uma pasta na qual exportar o projeto do Visual Studio do HoloLens.

Quando a exportação for concluída, uma pasta que contém o projeto de HoloLens exportado será exibida.

## <a name="deploy-the-hololens-application"></a>Implantar o aplicativo do HoloLens

Na pasta, clique duas vezes em **HELLOAR U3D. sln** para abrir o projeto no Visual Studio.

Altere a **configuração da solução** para **liberar**, altere a **plataforma da solução** para **x86**e selecione **dispositivo** nas opções de destino de implantação.

Se estiver usando o HoloLens 2, use o **ARM** como **plataforma de solução**, em vez de **x86**.

   ![Configuração do Visual Studio](./media/get-started-unity-hololens/visual-studio-configuration.png)

Ligue o dispositivo do HoloLens, entre e conecte o dispositivo ao computador usando um cabo USB.

Selecione **depurar** > **Iniciar Depuração** para implantar seu aplicativo e iniciar a depuração.

Siga as instruções no aplicativo para colocá-lo e relembrar uma âncora.

No Visual Studio, interrompa o aplicativo selecionando **parar depuração** ou Shift + F5.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Compartilhar âncoras espaciais entre dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
