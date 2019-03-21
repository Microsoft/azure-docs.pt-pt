---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: dc8d3c2d400204f53b05bb5536af95679541f3f6
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305242"
---
## <a name="create-a-spatial-anchors-resource"></a>Criar um recurso de âncoras espaciais

Aceda ao <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>.

No painel de navegação esquerdo no portal do Azure, selecione **criar um recurso**.

Utilize a caixa de pesquisa para procurar **geográficos âncoras**.

   ![Procure as âncoras espaciais](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Selecione **geográficos âncoras**. Na caixa de diálogo, selecione **criar**.

Na **geográficos de âncoras de conta** caixa de diálogo:

- Introduza um nome de recurso exclusiva.
- Selecione a subscrição que pretende anexar o recurso.
- Criar um grupo de recursos, selecionando **criar novo**. Atribua o nome **myResourceGroup** e selecione **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Selecione uma localização (região) onde colocar o recurso.
- Selecione **New** para começar a criar o recurso.

   ![Criar um recurso](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Depois do recurso é criado, pode ver as propriedades de recurso. Copiar do recurso **ID da conta** valor num editor de texto, porque irá precisar dele mais tarde.

   ![Propriedades de recurso](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Sob **configurações**, selecione **chave**. Copiar o **chave primária** valor num editor de texto. Este valor é o `Account Key`. Precisará dela mais tarde.

   ![Chave de conta](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
