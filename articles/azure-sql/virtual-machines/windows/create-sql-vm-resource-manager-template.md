---
title: Criar VM do servidor SQL (modelo de gestor de recursos Azure)
description: Aprenda a criar um servidor SQL na Máquina Virtual Azure (VM) utilizando um modelo de Gestor de Recursos Azure.
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: virtual-machines-sql
ms.openlocfilehash: 8b165f640548f28e5d94e5a791c0fe8545df4d78
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85852539"
---
# <a name="create-sql-server-vm-azure-resource-manager-template"></a>Criar VM do servidor SQL (modelo de gestor de recursos Azure)

Utilize este modelo de Gestor de Recursos Azure para implementar um servidor SQL na Máquina Virtual Azure (VM). 

[!INCLUDE [About Azure Resource Manager](../../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

O modelo SQL Server VM ARM requer o seguinte:

- A versão mais recente do [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) e/ou [PowerShell](/powershell/scripting/install/installing-powershell?view=powershell-7). 
- Um grupo de [recursos](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) pré-configurado com uma [rede virtual](../../../virtual-network/quick-create-portal.md) preparada e [uma sub-rede.](../../../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)
- Uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste arranque rápido é de [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/101-sql-vm-new-storage/).

:::code language="json" source="~/quickstart-templates/101-sql-vm-new-storage/azuredeploy.json" highlight="169-310":::

Os cinco recursos Azure são definidos no modelo: 

- [Microsoft.Network/publicIpAddresses](/azure/templates/microsoft.network/publicipaddresses): Cria um endereço IP público. 
- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups): Cria um grupo de segurança de rede. 
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces): Configura a interface de rede. 
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): Cria uma máquina virtual em Azure. 
- [Microsoft.SqlVirtualMachine/SqlVirtualMachines](/azure/templates/microsoft.sqlvirtualmachine/sqlvirtualmachines): regista a máquina virtual com o fornecedor de recursos SQL VM. 

Mais SQL Server em modelos Azure VM pode ser encontrado na galeria de [modelos quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sqlvirtualmachine).


## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria uma máquina virtual com a versão prevista do SQL Server instalada e registada com o fornecedor de recursos SQL VM. 

   [![Implementar no Azure](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

2. Selecione ou introduza os seguintes valores.

    * **Assinatura**: Selecione uma subscrição Azure.
    * **Grupo de recursos**: O grupo de recursos preparado para o seu SQL Server VM. 
    * **Região**: Selecione uma região.  Por exemplo, **E.U.A. Central**.
    * **Nome da máquina virtual**: Introduza um nome para máquina virtual SQL Server. 
    * **Tamanho da máquina virtual**: Escolha o tamanho adequado para a sua máquina virtual a partir do drop-down.
    * **Nome de rede virtual existente**: Introduza o nome da rede virtual preparada para o seu SQL Server VM. 
    * **Grupo de Recursos Vnet existente**: Insira o grupo de recursos onde a sua rede virtual foi preparada. 
    * Nome da **sub-rede existente**: O nome da sua sub-rede preparada. 
    * **Oferta de imagem**: Escolha a imagem do SQL Server e do Windows Server que melhor se adapte às necessidades do seu negócio. 
    * **SQL Sku**: Escolha a edição do SQL Server SKU que melhor se adapte às necessidades do seu negócio. 
    * **Nome de utilizador Admin**: O nome de utilizador para o administrador da máquina virtual. 
    * **Admin Password**: A palavra-passe utilizada pela conta de administrador VM. 
    * **Tipo de carga de trabalho de armazenamento**: O tipo de armazenamento para a carga de trabalho que melhor combina com o seu negócio. 
    * **Contagem de discos de dados sql**: O número de discos que o SQL Server utiliza para ficheiros de dados.  
    * **Caminho dos dados**: O caminho para os ficheiros de dados do SQL Server. 
    * **Contagem de discos de log sql**: O número de discos que o SQL Server utiliza para ficheiros de registo. 
    * **Log Path**: O caminho para os ficheiros de registo do SQL Server. 
    * **Localização**: A localização de todos os recursos, este valor deve continuar a ser o padrão de `[resourceGroup().location]` . 

3. Selecione **Rever + criar**. Depois de o SQL Server VM ter sido implementado com sucesso, recebe uma notificação.

O portal do Azure é utilizado para implementar o modelo. Além do portal Azure, também pode utilizar a Azure PowerShell, o Azure CLI e a REST API. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Pode utilizar o CLI Azure para verificar os recursos implantados. 


```azurecli-interactive
echo "Enter the resource group where your SQL Server VM exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos utilizando o Azure CLI ou o Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Passos seguintes

Para um tutorial passo a passo que o guia através do processo de criação de um modelo, consulte:

> [!div class="nextstepaction"]
> [Tutorial: Criar e implementar o seu primeiro modelo de Gestor de Recursos Azure](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)

Para outras formas de implementar um SQL Server VM, consulte: 
- [Portal do Azure](create-sql-vm-portal.md)
- [PowerShell](create-sql-vm-powershell.md)

Para saber mais, consulte [uma visão geral do SQL Server em VMs Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).
