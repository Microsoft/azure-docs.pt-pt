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
ms.openlocfilehash: 6c611d65c30cceba5fd6ff409ef71b906cd8674c
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94330313"
---
# <a name="enable-and-create-large-file-shares"></a>Ativar e criar grandes ações de ficheiros

Quando ativa grandes ações de ficheiros na sua conta de armazenamento, as suas ações de ficheiros podem escalar até 100 TiB, ao mesmo tempo que aumenta o IOPS e os limites de produção para as ações padrão. Também pode ativar este dimensionamento nas suas contas de armazenamento existentes para as suas ações de ficheiros existentes. Consulte [os alvos de partilha de ficheiros e escala de ficheiros](storage-files-scale-targets.md#azure-files-scale-targets) para obter mais detalhes. 

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Se pretender utilizar o Azure CLI, [instale a versão mais recente](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- Se pretender utilizar o módulo Azure PowerShell, [instale a versão mais recente](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-4.6.0).

## <a name="restrictions"></a>Restrições

Por enquanto, só pode utilizar armazenamento localmente redundante (LRS) ou armazenamento redundante de zona (ZRS) em grandes contas ativadas por ficheiros. Não é possível utilizar armazenamento redundante em zona geo-zona (GZRS), armazenamento geo-redundante (GRS), armazenamento geo-redundante de acesso à leitura (RA-GRS) ou armazenamento de zonas de acesso de leitura (RA-GZRS).

Permitir grandes ações de ficheiros numa conta é um processo irreversível. Depois de o ativar, não poderá converter a sua conta em GZRS, GRS, RA-GRS ou RA-GZRS.

## <a name="create-a-new-storage-account"></a>Criar uma nova conta de armazenamento

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No portal Azure, selecione **Todos os serviços**. 
1. Na lista de recursos, **insira contas de armazenamento.** À medida que escreve, a lista filtra-se com base na sua entrada. Selecione **Contas de Armazenamento**.
1. Na janela **'Contas de Armazenamento'** que aparece, selecione **Adicionar**.
1. Selecione a subscrição que utilizará para criar a conta de armazenamento.
1. No campo **Grupo de recursos** , selecione **Criar novo**. Insira um nome para o seu novo grupo de recursos.

    ![Captura de ecrã que mostra como criar um grupo de recursos no portal](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. A seguir, introduza um nome para a sua conta de armazenamento. O nome deve ser único em Azure. O nome também deve ter 3 a 24 caracteres de comprimento, e só pode ter números e letras minúsculas.
1. Selecione um local para a sua conta de armazenamento.
1. Deslote a replicação para **o armazenamento localmente redundante** ou **para o armazenamento redundante da zona.**
1. Deixe estes campos nos seus valores predefinidos:

   |Campo  |Valor  |
   |---------|---------|
   |Modelo de implementação     |Resource Manager         |
   |Desempenho     |Standard         |
   |Tipo de conta     |StorageV2 (fins gerais v2)         |
   |Camada de acesso     |Frequente         |

1. Selecione **Advanced** e, em seguida, selecione o botão de opção **Ativado** à direita das **grandes partilhas** de ficheiros .
1. Selecione **Rever + Criar** para rever as definições de conta de armazenamento e criar a conta.

    ![Screenshot com o botão de opção "ativado" numa nova conta de armazenamento no portal Azure](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Selecione **Criar**.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Em primeiro lugar, [instale a versão mais recente do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para que possa ativar grandes partilhas de ficheiros.

Para criar uma conta de armazenamento com grandes ações de ficheiro ativadas, utilize o seguinte comando. `<yourStorageAccountName>`Substitua, e pela sua `<yourResourceGroup>` `<yourDesiredRegion>` informação.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Em primeiro lugar, [instale a versão mais recente do PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) para que possa ativar grandes partilhas de ficheiros.

Para criar uma conta de armazenamento com grandes ações de ficheiro ativadas, utilize o seguinte comando. `<yourStorageAccountName>`Substitua, e pela sua `<yourResourceGroup>` `<yourDesiredRegion>` informação.

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```
---

## <a name="enable-large-files-shares-on-an-existing-account"></a>Ativar ações de grandes ficheiros numa conta existente

Também pode ativar grandes ações de ficheiros nas suas contas existentes. Se ativar grandes ações de ficheiros, não poderá converter-se em GZRS, GRS, RA-GRS ou RA-GZRS. Permitir grandes ações de ficheiros é irreversível nesta conta de armazenamento.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Abra o [portal Azure](https://portal.azure.com)e vá à conta de armazenamento onde pretende ativar grandes partilhas de ficheiros.
1. Abra a conta de armazenamento e **selecione Configuração**.
1. Selecione **Ativado** em **grandes partilhas de ficheiros** e, em seguida, selecione **Guardar**.
1. Selecione **Overview** e selecione **Refresh**.

![Selecionando o botão de opção Ativado numa conta de armazenamento existente no portal Azure](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

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
