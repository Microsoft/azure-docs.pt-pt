---
title: Início rápido - criar aplicação do iOS com as âncoras espaciais do Azure | Documentos da Microsoft
description: Neste início rápido, irá aprender a criar uma aplicação iOS com as âncoras espaciais.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 21b7972b230f8a05de304eff38575db87d6af586
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752240"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Início rápido: Criar uma aplicação iOS com o Azure âncoras espaciais, em Objective-C ou Swift

Este guia de introdução mostra como criar uma aplicação iOS com [âncoras geográficos de Azure](../overview.md) em Swift ou Objective-C. Âncoras espaciais do Azure é um serviço de programador para várias plataformas que permite-lhe criar experiências de realidade mista usando objetos que manter a respetiva localização em todos os dispositivos ao longo do tempo. Quando tiver terminado, terá uma aplicação do iOS ARKit que pode guardar e lembre-se de uma âncora de espacial.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de âncoras espaciais
> * Configurar a chave de conta e o identificador da conta de âncoras espaciais
> * Implementar e executar num dispositivo iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:

- Uma máquina de macOS desenvolvedor ativada com <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10 e posterior</a> e <a href="https://cocoapods.org" target="_blank">CocoaPods</a> instalado.
- Um desenvolvedor ativado <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">compatível com o ARKit</a> dispositivo iOS.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Abra o projeto de exemplo

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Instale os pods necessários com o CocoaPods:

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Navegue para `iOS/Swift/`.

```bash
cd ./iOS/Swift/
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Navegue para `iOS/Objective-C/`.

```bash
cd ./iOS/Objective-C/
```

***

Executar `pod install` para instalar os CocoaPods para o projeto.

Agora, abra o `.xcworkspace` no Xcode.

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

***

## <a name="configure-account-identifier-and-key"></a>Configurar o identificador de conta e chave

A próxima etapa é usar o identificador de conta e chave de conta que registou anteriormente quando configurar o recurso de âncoras geográficos para configurar a aplicação.

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Abra `iOS/Swift/SampleSwift/ViewController.swift`.

Localize a `SpatialAnchorsAccountKey` campo e substitua `Set me` com a chave de conta.

Localize a `SpatialAnchorsAccountId` campo e substitua `Set me` com o identificador de conta.

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Abra `iOS/Objective-C/SampleObjC/ViewController.m`.

Localize a `SpatialAnchorsAccountKey` campo e substitua `Set me` com a chave de conta.

Localize a `SpatialAnchorsAccountId` campo e substitua `Set me` com o identificador de conta.

***

## <a name="deploy-the-app-to-your-ios-device"></a>Implementar a aplicação no seu dispositivo iOS

Ligue o dispositivo iOS para o Mac e o conjunto a **esquema do Active Directory** para o seu dispositivo iOS.

![Selecione o dispositivo](./media/get-started-ios/select-device.png)

Selecione **criar e, em seguida, executar o esquema atual**.

![Implementar e executar](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Se vir um `library not found for -lPods-SampleObjC` erro, provavelmente aberta da `.xcodeproj` de ficheiros em vez do `.xcworkspace`. Abra o `.xcworkspace` e tente novamente.

No Xcode, pare a aplicação ao premir **parar**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Partilha âncoras geográficos em todos os dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
