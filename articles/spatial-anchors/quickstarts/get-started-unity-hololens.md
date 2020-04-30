---
title: 'Quickstart: Criar uma app HoloLens com Unidade'
description: Neste arranque rápido, aprende-se a construir uma aplicação HoloLens com a Unity usando âncoras espaciais.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9e946d7f4616799768711ee8c18584a2a8fcff2a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81482015"
---
# <a name="quickstart-create-a-unity-hololens-app-that-uses-azure-spatial-anchors"></a>Quickstart: Criar uma app HoloLens de unidade que usa âncoras espaciais Azure

Neste arranque rápido, você vai criar uma app Unity HoloLens que usa [âncoras espaciais Azure](../overview.md). Spatial Anchors é um serviço de desenvolvimento de plataformas cruzadas que permite criar experiências de realidade mista com objetos que persistem a sua localização através de dispositivos ao longo do tempo. Quando terminar, terá uma aplicação HoloLens construída com unidade que pode salvar e recordar uma âncora espacial.

Vai aprender a:

- Crie uma conta de Âncoras Espaciais.
- Prepare a unidade construir cenários.
- Configure o identificador de conta Spatial Anchors e a chave de conta.
- Exportar o projeto HoloLens Visual Studio.
- Implemente a aplicação e execute-a num dispositivo HoloLens.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

- É necessário um computador Windows com <a href="https://unity3d.com/get-unity/download" target="_blank">Unidade 2019.1 ou 2019.2</a> e <a href="https://www.visualstudio.com/downloads/" target="_blank">O Estúdio Visual 2019</a> ou posteriormente está instalado. A instalação do Seu Estúdio Visual deve incluir a carga de trabalho de desenvolvimento da **Plataforma Universal Windows** e o componente Windows **10 SDK (10.0.18362.0 ou mais recente).** Também deve instalar <a href="https://git-scm.com/download/win" target="_blank">git para Windows</a> e <a href="https://git-lfs.github.com/">Git LFS</a>.
- Precisa de um dispositivo HoloLens no modo de [desenvolvimento](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) ativado. [A atualização do Windows 10 outubro 2018](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018) (também conhecida como RS5) deve ser instalada no dispositivo. Para atualizar a mais recente versão do HoloLens, abra a aplicação **Definições,** vá ao **Update & Security**, e, em seguida, selecione Verificar se há **atualizações**.
- Na sua aplicação, tem de ativar a capacidade **de SpatialPerception.** Esta definição está na **Definição de Definições** > de**Definições** > do Jogador**Capacidades**de**publicação** > .
- Na sua aplicação, é necessário ativar o Suporte à **Realidade Virtual** com o Windows Mixed **Reality SDK**. Esta definição está em**Definições** >  **de Definições** > do Jogador de Construção**Definições XR**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Descarregue e abra o projeto de amostra da Unidade

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

Abra **as definições** de construção selecionando definições de**construção**de **ficheiros** > .

Na secção **Plataforma,** selecione **Universal Windows Platform**. Mude o **dispositivo-alvo** para **HoloLens**.

Selecione **Plataforma Switch** para mudar a plataforma para **Plataforma Universal Windows**. A unidade pode levar-te a instalar componentes de suporte da UWP se estiverem desaparecidos.

![Janela de definições de construção de unidade](./media/get-started-unity-hololens/unity-build-settings.png)

Feche a janela Definições de **Construção.**

## <a name="configure-the-account-identifier-and-key"></a>Configure o identificador de conta e a chave

No painel do **Projeto,** vá `Assets/AzureSpatialAnchors.Examples/Scenes` `AzureSpatialAnchorsBasicDemo.unity` e abra o arquivo de cena.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Guarde a cena selecionando **File** > **Save**.

## <a name="export-the-hololens-visual-studio-project"></a>Exportar o projeto HoloLens Visual Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Selecione **Construir**. Na caixa de diálogo, selecione uma pasta para exportar o projeto HoloLens Visual Studio.

Quando a exportação estiver concluída, aparecerá uma pasta contendo o projeto HoloLens exportado.

## <a name="deploy-the-hololens-application"></a>Implementar a aplicação HoloLens

Na pasta, clique duas vezes no **HelloAR U3D.sln** para abrir o projeto no Estúdio Visual.

Altere a configuração da **solução** para **o lançamento,** mude a Plataforma de **Solução** para **x86**, e selecione **O Dispositivo** a partir das opções-alvo de implementação.

Se utilizar hololens 2, utilize o **ARM64** como Plataforma de **Solução**, em vez de **x86**.

   ![Configuração do Estúdio Visual](./media/get-started-unity-hololens/visual-studio-configuration.png)

Ligue o dispositivo HoloLens, inscreva-se e ligue o dispositivo ao PC utilizando um cabo USB.

Selecione **Debug** > **Comece a depurar-se** para implementar a sua aplicação e comece a depurar.

Siga as instruções na aplicação para colocar e lembre-se de uma âncora.

No Estúdio Visual, pare a aplicação selecionando **stop Debugging** ou Shift+F5.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="unity-20193"></a>Unidade 2019.3

Devido a alterações de rutura, a Unidade 2019.3 não é atualmente apoiada. Utilize a Unidade 2019.1 ou 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Partilhar âncoras espaciais através de dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
