---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 8/27/2020
ms.author: rgarcia
ms.openlocfilehash: efd5ff494620d4fab3fb904d9bcf054b57a3290b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91358822"
---
## <a name="android"></a>[Android](#tab/Android)

A amostra de Android Java suporta a partilha através de dispositivos.
Abra o ficheiro `SharedActivity.java` a partir da pasta de amostras no Android Studio. Introduza o url que obteve no passo anterior (a partir da sua aplicação web ASP.NET Azure) como o valor para `SharingAnchorsServiceUrl` o `SharedActivity.java` ficheiro. Substitua `index.html` o url com `api/anchors` . Devia ser assim: `https://<app_name>.azurewebsites.net/api/anchors` .

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="ios"></a>[iOS](#tab/iOS)

A amostra objective-C iOS suporta a partilha entre dispositivos.
Abra o ficheiro `SharedDemoViewController.m` na pasta das amostras. Introduza o url que obteve no passo anterior (a partir da sua aplicação web ASP.NET Azure) como o valor para `SharingAnchorsServiceUrl` o `SharedDemoViewController.m` ficheiro. Substitua `index.html` o url com `api/anchors` . Devia ser assim: `https://<app_name>.azurewebsites.net/api/anchors` .

Implemente a aplicação para o seu dispositivo. Assim que a aplicação começar, escolha a ação Tap para iniciar a opção **Demo Partilhada.** Siga as instruções na aplicação. Pode **selecionar Toque para localizar Anchor pelo seu número de âncora** ou toque para criar âncora e **guardá-lo para o serviço**.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="xamarin"></a>[Xamarin](#tab/Xamarin)

Tanto as amostras de Xamarin Android como iOS suportam a partilha em todos os dispositivos.
Abra o ficheiro `AccountDetails.cs` na pasta das amostras. Introduza o url que obteve no passo anterior (a partir da sua aplicação web ASP.NET Azure) como o valor para `AnchorSharingServiceUrl` o `AccountDetails.cs` ficheiro. Substitua `index.html` o url com `api/anchors` . Devia ser assim: `https://<app_name>.azurewebsites.net/api/anchors` .

[!INCLUDE [Run shared sample](spatial-anchors-deploy-sample.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

## <a name="unity"></a>[Unity](#tab/Unity)

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Configurar um Dispositivo Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Configurar um dispositivo iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

No painel do **Projeto,** navegue `Assets\AzureSpatialAnchors.Examples\Resources` para. Selecione `SpatialAnchorSamplesConfig`. Em seguida, no painel de **inspetor,** insira a `Sharing Anchors Service url` (da sua ASP.NET aplicação web Azure implantação) como o valor `Base Sharing Url` para, substituindo `index.html` por `api/anchors` . Devia ser assim: `https://<app_name>.azurewebsites.net/api/anchors` .

Guarde a cena selecionando **o Ficheiro**  >  **Save**.

## <a name="deploy-to-your-device"></a>Implementar no seu novo dispositivo

### <a name="deploy-to-android-device"></a>Implementar para dispositivo Android

Inscreva-se no seu dispositivo Android e conecte-o ao seu computador utilizando um cabo USB.

Abrir **definições de construção** selecionando **File**  >  **Definições de construção de ficheiros**.

Under **Scenes In Build**, certifique-se de que todas as cenas têm uma marca de verificação ao seu lado.

Certifique-se de que **o Projeto exportação** não tem uma marca de verificação. Selecione **Build and Run**. Serão solicitados a guardar o seu `.apk` ficheiro. Pode escolher qualquer nome.

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

### <a name="deploy-to-an-ios-device"></a>Implementar para um dispositivo iOS

Abrir **definições de construção** selecionando **File**  >  **Definições de construção de ficheiros**.

Under **Scenes In Build**, certifique-se de que todas as cenas têm uma marca de verificação ao seu lado.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

[!INCLUDE [Run shared sample](spatial-anchors-run-sample.md)]

No Xcode, pare a aplicação selecionando **Stop**.
