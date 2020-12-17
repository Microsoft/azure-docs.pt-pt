---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 596b73f8fb205b6a5681fecf3d00fd2a67c1f59f
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97628736"
---
## <a name="create-a-spatial-anchors-resource"></a>Criar um recurso de âncoras espaciais

### <a name="portal"></a>[Portal](#tab/azure-portal)

Aceda ao <a href="https://portal.azure.com" target="_blank">portal do Azure</a>.

No painel esquerdo, **selecione Criar um recurso**.

Utilize a caixa de pesquisa para procurar **âncoras espaciais.**

![Screenshot mostrando os resultados de uma pesquisa de Âncoras Espaciais.](./media/spatial-anchors-get-started-create-resource/portal-search.png)

Selecione **Âncoras Espaciais** e, em seguida, selecione **Criar**.

No painel de contas de **âncoras espaciais,** faça o seguinte:

* Introduza um nome de recurso único utilizando caracteres alfanuméricos regulares.
* Selecione a subscrição a que pretende anexar o recurso.
* Criar um grupo de recursos selecionando **Criar novo**. Nomeie-o **myResourceGroup** e, em seguida, selecione **OK**.

  [!INCLUDE [resource group intro text](resource-group.md)]

* Selecione uma localização (região) na qual coloque o recurso.
* Selecione **Novo** para começar a criar o recurso.

![Screenshot do painel de âncoras espaciais para criar um recurso.](./media/spatial-anchors-get-started-create-resource/create-resource-form.png)

Após a criação do recurso, o portal Azure mostra que a sua implantação está completa.

![Screenshot mostrando que a implementação do recurso está completa.](./media/spatial-anchors-get-started-create-resource/deployment-complete.png)

Selecione **Ir para recurso**. Agora pode ver as propriedades dos recursos.

Copie o valor de **ID** da conta do recurso num editor de texto para utilização posterior.

![Screenshot do painel de propriedades de recursos.](./media/spatial-anchors-get-started-create-resource/view-resource-properties.png)

Copie também o valor do Domínio da **Conta** do recurso num editor de texto para posterior utilização.

![Screenshot mostrando o valor do domínio da conta do recurso.](./media/spatial-anchors-get-started-create-resource/view-resource-domain.png)

Em **Definições**, selecione **Tecla**. Copie o valor **da chave principal,** **Chave de Conta,** num editor de texto para posterior utilização.

![Screenshot do painel keys para a conta.](./media/spatial-anchors-get-started-create-resource/view-account-key.png)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Comece por preparar o seu ambiente para a CLI do Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](azure-cli-prepare-your-environment-no-header.md)]

