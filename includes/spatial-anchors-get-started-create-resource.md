---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 12/13/2018
ms.author: crtreasu
ms.openlocfilehash: 9e232c5a8f1c2e38c04ea2236691554952f443e8
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752334"
---
## <a name="create-a-spatial-anchors-resource"></a>Criar um recurso de âncoras espaciais

1. Navegue para o <a href="https://portal.azure.com" target="_blank">portal do Azure</a>.

2. No menu à esquerda no portal do Azure, selecione **criar um recurso**.

3. Procure "Âncoras espaciais" na barra de pesquisa.

   ![Procure as âncoras espaciais](./media/spatial-anchors-get-started-create-resource/portal-search.png)

4. Selecione **geográficos âncoras (pré-visualização)** para abrir uma caixa de diálogo e selecione **criar**.

5. Na **geográficos de âncoras de conta** formulário:

   1. Especifique um nome de recurso exclusiva.
   2. Selecione a subscrição para anexar o recurso.
   3. Criar um grupo de recursos, selecionando **criar novo** e dê o nome do grupo de recursos **myResourceGroup** e selecione **OK**.
      [!INCLUDE [resource group intro text](resource-group.md)]
   4. Selecione uma localização (região) onde o recurso será colocado.
   5. Selecione **New** para começar a criar o recurso.

   ![Criar um recurso](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

6. Após a criação de recursos foi concluída com êxito, é possível ver as propriedades de recurso. Anote o recurso **ID da conta** valor como será necessário mais tarde.

   ![Ver as propriedades de recurso](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

7. Sob **definições**, selecione **chave** e anote o **chave primária** valor. Este valor é o `Account Key` e serão utilizados posteriormente.

   ![Chave de conta do Vista](./media/spatial-anchors-get-started-create-resource/view-account-key.png)
