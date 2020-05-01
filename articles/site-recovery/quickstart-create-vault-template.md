---
title: Quickstart para criar um cofre de Serviços de Recuperação Azure usando um modelo de Gestor de Recursos Azure.
description: Neste arranque rápido, você aprende a criar um cofre azure Recovery Services usando um modelo de Gestor de Recursos Azure.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/29/2020
author: davidsmatlak
ms.author: v-dasmat
ms.openlocfilehash: 47c25ebd0fe18d470b04ccbcc85a8638c1ce0346
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598402"
---
# <a name="quickstart-create-a-recovery-services-vault-using-a-resource-manager-template"></a>Quickstart: Criar um cofre de serviços de recuperação usando um modelo de Gestor de Recursos

Este quickstart descreve como configurar um cofre de Serviços de Recuperação usando um modelo de Gestor de Recursos Azure. O serviço de recuperação de [sites Azure](site-recovery-overview.md) contribui para a continuidade do seu negócio e estratégia de recuperação de desastres (BCDR) para que as suas aplicações de negócio permaneçam online durante as interrupções planeadas e não planeadas. A Recuperação do Local gere a recuperação de desastres de máquinas no local e máquinas virtuais Azure (VM), incluindo replicação, failover e recuperação.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se não tiver uma subscrição azure ativa, pode criar uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Nenhum.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

### <a name="review-the-template"></a>Reveja o modelo

O modelo utilizado neste quickstart é de [modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/101-recovery-services-vault-create/).

:::code language="json" source="~/quickstart-templates/101-recovery-services-vault-create/azuredeploy.json" range="1-66" highlight="41-65":::

Dois recursos Azure são definidos no modelo:

- [Microsoft.RecoveryServices vaults](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults): cria o cofre.
- [Microsoft.RecoveryServices/vaults/backupconfig](/rest/api/backup/backupresourcestorageconfigs): configura as definições de redundância de reserva do cofre.

O modelo inclui parâmetros opcionais para a configuração de backup do cofre. As definições de redundância de armazenamento são armazenamento localmente redundante (LRS) ou armazenamento geo-redundante (GRS). Para mais informações, consulte [Definir a redundância](../backup/backup-create-rs-vault.md#set-storage-redundancy)de armazenamento .

Para mais modelos de Serviços de Recuperação Azure, consulte [os modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Recoveryservices&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>Implementar o modelo

Para implementar o modelo, são necessários o modelo **de Subscrição,** **Grupo de Recursos**e Nome **vault.**

1. Para iniciar sessão no Azure e abrir o modelo, selecione a imagem **De si para azure.**

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

1. Selecione ou introduza os seguintes valores:

   :::image type="content" source="media/quickstart-create-vault-template/create-vault-template.png" alt-text="Modelo para criar um cofre de Serviços de Recuperação.":::

   - **Subscrição**: selecione a sua subscrição Azure.
   - **Grupo de recursos**: selecione um grupo existente ou selecione **Criar novo** para adicionar um grupo.
   - **Localização**: falha na localização do grupo de recursos e fica indisponível após a separação de um grupo de recursos.
   - **Nome do cofre:** forneça um nome para o cofre.
   - **Tipo de armazenamento de alteração**: O predefinido é **falso**. Selecione **o verdadeiro** apenas se precisar de alterar o tipo de armazenamento do cofre.
   - **Tipo de armazenamento do cofre:** O padrão é **Globalmente Redundante**. Se o tipo de armazenamento for definido como **verdadeiro,** selecione **Localredundant**.
   - **Localização**: `[resourceGroup().location]` a função não está na localização do grupo de recursos. Para alterar a localização, insira um valor como **westus**.
   - Selecione a caixa de verificação **Concordo com os termos e condições acima indicados**.

1. Para iniciar a implantação do cofre, selecione o botão **de compra.** Após uma implementação bem sucedida, é apresentada uma notificação.

   :::image type="content" source="media/quickstart-create-vault-template/deployment-success.png" alt-text="O lançamento do cofre foi bem sucedido.":::

## <a name="validate-the-deployment"></a>Validar a implementação

Para confirmar que o cofre foi criado, utilize o Azure CLI ou o Azure PowerShell.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the vault name:" &&
read vaultName &&
az backup vault show --name $vaultName --resource-group $resourceGroupName &&
az backup vault backup-properties show --name $vaultName --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
$vaultName = Read-Host -Prompt "Enter the vault name"
$vaultBackupConfig = Get-AzRecoveryServicesVault -Name $vaultName
Get-AzRecoveryServicesVault -ResourceGroupName $resouceGroupName -Name $vaultName
Get-AzRecoveryServicesBackupProperty -Vault $vaultBackupConfig
Write-Host "Press [ENTER] to continue..."
```

---

A seguinte saída é um excerto da informação do cofre:

# <a name="cli"></a>[CLI](#tab/CLI)

```Output
"id": "/subscriptions/<Subscription Id>/resourceGroups/myResourceGroup
         /providers/Microsoft.RecoveryServices/vaults/myVault"
"location": "eastus"
"name": "myVault"
"resourceGroup": "myResourceGroup"

"storageModelType": "GeoRedundant"
"storageType": "GeoRedundant"
"type": "Microsoft.RecoveryServices/vaults/backupstorageconfig"
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : myVault
Type              : Microsoft.RecoveryServices/vaults
Location          : eastus
ResourceGroupName : myResourceGroup
SubscriptionId    : <Subscription Id>

BackupStorageRedundancy
-----------------------
GeoRedundant
```

---

## <a name="clean-up-resources"></a>Limpar recursos

Se planeias usar os novos recursos, não é necessária nenhuma ação. Caso contrário, pode remover o grupo de recursos e o cofre que foi criado neste arranque rápido. Para eliminar o grupo de recursos e os seus recursos, utilize o Azure CLI ou o Azure PowerShell.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resouceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um cofre dos Serviços de Recuperação. Para saber mais sobre a recuperação de desastres, continue para o próximo artigo de arranque rápido.

> [!div class="nextstepaction"]
> [Configurar a recuperação após desastre](azure-to-azure-quickstart.md)
