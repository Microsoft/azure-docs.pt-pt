---
title: Criar uma partilha de ficheiros Azure premium
description: Saiba como criar uma partilha de ficheiros Azure premium utilizando o portal Azure, o módulo Azure PowerShell ou o Azure CLI.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/26/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 75ba8e1e2037ba8ef249b548dfb38e5fd1618cb2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90564190"
---
# <a name="how-to-create-an-azure-premium-file-share"></a>Como criar uma partilha de ficheiros premium Azure

As ações de ficheiros premium são oferecidas em meios de armazenamento de discos de estado sólido (SSD) e são úteis para cargas de trabalho intensivas em IO, incluindo bases de dados de hospedagem e computação de alto desempenho (HPC). As ações de ficheiro premium estão hospedadas numa conta de armazenamento para fins especiais, chamada de conta FileStorage. As ações de ficheiros premium são projetadas para aplicações de alto desempenho e escala empresarial, proporcionando consistente baixa latência, alta IOPS e altas ações de produção.

Este artigo mostra-lhe como criar este novo tipo de conta utilizando o [portal Azure,](https://portal.azure.com/)o módulo Azure PowerShell e o Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Se pretender utilizar o Azure CLI, [instale a versão mais recente](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- Se pretender utilizar o módulo Azure PowerShell, [instale a versão mais recente](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-4.6.0).

## <a name="create-a-filestorage-storage-account"></a>Criar uma conta de armazenamento de fileStorage

Cada conta de armazenamento tem de pertencer a um grupo de recursos do Azure. Um grupo de recursos é um contentor lógico para agrupar os seus serviços do Azure. Quando cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou utilizar um grupo de recursos existente. As ações de ficheiros premium requerem uma conta FileStorage.

# <a name="portal"></a>[Portal](#tab/azure-portal)

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

Agora está pronto para criar a sua conta de armazenamento.

1. No portal Azure, selecione **Contas de Armazenamento** no menu esquerdo.

    ![Página principal do portal Azure seleciona conta de armazenamento](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. Na janela **Contas de Armazenamento** que é apresentada, escolha **Adicionar**.
1. Selecione a subscrição na qua pretende criar a conta de armazenamento.
1. No campo **Grupo de recursos**, selecione **Criar novo**. Introduza um nome para o novo grupo de recursos, conforme exemplificado na imagem seguinte.

1. A seguir, introduza um nome para a sua conta de armazenamento. O nome que escolher tem de ser exclusivo em todo o Azure. O nome também tem de ter entre 3 e 24 carateres de comprimento e apenas pode incluir números e letras minúsculas.
1. Selecione uma localização para a sua conta de armazenamento ou utilize a localização predefinida.
1. Para o **desempenho** selecione **Premium**.

    Tem de selecionar **Premium** for **FileStorage** para ser uma opção disponível no **dropdown do tipo Conta.**

1. Selecione **tipo de Conta** e escolha O Armazenamento de **Ficheiros.**
1. Deixe **a replicação** definida para o seu valor padrão de **armazenamento localmente redundante (LRS)**.

    ![Como criar uma conta de armazenamento para uma parte de ficheiro premium](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. Selecione **Rever + Criar** para rever as definições de conta de armazenamento e criar a conta.
1. Selecione **Criar**.

Uma vez criado o seu recurso de conta de armazenamento, navegue até ele.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Utilize o `Connect-AzAccount` comando e siga as instruções no ecrã para autenticar.

```powershell
Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar um novo grupo de recursos com o PowerShell, utilize o comando [New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-storage-account"></a>Criar uma conta de armazenamento de fileStorage

Para criar uma conta de armazenamento de filestorage da PowerShell, utilize o comando [New-AzStorageAccount:](/powershell/module/az.storage/New-azStorageAccount)

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para iniciar a Azure Cloud Shell, inscreva-se no [portal Azure](https://portal.azure.com).

Se quiser iniciar sessão na instalação local do CLI, certifique-se de que tem a versão mais recente e, em seguida, inicie sessão:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar um novo grupo de recursos com a CLI do Azure, utilize o comando [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-storage-account"></a>Criar uma conta de armazenamento de fileStorage

Para criar uma conta de armazenamento FileStorage a partir do CLI Azure, utilize o comando de criação de [conta de armazenamento az.](/cli/azure/storage/account)

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Obter a chave da conta de armazenamento

As chaves da conta de armazenamento controlam o acesso aos recursos numa conta de armazenamento, neste artigo, utilizamos a chave para criar uma partilha de ficheiros premium. As chaves são criadas automaticamente quando cria uma conta de armazenamento. Pode obter as chaves da conta de armazenamento para a mesma com o comando [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```
---

## <a name="create-a-premium-file-share"></a>Criar uma partilha de ficheiros premium

Agora que criou uma conta FileStorage, pode criar uma parte de ficheiro premium dentro dessa conta de armazenamento.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No menu esquerdo para a conta de armazenamento, percorra a secção **de serviço de Ficheiros** e, em seguida, selecione **Ficheiros**.
1. Selecione **a partilha de ficheiros** para criar uma partilha de ficheiros premium.
1. Introduza um nome e uma quota desejada para a sua partilha de ficheiros e, em seguida, **selecione Criar**.

> [!NOTE]
> As dimensões das ações provisidas são especificadas pela quota de ações, as ações de ficheiro são faturadas na dimensão prevista. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage/files/).

   ![Criar uma partilha de ficheiros premium](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para criar uma partilha de ficheiros premium com o módulo Azure PowerShell, utilize o cmdlet [New-AzStorageShare.](/powershell/module/az.storage/New-AzStorageShare)

> [!NOTE]
> As dimensões das ações provisidas são especificadas pela quota de ações, as ações de ficheiro são faturadas na dimensão prevista. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage/files/).

```powershell
New-AzStorageShare `
   -Name myshare `
   -EnabledProtocol SMB `
   -Context $storageAcct.Context
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para criar uma partilha de ficheiros premium com o Azure CLI, utilize o comando [de partilha de armazenamento az.](/cli/azure/storage/share)

> [!NOTE]
> As dimensões das ações provisidas são especificadas pela quota de ações, as ações de ficheiro são faturadas na dimensão prevista. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage/files/).

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --enabled-protocol SMB \
    --name "myshare" 
```
---

## <a name="clean-up-resources"></a>Limpar os recursos

# <a name="portal"></a>[Portal](#tab/azure-portal)

Se quiser limpar os recursos criados neste artigo, elimine o grupo de recursos. A eliminação do grupo de recursos também elimina a conta de armazenamento associada, bem como quaisquer outros recursos associados ao grupo de recursos.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Se quiser limpar os recursos criados neste artigo, elimine o grupo de recursos. A eliminação do grupo de recursos também elimina a conta de armazenamento associada, bem como quaisquer outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos e os seus recursos associados, incluindo a nova conta de armazenamento, utilize o comando [Remove-AzResourceGroup:](/powershell/module/az.resources/remove-azresourcegroup) 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Se quiser limpar os recursos criados neste artigo, elimine o grupo de recursos. A eliminação do grupo de recursos também elimina a conta de armazenamento associada, bem como quaisquer outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos e os respetivos recursos associados, incluindo a nova conta de armazenamento, utilize o comando [eliminação do grupo az](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```
---

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou uma parte de ficheiro premium. Para saber o desempenho que esta conta oferece, continue para a secção de desempenho do guia de planeamento.

> [!div class="nextstepaction"]
> [Níveis de partilha de ficheiros](storage-files-planning.md#storage-tiers)
