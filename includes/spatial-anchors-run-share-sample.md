---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 563c2bd561328561d30acee6910b70d53ef64c6b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305386"
---
## <a name="set-up-your-device"></a>Configurar o dispositivo

### <a name="set-up-an-android-device"></a>Configurar um dispositivo Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Configurar um dispositivo iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Configurar o identificador de conta e a chave

Na **projeto** painel, navegue até à `Assets/AzureSpatialAnchorsPlugin/Examples` e abra o `AzureSpatialAnchorsLocalSharedDemo.unity` ficheiro de cena.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Na **Inspetor** painel, introduza o `Sharing Anchors Service url` (da aplicação de web do ASP.NET a implementação do Azure) como o valor de `Base Sharing Url`, substituindo `index.html` com `api/anchors`. Deve ser semelhante isto: `https://<app_name>.azurewebsites.net/api/anchors`.

Salve a cena selecionando **arquivo** > **guardar**.

## <a name="to-deploy-the-app-to-an-android-device"></a>Para implementar a aplicação num dispositivo Android

Inicie sessão no seu dispositivo Android e ligá-la para o seu computador com um cabo USB.

Open **definições de criação** ao selecionar **ficheiro** > **definições de criação**.

Sob **plano na compilação**, coloque uma marca de verificação junto ao `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` cena e desmarque a verificação de marca de todos os outros nos bastidores.

Certifique-se **projeto exportar** não tem uma marca de verificação. Selecione **compilar e executar**. Será solicitado a salvar sua `.apk` ficheiro. Pode escolher qualquer nome para o mesmo.

Siga as instruções na aplicação. Pode selecionar **Criar & partilha de âncora** ou **localizar âncora partilhado**. A primeira opção permite-lhe criar uma âncora que pode ser localizada mais tarde no mesmo dispositivo ou na outra. A segunda opção, se já tiver executado a aplicação, no mesmo dispositivo ou em outra, permite-lhe localizar âncoras partilhadas anteriormente.

## <a name="to-deploy-the-app-to-an-ios-device"></a>Para implementar a aplicação num dispositivo iOS

Open **definições de criação** ao selecionar **ficheiro** > **definições de criação**.

Sob **plano na compilação**, coloque uma marca de verificação junto ao `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` cena e desmarque a verificação de marca de todos os outros nos bastidores.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Siga as instruções na aplicação. Pode selecionar **Criar & partilha de âncora** ou **localizar âncora partilhado**. A primeira opção permite-lhe criar uma âncora que pode ser localizada mais tarde no mesmo dispositivo ou na outra. A segunda opção, se já tiver executado a aplicação, no mesmo dispositivo ou em outra, permite-lhe localizar âncoras partilhadas anteriormente.

No Xcode, pare a aplicação, selecionando **parar**.
