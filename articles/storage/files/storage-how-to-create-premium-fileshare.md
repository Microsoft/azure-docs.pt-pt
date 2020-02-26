---
title: Criar uma partilha de ficheiros Azure premium
description: Neste artigo, aprende-se a criar uma partilha de ficheiros Azure premium.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 237846ec3adda208126aeb22e7900cbf5118ee95
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598668"
---
# <a name="how-to-create-an-premium-azure-file-share"></a>Como criar uma partilha de ficheiros Azure premium
As ações de ficheiros premium são oferecidas em meios de armazenamento de discos de estado sólido (SSD) e são úteis para cargas de trabalho intensivas em IO, incluindo bases de dados de hospedagem e computação de alto desempenho (HPC). As ações de ficheiropremium são hospedadas numa conta de armazenamento de finalidade especial, chamada conta FileStorage. As ações de ficheiros premium são projetadas para aplicações de alto desempenho e escala empresarial, proporcionando latência consistente baixa, iOPS elevados e altas ações de rendimento.

Este artigo mostra-lhe como criar este novo tipo de conta utilizando o [portal Azure,](https://portal.azure.com/)O Azure PowerShell e o Azure CLI.

## <a name="prerequisites"></a>Pré-requisitos

Para aceder aos recursos do Azure, incluindo ações de ficheiros Premium Azure, vai precisar de uma subscrição Azure. Se ainda não tem uma subscrição, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Criar uma partilha de ficheiropremium usando o portal Azure

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

### <a name="create-a-filestorage-storage-account"></a>Criar uma conta de armazenamento de ficheiros

Agora está pronto para criar a sua conta de armazenamento.

Cada conta de armazenamento tem de pertencer a um grupo de recursos do Azure. Um grupo de recursos é um contentor lógico para agrupar os seus serviços do Azure. Quando cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou utilizar um grupo de recursos existente. Este artigo mostra como criar um novo grupo de recursos.

1. No portal Azure, selecione Contas de **Armazenamento** no menu esquerdo.

    ![Página principal do portal Azure seleciona conta de armazenamento](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. Na janela **Contas de Armazenamento** que é apresentada, escolha **Adicionar**.
1. Selecione a subscrição na qua pretende criar a conta de armazenamento.
1. No campo **Grupo de recursos**, selecione **Criar novo**. Introduza um nome para o novo grupo de recursos, conforme exemplificado na imagem seguinte.

1. A seguir, introduza um nome para a sua conta de armazenamento. O nome que escolher tem de ser exclusivo em todo o Azure. O nome também tem de ter entre 3 e 24 carateres de comprimento e apenas pode incluir números e letras minúsculas.
1. Selecione uma localização para a sua conta de armazenamento ou utilize a localização predefinida.
1. Para **performance** selecione **Premium**.
1. Selecione **o tipo de conta** e escolha **fileStorage**.
1. Deixar a **replicação** definida ao seu valor padrão de **armazenamento redundante localmente (LRS)** .

    ![Como criar uma conta de armazenamento para uma parte de ficheiro premium](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. Selecione **Rever + Criar** para rever as definições de conta de armazenamento e criar a conta.
1. Selecione **Criar**.

Uma vez criado o seu recurso de conta de armazenamento, navegue para ele.

### <a name="create-a-premium-file-share"></a>Criar uma partilha de ficheiros premium

1. No menu esquerdo para a conta de armazenamento, desloque-se para a secção de **serviço de ficheiros** e, em seguida, selecione **Ficheiros**.
1. Selecione **a partilha de ficheiros** para criar uma partilha de ficheiro premium.
1. Introduza um nome e uma quota desejada para a sua parte de ficheiro e, em seguida, selecione **Criar**.

> [!NOTE]
> As dimensões das ações previstas são especificadas pela quota de ações, as ações de ficheiro são faturadas no tamanho previsto, consulte a página de [preços](https://azure.microsoft.com/pricing/details/storage/files/) para obter mais detalhes.

   ![Criar uma partilha de ficheiros premium](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>Limpar recursos

Se quiser limpar os recursos criados neste artigo, pode simplesmente apagar o grupo de recursos. A eliminação do grupo de recursos também elimina a conta de armazenamento associada, bem como quaisquer outros recursos associados ao grupo de recursos.

## <a name="create-a-premium-file-share-using-powershell"></a>Criar uma partilha de ficheiro premium usando o PowerShell

### <a name="create-an-account-using-powershell"></a>Criar uma conta com o PowerShell

Em primeiro lugar, instale a versão mais recente do módulo [PowerShellGet.](/powershell/scripting/gallery/installing-psget)

Em seguida, atualize o seu módulo PowerShell, inscreva-se na sua subscrição Azure, crie um grupo de recursos e, em seguida, crie uma conta de armazenamento.

### <a name="upgrade-your-powershell-module"></a>Atualize o seu módulo PowerShell

Para interagir com uma partilha de ficheiropremium a partir da PowerShell, terá de instalar uma versão 1.4.0 do módulo Az.Storage, ou o mais recente módulo dez.Storage.

Comece por abrir uma sessão do PowerShell com permissões elevadas.

Instale o módulo Az.Storage:

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Inscreva-se na sua Subscrição Azure

Utilize o comando `Connect-AzAccount` e siga as instruções no ecrã para autenticar.

```powershell
Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos:

Para criar um novo grupo de recursos com a PowerShell, utilize o comando [New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-storage-account"></a>Criar uma conta de armazenamento de FileStorage

Para criar uma conta de armazenamento de FileStorage a partir da PowerShell, utilize o comando [New-AzStorageAccount:](/powershell/module/az.storage/New-azStorageAccount)

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>Criar uma partilha de ficheiros premium

Agora que tem uma conta FileStorage, pode criar uma parte de ficheiro premium. Utilize o cmdlet [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) para criar um.

> [!NOTE]
> As dimensões das ações previstas são especificadas pela quota de ações, as ações de ficheiro são faturadas no tamanho previsto, consulte a página de [preços](https://azure.microsoft.com/pricing/details/storage/files/) para obter mais detalhes.

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

### <a name="clean-up-resources"></a>Limpar recursos

Para remover o grupo de recursos e os seus recursos associados, incluindo a nova conta de armazenamento, utilize o comando [Remove-AzResourceGroup:](/powershell/module/az.resources/remove-azresourcegroup) 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-a-premium-file-share-using-azure-cli"></a>Criar uma partilha de ficheiropremium usando o Azure CLI

Para iniciar a Casca de Nuvem Azure, inscreva-se no [portal Azure.](https://portal.azure.com)

Se pretender iniciar sessão na sua instalação local do CLI, certifique-se primeiro de que tem a versão mais recente e, em seguida, execute o comando de login:

```cli
az login
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos:

Para criar um novo grupo de recursos com a CLI do Azure, utilize o comando [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-storage-account"></a>Criar uma conta de armazenamento de FileStorage

Para criar uma conta de armazenamento fileStorage a partir do Azure CLI, utilize a conta de [armazenamento az criar](/cli/azure/storage/account) comando.

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Obter a chave da conta de armazenamento

As chaves da conta de armazenamento controlam o acesso aos recursos numa conta de armazenamento, neste artigo, usamos a chave para criar uma partilha de ficheiropremium. As chaves são criadas automaticamente quando cria uma conta de armazenamento. Pode obter as chaves da conta de armazenamento para a mesma com o comando [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>Criar uma partilha de ficheiros premium

Agora que tem uma conta de armazenamento de ficheiros, pode criar uma parte de ficheiro premium. Use a [parte de armazenamento az criar](/cli/azure/storage/share) comando para criar um.

> [!NOTE]
> As dimensões das ações previstas são especificadas pela quota de ações, as ações de ficheiro são faturadas no tamanho previsto, consulte a página de [preços](https://azure.microsoft.com/pricing/details/storage/files/) para obter mais detalhes.

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

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou uma quota de ficheiro premium. Para conhecer o desempenho que esta conta oferece, continue até à secção de nível de desempenho do guia de planeamento.

> [!div class="nextstepaction"]
> [Níveis de partilha de ficheiros](storage-files-planning.md#storage-tiers)