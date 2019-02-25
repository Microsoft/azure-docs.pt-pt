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
ms.openlocfilehash: b35aafdad081a48c0d6048743f87e10c6a6b3a77
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752338"
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

- Um Windows ou macOS do computador com <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3 +</a> e <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3 +</a> instalado.
- R <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">desenvolvedor ativada</a> e <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore com capacidade</a> dispositivo Android.

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

Selecione **exportar** para abrir uma caixa de diálogo. Em seguida, selecione uma pasta para exportar o projeto do Android Studio.

Quando a exportação for concluída, uma pasta será exibida contendo o projeto do Android Studio exportado, com uma subpasta denominada **HelloAR U3D**.

## <a name="deploy-the-android-application"></a>Implementar a aplicação Android

Abra o Android Studio e selecione **abrir um projeto existente do Android Studio**. Em seguida, selecione o **HelloAR U3D** sub-pasta a partir de projeto do Android Studio exportado e clique em **OK**.

Após a abertura, será apresentada uma mensagem solicitando a utilizar o Gradle wrapper. Selecione **OK** para utilizar o Gradle wrapper e para abrir o projeto.

Ligar o dispositivo Android, inicie sessão e ligá-la no PC com um cabo USB.

Selecione **executar** da barra de ferramentas do Android Studio.

![Android Studio, implementar e executar](./media/get-started-unity-android/android-studio-deploy-run.png)

Selecione o dispositivo Android no **selecione o destino de implementação** caixa de diálogo e selecione **OK** para executar a aplicação no dispositivo Android.

Siga as instruções na aplicação para colocar e lembre-se de uma âncora.

Pare a aplicação, selecionando **parar** da barra de ferramentas do Android Studio.

![Parar de Android Studio](./media/get-started-unity-android/android-studio-stop.png)

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Partilha âncoras geográficos em todos os dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
