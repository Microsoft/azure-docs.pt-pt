---
title: Create a storage account
titleSuffix: Azure Storage
description: Saiba como criar uma conta de armazenamento usando a portal do Azure, Azure PowerShell ou a CLI do Azure. Uma conta de armazenamento do Azure fornece um namespace exclusivo no Microsoft Azure para armazenar e acessar seus dados.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/17/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: c9d3a876b75e7d3ed8ff43217227db1a524206f2
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76273484"
---
# <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure

Uma conta de armazenamento do Azure contém todos os seus objetos de dados do armazenamento do Azure: BLOBs, arquivos, filas, tabelas e discos. A conta de armazenamento fornece um namespace exclusivo para os dados do armazenamento do Azure que podem ser acessados de qualquer lugar do mundo por HTTP ou HTTPS. Os dados em sua conta de armazenamento do Azure são duráveis e altamente disponíveis, seguros e amplamente escalonáveis.

Neste artigo de instruções, você aprende a criar uma conta de armazenamento usando o [portal do Azure](https://portal.azure.com/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview), [CLI do Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)ou um [modelo Azure Resource Manager](../../azure-resource-manager/management/overview.md).  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Nenhum.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Este artigo de instruções requer o módulo Azure PowerShell AZ versão 0,7 ou posterior. Execute `Get-Module -ListAvailable Az` para encontrar a versão atual. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-Az-ps)(Instalar o módulo do Azure PowerShell).

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Você pode entrar no Azure e executar CLI do Azure comandos de uma das duas maneiras:

- Você pode executar comandos da CLI de dentro do portal do Azure, em Azure Cloud Shell.
- Você pode instalar a CLI e executar comandos da CLI localmente.

### <a name="use-azure-cloud-shell"></a>Utilizar o Azure Cloud Shell

O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. O CLI do Azure é pré-instalado e configurado para ser usado com sua conta. Clique no botão **Cloud Shell** no menu na seção superior direita da portal do Azure:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

O botão inicia um shell interativo que você pode usar para executar as etapas descritas neste artigo de instruções:

[![Captura de ecrã que mostra a janela do Cloud Shell no portal](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Instalar a CLI localmente

Também pode instalar e utilizar a CLI do Azure localmente. Este artigo de instruções requer que você esteja executando o CLI do Azure versão 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

Nenhum.

---

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Inicie sessão no [Portal do Azure](https://portal.azure.com).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Inicie sessão na sua subscrição do Azure com o `Connect-AzAccount` de comando e siga na tela direções para autenticar.

```powershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para iniciar o Azure Cloud Shell, entre no [portal do Azure](https://portal.azure.com).

Para fazer logon em sua instalação local da CLI, execute o comando [AZ login](/cli/azure/reference-index#az-login) :

```cli
az login
```

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

N/A

---

## <a name="create-a-storage-account"></a>Create a storage account

Agora você está pronto para criar uma conta de armazenamento.

Cada conta de armazenamento tem de pertencer a um grupo de recursos do Azure. Um grupo de recursos é um contentor lógico para agrupar os seus serviços do Azure. Quando cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou utilizar um grupo de recursos existente. Este artigo mostra como criar um novo grupo de recursos.

Uma conta de armazenamento **v2 para fins gerais** concede acesso a todos os serviços de Armazenamento do Azure: blobs, ficheiros, filas, tabelas e discos. As etapas descritas aqui criam uma conta de armazenamento de uso geral v2, mas as etapas para criar qualquer tipo de conta de armazenamento são semelhantes.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Primeiro, crie um novo grupo de recursos com o PowerShell usando o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) :

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Se você não tiver certeza de qual região especificar para o parâmetro `-Location`, poderá recuperar uma lista de regiões com suporte para sua assinatura com o comando [Get-AzLocation](/powershell/module/az.resources/get-azlocation) :

```powershell
Get-AzLocation | select Location
$location = "westus"
```

Em seguida, crie uma conta de armazenamento de uso geral V2 com armazenamento com redundância geográfica com acesso de leitura (RA-GRS) usando o comando [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) . Lembre-se de que o nome da sua conta de armazenamento deve ser exclusivo no Azure, portanto, substitua o valor do espaço reservado entre colchetes com seu próprio valor exclusivo:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

Para criar uma conta de armazenamento de uso geral V2 com uma opção de replicação diferente, substitua o valor desejado na tabela abaixo para o parâmetro **SkuName** .

|Opção de replicação  |Parâmetro SkuName  |
|---------|---------|
|Armazenamento localmente redundante (LRS)     |Standard_LRS         |
|Armazenamento com redundância entre zonas (ZRS)     |Standard_ZRS         |
|Armazenamento georredundante (GRS)     |Standard_GRS         |
|Armazenamento georredundante com acesso de leitura (GRS)     |Standard_RAGRS         |
|Armazenamento com redundância de zona geográfica (GZRS) (visualização)    |Standard_GZRS         |
|Armazenamento com redundância de zona geográfica com acesso de leitura (RA-GZRS) (visualização)    |Standard_RAGZRS         |

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Em primeiro lugar, crie um novo grupo de recursos com a CLI do Azure, através do comando [az group create](/cli/azure/group#az_group_create).

```azurecli-interactive
az group create \
    --name storage-resource-group \
    --location westus
```

Se não tiver a certeza de qual a região a especificar para o parâmetro `--location`, pode obter uma lista de regiões suportadas para a sua subscrição com o comando [az account list-locations](/cli/azure/account#az_account_list).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Em seguida, crie uma conta de armazenamento de uso geral V2 com o armazenamento com redundância geográfica com acesso de leitura usando o comando [AZ Storage Account Create](/cli/azure/storage/account#az_storage_account_create) . Lembre-se de que o nome da sua conta de armazenamento deve ser exclusivo no Azure, portanto, substitua o valor do espaço reservado entre colchetes com seu próprio valor exclusivo:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

Para criar uma conta de armazenamento de uso geral V2 com uma opção de replicação diferente, substitua o valor desejado na tabela abaixo para o parâmetro **SKU** .

|Opção de replicação  |parâmetro de sku  |
|---------|---------|
|Armazenamento localmente redundante (LRS)     |Standard_LRS         |
|Armazenamento com redundância entre zonas (ZRS)     |Standard_ZRS         |
|Armazenamento georredundante (GRS)     |Standard_GRS         |
|Armazenamento georredundante com acesso de leitura (GRS)     |Standard_RAGRS         |
|Armazenamento com redundância de zona geográfica (GZRS) (visualização)    |Standard_GZRS         |
|Armazenamento com redundância de zona geográfica com acesso de leitura (RA-GZRS) (visualização)    |Standard_RAGZRS         |

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

Você pode usar o Azure PowerShell ou o CLI do Azure para implantar um modelo do Resource Manager para criar uma conta de armazenamento. O modelo usado neste artigo de instruções é de [Azure Resource Manager modelos de início rápido](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Para executar os scripts, selecione **Experimente** para abrir o Azure cloud Shell. Para colar o script, clique com o botão direito do mouse no Shell e selecione **colar**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-file "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Para saber como criar modelos, consulte:

- [Azure Resource Manager documentação](/azure/azure-resource-manager/).
- [Referência de modelo da conta de armazenamento](/azure/templates/microsoft.storage/allversions).
- [Exemplos de modelo de conta de armazenamento adicionais](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

Para obter mais informações sobre as opções de replicação disponíveis, veja [Opções de replicação do Azure](storage-redundancy.md).

## <a name="delete-a-storage-account"></a>Eliminar uma conta do Storage

A exclusão de uma conta de armazenamento exclui a conta inteira, incluindo todos os dados na conta e não pode ser desfeita.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Navegue até a conta de armazenamento no [portal do Azure](https://portal.azure.com).
1. Clique em **Eliminar**.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Para excluir a conta de armazenamento, use o comando [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) :

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para excluir a conta de armazenamento, use o comando [AZ Storage Account Delete](/cli/azure/storage/account#az-storage-account-delete) :

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

Para excluir a conta de armazenamento, use Azure PowerShell ou CLI do Azure.

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

Como alternativa, você pode excluir o grupo de recursos, que exclui a conta de armazenamento e todos os outros recursos nesse grupo de recursos. Para obter mais informações sobre como excluir um grupo de recursos, consulte [excluir recursos e grupo](../../azure-resource-manager/management/delete-resource-group.md)de recursos.

> [!WARNING]
> Não é possível restaurar uma conta do Storage eliminada ou obter os conteúdos que esta continha antes da eliminação. Certifique-se de que faz uma cópia de segurança de tudo o que pretende guardar antes de eliminar a conta. Isto também se aplica a quaisquer recursos na conta – depois de eliminar um blob, tabela, fila ou ficheiro, este é eliminado permanentemente.
>
> Se tentar eliminar uma conta de armazenamento associada a uma máquina virtual do Azure, poderá receber um erro informando-o de que a conta de armazenamento ainda está em utilização. Para obter ajuda para solucionar esse erro, consulte [solucionar erros ao excluir contas de armazenamento](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md).

## <a name="next-steps"></a>Passos seguintes

Neste artigo de instruções, você criou uma conta de armazenamento padrão v2 de uso geral. Para saber como carregar e baixar blobs de e para sua conta de armazenamento, continue em um dos guias de início rápido do armazenamento de BLOBs.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

> [!div class="nextstepaction"]
> [Trabalhar com blobs ao utilizar o portal do Azure](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [Trabalhar com blobs ao utilizar o PowerShell](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Trabalhar com BLOBs usando o CLI do Azure](../blobs/storage-quickstart-blobs-cli.md)

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

> [!div class="nextstepaction"]
> [Trabalhar com blobs ao utilizar o portal do Azure](../blobs/storage-quickstart-blobs-portal.md)

---