1. Depois de iniciar sina, utilize o comando [conjunto de conta az](/cli/azure/account#az_account_set) para selecionar a subscrição na qual configurar a conta de âncoras espaciais:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Executar o [grupo az criar](/cli/azure/group#az_group_create) comando para criar um grupo de recursos, ou usar um grupo de recursos existente:

   ```azurecli
   az group create --name myResourceGroup --location eastus2
   ```

   [!INCLUDE [resource group intro text](resource-group.md)]

   Pode ver as contas das suas âncoras espaciais atuais para um grupo de recursos utilizando o comando [da lista de âncoras espaciais az:](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_list)

   ```azurecli
   az spatial-anchors-account list --resource-group myResourceGroup
   ```

   Também pode ver as contas das âncoras espaciais para a sua subscrição:

   ```azurecli
   az spatial-anchors-account list
   ```

1. Executar a [conta de âncoras espaciais az criar](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_create) comando para criar a sua conta de âncoras espaciais:

   ```azurecli
   az spatial-anchors-account create --resource-group myResourceGroup --name MySpatialAnchorsQuickStart --location eastus2
   ```

1. Ver as propriedades dos recursos utilizando o comando [de demonstração de âncoras espaciais az:](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_show)

   ```azurecli
   az spatial-anchors-account show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   Copie o valor **de ID da conta de** recurso e o valor do domínio da conta **de** recurso num editor de texto para posterior utilização.

1. Executar o comando [de chave de conta de âncoras espaciais az](/cli/azure/ext/mixed-reality/spatial-anchors-account/key#ext_mixed_reality_az_spatial_anchors_account_key_show) para obter as suas chaves primárias e secundárias:

   ```azurecli
   az spatial-anchors-account key show --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
   ```

   Copie os valores-chave num editor de texto para utilização posterior.

   Se precisar de regenerar as chaves, utilize o comando [de renovação da chave de renovação da conta de âncoras espaciais az:](/cli/azure/ext/mixed-reality/spatial-anchors-account/key#ext_mixed_reality_az_spatial_anchors_account_key_renew)

   ```azurecli
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key primary
   az spatial-anchors-account key renew --resource-group myResourceGroup --name example --key secondary
   ```

Pode eliminar uma conta utilizando o comando [de eliminação de âncoras espaciais az:](/cli/azure/ext/mixed-reality/spatial-anchors-account#ext_mixed_reality_az_spatial_anchors_account_delete)

```azurecli
az spatial-anchors-account delete --resource-group myResourceGroup --name MySpatialAnchorsQuickStart
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Comece por preparar o seu ambiente para o Azure PowerShell:

[!INCLUDE [azure-powershell-requirements-no-header.md](azure-powershell-requirements-no-header.md)]

> [!IMPORTANT]
> Enquanto o módulo **Az.MixedReality** PowerShell estiver em pré-visualização, deve instalá-lo separadamente utilizando o `Install-Module` cmdlet. Quando este módulo do PowerShell entrar em disponibilidade geral, fará parte das versões futuras do módulo Az PowerShell e estará disponível por predefinição a partir do Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.MixedReality
```

1. Depois de iniciar sina, utilize a [cmdlet Set-AzContext](/powershell/module/az.accounts/set-azcontext) para selecionar a subscrição na qual configurar a conta de âncoras espaciais:

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. Executar o [cmdlet New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) para criar um grupo de recursos, ou utilizar um grupo de recursos existente:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location eastus2
   ```

   [!INCLUDE [resource group intro text](resource-group.md)]

   Pode ver as contas das suas âncoras espaciais atuais para um grupo de recursos utilizando o cmdlet [Get-AzSpatialAnchorsAccount:](/powershell/module/az.mixedreality/get-azspatialanchorsaccount)

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup
   ```

   Também pode ver as contas das âncoras espaciais para a sua subscrição:

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount
   ```

1. Executar o [cmdlet New-AzSpatialAnchorsAccount](/powershell/module/az.mixedreality/new-azspatialanchorsaccount) para criar a sua conta de âncoras espaciais:

   ```azurepowershell-interactive
   New-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart -Location eastus2
   ```

1. Ver as propriedades dos recursos utilizando o [cmdlet Get-AzSpatialAnchorsAccount:](/powershell/module/az.mixedreality/get-azspatialanchorsaccount)

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
   ```

   Copie o valor **da conta de propriedadeDese** a conta de **propriedadeDomain** valor em um editor de texto para posterior utilização.

1. Executar o [cmdlet Get-AzSpatialAnchorsAccountKey](/powershell/module/az.mixedreality/get-azspatialanchorsaccountkey) para obter as suas teclas primárias e secundárias:

   ```azurepowershell-interactive
   Get-AzSpatialAnchorsAccountKey -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
   ```

   Copie os valores-chave num editor de texto para utilização posterior.

   Se precisar de regenerar as chaves, utilize o cmdlet [New-AzSpatialAnchorsAccountKey:](/powershell/module/az.mixedreality/new-azspatialanchorsaccountkey)

   ```azurepowershell-interactive
   New-AzSpatialAnchorsAccountKey -ResourceGroupName myResourceGroup -Name MySpatialAnchorsQuickStart -Primary
   New-AzSpatialAnchorsAccountKey -ResourceGroupName myResourceGroup -Name MySpatialAnchorsQuickStart -Secondary
   ```

Pode eliminar uma conta utilizando o [cmdlet Remove-AzSpatialAnchorsAccount:](/powershell/module/az.mixedreality/remove-azspatialanchorsaccount)

```azurepowershell-interactive
Remove-AzSpatialAnchorsAccount -ResourceGroup myResourceGroup -Name MySpatialAnchorsQuickStart
```

---
