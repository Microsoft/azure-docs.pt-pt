---
title: 'Quickstart: Criar uma aplicação Para Android unidade'
description: Neste quickstart, você aprende a construir uma aplicação Android com Unidade usando Âncoras Espaciais.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/31/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7e92da72e8125c22655a7eb9986f0554963aefeb
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91280909"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Quickstart: Criar uma aplicação Para Android unidade com âncoras espaciais Azure

Este quickstart cobre como criar uma aplicação Unity Android usando [Âncoras Espaciais Azure.](../overview.md) Azure Spatial Anchors é um serviço de desenvolvedor de plataformas cruzadas que permite criar experiências de realidade mista usando objetos que persistem a sua localização através dos dispositivos ao longo do tempo. Quando terminar, terá uma aplicação ARCore Android construída com Unidade que pode salvar e recordar uma âncora espacial.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de Âncoras Espaciais
> * Preparar definições de construção de unidade
> * Configure o identificador de conta Spatial Anchors e a chave de conta
> * Exportar o projeto Android Studio
> * Implementar e executar num dispositivo Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:

- Uma máquina Windows ou macOS com <a href="https://unity3d.com/get-unity/download" target="_blank">Unidade 2019.4 (LTS)</a>, incluindo o **Android Build Support** com Android **SDK & ferramentas NDK** e módulos **OpenJDK.**
  - Se estiver a funcionar no Windows, também necessitará de <a href="https://git-scm.com/download/win" target="_blank">Git para Windows</a> e Git <a href="https://git-lfs.github.com/">LFS</a>.
  - Se estiver a funcionar com macOS, instale o Git via HomeBrew. Introduza o seguinte comando numa única linha do Terminal: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` . Então, corra `brew install git` `brew install git-lfs` e.
- Um dispositivo Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">habilitado para o desenvolvedor</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">capaz de ARCore.</a>
  - Podem ser necessários controladores adicionais para que o computador comunique com o seu dispositivo Android. Para mais informações e instruções, consulte [aqui.](https://developer.android.com/studio/run/device.html)

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Descarregue e abra o projeto de amostra de Unidade

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-android-studio-project"></a>Exportar o projeto Android Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Selecione o seu dispositivo no **Dispositivo run** e, em seguida, selecione Build **And Run**. Pedir-lhe-ão que guarde um `.apk` ficheiro, para o qual pode escolher qualquer nome.

Assim que a aplicação começar, deverá ver um ecrã com este aspeto:

![Tela de aplicativo](./media/get-started-unity-android/demo-app-screen.png)

Siga as instruções na aplicação para colocar e lembre-se de uma âncora.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="rendering-issues"></a>Questões de renderização

Ao executar a aplicação, se não vir a câmara como o fundo (por exemplo, vê uma textura em branco, azul ou outra) então provavelmente terá de reimportar ativos em Unidade. Pare a aplicação. A partir do menu de topo em Unidade, escolha **Ativos -> Reimport todos**. Em seguida, executar a aplicação novamente.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Partilhar Âncoras Espaciais através de dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [Como: Configurar âncoras espaciais Azure num projeto de unidade](../how-tos/setup-unity-project.md)
