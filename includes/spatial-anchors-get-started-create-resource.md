---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: cee446e54a37ce47fc82d72724e831b8270f3769
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57908489"
---
## <a name="create-a-spatial-anchors-resource"></a>Criar um recurso de âncoras espaciais

1. Navegue para o <a href="https://portal.azure.com" target="_blank">portal do Azure</a>.

2. No menu à esquerda no portal do Azure, selecione **criar um recurso**.

3. Procure "Âncoras espaciais" na barra de pesquisa.

   ![Procure as âncoras espaciais](./media/spatial-anchors-get-started-create-resource/portal-search.png)

4. Selecione **geográficos âncoras** para abrir uma caixa de diálogo e selecione **criar**.

5. Na **geográficos de âncoras de conta** formulário:

   1. Especifique um nome de recurso exclusiva.
   2. Selecione a subscrição para anexar o recurso.
   3. Criar um grupo de recursos, selecionando **criar novo** e dê o nome do grupo de recursos **myResourceGroup** e selecione **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
   4. Selecione uma localização (região) onde o recurso será colocado.
   5. Selecione **New** para começar a criar o recurso.

   ![Criar um recurso](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

6. Após a criação de recursos foi concluída com êxito, é possível ver as propriedades de recurso. Copiar do recurso **ID da conta** valor num editor de texto, como será necessário mais tarde.

   ![Ver as propriedades de recurso](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

7. Sob **configurações**, selecione **chave**e copiar para um editor de texto a **chave primária** valor. Este valor é o `Account Key` e serão utilizados posteriormente.

   ![Chave de conta do Vista](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
