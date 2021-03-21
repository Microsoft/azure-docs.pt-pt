---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 43c8c578587c65b6e0317caf77ff2d0604cb4fa3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102044671"
---
### <a name="build-and-deploy-the-app"></a>Criar e implementar a aplicação

Abra o `.sln` ficheiro gerado pela Unidade. Altere a configuração de construção para o seguinte.

:::image type="content" source="./media/object-anchors-quickstarts-unity/aoa-unity-vs-build-config.png" alt-text="configuração de construção":::

Em seguida, terá de configurar o **endereço IP da Máquina Remota** para implementar e depurar a aplicação.

Clique com o botão direito no projeto app e selecione **Propriedades.** Na página de propriedades, **selecione Configuration Properties -> Debugging**. Altere o valor **do nome** da máquina para o endereço IP do seu dispositivo HoloLens e clique em **Aplicar**.

:::image type="content" source="./media/object-anchors-quickstarts-unity/aoa-unity-vs-remote-debug.png" alt-text="depurar remoto":::

Feche a página da propriedade. Clique **na máquina remota**. A aplicação deve começar a construir e a implantar para o seu dispositivo remoto. Certifique-se de que o seu dispositivo está ativo.
