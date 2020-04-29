---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: ec8fb6efab126dcf5556a9abfdf58d1fd69d4212
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "72882326"
---
## <a name="android"></a>[Android](#tab/Android)

A amostra java android suporta a partilha entre dispositivos.
Abra o `SharedActivity.java` ficheiro da pasta de amostras no Android Studio. Introduza o url obtido no passo anterior (a partir da sua `SharingAnchorsServiceUrl` ASP.NET `SharedActivity.java` implementação da aplicação web Azure) como o valor para o ficheiro. Substitua `index.html` a url `api/anchors`por . Deve ser assim: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

A amostra objectiva-C iOS suporta a partilha entre dispositivos.
Abra o `SharedDemoViewController.m` ficheiro na pasta de amostras. Introduza o url obtido no passo anterior (a partir da sua `SharingAnchorsServiceUrl` ASP.NET `SharedActivity.java` implementação da aplicação web Azure) como o valor para o ficheiro. Substitua `index.html` a url `api/anchors`por . Deve ser assim: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Tanto as amostras do Xamarin Android como do iOS suportam a partilha entre dispositivos.
Abra o `AccountDetails.cs` ficheiro na pasta de amostras. Introduza o url obtido no passo anterior (a partir da sua `AnchorSharingServiceUrl` ASP.NET `SharedActivity.java` implementação da aplicação web Azure) como o valor para o ficheiro. Substitua `index.html` a url `api/anchors`por . Deve ser assim: `https://<app_name>.azurewebsites.net/api/anchors`.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unidade](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Configurar um dispositivo Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Configurar um dispositivo iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Configure o identificador de conta e a chave

No painel do **Projeto,** navegue e `Assets/AzureSpatialAnchorsPlugin/Examples` abra o arquivo de `AzureSpatialAnchorsLocalSharedDemo.unity` cena.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

No painel do **Projeto,** navegue para `Assets\AzureSpatialAnchors.Examples\Resources`. Selecione `SpatialAnchorSamplesConfig`. Em seguida, no painel **do Inspetor,** insira `Sharing Anchors Service url` a (a partir `Base Sharing Url`da `index.html` sua `api/anchors`ASP.NET aplicação web implantação Azure) como o valor para, substituindo por . Deve ser assim: `https://<app_name>.azurewebsites.net/api/anchors`.

Guarde a cena selecionando **File** > **Save**.

## <a name="deploy-to-your-device"></a>Implementar no seu novo dispositivo

### <a name="deploy-to-android-device"></a>Implemente para dispositivo Android

Inscreva-se no seu dispositivo Android e conecte-o ao seu computador utilizando um cabo USB.

Abra **as definições** de construção selecionando definições de**construção**de **ficheiros** > .

Em **Scenes In Build,** certifique-se de que todas as cenas têm uma marca de verificação ao lado delas.

Certifique-se de que **o Projeto de Exportação** não tem uma marca de verificação. Selecione **construir e executar**. Ser-lhe-á pedido que `.apk` guarde o seu ficheiro. Pode escolher qualquer nome.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Implementar para um dispositivo iOS

Abra **as definições** de construção selecionando definições de**construção**de **ficheiros** > .

Em **Scenes In Build,** certifique-se de que todas as cenas têm uma marca de verificação ao lado delas.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

No Xcode, pare a aplicação selecionando **Stop**.
