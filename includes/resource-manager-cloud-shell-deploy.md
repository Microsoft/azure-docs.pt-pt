---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 7c081b3bc5f9e6273f680b24897f9aced4999afa
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66129017"
---
## <a name="deploy-template-from-cloud-shell"></a>Implementar o modelo a partir do Cloud Shell

Pode utilizar o [Cloud Shell](../articles/cloud-shell/overview.md) para implementar o modelo. Para implementar um modelo externo, forneça o URI do modelo exatamente como faria para qualquer implementação externa. Para implementar um modelo de local, primeiro tem de carregar o modelo para a conta de armazenamento para o Cloud Shell. Esta secção descreve como carregar o modelo para a sua conta do cloud shell e implementá-lo como um ficheiro local. Se ainda não utilizou o Cloud Shell, consulte [descrição geral do Azure Cloud Shell](../articles/cloud-shell/overview.md) para obter informações sobre como configurá-lo.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione o grupo de recursos do Cloud Shell. O padrão de nome é `cloud-shell-storage-<region>`.

   ![Selecionar grupo de recursos](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. Selecione a conta de armazenamento para o Cloud Shell.

   ![Selecionar conta de armazenamento](./media/resource-manager-cloud-shell-deploy/select-storage.png)

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

   ![Carregar ficheiro](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. Depois de ter carregado, selecione o modelo.

   ![Selecione o novo modelo](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Copie o URL.

   ![Copiar URL](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Abra o prompt.

   ![Abrir o Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
