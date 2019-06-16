---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: b46a2b18309851bbe2934980137a53d2de6f6efc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135368"
---
## <a name="set-up-your-device-in-unity"></a>Configurar o dispositivo no Unity

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

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

Sob **plano criar**, certifique-se de que todos os plano têm uma marca de verificação junto a eles.

Certifique-se **projeto exportar** não tem uma marca de verificação. Selecione **compilar e executar**. Será solicitado a salvar sua `.apk` ficheiro. Pode escolher qualquer nome para o mesmo.

Assim que a aplicação for iniciada, além do **escolha demonstração de um** caixa de diálogo, utilize as setas do esquerda ou direita para selecionar o **LocalShare** opção e toque em **ir!** . Siga as instruções na aplicação. Pode selecionar **Criar & partilha de âncora** ou **localizar âncora partilhado**.

O primeiro cenário permite-lhe criar uma âncora que pode ser localizada mais tarde no mesmo dispositivo ou na outra.
O segundo cenário, se já tiver executado a aplicação, no mesmo dispositivo ou em outra, permite-lhe localizar âncoras partilhadas anteriormente. Depois de escolher o seu cenário, a aplicação irá guiá-lo com mais instruções sobre o que fazer. Por exemplo, será solicitado para mover o seu dispositivo para recolher informações do ambiente. Mais tarde em diante, irá colocar uma âncora do mundo, aguarde a salvar sua e assim por diante.

### <a name="deploy-to-an-ios-device"></a>Implementar num dispositivo iOS

Open **definições de criação** ao selecionar **ficheiro** > **definições de criação**.

Sob **plano criar**, certifique-se de que todos os plano têm uma marca de verificação junto a eles.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Assim que a aplicação for iniciada, além do **escolha demonstração de um** caixa de diálogo, utilize as setas do esquerda ou direita para selecionar o **LocalShare** opção e toque em **ir!** . Siga as instruções na aplicação. Pode selecionar **Criar & partilha de âncora** ou **localizar âncora partilhado**.

O primeiro cenário permite-lhe criar uma âncora que pode ser localizada mais tarde no mesmo dispositivo ou na outra.
O segundo cenário, se já tiver executado a aplicação, no mesmo dispositivo ou em outra, permite-lhe localizar âncoras partilhadas anteriormente. Depois de escolher o seu cenário, a aplicação irá guiá-lo com mais instruções sobre o que fazer. Por exemplo, será solicitado para mover o seu dispositivo para recolher informações do ambiente. Mais tarde em diante, irá colocar uma âncora do mundo, aguarde a salvar sua e assim por diante.

No Xcode, pare a aplicação, selecionando **parar**.
