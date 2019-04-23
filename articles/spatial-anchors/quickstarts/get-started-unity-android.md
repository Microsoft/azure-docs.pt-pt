---
title: Início rápido - criar aplicação do Android Unity com âncoras espaciais do Azure | Documentos da Microsoft
description: Neste guia de introdução, saiba como criar uma aplicação Android com o Unity usando âncoras espaciais.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: bce71db594d2bbd869dcc5a1ff5cb494a7a6f1c2
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "59994951"
---
# <a name="quickstart-create-an-android-unity-app-with-azure-spatial-anchors"></a>Início rápido: Criar uma aplicação Android Unity com o Azure âncoras espaciais

Este guia de introdução mostra como criar uma aplicação do Android Unity com [âncoras geográficos de Azure](../overview.md). Âncoras espaciais do Azure é um serviço de programador para várias plataformas que permite-lhe criar experiências de realidade mista usando objetos que manter a respetiva localização em todos os dispositivos ao longo do tempo. Quando tiver terminado, terá uma aplicação ARCore Android criada com o Unity, que pode guardar e lembre-se de uma âncora de espacial.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de âncoras espaciais
> * Preparar as definições de compilação do Unity
> * Transferir e importar o SDK de ARCore para Unity
> * Configurar a chave de conta e o identificador da conta de âncoras espaciais
> * Exportar o projeto do Android Studio
> * Implementar e executar num dispositivo Android

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:

- Um Windows ou macOS do computador com <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3 +</a> e <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3 +</a>.
  - Se executar no Windows, também precisará <a href="https://git-scm.com/download/win" target="_blank">Git para Windows</a>.
  - Se executar no macOS, obtenha o Git instalado através do HomeBrew. Introduza o seguinte comando numa única linha de Terminal: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Em seguida, execute `brew install git`.
- R <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">desenvolvedor ativada</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore com capacidade</a> dispositivo Android.
- A aplicação tem de utilizar a versão **1.7** do SDK do ARCore para Unity.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project-in-unity"></a>Abra o projeto de exemplo no Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Configurar o identificador de conta e chave

Na **projeto** painel, navegue até à `Assets/AzureSpatialAnchorsPlugin/Examples` e abra o `AzureSpatialAnchorsBasicDemo.unity` ficheiro de cena.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Salve a cena selecionando **arquivo** -> **guardar**.

## <a name="export-the-android-studio-project"></a>Exportar o projeto do Android Studio

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Certifique-se a **projeto exportar** caixa de verificação não tem uma marca de verificação. Clique em **compilar e executar**. Será solicitado a salvar sua `.apk` ficheiro, pode escolher qualquer nome para o mesmo.

Siga as instruções na aplicação para colocar e lembre-se de uma âncora.

> [!NOTE]
> Ao executar a aplicação, se não vir a câmera como plano de fundo (para a instância em vez disso, verá um espaço em branco, azul ou outras texturas), em seguida, provavelmente terá de voltar a importar ativos no Unity. Pare a aplicação. No menu superior no Unity, escolha **elementos -> importe novamente todos os**. Em seguida, execute novamente a aplicação.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Partilha âncoras geográficos em todos os dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
