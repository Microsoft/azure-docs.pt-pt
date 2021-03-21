---
title: 'Quickstart: Criar uma aplicação iOS da Unidade'
description: Neste quickstart, aprende-se a construir uma aplicação iOS com Unidade usando Âncoras Espaciais.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 03/18/2021
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 0fe193ee76c56ec57d0643f4a156739d1a51230c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670100"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>Quickstart: Criar uma aplicação iOS da Unidade com âncoras espaciais Azure

Este quickstart abrange como criar uma aplicação unity iOS usando [Âncoras Espaciais Azure](../overview.md). Azure Spatial Anchors é um serviço de desenvolvedor de plataformas cruzadas que permite criar experiências de realidade mista usando objetos que persistem a sua localização através dos dispositivos ao longo do tempo. Quando terminar, terá uma aplicação ARKit iOS construída com Unidade que pode salvar e recordar uma âncora espacial.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de Âncoras Espaciais
> * Preparar definições de construção de unidade
> * Configure o identificador de conta Spatial Anchors e a chave de conta
> * Exportar o projeto Xcode
> * Implementar e executar num dispositivo iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:

- Uma máquina macOS com a versão mais recente do <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> and <a href="https://unity3d.com/get-unity/download" target="_blank">Unitity (LTS)</a> instalada. Use **Unity 2020 LTS** com a versão 2.9 ou posterior da ASA SDK (que utiliza o [Quadro Plug-in Unitário XR)](https://docs.unity3d.com/Manual/XRPluginArchitecture.html)ou **Unity 2019 LTS** com a versão 2.8 ou mais cedo da ASA SDK.
- Git instalado via HomeBrew. Introduza o seguinte comando numa única linha do Terminal: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` . Então, corra `brew install git` `brew install git-lfs` e.
- Um desenvolvedor permitiu que o dispositivo iOS <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">compatível com ARKit.</a>

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Descarregue e abra o projeto de amostra de Unidade

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

## <a name="export-the-xcode-project"></a>Exportar o projeto Xcode

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

Na aplicação, selecione **BasicDemo** usando as setas e, em seguida, pressione o **Go!** botão para executar a demonstração. Siga as instruções para colocar e chame uma âncora.

![Screenshot 1 ](./media/get-started-unity-ios/screenshot-1.jpg)
 ![ Screenshot 2 ](./media/get-started-unity-ios/screenshot-2.jpg)
 ![ Screenshot 3](./media/get-started-unity-ios/screenshot-3.jpg)

Quando terminar, pare a aplicação premindo **Stop** in Xcode.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="rendering-issues"></a>Questões de renderização

Ao executar a aplicação, se não vir a câmara como o fundo (por exemplo, vê uma textura em branco, azul ou outra) então provavelmente terá de re importar ativos em Unidade. Pare a aplicação. A partir do menu de topo em Unidade, escolha **Ativos -> Re-importe todos**. Em seguida, executar a aplicação novamente.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Partilhar Âncoras Espaciais através de dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)

> [!div class="nextstepaction"]
> [Como: Configurar âncoras espaciais Azure num projeto de unidade](../how-tos/setup-unity-project.md)
