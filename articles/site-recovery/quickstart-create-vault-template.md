---
title: Quickstart para criar um cofre Azure Recovery Services usando um modelo de Gestor de Recursos Azure.
description: Neste arranque rápido, você aprende a criar um cofre Azure Recovery Services usando um modelo Azure Resource Manager (modelo ARM).
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.date: 04/29/2020
ms.openlocfilehash: c1c6d625c67130a4e3608cee6134b8c81404c01b
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92745878"
---
# <a name="quickstart-create-a-recovery-services-vault-using-an-arm-template"></a>Quickstart: Criar um cofre de serviços de recuperação usando um modelo ARM

Este quickstart descreve como configurar um cofre de Serviços de Recuperação utilizando um modelo de Gestor de Recursos Azure (modelo ARM). O serviço [de Recuperação de Sítios Azure](site-recovery-overview.md) contribui para a estratégia de continuidade do seu negócio e recuperação de desastres (BCDR) para que as suas aplicações de negócio permaneçam on-line durante as paragens planeadas e não planeadas. A Recuperação do Site gere a recuperação de desastres de máquinas no local e máquinas virtuais Azure (VM), incluindo replicação, failover e recuperação.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure** . O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição ativa do Azure, pode criar uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-recovery-services-vault-create/).

:::code language="json" source="~/quickstart-templates/101-recovery-services-vault-create/azuredeploy.json":::

Dois recursos Azure são definidos no modelo:

- [Cofres Microsoft.RecoveryServices:](/azure/templates/microsoft.recoveryservices/vaults)cria o cofre.
- [Microsoft.RecoveryServices/vaults/backupstorageconfig](/rest/api/backup/backupresourcestorageconfigs): configura as definições de redundância de backup do cofre.

O modelo inclui parâmetros opcionais para a configuração de backup do cofre. As definições de redundância de armazenamento são armazenamento localmente redundante (LRS) ou armazenamento geo-redundante (GRS). Para obter mais informações, consulte [set armazenamento redundância](../backup/backup-create-rs-vault.md#set-storage-redundancy).

Para obter mais modelos de Serviços de Recuperação Azure, consulte [os modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Recoveryservices&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implementar o modelo

Para implementar o modelo, é necessário o nome **De assinatura,** **grupo de recursos** e **Cofre.**

1. Para iniciar súm na Azure e abrir o modelo, selecione a imagem **'Implementar para Azul'.**

   [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

1. Selecione ou introduza os seguintes valores:

   :::image type="content" source="media/quickstart-create-vault-template/create-vault-template.png" alt-text="Modelo para criar um cofre de Serviços de Recuperação.":::

   - **Subscrição** : selecione a sua subscrição Azure.
   - **Grupo de recursos** : selecione um grupo existente ou selecione **Criar novo** para adicionar um grupo.
   - **Localização** : predefinição da localização do grupo de recursos e fica indisponível após a seleção de um grupo de recursos.
   - **Nome do cofre:** Forneça um nome para o cofre.
   - **Tipo de armazenamento de** alteração: O predefinição é **falso** . Selecione **true** only if you need to change the vault's storage type.
   - **Tipo de armazenamento de** cofre : O padrão é **globalmenteredundant** . Se o tipo de armazenamento foi definido como **verdadeiro,** selecione **LocallyRedundant** .
   - **Localização:** a função `[resourceGroup().location]` é padrão para a localização do grupo de recursos. Para alterar a localização, insira um valor como **o westus.**
   - Selecione a caixa de verificação **Concordo com os termos e condições acima indicados** .

1. Para iniciar a implantação do cofre, selecione o botão **'Comprar'.** Após uma implementação bem sucedida, é apresentada uma notificação.

   :::image type="content" source="media/quickstart-create-vault-template/deployment-success.png" alt-text="Modelo para criar um cofre de Serviços de Recuperação.":::

## <a name="validate-the-deployment"></a>Validar a implementação

Para confirmar que o cofre foi criado, use Azure CLI ou Azure PowerShell.

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

Se planeia usar os novos recursos, não é necessária nenhuma ação. Caso contrário, pode remover o grupo de recursos e o cofre que foi criado neste arranque rápido. Para eliminar o grupo de recursos e os seus recursos utiliza a Azure CLI ou a Azure PowerShell.

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

Neste arranque rápido, criaste um cofre dos Serviços de Recuperação. Para saber mais sobre a recuperação de desastres, continue para o próximo artigo de arranque rápido.

> [!div class="nextstepaction"]
> [Configurar a recuperação após desastre](azure-to-azure-quickstart.md)
