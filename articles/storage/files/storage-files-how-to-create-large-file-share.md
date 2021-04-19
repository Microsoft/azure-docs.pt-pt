---
title: Ativar e criar grandes partilhas de ficheiros - Azure Files
description: Neste artigo, aprende-se a ativar e a criar grandes partilhas de ficheiros.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/29/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 20f9aaf73fe0cb30b136254d57e6c9b960c16af4
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717005"
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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Em primeiro lugar, [instale a versão mais recente do Azure CLI](/cli/azure/install-azure-cli) para que possa ativar grandes partilhas de ficheiros.

Para criar uma conta de armazenamento com grandes ações de ficheiro ativadas, utilize o seguinte comando. `<yourStorageAccountName>`Substitua, e pela sua `<yourResourceGroup>` `<yourDesiredRegion>` informação.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Em primeiro lugar, [instale a versão mais recente do PowerShell](/powershell/azure/install-az-ps) para que possa ativar grandes partilhas de ficheiros.

Para criar uma conta de armazenamento com grandes ações de ficheiro ativadas, utilize o seguinte comando. `<yourStorageAccountName>`Substitua, e pela sua `<yourResourceGroup>` `<yourDesiredRegion>` informação.

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```
---

## <a name="enable-large-files-shares-on-an-existing-account"></a>Ativar ações de grandes ficheiros numa conta existente

Também pode ativar grandes ações de ficheiros nas suas contas existentes. Se ativar grandes ações de ficheiros, não poderá converter-se em GZRS, GRS, RA-GRS ou RA-GZRS. Permitir grandes ações de ficheiros é irreversível nesta conta de armazenamento.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Abra o [portal Azure](https://portal.azure.com)e navegue para a conta de armazenamento onde pretende ativar grandes partilhas de ficheiros.
1. Abra a conta de armazenamento e selecione **ações de arquivo.**
1. Selecione **Ativado** em **grandes partilhas de ficheiros** e, em seguida, selecione **Guardar**.
1. Selecione **Overview** e selecione **Refresh**.
1. Selecione **A capacidade de partilha** e, em seguida, selecione **100 TiB** e **Guarde**.

    :::image type="content" source="media/storage-files-how-to-create-large-file-share/files-enable-large-file-share-existing-account.png" alt-text="Screenshot da conta de armazenamento azul, corte de partilha de ficheiros com 100 ações tib destacadas.":::

Agora ativou grandes ações de ficheiros na sua conta de armazenamento. Em seguida, deve [atualizar a quota de ações existente](#expand-existing-file-shares) para aproveitar o aumento da capacidade e da escala.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para ativar grandes ações de ficheiros na sua conta existente, utilize o seguinte comando. Substitua `<yourStorageAccountName>` e `<yourResourceGroup>` pela sua informação.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

Agora ativou grandes ações de ficheiros na sua conta de armazenamento. Em seguida, deve [atualizar a quota de ações existente](#expand-existing-file-shares) para aproveitar o aumento da capacidade e da escala.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para ativar grandes ações de ficheiros na sua conta existente, utilize o seguinte comando. Substitua `<yourStorageAccountName>` e `<yourResourceGroup>` pela sua informação.

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

Agora ativou grandes ações de ficheiros na sua conta de armazenamento. Em seguida, deve [atualizar a quota de ações existente](#expand-existing-file-shares) para aproveitar o aumento da capacidade e da escala.

---

## <a name="create-a-large-file-share"></a>Criar uma grande partilha de ficheiros

Depois de ter ativado grandes ações de ficheiros na sua conta de armazenamento, pode criar ações de ficheiros nessa conta com quotas mais elevadas. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Criar uma grande partilha de ficheiros é quase idêntico à criação de uma partilha de ficheiros padrão. A principal diferença é que pode definir uma quota até 100 TiB.

1. A partir da sua conta de armazenamento, selecione **ações de ficheiros.**
1. Selecione **+ Partilha de ficheiros**.
1. Insira um nome para a sua parte do ficheiro. Também pode definir o tamanho da quota que você gostaria, até 100 TiB. Em seguida, selecione **Criar**. 

![O portal Azure UI mostrando as caixas de nome e quota](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para criar uma grande partilha de ficheiros, utilize o seguinte comando. `<yourStorageAccountName>`Substitua, e pela sua `<yourStorageAccountKey>` `<yourFileShareName>` informação.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para criar uma grande partilha de ficheiros, utilize o seguinte comando. `<YourStorageAccountName>`Substitua, e pela sua `<YourStorageAccountKey>` `<YourStorageAccountFileShareName>` informação.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```
---

## <a name="expand-existing-file-shares"></a>Expandir as ações de ficheiros existentes

Depois de ter ativado grandes ações de ficheiros na sua conta de armazenamento, também pode expandir as ações de ficheiros existentes nessa conta para a quota mais elevada. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. A partir da sua conta de armazenamento, selecione **ações de ficheiros.**
1. Clique com o botão direito na sua partilha de ficheiros e, em seguida, **selecione Quota**.
1. Introduza o novo tamanho que pretende e, em seguida, selecione **OK**.

![O portal Azure UI com quota de ações de ficheiros existentes](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para fixar a quota para o tamanho máximo, utilize o seguinte comando. `<yourStorageAccountName>`Substitua, e pela sua `<yourStorageAccountKey>` `<yourFileShareName>` informação.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para fixar a quota para o tamanho máximo, utilize o seguinte comando. `<YourStorageAccountName>`Substitua, e pela sua `<YourStorageAccountKey>` `<YourStorageAccountFileShareName>` informação.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```
---

## <a name="next-steps"></a>Passos seguintes

* [Conecte e monte uma partilha de ficheiros no Windows](storage-how-to-use-files-windows.md)
* [Conecte e monte uma partilha de ficheiros no Linux](storage-how-to-use-files-linux.md)
* [Conecte e monte uma partilha de ficheiros no macOS](storage-how-to-use-files-mac.md)