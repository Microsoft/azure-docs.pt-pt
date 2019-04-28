---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 397a8a9b07b4d7a88d0345399ac4abcc3e738a82
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60681389"
---
## <a name="set-up-your-device"></a>Configurar o dispositivo

No Unity, abra o projeto no `Unity` pasta.

![Janela do Unity](./media/spatial-anchors-unity/unity-window.png)

### <a name="set-up-an-android-device"></a>Configurar um dispositivo Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Configurar um dispositivo iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Configurar o identificador de conta e a chave

Na **projeto** painel, navegue até à `Assets/AzureSpatialAnchorsPlugin/Examples` e abra o `AzureSpatialAnchorsLocalSharedDemo.unity` ficheiro de cena.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

Na **Inspetor** painel, introduza o `Sharing Anchors Service url` (da aplicação de web do ASP.NET a implementação do Azure) como o valor de `Base Sharing Url`, substituindo `index.html` com `api/anchors`. Deve ser semelhante isto: `https://<app_name>.azurewebsites.net/api/anchors`.

Salve a cena selecionando **arquivo** > **guardar**.

## <a name="deploy-to-your-device"></a>Implementar no seu novo dispositivo

### <a name="deploy-to-android-device"></a>Implementar em dispositivos Android

Inicie sessão no seu dispositivo Android e ligá-la para o seu computador com um cabo USB.

Open **definições de criação** ao selecionar **ficheiro** > **definições de criação**.

Sob **plano na compilação**, coloque uma marca de verificação junto ao `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` cena e desmarque a verificação de marca de todos os outros nos bastidores.

Certifique-se **projeto exportar** não tem uma marca de verificação. Selecione **compilar e executar**. Será solicitado a salvar sua `.apk` ficheiro. Pode escolher qualquer nome para o mesmo.

Siga as instruções na aplicação. Pode selecionar **Criar & partilha de âncora** ou **localizar âncora partilhado**. O primeiro cenário permite-lhe criar uma âncora que pode ser localizada mais tarde no mesmo dispositivo ou na outra. O segundo cenário, se já tiver executado a aplicação, no mesmo dispositivo ou em outra, permite-lhe localizar âncoras partilhadas anteriormente. Depois de escolher o seu cenário, a aplicação irá guiá-lo com mais instruções sobre o que fazer para. Por exemplo, deverá mover em torno do seu dispositivo para recolher informações do ambiente. Mais tarde no, irá colocar uma âncora do mundo, irá aguardar enquanto carrega e assim por diante.

### <a name="deploy-to-an-ios-device"></a>Implementar num dispositivo iOS

Open **definições de criação** ao selecionar **ficheiro** > **definições de criação**.

Sob **plano na compilação**, coloque uma marca de verificação junto ao `AzureSpatialAnchorsPlugin/Examples/AzureSpatialAnchorsLocalSharedDemo` cena e desmarque a verificação de marca de todos os outros nos bastidores.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Siga as instruções na aplicação. Pode selecionar **Criar & partilha de âncora** ou **localizar âncora partilhado**. O primeiro cenário permite-lhe criar uma âncora que pode ser localizada mais tarde no mesmo dispositivo ou na outra. O segundo cenário, se já tiver executado a aplicação, no mesmo dispositivo ou em outra, permite-lhe localizar âncoras partilhadas anteriormente. Depois de escolher o seu cenário, a aplicação irá guiá-lo com mais instruções sobre o que fazer para. Por exemplo, deverá mover em torno do seu dispositivo para recolher informações do ambiente. Mais tarde no, irá colocar uma âncora do mundo, irá aguardar enquanto carrega e assim por diante.

No Xcode, pare a aplicação, selecionando **parar**.
