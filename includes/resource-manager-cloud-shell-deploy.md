---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/25/2018
ms.author: tomfitz
ms.openlocfilehash: a2ee8705be3f34b6df113c68d88e375411f84bf2
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52440366"
---
## <a name="deploy-template-from-cloud-shell"></a>Implementar o modelo a partir do Cloud Shell

Pode utilizar o [Cloud Shell](../articles/cloud-shell/overview.md) para implementar o modelo. No entanto, primeiro tem de carregar o modelo para a conta de armazenamento para o Cloud Shell. Se não utilizou o Cloud Shell, consulte a [Descrição Geral do Azure Cloud Shell](../articles/cloud-shell/overview.md) para obter informações sobre como o configurar.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione o grupo de recursos do Cloud Shell. O padrão de nome é `cloud-shell-storage-<region>`.

   ![Selecionar grupo de recursos](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. Selecione a conta de armazenamento para o Cloud Shell.

   ![Selecionar a conta de armazenamento](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Selecione **Blobs**.

   ![Selecionar blobs](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. Selecionar **+ Contentor**.

   ![Adicionar contentor](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. Dê o seu contentor um nome e um nível de acesso. O modelo de exemplo neste artigo contém não existem informações confidenciais, assim, permita o acesso de leitura anónimo. Selecione **OK**.

   ![Forneça os valores de contentor](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. Selecione o contentor que criou.

   ![Selecionar novo contentor](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. Selecione **Upload**.

   ![Carregar blob](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. Localize e carregue o modelo.

   ![Carregar o ficheiro](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. Depois de ter carregado, selecione o modelo.

   ![Selecione o novo modelo](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Copie o URL.

   ![Copiar URL](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Abra o prompt.

   ![Abrir o Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
