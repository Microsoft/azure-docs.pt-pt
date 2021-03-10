---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 03/02/2021
ms.author: crtreasu
ms.openlocfilehash: d8dfc3d4b7a8447250481b98c1adadc865a29da1
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102532766"
---
### <a name="upload-your-model"></a>Faça upload do seu modelo

Se ainda não tiver um modelo de Âncoras de Objetos, siga as instruções da [Create a model](/azure/object-anchors/quickstarts/get-started-model-conversion) to create one. Então, volte aqui.

Com os hololens ligados ao Portal do Dispositivo Windows, siga estes passos para carregar um modelo para a aplicação utilizar:

1. No Portal do Dispositivo Windows, aceda ao **explorador de ficheiros System > > LocalAppData**. Aí, você deve ver a sua aplicação na lista de aplicações.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localappdata.png" alt-text="explorador de arquivos":::

2. Abra a sua aplicação e clique na `LocalState` pasta.

    :::image type="content" source="./media/object-anchors-quickstarts-unity/portal-localstate.png" alt-text="Abra a pasta Estado Local":::

3. Faça o upload do ficheiro do modelo para a `LocalState` pasta.

    :::image type="content" source="./media/object-anchors-quickstarts/portal-upload-model.png" alt-text="carregar o modelo no portal":::

    Lançar a aplicação dos HoloLens novamente. Agora é possível detetar objetos correspondentes ao modelo.
