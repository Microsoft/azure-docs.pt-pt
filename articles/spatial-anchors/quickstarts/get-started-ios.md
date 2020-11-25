---
title: 'Quickstart: Criar uma aplicação para iOS'
description: Aprenda a criar uma aplicação iOS utilizando âncoras espaciais Azure programáticamente em Swift ou Objective-C.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.custom: has-adal-ref
ms.openlocfilehash: 4434118313989eb8bc70f1d44e977b2df3633050
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2020
ms.locfileid: "96009080"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Quickstart: Criar uma aplicação iOS com âncoras espaciais Azure, em Swift ou Objective-C

Este quickstart abrange como criar uma aplicação iOS usando [âncoras espaciais Azure](../overview.md) em Swift ou Objective-C. Azure Spatial Anchors é um serviço de desenvolvedor de plataformas cruzadas que permite criar experiências de realidade mista usando objetos que persistem a sua localização através dos dispositivos ao longo do tempo. Quando terminar, terá uma aplicação ARKit iOS que pode guardar e recordar uma âncora espacial.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de Âncoras Espaciais
> * Configure o identificador de conta Spatial Anchors e a chave de conta
> * Implementar e executar num dispositivo iOS

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:

- Um desenvolvedor permitiu que a máquina macOS com a versão mais recente de <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a> e <a href="https://cocoapods.org" target="_blank">CocoaPods instalada.</a>
- Git instalado via HomeBrew:
  1. Introduza o seguinte comando como uma única linha no terminal: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` . 
  1. Execute `brew install git` e `brew install git-lfs`.
  1. Atualize o seu git config com `git lfs install` (para o utilizador atual) ou `git lfs install --system` (para todo o sistema).
- Um desenvolvedor permitiu que o dispositivo iOS <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">compatível com ARKit.</a>

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Abra o projeto de amostra

Utilize o Terminal para executar as seguintes ações.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Instale as cápsulas necessárias utilizando cacau:

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

Corra `pod install --repo-update` para instalar os Cacau para o projeto.

Agora abra o `.xcworkspace` código de acesso.

> [!NOTE]
> Consulte [aqui](#cocoapods-issues-on-macos-catalina-1015) os passos de resolução de problemas se tiver problemas com o Cacau depois de atualizar para o macOS Catalina (10.15).

# <a name="swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>Configure identificador de conta e chave

O próximo passo é configurar a app para usar o identificador de conta e a chave da conta. Copiou-os para um editor de texto ao [configurar o recurso Spatial Anchors](#create-a-spatial-anchors-resource).

# <a name="swift"></a>[Swift](#tab/openproject-swift)

Abra `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`.

Localize o `spatialAnchorsAccountKey` campo e `Set me` substitua-o pela chave de conta.

Localize o `spatialAnchorsAccountId` campo e `Set me` substitua-o pelo identificador de conta.

Localize o `spatialAnchorsAccountDomain` campo e `Set me` substitua-o pelo domínio da conta.

# <a name="objective-c"></a>[Objective-C](#tab/openproject-objc)

Abra `iOS/Objective-C/SampleObjC/BaseViewController.m`.

Localize o `SpatialAnchorsAccountKey` campo e `Set me` substitua-o pela chave de conta.

Localize o `SpatialAnchorsAccountId` campo e `Set me` substitua-o pelo identificador de conta.

Localize o `SpatialAnchorsAccountDomain` campo e `Set me` substitua-o pelo domínio da conta.

---

## <a name="deploy-the-app-to-your-ios-device"></a>Implemente a aplicação para o seu dispositivo iOS

Ligue o dispositivo iOS ao Mac e desaponda o **esquema ativo** ao seu dispositivo iOS.

![Selecione o dispositivo](./media/get-started-ios/select-device.png)

Selecione **Build e, em seguida, executar o esquema atual**.

![Implementar e executar](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Se vir um `library not found for -lPods-SampleObjC` erro, é provável que tenha aberto o `.xcodeproj` ficheiro em vez do `.xcworkspace` . Abra o `.xcworkspace` e tente de novo.

No Xcode, pare a aplicação premindo **Stop**.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>Problemas de cacau no macOS Catalina (10.15)

Se recentemente atualizado para o macOS Catalina (10.15) e tiver os Cacau instalados previamente, os CocoaPods podem estar em estado de avaria e não configurar adequadamente os seus pods e `.xcworkspace` ficheiros de projeto. Para resolver este problema, terá de reinstalar cacau executando os seguintes comandos:

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

### <a name="app-crashes-when-deploying-to-ios-1031-from-a-personal-provisioning-profiledeveloper-account"></a>A aplicação falha ao ser implantada no iOS 10.3.1 a partir de um perfil de provisionamento pessoal/conta de programador 

Se implementar a sua aplicação iOS no iOS 10.3.1 a partir de uma conta pessoal de provisão/programador, poderá ver este erro: `Library not loaded: @rpath/ADAL...` . 

Para resolver o problema:

- Utilize um perfil de provisionamento que não seja um perfil de Equipa Pessoal (conta de programador paga).
- Implemente a sua aplicação para um dispositivo iOS que executa o iOS 13.3 ou mais cedo, ou para um que executa a versão beta ou versão de lançamento do iOS 13.4.
- Leia mais sobre este assunto no [Stack Overflow](https://stackoverflow.com/questions/60015309/running-ios-apps-causes-runtime-error-for-frameworks-code-signature-invalid).


[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Tutorial: Partilhar Âncoras Espaciais através de dispositivos](../tutorials/tutorial-share-anchors-across-devices.md)
