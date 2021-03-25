---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 03/02/2021
ms.author: crtreasu
ms.openlocfilehash: d06a6ecd8af16da3e6df21e984fbf6a727fbc27e
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104914"
---
### <a name="upload-your-model"></a>Faça upload do seu modelo

Se ainda não tiver um modelo de Âncoras de Objetos, siga as instruções da [Create a model](../articles/object-anchors/quickstarts/get-started-model-conversion.md) to create one. Então, volte aqui.

Com os hololens ligados ao Portal do Dispositivo Windows, siga estes passos para carregar um modelo para a aplicação utilizar:

1. No Portal do Dispositivo Windows, aceda ao **explorador de ficheiros System > > LocalAppData**. Aí, você deve ver a sua aplicação na lista de aplicações.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localappdata.png" alt-text="explorador de arquivos":::

2. Abra a sua aplicação e clique na `LocalState` pasta.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localstate.png" alt-text="Abra a pasta Estado Local":::

3. Faça o upload do ficheiro do modelo para a `LocalState` pasta.

    :::image type="content" source="./media/object-anchors-quickstarts/portal-upload-model.png" alt-text="carregar o modelo no portal":::

    Lançar a aplicação dos HoloLens novamente. Agora é possível detetar objetos correspondentes ao modelo.