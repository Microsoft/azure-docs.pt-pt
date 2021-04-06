---
title: Implementar modelos com Cloud Shell
description: Use o Azure Resource Manager e a Azure Cloud Shell para implantar recursos para a Azure. Os recursos são definidos num modelo de Gestor de Recursos Azure (modelo ARM).
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: c67251a33b6197603be27086bcc6cd047e0c414b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028612"
---
# <a name="deploy-arm-templates-from-azure-cloud-shell"></a>Implemente modelos ARM a partir de Azure Cloud Shell

Pode utilizar [a Azure Cloud Shell](../../cloud-shell/overview.md) para implementar um modelo de Gestor de Recursos Azure (modelo ARM). Pode implementar um modelo ARM que é armazenado remotamente ou um modelo ARM que é armazenado na conta de armazenamento local para Cloud Shell.

Pode ser implantado em qualquer âmbito. Este artigo mostra a implantação para um grupo de recursos.

## <a name="deploy-remote-template"></a>Implementar o modelo remoto

Para implementar um modelo externo, forneça o URI do modelo exatamente como faria para qualquer implementação externa. O modelo externo pode estar num repositório GitHub ou numa conta de armazenamento externo.

1. Abra o pedido da Cloud Shell.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Abrir o Cloud Shell":::

1. Para implementar o modelo, utilize os seguintes comandos:

   # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "Central US"
   az deployment group create \
     --name ExampleDeployment \
     --resource-group ExampleGroup \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="deploy-local-template"></a>Implementar um modelo local

Para implementar um modelo local, tem primeiro de carregar o seu modelo para a conta de armazenamento que está ligada à sua sessão Cloud Shell.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione o grupo de recursos do Cloud Shell. O padrão de nome é `cloud-shell-storage-<region>`.

   ![Selecionar o grupo de recursos](./media/deploy-cloud-shell/select-cloud-shell-resource-group.png)

1. Selecione a conta de armazenamento para o Cloud Shell.

   :::image type="content" source="./media/deploy-cloud-shell/cloud-shell-storage.png" alt-text="Selecione conta de armazenamento":::

1. Selecione **ações de ficheiros**.

   :::image type="content" source="./media/deploy-cloud-shell/files-shares.png" alt-text="Selecione ações de ficheiros":::

1. Selecione a partilha de ficheiros predefinido para Cloud Shell. A partilha de ficheiros tem o formato de nome de `cs-<user>-<domain>-com-<uniqueGuid>` .

   :::image type="content" source="./media/deploy-cloud-shell/select-file-share.png" alt-text="Partilha de ficheiros predefinidos":::

1. Adicione um novo diretório para manter os seus modelos. Selecione o diretório.

   :::image type="content" source="./media/deploy-cloud-shell/add-directory.png" alt-text="Adicionar diretório":::

1. Selecione **Carregar**.

   :::image type="content" source="./media/deploy-cloud-shell/upload-template.png" alt-text="Modelo de upload":::

1. Localize e carregue o modelo.

   :::image type="content" source="./media/deploy-cloud-shell/select-template.png" alt-text="Selecione o modelo":::

1. Abra o pedido da Cloud Shell.

   :::image type="content" source="./media/deploy-cloud-shell/open-cloud-shell.png" alt-text="Abrir o Cloud Shell":::

1. Navegue para o **diretório clouddrive.** Navegue para o diretório que adicionou para segurar os modelos.

1. Para implementar o modelo, utilize os seguintes comandos:

   # <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

   ```azurecli-interactive
   az group create --name ExampleGroup --location "South Central US"
   az deployment group create \
     --resource-group ExampleGroup \
     --template-file azuredeploy.json \
     --parameters storageAccountType=Standard_GRS
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name ExampleGroup -Location "Central US"
   New-AzResourceGroupDeployment `
     -DeploymentName ExampleDeployment `
     -ResourceGroupName ExampleGroup `
     -TemplateFile azuredeploy.json `
     -storageAccountType Standard_GRS
   ```

   ---

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre comandos de implantação, consulte [implementar recursos com modelos ARM e Azure CLI](deploy-cli.md) e [implementar recursos com modelos ARM e Azure PowerShell](deploy-powershell.md).
- Para pré-visualizar alterações antes de implementar um modelo, consulte [a implementação do modelo ARM e se funcionar](template-deploy-what-if.md).
