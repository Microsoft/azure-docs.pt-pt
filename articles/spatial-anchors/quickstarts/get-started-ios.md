---
title: 'Quickstart: Criar uma aplicação iOS'
description: Neste arranque rápido, aprende-se a construir uma aplicação iOS utilizando âncoras espaciais.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 6304077a26f5c0ecb91e1ec4936bd79b3d839d95
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79471222"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Quickstart: Criar uma aplicação iOS com âncoras espaciais Azure, em Swift ou Objective-C

Este quickstart cobre como criar uma aplicação iOS utilizando [âncoras espaciais Azure](../overview.md) em Swift ou Objective-C. O Azure Spatial Anchors é um serviço de desenvolvimento de plataformas cruzadas que permite criar experiências de realidade mista utilizando objetos que persistem a sua localização através de dispositivos ao longo do tempo. Quando terminar, terá uma aplicação ARKit iOS que pode guardar e recordar uma âncora espacial.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de Âncoras Espaciais
> * Configure o identificador de conta Spatial Anchors e a chave de conta
> * Implementar e executar num dispositivo iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:

- Um desenvolvedor ativou a máquina macOS com a versão mais recente de <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> e <a href="https://cocoapods.org" target="_blank">CocoaPods instaladas.</a>
- Git instalado via HomeBrew:
  1. Introduza o seguinte comando como uma `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`única linha no terminal: . 
  1. Execute `brew install git` e `brew install git-lfs`.
  1. Atualize o seu `git lfs install` git config com `git lfs install --system` (para o utilizador atual) ou (para todo o sistema).
- Um desenvolvedor ativou o dispositivo <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">iOS compatível com ARKit.</a>

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Abra o projeto da amostra

Utilize o Terminal para realizar as seguintes ações.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Instale as cápsulas necessárias utilizando CocoaPods:

# <a name="swift"></a>[Swift](#tab/openproject-swift)

Navegue para `iOS/Swift/`.

```bash
cd ./iOS/Swift/
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

Navegue para `iOS/Objective-C/`.

```bash
cd ./iOS/Objective-C/
```

---

Corra `pod install --repo-update` para instalar os CocoaPods para o projeto.

Agora abra `.xcworkspace` o xcode.

> [!NOTE]
> Veja aqui os [passos](#cocoapods-issues-on-macos-catalina-1015) de resolução de problemas se tiver problemas com o CocoaPod depois de atualizar para macOS Catalina (10.15).

# <a name="swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>Configurar identificador de conta e chave

O próximo passo é configurar a aplicação para utilizar o identificador de conta e a chave de conta. Copiou-os para um editor de texto ao [criar o recurso Spatial Anchors.](#create-a-spatial-anchors-resource)

# <a name="swift"></a>[Swift](#tab/openproject-swift)

Abra `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`.

Localize `spatialAnchorsAccountKey` o `Set me` campo e substitua-o pela chave da conta.

Localize `spatialAnchorsAccountId` o `Set me` campo e substitua-o pelo identificador de conta.

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

Abra `iOS/Objective-C/SampleObjC/BaseViewController.m`.

Localize `SpatialAnchorsAccountKey` o `Set me` campo e substitua-o pela chave da conta.

Localize `SpatialAnchorsAccountId` o `Set me` campo e substitua-o pelo identificador de conta.

---

## <a name="deploy-the-app-to-your-ios-device"></a>Implemente a aplicação para o seu dispositivo iOS

Ligue o dispositivo iOS ao Mac e detete **tede** o esquema ativo para o seu dispositivo iOS.

![Selecione o dispositivo](./media/get-started-ios/select-device.png)

Selecione **Construir e, em seguida, executar o esquema atual**.

![Implementar e executar](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Se vir `library not found for -lPods-SampleObjC` um erro, provavelmente abriu o `.xcodeproj` ficheiro em vez do `.xcworkspace`. Abra `.xcworkspace` o e tente de novo.

No Xcode, pare a aplicação premindo **Stop**.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>Problemas de CocoaPods no macOS Catalina (10.15)

Se atualizou recentemente o macOS Catalina (10.15) e mandou instalar previamente cocoaPods, os CocoaPods podem `.xcworkspace` estar em estado de sabotado e não configurar corretamente as suas cápsulas e ficheiros de projeto. Para resolver este problema, terá de reinstalar os CocoaPods executando os seguintes comandos:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="app-crashes-when-deploying-to-ios-1031-from-a-personal-provisioning-profiledeveloper-account"></a>Falha na aplicação ao ser implantado no iOS 10.3.1 a partir de uma conta pessoal de fornecimento de perfil/desenvolvedor 

Se implementar a sua aplicação iOS no iOS 10.3.1 a partir de `Library not loaded: @rpath/ADAL...`uma conta pessoal de perfil/desenvolvedor, poderá ver este erro: . 

Para resolver a questão:

- Utilize um perfil de provisionamento que não seja um perfil de Equipa Pessoal (conta de desenvolvedor pago).
- Implemente a sua aplicação para um dispositivo iOS que execute o iOS 13.3 ou mais cedo, ou para um que execute a versão beta ou versão de lançamento do iOS 13.4.
- Leia mais sobre este assunto no [Stack Overflow](https://stackoverflow.com/questions/60015309/running-ios-apps-causes-runtime-error-for-frameworks-code-signature-invalid).


[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Partilhar âncoras espaciais em dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
