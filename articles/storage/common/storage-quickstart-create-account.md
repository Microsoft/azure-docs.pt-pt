---
title: Criar uma conta de armazenamento - armazenamento do Azure
description: Neste artigo de procedimentos, vai aprender a criar uma conta de armazenamento com o portal do Azure, Azure PowerShell ou a CLI do Azure. Uma conta de armazenamento do Azure oferece um espaço de nomes exclusivo no Microsoft Azure para armazenar e aceder aos objetos de dados que criar no Armazenamento do Azure.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: article
ms.date: 05/06/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 8375f4c54dc436ecf0694ec5f629c81d3591594d
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65234170"
---
# <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Uma conta de armazenamento do Azure contém todos os seus objetos de dados do armazenamento do Azure: blobs, ficheiros, filas, tabelas e discos. A conta de armazenamento fornece um espaço de nomes exclusivo para os seus dados de armazenamento do Azure que é acessíveis a partir de qualquer lugar no mundo através de HTTP ou HTTPS. Dados da sua conta de armazenamento do Azure são durável e altamente disponível, segura e dimensionável em massa.

Neste artigo de procedimento, vai aprender a criar uma conta de armazenamento com o [portal do Azure](https://portal.azure.com/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview), [da CLI do Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest), ou um [do Azure Resource Manager modelo](../../azure-resource-manager/resource-group-overview.md).  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Nenhum.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Este artigo que mostra como requer o módulo Azure PowerShell Az versão 0,7 ou posterior. Execute `Get-Module -ListAvailable Az` para encontrar a versão atual. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-Az-ps)(Instalar o módulo do Azure PowerShell).

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Pode iniciar sessão no Azure e executar comandos da CLI do Azure de uma de duas formas:

- Pode executar comandos da CLI no portal do Azure, no Azure Cloud Shell.
- Pode instalar a CLI e executar comandos da CLI localmente.

### <a name="use-azure-cloud-shell"></a>Utilizar o Azure Cloud Shell

O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. A CLI do Azure é previamente instalada e configurada para utilizar com a sua conta. Clique nas **Cloud Shell** botão no menu na secção canto superior direito do portal do Azure:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

O botão inicia uma shell interativa que pode utilizar para executar os passos descritos neste artigo de procedimentos:

[![Captura de ecrã que mostra a janela do Cloud Shell no portal](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Instalar a CLI localmente

Também pode instalar e utilizar a CLI do Azure localmente. Este artigo que mostra como requer a execução da versão 2.0.4 da CLI do Azure ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

Nenhum.

---

## <a name="sign-in-to-azure"></a>Inicie sessão no  Azure

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Inicie sessão no [portal do Azure](https://portal.azure.com).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Inicie sessão na sua subscrição do Azure com o `Connect-AzAccount` de comando e siga na tela direções para autenticar.

```powershell
Connect-AzAccount
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para iniciar o Azure Cloud Shell, inicie sessão para o [portal do Azure](https://portal.azure.com).

Para iniciar sessão na sua instalação local da CLI, execute o [início de sessão az](/cli/azure/reference-index#az-login) comando:

```cli
az login
```

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

N/A

---

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Agora, está pronto para criar a sua conta de armazenamento.

Cada conta de armazenamento tem de pertencer a um grupo de recursos do Azure. Um grupo de recursos é um contentor lógico para agrupar os seus serviços do Azure. Quando cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou utilizar um grupo de recursos existente. Este artigo mostra como criar um novo grupo de recursos.

Uma conta de armazenamento **v2 para fins gerais** concede acesso a todos os serviços de Armazenamento do Azure: blobs, ficheiros, filas, tabelas e discos. As etapas descritas aqui criam uma conta de armazenamento para fins gerais v2, mas os passos para criar qualquer tipo de conta de armazenamento são semelhantes.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Primeiro, crie um novo grupo de recursos com o PowerShell através da [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) comando:

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Se não tiver a certeza de qual a região a especificar para o `-Location` parâmetro, pode obter uma lista de regiões suportadas para a sua subscrição com o [Get-AzLocation](/powershell/module/az.resources/get-azlocation) comando:

```powershell
Get-AzLocation | select Location
$location = "westus"
```

Em seguida, criar uma conta de armazenamento para fins gerais v2 com armazenamento georredundante com acesso de leitura (RA-GRS) com o [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) comando. Lembre-se de que o nome da conta de armazenamento tem de ser exclusivo em todo o Azure, por isso, substitua o valor de marcador de posição entre parênteses Retos seu próprio valor exclusivo:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2 
```

Para criar uma conta de armazenamento para fins gerais v2 com uma opção de replicação diferente, substitua o valor desejado na tabela a seguir para o **SkuName** parâmetro.

|Opção de replicação  |Parâmetro SkuName  |
|---------|---------|
|Armazenamento localmente redundante (LRS)     |Standard_LRS         |
|Armazenamento com redundância entre zonas (ZRS)     |Standard_ZRS         |
|Armazenamento georredundante (GRS)     |Standard_GRS         |
|Armazenamento georredundante com acesso de leitura (GRS)     |Standard_RAGRS         |

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

Em seguida, criar uma conta de armazenamento para fins gerais v2 com armazenamento georredundante com acesso de leitura com o [criar conta de armazenamento az](/cli/azure/storage/account#az_storage_account_create) comando. Lembre-se de que o nome da conta de armazenamento tem de ser exclusivo em todo o Azure, por isso, substitua o valor de marcador de posição entre parênteses Retos seu próprio valor exclusivo:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

Para criar uma conta de armazenamento para fins gerais v2 com uma opção de replicação diferente, substitua o valor desejado na tabela a seguir para o **sku** parâmetro.

|Opção de replicação  |parâmetro de sku  |
|---------|---------|
|Armazenamento localmente redundante (LRS)     |Standard_LRS         |
|Armazenamento com redundância entre zonas (ZRS)     |Standard_ZRS         |
|Armazenamento georredundante (GRS)     |Standard_GRS         |
|Armazenamento georredundante com acesso de leitura (GRS)     |Standard_RAGRS         |

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

Pode utilizar o Azure Powershell ou a CLI do Azure para implementar um modelo do Resource Manager para criar uma conta de armazenamento. O modelo utilizado neste artigo que mostra como é a partir [modelos de início rápido do Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Para executar os scripts, selecione **experimente** para abrir o Azure Cloud shell. Colar o script, o shell com o botão direito e, em seguida, selecione **colar**.

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

Para saber como criar modelos, veja:

- [Documentação do Azure Resource Manager](/azure/azure-resource-manager/).
- [Referência de modelo de conta de armazenamento](/azure/templates/microsoft.storage/allversions).
- [Exemplos de modelos de conta de armazenamento adicional](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

Para obter mais informações sobre as opções de replicação disponíveis, veja [Opções de replicação do Azure](storage-redundancy.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se quiser limpar os recursos criados por este artigo de procedimento, pode eliminar o grupo de recursos. Ao eliminar o grupo de recursos também elimina a conta de armazenamento associada e quaisquer outros recursos associados ao grupo de recursos.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Para remover um grupo de recursos através do portal do Azure:

1. No portal do Azure, expanda o menu no lado esquerdo para abrir o menu de serviços e escolha **Grupos de Recursos**, para apresentar a lista dos seus grupos de recursos.
2. Encontre o grupo de recursos a eliminar e clique com o botão direito do rato em **Mais** (**...** ) no lado direito da lista.
3. Selecione **Eliminar grupo de recursos** e confirme.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Para remover o grupo de recursos e respetivos recursos associados, incluindo a nova conta de armazenamento, utilize o [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) comando:

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para remover o grupo de recursos e os respetivos recursos associados, incluindo a nova conta de armazenamento, utilize o comando [eliminação do grupo az](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name storage-resource-group
```

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

Para remover o grupo de recursos e respetivos recursos associados, incluindo a nova conta de armazenamento, utilize o Azure PowerShell ou a CLI do Azure.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>Passos Seguintes

Neste artigo de procedimentos, acabou de criar uma conta de armazenamento standard para fins gerais v2. Para saber como carregar e transferir blobs para e da sua conta de armazenamento, avance para um dos inícios rápidos de armazenamento de Blobs.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

> [!div class="nextstepaction"]
> [Trabalhar com blobs ao utilizar o portal do Azure](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

> [!div class="nextstepaction"]
> [Trabalhar com blobs ao utilizar o PowerShell](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Trabalhar com blobs com a CLI do Azure](../blobs/storage-quickstart-blobs-cli.md)

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

> [!div class="nextstepaction"]
> [Trabalhar com blobs ao utilizar o portal do Azure](../blobs/storage-quickstart-blobs-portal.md)

---
