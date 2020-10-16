---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 8/27/2020
ms.author: rgarcia
ms.openlocfilehash: 50e4799f09322eab05b4f8ddf7004c2e0078fdab
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971494"
---
## <a name="android"></a>[Android](#tab/Android)

A amostra java Android suporta a partilha através de dispositivos.

No Android Studio, abra o ficheiro *SharedActivity.java* a partir da pasta de amostras. 

Introduza o URL que copiou no passo anterior (a partir da sua aplicação web ASP.NET Azure) como o valor para `SharingAnchorsServiceUrl` o ficheiro *SharedActivity.java.* 

Substitua `index.html` o URL no URL por `api/anchors` . Devia ser assim: `https://<app_name>.azurewebsites.net/api/anchors` .

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

A amostra objective-C iOS suporta a partilha entre dispositivos.

Abra o ficheiro *SharedDemoViewController.m* na pasta de amostras. 

Introduza o URL obtido no passo anterior (a partir da sua aplicação web ASP.NET Azure) como o valor para `SharingAnchorsServiceUrl` o ficheiro *SharedDemoViewController.m.* 

Substitua `index.html` o URL no URL por `api/anchors` . Devia ser assim: `https://<app_name>.azurewebsites.net/api/anchors` .

Implemente a aplicação para o seu dispositivo. 

Depois de a aplicação começar, selecione a **ação Tap para iniciar a** opção Demo Partilhada e, em seguida, siga as instruções na aplicação. Pode **selecionar Toque para localizar Anchor pelo seu número de âncora** ou toque para criar âncora e **guardá-lo para o serviço**.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Tanto as amostras de Xamarin Android como iOS suportam a partilha em todos os dispositivos.

Abra o ficheiro *AccountDetails.cs* na pasta das amostras. 

Introduza o URL obtido no passo anterior (a partir da sua aplicação web ASP.NET Azure) como o valor para `AnchorSharingServiceUrl` o *ficheiro AccountDetails.cs.* 

Substitua `index.html` o URL no URL por `api/anchors` . Devia ser assim: `https://<app_name>.azurewebsites.net/api/anchors` .

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Configurar um dispositivo Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Configurar um dispositivo iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

No painel do **Projeto,** vá a `Assets\AzureSpatialAnchors.Examples\Resources` . 

Selecione **SpatialAnchorSamplesConfig**. Em seguida, no painel de **inspetor,** insira o `Sharing Anchors Service` URL (a partir da sua aplicação web ASP.NET Azure) como o valor para `Base Sharing Url` . Substitua `index.html` por `api/anchors`. Devia ser assim: `https://<app_name>.azurewebsites.net/api/anchors` .

Guarde a cena selecionando **o Ficheiro**  >  **Save**.

## <a name="deploy-to-your-device"></a>Implementar no seu novo dispositivo

### <a name="deploy-to-an-android-device"></a>Implementar para um dispositivo Android

Inscreva-se no seu dispositivo Android e conecte-o ao seu computador utilizando um cabo USB.

Abrir **definições de construção** selecionando **File**  >  **Definições de construção de ficheiros**.

Under **Scenes In Build**, certifique-se de que cada cena tem uma marca de verificação ao lado.

Certifique-se de que o **Projeto exportação** não tem uma marca de verificação. Selecione **Build and Run**. Serão solicitados para guardar o seu ficheiro *.apk.* Pode escolher qualquer nome.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Implementar para um dispositivo iOS

Abrir **definições de construção** selecionando **File**  >  **Definições de construção de ficheiros**.

Under **Scenes In Build**, certifique-se de que cada cena tem uma marca de verificação ao lado.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

No Xcode, pare a aplicação selecionando **Stop**.
