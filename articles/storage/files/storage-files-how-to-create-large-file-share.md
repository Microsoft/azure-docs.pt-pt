---
title: Ativar e criar grandes partilhas de ficheiros - Azure Files
description: Neste artigo, aprende-se a ativar e a criar grandes partilhas de ficheiros.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/20/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a53f964020583c41e2400d97ad244bacd33813bc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818265"
---
# <a name="enable-and-create-large-file-shares"></a>Ativar e criar grandes ações de ficheiros
As suas ações de ficheiroS Azure podem aumentar até 100 TiB depois de ativar grandes ações de ficheiros na sua conta de armazenamento. Quando ativa grandes ações de ficheiros, também pode aumentar os limites de IOPS da sua parte de ficheiros e limites de produção. Também pode ativar este dimensionamento nas suas contas de armazenamento existentes para as ações de ficheiros existentes e novas. Para obter detalhes sobre as diferenças de desempenho, consulte [as metas de partilha de ficheiros e escala de ficheiros](storage-files-scale-targets.md#azure-files-scale-targets).

## <a name="prerequisites"></a>Pré-requisitos
- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Se pretender utilizar o Azure CLI, [instale a versão mais recente](/cli/azure/install-azure-cli).
- Se pretender utilizar o módulo Azure PowerShell, [instale a versão mais recente](/powershell/azure/install-az-ps).

## <a name="restrictions"></a>Restrições
Por enquanto, só pode utilizar armazenamento localmente redundante (LRS) ou armazenamento redundante de zona (ZRS) em contas de armazenamento com grandes ações de ficheiros ativadas. Não é possível utilizar armazenamento redundante em zona geo-zona (GZRS), armazenamento geo-redundante (GRS), armazenamento geo-redundante de acesso à leitura (RA-GRS) ou armazenamento de zonas de acesso de leitura (RA-GZRS).

Permitir grandes ações de ficheiros numa conta é um processo irreversível. Depois de o ativar, não poderá converter a sua conta em GZRS, GRS, RA-GRS ou RA-GZRS.

## <a name="create-a-new-storage-account"></a>Criar uma nova conta de armazenamento

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No portal Azure, selecione **Todos os serviços**. 
1. Na lista de recursos, **insira contas de armazenamento.** À medida que escreve, a lista filtra-se com base na sua entrada. Selecione **Contas de Armazenamento**.
1. Na lâmina **de Contas de Armazenamento** que aparece, selecione + **Novo**.
1. Na lâmina básica, preencha as seleções como desejar.
1. Certifique-se de que **o Desempenho** está definido para **Standard**.
1. Definir **redundância** para **armazenamento localmente redundante** ou **armazenamento redundante de zona**.
1. Selecione a lâmina **Avançada** e, em seguida, selecione o botão de opção **Ativado** à direita das **grandes partilhas** de ficheiros .
1. Selecione **Rever + Criar** para rever as definições de conta de armazenamento e criar a conta.
1. Selecione **Criar**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para criar uma conta de armazenamento com grandes ações de ficheiro ativadas, utilize o seguinte comando. `<yourStorageAccountName>`Substitua, e pela sua `<yourResourceGroup>` `<yourDesiredRegion>` informação.

```powershell
# This command creates a large file share–enabled account. It will not support changing the 
# redundancy to GRS, GZRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount `
    -ResourceGroupName <yourResourceGroup> `
    -Name <yourStorageAccountName> `
    -Location <yourDesiredRegion> `
    -SkuName Standard_LRS `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
Para criar uma conta de armazenamento com grandes ações de ficheiro ativadas, utilize o seguinte comando. `<yourStorageAccountName>`Substitua, e pela sua `<yourResourceGroup>` `<yourDesiredRegion>` informação.

```azurecli-interactive
# This command creates a large file share–enabled account. It will not support changing the 
# redundancy to GRS, GZRS, RA-GRS, or RA-GZRS.
az storage account create \
    --name <yourStorageAccountName> \
    -g <yourResourceGroup> \
    -l <yourDesiredRegion> \
    --sku Standard_LRS \
    --kind StorageV2 \
    --enable-large-file-share
```

---

Depois de criar uma grande conta de armazenamento ativada por ficheiros, pode criar uma partilha de ficheiros que tire partido dos limites de grande capacidade e escala. Para obter mais informações sobre como criar contas de armazenamento e ações de ficheiros Azure, consulte [Criar uma partilha de ficheiros Azure](storage-how-to-create-file-share.md).

## <a name="enable-large-files-shares-on-an-existing-account"></a>Ativar ações de grandes ficheiros numa conta existente
Também pode ativar grandes ações de ficheiros nas suas contas de armazenamento LRS e ZRS existentes. Se tiver uma conta GRS, GZRS, RA-GRS ou RA-GZRS, terá de convertê-la numa conta LRS antes de prosseguir.

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Abra o [portal Azure](https://portal.azure.com)e navegue para a conta de armazenamento onde pretende ativar grandes partilhas de ficheiros.
1. Abra a conta de armazenamento e selecione **ações de arquivo.**
1. Selecione **Ativado** em **grandes partilhas de ficheiros** e, em seguida, selecione **Guardar**.
1. Selecione **Overview** e selecione **Refresh**.
1. Selecione **A capacidade de partilha** e, em seguida, selecione **100 TiB** e **Guarde**.

    :::image type="content" source="media/storage-files-how-to-create-large-file-share/files-enable-large-file-share-existing-account.png" alt-text="Screenshot da conta de armazenamento azul, corte de partilha de ficheiros com 100 ações tib destacadas.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para ativar grandes ações de ficheiros na sua conta existente, utilize o seguinte comando. Substitua `<yourStorageAccountName>` e `<yourResourceGroup>` pela sua informação.

```powershell
Set-AzStorageAccount `
    -ResourceGroupName <yourResourceGroup> `
    -Name <yourStorageAccountName> `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
Para ativar grandes ações de ficheiros na sua conta existente, utilize o seguinte comando. Substitua `<yourStorageAccountName>` e `<yourResourceGroup>` pela sua informação.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

---

Agora ativou grandes ações de ficheiros na sua conta de armazenamento. Em seguida, deve [atualizar a quota de ações existente](#expand-existing-file-shares) para aproveitar o aumento da capacidade e da escala. 

> [!Important]  
> As ações de ficheiros existentes não irão aumentar até aos limites anunciados para grandes ações de ficheiros, a menos que a quota tenha sido alterada.

## <a name="expand-existing-file-shares"></a>Expandir as ações de ficheiros existentes
Depois de ter ativado grandes ações de ficheiros na sua conta de armazenamento, tem de expandir as ações de ficheiros existentes nessa conta de armazenamento para tirar partido do aumento da capacidade e escala. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. A partir da sua conta de armazenamento, selecione **ações de ficheiros.**
1. Clique com o botão direito na sua partilha de ficheiros e, em seguida, **selecione Quota**.
1. Introduza o novo tamanho que pretende e, em seguida, selecione **OK**.

![O portal Azure UI com quota de ações de ficheiros existentes](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para fixar a quota para o tamanho máximo, utilize o seguinte comando. `<YourResourceGroupName>`Substitua, e pela sua `<YourStorageAccountName>` `<YourStorageAccountFileShareName>` informação.

```powershell
$resourceGroupName = "<YourResourceGroupName>"
$storageAccountName = "<YourStorageAccountName>"
$shareName="<YourStorageAccountFileShareName>"

# update quota
Set-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 102400
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
Para fixar a quota para o tamanho máximo, utilize o seguinte comando. `<yourResourceGroupName>`Substitua, e pela sua `<yourStorageAccountName>` `<yourFileShareName>` informação.

```azurecli-interactive
az storage share-rm update \
    --resource-group <yourResourceGroupName> \
    --storage-account <yourStorageAccountName> \
    --name <yourFileShareName> \
    --quota 102400
```

---

## <a name="next-steps"></a>Passos seguintes
* [Conecte e monte uma partilha de ficheiros no Windows](storage-how-to-use-files-windows.md)
* [Conecte e monte uma partilha de ficheiros no Linux](storage-how-to-use-files-linux.md)
* [Conecte e monte uma partilha de ficheiros no macOS](storage-how-to-use-files-mac.md)