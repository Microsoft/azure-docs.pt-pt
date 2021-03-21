---
title: 'Quickstart: Criar uma app HoloLens com Unidade'
description: Neste quickstart, você aprende a construir uma aplicação HoloLens com Unidade usando Âncoras Espaciais.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 03/18/2021
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: b99c2efcb746a056adc76fdc161c8d8a8e2799e1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670125"
---
# <a name="quickstart-create-a-unity-hololens-app-that-uses-azure-spatial-anchors"></a>Quickstart: Criar uma app Unity HoloLens que usa âncoras espaciais Azure

Neste quickstart, você vai criar uma aplicação Unity HoloLens que usa [Âncoras Espaciais Azure.](../overview.md) O Spatial Anchors é um serviço de desenvolvedores de plataformas cruzadas que permite criar experiências de realidade mista com objetos que persistem a sua localização através dos dispositivos ao longo do tempo. Quando terminar, terá uma aplicação HoloLens construída com Unidade que pode salvar e recordar uma âncora espacial.

Vai aprender a:

- Criar uma conta de Âncoras Espaciais.
- Prepare as definições de construção da Unidade.
- Configurar o identificador de conta Spatial Anchors e a chave de conta.
- Exportar o projeto HoloLens Visual Studio.
- Implemente a aplicação e execute-a num dispositivo HoloLens.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

- Precisa de um computador Windows com <a href="https://unity3d.com/get-unity/download" target="_blank">Unidade (LTS)</a> e <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> ou posteriormente instalado. Use **Unity 2020 LTS** com a versão 2.9 ou posterior da ASA SDK (que utiliza o [Quadro Plug-in Unitário XR)](https://docs.unity3d.com/Manual/XRPluginArchitecture.html)ou **Unity 2019 LTS** com a versão 2.8 ou mais cedo da ASA SDK. A instalação do Seu Estúdio Visual deve incluir a carga de trabalho **de desenvolvimento da Plataforma Universal Windows** e o componente Windows **10 SDK (10.0.18362.0 ou mais recente).** Também tem de instalar <a href="https://git-scm.com/download/win" target="_blank">o Git para Windows</a> e Git <a href="https://git-lfs.github.com/">LFS</a>.
- Precisa de um dispositivo HoloLens em que [modo de desenvolvimento](/windows/mixed-reality/using-visual-studio) está ativado. [A atualização do Windows 10 May 2020](/windows/mixed-reality/whats-new/release-notes-may-2020) deve ser instalada no dispositivo. Para atualizar a versão mais recente do HoloLens, abra a aplicação **Definições,** vá a **Update & Security** e, em seguida, selecione Check for **updates**.
- Na sua aplicação, precisa de ativar a capacidade **de Perceção Espacial.** Esta definição está em **Configurações de Definições** do leitor de  >  **definições**  >  **de**  >  **definições de configurações de configurações .**
- Na sua aplicação, precisa de ativar a **realidade virtual suportada** com **o Windows Mixed Reality SDK.** Esta definição está em **Definições do** leitor de  >  **definições**  >  **XR**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Descarregue e abra o projeto de amostra de Unidade

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

Abrir **definições de construção** selecionando   >  **Definições de construção de ficheiros**.

Na secção **Plataforma,** selecione **Universal Windows Platform**. Altere o **dispositivo alvo** para **HoloLens**.

Selecione **a Plataforma Switch** para alterar a plataforma para a Plataforma Universal do **Windows**. A unidade pode levá-lo a instalar componentes de suporte UWP se estiverem desaparecidos.

![Janela de configurações de construção de unidade](./media/get-started-unity-hololens/unity-build-settings.png)

Feche a janela **'Definições de construção'.**

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-hololens-visual-studio-project"></a>Exportar o projeto HoloLens Visual Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Selecione **Build**. Na caixa de diálogo, selecione uma pasta para exportar o projeto HoloLens Visual Studio.

Quando a exportação estiver concluída, aparecerá uma pasta contendo o projeto HoloLens exportado.

## <a name="deploy-the-hololens-application"></a>Implementar a aplicação HoloLens

Na pasta, clique duas vezes **em HelloAR U3D.sln** para abrir o projeto no Visual Studio.

Alterar a Configuração de **Solução** para **Desbloquear,** alterar a **Plataforma de Solução** para **x86** e selecionar **o Dispositivo** a partir das opções-alvo de implementação.

Se utilizar hololens 2, utilize **o ARM64** como **Plataforma de Solução,** em vez de **x86**.

   ![Configuração do Estúdio Visual](./media/get-started-unity-hololens/visual-studio-configuration.png)

Ligue o dispositivo HoloLens, inscreva-se e ligue o dispositivo ao PC utilizando um cabo USB.

Selecione **Debug**  >  **Comece a depurar** para implementar a sua aplicação e comece a depurar.

Na aplicação, selecione **BasicDemo** usando as setas e, em seguida, pressione o **Go!** botão para executar a demonstração. Siga as instruções para colocar e chame uma âncora.

![Screenshot 1 ](./media/get-started-unity-hololens/screenshot-1.jpg)
 ![ Screenshot 2 ](./media/get-started-unity-hololens/screenshot-2.jpg)
 ![ Screenshot 3 ](./media/get-started-unity-hololens/screenshot-3.jpg)
 ![ Screenshot 4](./media/get-started-unity-hololens/screenshot-4.jpg)

No Visual Studio, pare a aplicação selecionando **Stop Debugging** ou Shift+F5.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Partilhar âncoras espaciais em dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [Como: Configurar âncoras espaciais Azure num projeto de unidade](../how-tos/setup-unity-project.md)