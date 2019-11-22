---
title: 'Início rápido: criar um aplicativo iOS'
description: Neste guia de início rápido, você aprende a criar um aplicativo iOS usando âncoras espaciais.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 39732ef785fc610572f917be77dec3f560698fb7
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277195"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Início rápido: criar um aplicativo iOS com âncoras espaciais do Azure, em Swift ou Objective-C

Este guia de início rápido aborda como criar um aplicativo iOS usando [âncoras espaciais do Azure](../overview.md) em Swift ou Objective-C. As âncoras espaciais do Azure são um serviço de desenvolvedor de plataforma cruzada que permite que você crie experiências de realidade mista usando objetos que persistem seu local entre dispositivos ao longo do tempo. Quando tiver terminado, você terá um aplicativo ARKit iOS que pode salvar e lembrar uma âncora espacial.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de âncoras espaciais
> * Configurar o identificador de conta de âncoras espaciais e a chave de conta
> * Implantar e executar em um dispositivo iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:

- Uma máquina macOS habilitada para o desenvolvedor com a versão mais recente do <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> e do <a href="https://cocoapods.org" target="_blank">CocoaPods</a> instalada.
- Git instalado via HomeBrew. Digite o seguinte comando em uma única linha do terminal: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Em seguida, execute `brew install git`.
- Um dispositivo IOS <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">compatível com o ARKit</a> habilitado para desenvolvedor.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Abrir o projeto de exemplo

Use o terminal para executar as seguintes ações.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Instale o pods necessário usando CocoaPods:

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

---

Execute `pod install --repo-update` para instalar o CocoaPods para o projeto.

Agora, abra o `.xcworkspace` no Xcode.

> [!NOTE]
> Consulte as etapas de solução de problemas [aqui](#cocoapods-issues-on-macos-catalina-1015) se você estiver tendo problemas de CocoaPod após a atualização para o MacOS Catalina (10,15).

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>Configurar o identificador e a chave da conta

A próxima etapa é configurar o aplicativo para usar o identificador de conta e a chave de conta. Você os copiou em um editor de texto ao [Configurar o recurso âncoras espaciais](#create-a-spatial-anchors-resource).

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Abra `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`.

Localize o campo `spatialAnchorsAccountKey` e substitua `Set me` pela chave de conta.

Localize o campo `spatialAnchorsAccountId` e substitua `Set me` pelo identificador da conta.

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Abra `iOS/Objective-C/SampleObjC/BaseViewController.m`.

Localize o campo `SpatialAnchorsAccountKey` e substitua `Set me` pela chave de conta.

Localize o campo `SpatialAnchorsAccountId` e substitua `Set me` pelo identificador da conta.

---

## <a name="deploy-the-app-to-your-ios-device"></a>Implantar o aplicativo em seu dispositivo iOS

Conecte o dispositivo iOS ao Mac e defina o **esquema ativo** para seu dispositivo IOS.

![Selecionar o dispositivo](./media/get-started-ios/select-device.png)

Selecione **Compilar e execute o esquema atual**.

![Implantar e executar](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Se você vir um erro `library not found for -lPods-SampleObjC`, provavelmente abriu o arquivo `.xcodeproj` em vez do `.xcworkspace`. Abra o `.xcworkspace` e tente novamente.

No Xcode, pare o aplicativo pressionando **parar**.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>Problemas de CocoaPods no Catalina do macOS (10,15)

Se você atualizou recentemente para o macOS (10,15) e tinha o CocoaPods instalado com antecedência, CocoaPods pode estar em um estado rompido e falhar ao configurar corretamente seus arquivos de projeto e de `.xcworkspace`. Para resolver esse problema, você precisará reinstalar o CocoaPods executando os seguintes comandos:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: compartilhar âncoras espaciais entre dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
