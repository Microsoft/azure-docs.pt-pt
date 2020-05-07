---
title: Criar uma conta de armazenamento
titleSuffix: Azure Storage
description: Aprenda a criar uma conta de armazenamento utilizando o portal Azure, Azure PowerShell ou o Azure CLI. Uma conta de armazenamento Azure fornece um espaço de nome único no Microsoft Azure para armazenar e aceder aos seus dados.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/07/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 7ff7db383a74ce01f7f1a7bf49a33e41f91decf8
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853493"
---
# <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure

Uma conta de armazenamento Azure contém todos os objetos de dados do Seu Armazenamento Azure: blobs, ficheiros, filas, mesas e discos. A conta de armazenamento fornece um espaço de nome único para os seus dados de Armazenamento Azure que é acessível a partir de qualquer parte do mundo em HTTP ou HTTPS. Os dados na sua conta de armazenamento Azure são duráveis e altamente disponíveis, seguros e massivamente escaláveis.

Neste artigo de como fazer, aprende-se a criar uma conta de armazenamento utilizando o [portal Azure,](https://portal.azure.com/) [Azure PowerShell,](https://docs.microsoft.com/powershell/azure/overview) [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)ou um modelo de Gestor de [Recursos Azure.](../../azure-resource-manager/management/overview.md)  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Nenhum.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para criar uma conta de armazenamento Azure com a PowerShell, certifique-se de que instalou o módulo Azure PowerShell a versão 0.7 ou posterior. Para mais informações, consulte [A introdução do módulo Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

Para encontrar a sua versão atual, execute o seguinte comando:

```powershell
Get-InstalledModule -Name "Az"
```

Para instalar ou atualizar o Azure PowerShell, consulte instalar o [módulo PowerShell Azure](/powershell/azure/install-Az-ps).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Pode iniciar sessão no Azure e executar comandos Azure CLI de uma de duas formas:

- Pode executar comandos CLI dentro do portal Azure, em Azure Cloud Shell.
- Pode instalar o CLI e executar comandos CLI localmente.

### <a name="use-azure-cloud-shell"></a>Utilizar o Azure Cloud Shell

O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. O Azure CLI está pré-instalado e configurado para utilizar com a sua conta. Clique no botão **Cloud Shell** no menu na secção superior direita do portal Azure:

[![Casca de Nuvem](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

O botão lança uma concha interativa que pode utilizar para executar os passos descritos neste artigo de como:

[![Screenshot mostrando a janela Cloud Shell no portal](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Instalar a CLI localmente

Também pode instalar e utilizar a CLI do Azure localmente. Este artigo de como fazer requer que esteja a executar a versão Azure CLI 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

# <a name="template"></a>[Modelo](#tab/template)

Nenhum.

---

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

# <a name="portal"></a>[Portal](#tab/azure-portal)

Inicie sessão no [portal do Azure](https://portal.azure.com).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Inscreva-se na sua subscrição Azure com o `Connect-AzAccount` comando e siga as instruções no ecrã para autenticar.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para lançar a Azure Cloud Shell, inscreva-se no [portal Azure.](https://portal.azure.com)

Para iniciar sessão na instalação local do CLI, execute o comando [de login az:](/cli/azure/reference-index#az-login)

```azurecli-interactive
az login
```

# <a name="template"></a>[Modelo](#tab/template)

N/D

---

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Agora está pronto para criar uma conta de armazenamento.

Cada conta de armazenamento tem de pertencer a um grupo de recursos do Azure. Um grupo de recursos é um contentor lógico para agrupar os seus serviços do Azure. Quando cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou utilizar um grupo de recursos existente. Este artigo mostra como criar um novo grupo de recursos.

Uma conta de armazenamento **v2 para fins gerais** concede acesso a todos os serviços de Armazenamento do Azure: blobs, ficheiros, filas, tabelas e discos. Os passos aqui descritos criam uma conta de armazenamento v2 de propósito geral, mas os passos para criar qualquer tipo de conta de armazenamento são semelhantes.

# <a name="portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Em primeiro lugar, crie um novo grupo de recursos com a PowerShell utilizando o comando [New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
$location = "westus"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Se não tiver a certeza de `-Location` qual região especificar para o parâmetro, pode recuperar uma lista de regiões suportadas para a sua subscrição com o comando [Get-AzLocation:](/powershell/module/az.resources/get-azlocation)

```powershell
Get-AzLocation | select Location
```

Em seguida, crie uma conta de armazenamento v2 de uso geral com armazenamento geo-redundante de acesso de leitura (RA-GRS) utilizando o comando [New-AzStorageAccount.](/powershell/module/az.storage/New-azStorageAccount) Lembre-se que o nome da sua conta de armazenamento deve ser único em todo o Azure, por isso substitua o valor do espaço reservado em parênteses pelo seu próprio valor único:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

> [!IMPORTANT]
> Se planeia utilizar o Armazenamento do `-EnableHierarchicalNamespace $True` [Lago Azure Data,](https://azure.microsoft.com/services/storage/data-lake-storage/)inclua nesta lista de parâmetros.

Para criar uma conta de armazenamento v2 de uso geral com uma opção de replicação diferente, substitua o valor desejado na tabela abaixo para o parâmetro **SkuName.**

|Opção de replicação  |Parâmetro SkuName  |
|---------|---------|
|Armazenamento localmente redundante (LRS)     |Standard_LRS         |
|Armazenamento com redundância entre zonas (ZRS)     |Standard_ZRS         |
|Armazenamento georredundante (GRS)     |Standard_GRS         |
|Armazenamento georredundante com acesso de leitura (GRS)     |Standard_RAGRS         |
|Armazenamento com redundância entre zonas (GZRS)    |Standard_GZRS         |
|Armazenamento geozona-redundante de acesso de leitura (RA-GZRS)    |Standard_RAGZRS         |

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

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

Em seguida, crie uma conta de armazenamento v2 de uso geral com armazenamento geo-redundante de acesso de leitura utilizando a conta de [armazenamento az criar](/cli/azure/storage/account#az_storage_account_create) comando. Lembre-se que o nome da sua conta de armazenamento deve ser único em todo o Azure, por isso substitua o valor do espaço reservado em parênteses pelo seu próprio valor único:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

> [!IMPORTANT]
> Se planeia utilizar o Armazenamento do `--enable-hierarchical-namespace true` [Lago Azure Data,](https://azure.microsoft.com/services/storage/data-lake-storage/)inclua nesta lista de parâmetros. 

Para criar uma conta de armazenamento v2 de propósito geral com uma opção de replicação diferente, substitua o valor desejado na tabela abaixo para o parâmetro **sku.**

|Opção de replicação  |parâmetro de sku  |
|---------|---------|
|Armazenamento localmente redundante (LRS)     |Standard_LRS         |
|Armazenamento com redundância entre zonas (ZRS)     |Standard_ZRS         |
|Armazenamento georredundante (GRS)     |Standard_GRS         |
|Armazenamento georredundante com acesso de leitura (GRS)     |Standard_RAGRS         |
|Armazenamento com redundância entre zonas (GZRS)    |Standard_GZRS         |
|Armazenamento geozona-redundante de acesso de leitura (RA-GZRS)    |Standard_RAGZRS         |

# <a name="template"></a>[Modelo](#tab/template)

Pode utilizar o Azure PowerShell ou o Azure CLI para implementar um modelo de Gestor de Recursos para criar uma conta de armazenamento. O modelo utilizado neste artigo de como-a-artigo é de [modelos de quickstart do Gestor](https://azure.microsoft.com/resources/templates/101-storage-account-create/)de Recursos Do Azure . Para executar os scripts, selecione **Experimente-o** para abrir a Casca de Nuvem Azure. Para colar o script, clique na concha e, em seguida, selecione **Pasta**.

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

> [!NOTE]
> Este modelo serve apenas como um exemplo. Existem muitas definições de conta de armazenamento que não estão configuradas como parte deste modelo. Por exemplo, se quiser utilizar o Armazenamento do [Lago de Dados Azure,](https://azure.microsoft.com/services/storage/data-lake-storage/)modifica-se este modelo definindo a `isHnsEnabledad` propriedade do `StorageAccountPropertiesCreateParameters` objeto para `true`. 

Para aprender a modificar este modelo ou criar novos, consulte:

- [Documentação do Gestor de Recursos Azure.](/azure/azure-resource-manager/)
- Referência do modelo da conta de [armazenamento.](/azure/templates/microsoft.storage/allversions)
- [Amostras adicionais](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)do modelo da conta de armazenamento.

---

Para obter mais informações sobre as opções de replicação disponíveis, veja [Opções de replicação do Azure](storage-redundancy.md).

## <a name="delete-a-storage-account"></a>Eliminar uma conta do Storage

A eliminação de uma conta de armazenamento elimina toda a conta, incluindo todos os dados da conta, e não pode ser desfeita.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navegue na conta de armazenamento no [portal Azure.](https://portal.azure.com)
1. Clique em **Apagar**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para eliminar a conta de armazenamento, utilize o comando [Remove-AzStorageAccount:](/powershell/module/az.storage/remove-azstorageaccount)

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para eliminar a conta de armazenamento, utilize a conta de [armazenamento az eliminar](/cli/azure/storage/account#az-storage-account-delete) o comando:

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[Modelo](#tab/template)

Para eliminar a conta de armazenamento, utilize o Azure PowerShell ou o Azure CLI.

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

Alternadamente, pode eliminar o grupo de recursos, que elimina a conta de armazenamento e quaisquer outros recursos desse grupo de recursos. Para obter mais informações sobre a eliminação de um grupo de recursos, consulte [Eliminar o grupo de recursos e os recursos.](../../azure-resource-manager/management/delete-resource-group.md)

> [!WARNING]
> Não é possível restaurar uma conta do Storage eliminada ou obter os conteúdos que esta continha antes da eliminação. Certifique-se de que faz uma cópia de segurança de tudo o que pretende guardar antes de eliminar a conta. Isto também se aplica a quaisquer recursos na conta – depois de eliminar um blob, tabela, fila ou ficheiro, este é eliminado permanentemente.
>
> Se tentar eliminar uma conta de armazenamento associada a uma máquina virtual do Azure, poderá receber um erro informando-o de que a conta de armazenamento ainda está em utilização. Para ajudar a resolver este erro, consulte [erros de resolução de problemas quando eliminar as contas](../common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md)de armazenamento .

## <a name="next-steps"></a>Passos seguintes

Neste artigo de como fazer, criou uma conta de armazenamento padrão v2 de propósito geral. Para aprender a carregar e descarregar bolhas de e para a sua conta de armazenamento, continue a ser um dos quickstarts de armazenamento blob.

# <a name="portal"></a>[Portal](#tab/azure-portal)

> [!div class="nextstepaction"]
> [Trabalhar com blobs ao utilizar o portal do Azure](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [Trabalhar com blobs ao utilizar o PowerShell](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Trabalhe com bolhas usando o Azure CLI](../blobs/storage-quickstart-blobs-cli.md)

# <a name="template"></a>[Modelo](#tab/template)

> [!div class="nextstepaction"]
> [Trabalhar com blobs ao utilizar o portal do Azure](../blobs/storage-quickstart-blobs-portal.md)

---
