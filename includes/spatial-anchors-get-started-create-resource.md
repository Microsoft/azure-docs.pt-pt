---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: b2b3ca886359a0b4c906b89ed76f57486fc2c368
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83639006"
---
## <a name="create-a-spatial-anchors-resource"></a>Criar um recurso de âncoras espaciais

Aceda ao <a href="https://portal.azure.com" target="_blank">portal do Azure</a>.

No painel de navegação à esquerda no portal Azure, selecione **Criar um recurso**.

Utilize a caixa de pesquisa para procurar **âncoras espaciais.**

   ![Pesquisa de Âncoras Espaciais](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Selecione **Âncoras Espaciais**. Na caixa de diálogo, selecione **Criar**.

Na caixa de diálogo da **conta de âncoras espaciais:**

- Introduza um nome de recurso único, utilizando caracteres alfanuméricos regulares.
- Selecione a subscrição a que pretende anexar o recurso.
- Criar um grupo de recursos selecionando **Criar novo**. Nomeie-o **myResourceGroup** e selecione **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Selecione uma localização (região) na qual coloque o recurso.
- Selecione **Novo** para começar a criar o recurso.

   ![Criar um recurso](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Após a criação do recurso, o Portal Azure mostrará que a sua implementação está completa. Clique em **Ir para recurso**.

![Implementação concluída](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Em seguida, pode ver as propriedades dos recursos. Copie o valor de **ID** da conta do recurso num editor de texto porque vai precisar dele mais tarde.

   ![Propriedades de recursos](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Também copie o valor do Domínio da **Conta** do recurso num editor de texto, pois vai precisar dele mais tarde.

   ![Domínio da conta](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

Em **Definições**, selecione **Tecla**. Copie o valor **da chave primária** num editor de texto. Este valor é o `Account Key` . Precisará dela mais tarde.

   ![Chave da conta](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
