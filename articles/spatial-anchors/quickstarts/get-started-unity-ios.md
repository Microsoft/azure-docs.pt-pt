---
title: 'Quickstart: Criar uma app iOS de unidade'
description: Neste arranque rápido, aprende-se a construir uma aplicação iOS com a Unity utilizando âncoras espaciais.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 37a82c4001dd42a4cfbbb9dabec29f28359afd75
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240591"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>Quickstart: Criar uma app de unidade iOS com âncoras espaciais Azure

Este quickstart cobre como criar uma aplicação Unity iOS utilizando [âncoras espaciais Azure](../overview.md). O Azure Spatial Anchors é um serviço de desenvolvimento de plataformas cruzadas que permite criar experiências de realidade mista utilizando objetos que persistem a sua localização através de dispositivos ao longo do tempo. Quando terminar, terá uma aplicação ARKit iOS construída com Unidade que pode salvar e recordar uma âncora espacial.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de Âncoras Espaciais
> * Prepare as configurações de construção da Unidade
> * Configure o identificador de conta Spatial Anchors e a chave de conta
> * Exportar o projeto Xcode
> * Implementar e executar num dispositivo iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:

- Uma máquina macOS com <a href="https://unity3d.com/get-unity/download" target="_blank">Unidade 2019.1 ou 2019.2</a>, a versão mais recente do <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a>e <a href="https://cocoapods.org" target="_blank">CocoaPods instaladas.</a>
- Git instalado via HomeBrew. Introduza o seguinte comando numa única `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`linha do Terminal: . Então, `brew install git` `brew install git-lfs`corra e.
- Um desenvolvedor ativou o dispositivo <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">iOS compatível com ARKit.</a>

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Descarregue e abra o projeto de amostra da Unidade

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Configurar identificador de conta e chave

No painel do **Projeto,** navegue e `Assets/AzureSpatialAnchors.Examples/Scenes` abra o arquivo de `AzureSpatialAnchorsBasicDemo.unity` cena.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Guarde a cena selecionando **File** -> **Save**.

## <a name="export-the-xcode-project"></a>Exportar o projeto Xcode

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

Siga as instruções na aplicação para colocar e lembre-se de uma âncora.

Quando terminar, pare a aplicação premindo **Stop** in Xcode.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="rendering-issues"></a>Questões de renderização

Ao executar a aplicação, se não vê a câmara como o fundo (por exemplo, vê uma textura em branco, azul ou outra) então provavelmente precisa de reimportar ativos na Unidade. Pare a aplicação. A partir do menu superior da Unidade, escolha **Ativos -> Reimportar tudo.** Em seguida, executar a aplicação novamente.

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>Problemas de CocoaPods no macOS Catalina (10.15)

Se atualizou recentemente o macOS Catalina (10.15) e mandou instalar previamente cocoaPods, os CocoaPods podem `.xcworkspace` estar em estado de sabotado e não configurar corretamente as suas cápsulas e ficheiros de projeto. Para resolver este problema, terá de reinstalar os CocoaPods executando os seguintes comandos:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="unity-20193"></a>Unidade 2019.3

Devido a alterações de rutura, a Unidade 2019.3 não é atualmente apoiada. Utilize a Unidade 2019.1 ou 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Partilhar âncoras espaciais em dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
