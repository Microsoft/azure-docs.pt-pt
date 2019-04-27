---
title: Criar uma conta de armazenamento de ficheiros do Azure premium
description: Neste artigo, saiba como criar uma conta de armazenamento de ficheiros do Azure premium e uma partilha de ficheiros de premium.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 72dec14dde47580313e57bb3b8d7315604929277
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844547"
---
# <a name="how-to-create-an-azure-premium-file-share"></a>Como criar uma partilha de ficheiros do Azure premium

O tipo de conta de armazenamento FileStorage (pré-visualização) representa uma nova camada para ficheiros do Azure, permitindo-lhe criar partilhas de ficheiros com características de desempenho premium. Estas partilhas de ficheiros foram concebidas para aplicativos de escala empresarial, fornecendo partilhas de alto débito, IOPS elevado e baixa latência consistente e de elevado desempenho.

Este artigo mostra-lhe como criar esta nova com tipo de conta [portal do Azure](https://portal.azure.com/), o Azure PowerShell e CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para aceder ao armazenamento do Azure, terá uma subscrição do Azure. Se ainda não tiver uma subscrição, em seguida, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Criar uma partilha de ficheiros de premium com o portal do Azure

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

### <a name="create-a-filestorage-preview-storage-account"></a>Criar uma conta de armazenamento FileStorage (pré-visualização)

Agora, está pronto para criar a sua conta de armazenamento.

Cada conta de armazenamento tem de pertencer a um grupo de recursos do Azure. Um grupo de recursos é um contentor lógico para agrupar os seus serviços do Azure. Quando cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou utilizar um grupo de recursos existente. Este artigo mostra como criar um novo grupo de recursos.

1. No portal do Azure, selecione **contas de armazenamento** no menu da esquerda.

    ![Página principal do portal do Azure selecione a conta de armazenamento](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. Na janela **Contas de Armazenamento** que é apresentada, escolha **Adicionar**.
1. Selecione a subscrição na qua pretende criar a conta de armazenamento.
1. No campo **Grupo de recursos**, selecione **Criar novo**. Introduza um nome para o novo grupo de recursos, conforme exemplificado na imagem seguinte.

1. A seguir, introduza um nome para a sua conta de armazenamento. O nome que escolher tem de ser exclusivo em todo o Azure. O nome também tem de ter entre 3 e 24 carateres de comprimento e apenas pode incluir números e letras minúsculas.
1. Selecione uma localização para a sua conta de armazenamento ou utilize a localização predefinida.
1. Para **desempenho** selecionar **Premium**.
1. Selecione **tipo de conta** e escolha **FileStorage (pré-visualização)**.
1. Deixe **replicação** definido como seu valor padrão **armazenamento localmente redundante (LRS)**.

    ![Como criar uma conta de armazenamento de ficheiros de premium](media/storage-how-to-create-premium-fileshare/premium-files-storage-account.png)

1. Selecione **Rever + Criar** para rever as definições de conta de armazenamento e criar a conta.
1. Selecione **Criar**.

Quando o recurso de conta de armazenamento tiver sido criado, navegar até ele.

### <a name="create-a-premium-file-share"></a>Criar uma partilha de ficheiros premium

1. No menu da esquerda para a conta de armazenamento, desloque-se para o **serviço de ficheiros** secção, em seguida, selecione **ficheiros (pré-visualização)**.
1. Selecione **+ partilha de ficheiros** para criar uma partilha de ficheiros de premium.
1. Introduza um nome e uma quota pretendida para a partilha de ficheiros, em seguida, selecione **criar**.

> [!NOTE]
> Tamanhos de partilha aprovisionado é especificado pela quota de partilha, partilhas de ficheiros são cobradas no tamanho aprovisionado, consulte a [página de preços](https://azure.microsoft.com/pricing/details/storage/files/) para obter mais detalhes.

   ![Criar uma partilha de ficheiros premium](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>Limpar recursos

Se quiser limpar os recursos criados neste artigo, basta eliminar o grupo de recursos. Eliminar o grupo de recursos também elimina a conta de armazenamento associado, bem como quaisquer outros recursos associados ao grupo de recursos.

## <a name="create-a-premium-file-share-using-powershell"></a>Criar uma partilha de ficheiros de premium com o PowerShell

### <a name="create-an-account-using-powershell"></a>Criar uma conta com o PowerShell

Em primeiro lugar, instale a versão mais recente dos [PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget) módulo.

Em seguida, atualizar o módulo do powershell, inicie sessão na sua subscrição do Azure, crie um grupo de recursos e, em seguida, criar uma conta de armazenamento.

### <a name="upgrade-your-powershell-module"></a>Atualizar o módulo do powershell

Para interagir com os ficheiros de premium com o PowerShell, terá de instalar o módulo de Az.Storage mais recente.

Comece por abrir uma sessão do PowerShell com permissões elevadas.

Instale o módulo de Az.Storage:

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowPrerelease -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Inicie sessão na sua subscrição do Azure

Utilize o `Login-AzAccount` de comando e siga na tela direções para autenticar.

```powershell
Login-AzAccount
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar um novo grupo de recursos com o PowerShell, utilize o [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) comando:

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-preview-storage-account"></a>Criar uma conta de armazenamento filestorage (pré-visualização)

Para criar uma conta de armazenamento FileStorage (pré-visualização) do PowerShell, utilize o [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) comando:

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>Criar uma partilha de ficheiros premium

Agora que tem uma conta de FileStorage, pode criar uma partilha de ficheiros de premium. Utilize o [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) cmdlet para criar um.

> [!NOTE]
> Tamanhos de partilha aprovisionado é especificado pela quota de partilha, partilhas de ficheiros são cobradas no tamanho aprovisionado, consulte a [página de preços](https://azure.microsoft.com/pricing/details/storage/files/) para obter mais detalhes.

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

### <a name="clean-up-resources"></a>Limpar recursos

Para remover o grupo de recursos e respetivos recursos associados, incluindo a nova conta de armazenamento, utilize o [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) comando: 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-a-premium-file-share-using-azure-cli"></a>Criar uma partilha de ficheiros de premium com a CLI do Azure

Para iniciar o Azure Cloud Shell, inicie sessão para o [portal do Azure](https://portal.azure.com).

Se quiser iniciar sessão na sua instalação local da CLI, execute o comando de início de sessão:

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-premium-files"></a>Adicionar a extensão da CLI para os ficheiros do Azure premium

Para interagir com os ficheiros de premium com a CLI, terá de adicionar uma extensão ao seu shell.

Para tal, introduza o seguinte comando ao utilizar o Cloud Shell ou um shell local: `az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar um novo grupo de recursos com a CLI do Azure, utilize o comando [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-preview-storage-account"></a>Criar uma conta de armazenamento FileStorage (pré-visualização)

Para criar uma conta de armazenamento FileStorage (pré-visualização) a partir da CLI do Azure, utilize o [criar conta de armazenamento az](/cli/azure/storage/account) comando.

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Obter a chave da conta de armazenamento

Chaves de conta de armazenamento controlam o acesso aos recursos numa conta de armazenamento, neste artigo, vamos utilizar a chave para criar uma partilha de ficheiros de premium. As chaves são criadas automaticamente quando cria uma conta de armazenamento. Pode obter as chaves da conta de armazenamento para a mesma com o comando [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>Criar uma partilha de ficheiros premium

Agora que tem uma conta de FileStorage, pode criar uma partilha de ficheiros de premium. Utilize o [criar partilha de armazenamento az](/cli/azure/storage/share) comando para criar um.

> [!NOTE]
> Tamanhos de partilha aprovisionado é especificado pela quota de partilha, partilhas de ficheiros são cobradas no tamanho aprovisionado, consulte a [página de preços](https://azure.microsoft.com/pricing/details/storage/files/) para obter mais detalhes.

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

### <a name="clean-up-resources"></a>Limpar recursos

Para remover o grupo de recursos e os respetivos recursos associados, incluindo a nova conta de armazenamento, utilize o comando [eliminação do grupo az](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, criou uma conta de armazenamento de ficheiros de premium. Para saber mais sobre o desempenho que oferece esta conta, avance para a secção de escalão de desempenho do guia de planeamento.

> [!div class="nextstepaction"]
> [Escalões de desempenho de partilha de ficheiros](storage-files-planning.md#file-share-performance-tiers)