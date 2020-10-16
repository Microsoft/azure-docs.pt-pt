---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: aac2f3ea2b52ac0319f96279deed13c1145749bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74451506"
---
## <a name="deploy-template-from-cloud-shell"></a>Implementar o modelo a partir do Cloud Shell

Pode utilizar o [Cloud Shell](../articles/cloud-shell/overview.md) para implementar o modelo. Para implementar um modelo externo, forneça o URI do modelo exatamente como faria para qualquer implementação externa. Para implementar um modelo local, você deve primeiro carregar o seu modelo na conta de armazenamento para a sua Cloud Shell. Esta secção descreve como carregar o modelo na sua conta de concha de nuvem e implantá-lo como um ficheiro local. Se não usou a Cloud Shell, consulte [a visão geral da Azure Cloud Shell](../articles/cloud-shell/overview.md) para obter informações sobre a sua configuração.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione o grupo de recursos do Cloud Shell. O padrão de nome é `cloud-shell-storage-<region>`.

   ![Selecionar o grupo de recursos](./media/resource-manager-cloud-shell-deploy/select-cloud-shell-resource-group.png)

1. Selecione a conta de armazenamento para o Cloud Shell.

   ![Selecionar a conta de armazenamento](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Selecione **Blobs**.

   ![Selecionar blobs](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. Selecionar **+ Contentor**.

   ![Adicionar contentor](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. Dê ao seu recipiente um nome e um nível de acesso. O modelo de amostra deste artigo não contém nenhuma informação sensível, por isso permita o acesso de leitura anónima. Selecione **OK**.

   ![Fornecer valores de contentores](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. Selecione o contentor que criou.

   ![Selecione novo recipiente](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. Selecione **Carregar**.

   ![Bolha de upload](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. Localize e carregue o modelo.

   ![Carregar o ficheiro](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. Depois de ter sido carregado, selecione o modelo.

   ![Selecione novo modelo](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Copie o URL.

   ![Copiar URL](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Abra o prompt.

   ![Abrir o Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
