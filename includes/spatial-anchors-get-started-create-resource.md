---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 32f4545a45eda8acddd7c93cc4917dbadca9ad4d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67134989"
---
## <a name="create-a-spatial-anchors-resource"></a>Criar um recurso de Âncoras Espaciais

Vá ao <a href="https://portal.azure.com" target="_blank">portal Azure.</a>

No painel de navegação à esquerda no portal Azure, selecione **Criar um recurso**.

Utilize a caixa de pesquisa para procurar **âncoras espaciais**.

   ![Pesquisa de Âncoras Espaciais](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Selecione **Âncoras Espaciais**. Na caixa de diálogo, selecione **Criar**.

Na caixa de diálogo **da Conta de Âncoras Espaciais:**

- Introduza um nome único de recurso, utilizando caracteres alfanuméricos regulares.
- Selecione a subscrição a que pretende anexar o recurso.
- Criar um grupo de recursos selecionando **criar novos**. Nomeie-o **myResourceGroup** e selecione **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
- Selecione uma localização (região) para colocar o recurso.
- Selecione **Novo** para começar a criar o recurso.

   ![Criar um recurso](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Após a criação do recurso, o Portal Azure mostrará que a sua implementação está completa. Clique em **Ir para recurso**.

![Implementação concluída](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Depois, pode ver as propriedades dos recursos. Copie o valor de ID da **conta** do recurso num editor de texto porque vai precisar dele mais tarde.

   ![Propriedades de recursos](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Em **Definições,** selecione **Tecla**. Copie o **valor-chave primário** num editor de texto. Este valor `Account Key`é o . Precisará dela mais tarde.

   ![Chave de conta](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
