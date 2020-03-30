---
title: 'Quickstart: Criar uma aplicação Android unidade'
description: Neste arranque rápido, aprende-se a construir uma aplicação Android com a Unity utilizando âncoras espaciais.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7acff7f0249cdedcebd367fc315be92cafb9ab78
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77615447"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Quickstart: Criar uma app Android unidade com âncoras espaciais Azure

Este quickstart cobre como criar uma aplicação Unity Android usando [âncoras espaciais Azure](../overview.md). O Azure Spatial Anchors é um serviço de desenvolvimento de plataformas cruzadas que permite criar experiências de realidade mista utilizando objetos que persistem a sua localização através de dispositivos ao longo do tempo. Quando terminar, terá uma aplicação ARCore Android construída com Unidade que pode salvar e recordar uma âncora espacial.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de Âncoras Espaciais
> * Prepare as configurações de construção da Unidade
> * Configure o identificador de conta Spatial Anchors e a chave de conta
> * Exportar o projeto Android Studio
> * Implementar e executar num dispositivo Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:

- Uma máquina Windows ou macOS com <a href="https://unity3d.com/get-unity/download" target="_blank">Unidade 2019.1 ou 2019.2,</a> incluindo os módulos Android Build Support e Android SDK & NDK Tools.
  - Se estiver a funcionar no Windows, também vai precisar de <a href="https://git-scm.com/download/win" target="_blank">Git para Windows</a> e <a href="https://git-lfs.github.com/">Git LFS</a>.
  - Se funcionar no macOS, instale o Git via HomeBrew. Introduza o seguinte comando numa única `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`linha do Terminal: . Então, `brew install git` `brew install git-lfs`corra e.
- Um <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">desenvolvedor ativado</a> e dispositivo Android <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">capaz de ARCore.</a>
  - Podem ser necessários controladores adicionais para que o seu computador se comunique com o seu dispositivo Android. Consulte [aqui](https://developer.android.com/studio/run/device.html) informações e instruções adicionais.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Descarregue e abra o projeto de amostra da Unidade

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Configurar identificador de conta e chave

No painel do **Projeto,** navegue e `Assets/AzureSpatialAnchors.Examples/Scenes` abra o arquivo de `AzureSpatialAnchorsBasicDemo.unity` cena.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Guarde a cena selecionando **File** -> **Save**.

## <a name="export-the-android-studio-project"></a>Exportar o projeto Android Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Selecione o seu dispositivo no **Dispositivo executar** e clique em Construir **e Executar**. Será-lhe pedido que `.apk` guarde um ficheiro para o qual pode escolher qualquer nome.

Siga as instruções na aplicação para colocar e lembre-se de uma âncora.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="rendering-issues"></a>Questões de renderização

Ao executar a aplicação, se não vê a câmara como o fundo (por exemplo, vê uma textura em branco, azul ou outra) então provavelmente precisa de reimportar ativos na Unidade. Pare a aplicação. A partir do menu superior da Unidade, escolha **Ativos -> Reimportar tudo.** Em seguida, executar a aplicação novamente.

### <a name="unity-20193"></a>Unidade 2019.3

Devido a alterações de rutura, a Unidade 2019.3 não é atualmente apoiada. Utilize a Unidade 2019.1 ou 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Partilhar âncoras espaciais em dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
