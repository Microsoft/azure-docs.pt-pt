---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 5f1e0153b1f919bc9d7e921d2a1b3ae745b2b01f
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752155"
---
## <a name="open-the-sample-project-in-unity"></a>Abra o projeto de exemplo no Unity

[!INCLUDE [Clone Sample Repo](spatial-anchors-clone-sample-repository.md)]

## <a name="to-set-up-for-an-android-device"></a>Para configurar a para um dispositivo Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

## <a name="to-set-up-for-an-ios-device"></a>Para configurar a para um dispositivo iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Configurar o identificador de conta e chave

Na **projeto** painel, navegue até à `Assets/AzureSpatialAnchorsPlugin/Examples` e abra o `AzureSpatialAnchorsLocalSharedDemo.unity` ficheiro de cena.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Além disso, na **Inspetor** painel, introduza o `Sharing Anchors Service url` (da aplicação de web do ASP.NET a implementação do Azure) como o valor de `Base Sharing Url`, substituindo `index.html` com `api/anchors`. Por isso, deve ser semelhante: `https://<app_name>.azurewebsites.net/api/anchors`.

Salve a cena selecionando **arquivo** -> **guardar**.

## <a name="to-deploy-to-an-android-device"></a>Para implementar num dispositivo Android

Ligar o dispositivo Android, inicie sessão e ligá-la no PC com um cabo USB.

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
