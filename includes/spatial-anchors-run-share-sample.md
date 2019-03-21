---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: bfeb8bddf5fe3b4a76e662aed6c5a07439d2f1cf
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57908635"
---
## <a name="set-up-your-device"></a>Configurar o dispositivo

### <a name="set-up-an-android-device"></a>Configurar um dispositivo Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Configurar um dispositivo iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Configurar o identificador de conta e chave

Na **projeto** painel, navegue até à `Assets/AzureSpatialAnchorsPlugin/Examples` e abra o `AzureSpatialAnchorsLocalSharedDemo.unity` ficheiro de cena.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Além disso, na **Inspetor** painel, introduza o `Sharing Anchors Service url` (da aplicação de web do ASP.NET a implementação do Azure) como o valor de `Base Sharing Url`, substituindo `index.html` com `api/anchors`. Por isso, deve ser semelhante: `https://<app_name>.azurewebsites.net/api/anchors`.

Salve a cena selecionando **arquivo** -> **guardar**.

## <a name="to-deploy-to-an-android-device"></a>Para implementar num dispositivo Android

Inicie sessão no seu dispositivo Android e ligá-la no PC com um cabo USB.

Open **definições de criação** ao selecionar **ficheiro** -> **definições de criação**.

Sob **plano na compilação**, coloque uma marca de verificação junto aos `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` marcas de todos os outros nos bastidores de seleção de cena e desmarque.

Certifique-se a **projeto exportar** caixa de verificação não tem uma marca de verificação. Clique em **compilar e executar**. Será solicitado a salvar sua `.apk` ficheiro, pode escolher qualquer nome para o mesmo.

Siga as instruções na aplicação. Pode optar por **Criar & partilha de âncora**, ou **localizar âncora partilhado**. A primeira opção permite-lhe criar uma âncora que pode ser posteriormente localizada no mesmo dispositivo ou num diferente. A segunda opção, se anteriormente tiver executado a aplicação, no mesmo dispositivo ou um diferente, permite-lhe localizar âncoras partilhadas anteriormente.

## <a name="to-deploy-to-an-ios-device"></a>Para implementar num dispositivo iOS

Open **definições de criação** ao selecionar **ficheiro** -> **definições de criação**.

Sob **plano na compilação**, coloque uma marca de verificação junto aos `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` marcas de todos os outros nos bastidores de seleção de cena e desmarque.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Siga as instruções na aplicação. Pode optar por **Criar & partilha de âncora**, ou **localizar âncora partilhado**. A primeira opção permite-lhe criar uma âncora que pode ser posteriormente localizada no mesmo dispositivo ou num diferente. A segunda opção, se anteriormente tiver executado a aplicação, no mesmo dispositivo ou um diferente, permite-lhe localizar âncoras partilhadas anteriormente.

No Xcode, pare a aplicação ao premir **parar**.
