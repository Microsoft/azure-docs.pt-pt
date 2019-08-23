---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 1007533df077c58d9e4d57f9e86b035730ea917f
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69903992"
---
## <a name="set-up-your-device-in-unity"></a>Configurar seu dispositivo no Unity

[!INCLUDE [Open Unity Project](spatial-anchors-open-unity-project.md)]

### <a name="set-up-an-android-device"></a>Configurar um dispositivo Android

[!INCLUDE [Android Unity Build Settings](spatial-anchors-unity-android-build-settings.md)]

### <a name="set-up-an-ios-device"></a>Configurar um dispositivo iOS

[!INCLUDE [iOS Unity Build Settings](spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-the-account-identifier-and-key"></a>Configurar o identificador e a chave da conta

No painel **projeto** , navegue até `Assets/AzureSpatialAnchorsPlugin/Examples` e abra o arquivo `AzureSpatialAnchorsLocalSharedDemo.unity` de cena.

[!INCLUDE [Configure Unity Scene](spatial-anchors-unity-configure-scene.md)]

No painel **projeto** , navegue até `Assets\AzureSpatialAnchors.Examples\Resources`. Selecione `SpatialAnchorSamplesConfig`. Em seguida, no painel **Inspetor** , insira o `Sharing Anchors Service url` (da sua implantação do aplicativo Web do ASP.net do Azure) como o valor `index.html` para `api/anchors` `Base Sharing Url`, substituindo por. Ele deve ser assim: `https://<app_name>.azurewebsites.net/api/anchors`.

Salve a cena selecionando **arquivo** > **salvar**.

## <a name="deploy-to-your-device"></a>Implementar no seu novo dispositivo

### <a name="deploy-to-android-device"></a>Implantar no dispositivo Android

Entre no seu dispositivo Android e conecte-o ao seu computador usando um cabo USB.

Abra **configurações de compilação** selecionando **arquivo** > **configurações de compilação**.

Em **cenas em compilação**, verifique se todas as cenas têm uma marca de seleção ao lado delas.

Certifique-se de que **Exportar projeto** não tenha uma marca de seleção. Selecione **Compilar e executar**. Você será solicitado a salvar o `.apk` arquivo. Você pode escolher qualquer nome para ele.

Quando o aplicativo for iniciado, na caixa de diálogo **escolher uma demonstração** , use as setas para a esquerda ou para a direita para selecionar a opção **LocalShare** e toque em **go!** . Siga as instruções no aplicativo. Você pode selecionar **criar & âncora de compartilhamento** ou **Localizar âncora compartilhada**.

O primeiro cenário permite criar uma âncora que pode ser localizada posteriormente no mesmo dispositivo ou em uma diferente.
O segundo cenário, se você já tiver executado o aplicativo, no mesmo dispositivo ou em outro, o permitirá localizar âncoras compartilhadas anteriormente. Depois de escolher seu cenário, o aplicativo guiará você com mais instruções sobre o que fazer. Por exemplo, você será solicitado a mover seu dispositivo para coletar informações de ambiente. Posteriormente, você colocará uma âncora no mundo, aguardará que ele seja salvo e assim por diante.

### <a name="deploy-to-an-ios-device"></a>Implantar em um dispositivo iOS

Abra **configurações de compilação** selecionando **arquivo** > **configurações de compilação**.

Em **cenas em compilação**, verifique se todas as cenas têm uma marca de seleção ao lado delas.

[!INCLUDE [Configure Xcode](spatial-anchors-unity-ios-xcode.md)]

Quando o aplicativo for iniciado, na caixa de diálogo **escolher uma demonstração** , use as setas para a esquerda ou para a direita para selecionar a opção **LocalShare** e toque em **go!** . Siga as instruções no aplicativo. Você pode selecionar **criar & âncora de compartilhamento** ou **Localizar âncora compartilhada**.

O primeiro cenário permite criar uma âncora que pode ser localizada posteriormente no mesmo dispositivo ou em uma diferente.
O segundo cenário, se você já tiver executado o aplicativo, no mesmo dispositivo ou em outro, o permitirá localizar âncoras compartilhadas anteriormente. Depois de escolher seu cenário, o aplicativo guiará você com mais instruções sobre o que fazer. Por exemplo, você será solicitado a mover seu dispositivo para coletar informações de ambiente. Posteriormente, você colocará uma âncora no mundo, aguardará que ele seja salvo e assim por diante.

No Xcode, interrompa o aplicativo selecionando **parar**.
