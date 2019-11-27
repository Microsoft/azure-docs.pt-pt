---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: aac2f3ea2b52ac0319f96279deed13c1145749bd
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74451506"
---
## <a name="deploy-template-from-cloud-shell"></a>Implementar o modelo a partir do Cloud Shell

Pode utilizar o [Cloud Shell](../articles/cloud-shell/overview.md) para implementar o modelo. Para implantar um modelo externo, forneça o URI do modelo exatamente como você faria para qualquer implantação externa. Para implantar um modelo local, você deve primeiro carregar o modelo na conta de armazenamento para seu Cloud Shell. Esta seção descreve como carregar o modelo para sua conta do Cloud Shell e implantá-lo como um arquivo local. Se você não tiver usado Cloud Shell, consulte [visão geral do Azure cloud Shell](../articles/cloud-shell/overview.md) para obter informações sobre como configurá-lo.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. Selecione o grupo de recursos do Cloud Shell. O padrão de nome é `cloud-shell-storage-<region>`.

   ![Selecionar grupo de recursos](./media/resource-manager-cloud-shell-deploy/select-cloud-shell-resource-group.png)

1. Selecione a conta de armazenamento para o Cloud Shell.

   ![Selecionar a conta de armazenamento](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Selecione **Blobs**.

   ![Selecionar blobs](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. Selecionar **+ Contentor**.

   ![Adicionar contentor](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. Dê um nome e um nível de acesso ao seu contêiner. O modelo de exemplo neste artigo não contém informações confidenciais, portanto, permita acesso de leitura anônimo. Selecione **OK**.

   ![Fornecer valores de contêiner](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. Selecione o contêiner que você criou.

   ![Selecionar novo contêiner](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. Selecione **Upload**.

   ![Carregar blob](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. Localize e carregue o modelo.

   ![Carregar o ficheiro](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. Depois de carregado, selecione o modelo.

   ![Selecionar novo modelo](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Copie a URL.

   ![Copiar URL](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Abra o prompt.

   ![Abrir o Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
