---
title: Ativar e criar grandes partilhas de ficheiros - Ficheiros Azure
description: Neste artigo, aprende-se a ativar e criar grandes partilhas de ficheiros.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bd7726d2bbf2830d18d78b5f0b0d7202b734124d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537683"
---
# <a name="enable-and-create-large-file-shares"></a>Ativar e criar grandes partilhas de ficheiros

Quando ativa grandes ações de ficheiros na sua conta de armazenamento, as suas ações de ficheiro podem escalar até 100 TiB. Pode ativar esta escala nas suas contas de armazenamento existentes para as suas partilhas de ficheiros existentes.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Se pretender utilizar o Azure CLI, [instale a versão mais recente.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Se pretende utilizar o Azure PowerShell, [instale a versão mais recente.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

## <a name="restrictions"></a>Restrições

Por enquanto, só pode utilizar armazenamento redundante localmente (LRS) ou armazenamento redundante (ZRS) em contas ativadas por grandes partilhas de ficheiros. Não é possível utilizar armazenamento geo-zona redundante (GZRS), armazenamento geo-redundante (GRS) ou armazenamento geo-redundante de acesso de leitura (RA-GRS).
Permitir grandes ações de ficheiros numa conta é um processo irreversível. Depois de o ativar, não poderá converter a sua conta em GZRS, GRS ou RA-GRS.

## <a name="create-a-new-storage-account"></a>Criar uma nova conta de armazenamento

### <a name="portal"></a>Portal

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No portal Azure, selecione **Todos os serviços.** 
1. Na lista de recursos, insira Contas de **Armazenamento.** À medida que escreve, a lista filtra com base na sua entrada. Selecione **Contas de Armazenamento**.
1. Na janela Contas de **Armazenamento** que aparece, selecione **Adicionar**.
1. Selecione a subscrição que utilizará para criar a conta de armazenamento.
1. No campo **Grupo de recursos**, selecione **Criar novo**. Insira um nome para o seu novo grupo de recursos.

    ![Captura de ecrã que mostra como criar um grupo de recursos no portal](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. A seguir, introduza um nome para a sua conta de armazenamento. O nome deve ser único em Azure. O nome também deve ter 3 a 24 caracteres de comprimento, e só pode ter números e letras minúsculas.
1. Selecione um local para a sua conta de armazenamento.
1. Delineie a replicação para **armazenamento localmente redundante** ou **armazenamento redundante**da zona .
1. Deixe estes campos nos seus valores padrão:

   |Campo  |Valor  |
   |---------|---------|
   |Modelo de implementação     |Resource Manager         |
   |Desempenho     |Standard         |
   |Tipo de conta     |StorageV2 (general-purpose v2)         |
   |Escalão de acesso     |Acesso Frequente         |

1. Selecione **Advanced**, e, em seguida, selecione o botão de opção **Ativado** à direita das **grandes partilhas**de ficheiros .
1. Selecione **Rever + Criar** para rever as definições de conta de armazenamento e criar a conta.

    ![Screenshot com o botão de opção "ativado" numa nova conta de armazenamento no portal Azure](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Selecione **Criar**.

### <a name="cli"></a>CLI

Em primeiro lugar, [instale a versão mais recente do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para que possa ativar grandes partilhas de ficheiros.

Para criar uma conta de armazenamento com grandes partilhas de ficheiros ativadas, utilize o seguinte comando. Substitua, `<yourStorageAccountName>` `<yourResourceGroup>` `<yourDesiredRegion>` e com a sua informação.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Em primeiro lugar, [instale a versão mais recente do PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) para que possa ativar grandes partilhas de ficheiros.

Para criar uma conta de armazenamento com grandes partilhas de ficheiros ativadas, utilize o seguinte comando. Substitua, `<yourStorageAccountName>` `<yourResourceGroup>` `<yourDesiredRegion>` e com a sua informação.

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-large-files-shares-on-an-existing-account"></a>Ativar grandes partilhas de ficheiros numa conta existente

Também pode ativar grandes partilhas de ficheiros nas suas contas existentes. Se ativar grandes ações de ficheiros, não poderá converter-se em GZRS, GRS ou RA-GRS. Permitir grandes ações de ficheiros é irreversível nesta conta de armazenamento.

### <a name="portal"></a>Portal

1. Abra o [portal Azure](https://portal.azure.com)e vá para a conta de armazenamento onde pretende ativar grandes ações de ficheiros.
1. Abra a conta de armazenamento e **selecione Configuração**.
1. Selecione **Ativado** em **grandes partilhas**de ficheiros e, em seguida, selecione **Guardar**.
1. Selecione **visão geral** e selecione **Refresh**.

![Selecionar o botão de opção Ativado numa conta de armazenamento existente no portal Azure](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Agora ativou grandes ações de ficheiros na sua conta de armazenamento. Em seguida, deve atualizar a quota de ações existente para tirar partido do aumento da capacidade e escala.

Se receber a mensagem de erro "As grandes ações de ficheiro ainda não estão disponíveis para a conta", a sua região poderá estar a meio de concluir o seu lançamento. Suporte de contato se tiver necessidade urgente de grandes ações de ficheiros.

### <a name="cli"></a>CLI

Para permitir grandes partilhas de ficheiros na sua conta existente, utilize o seguinte comando. `<yourStorageAccountName>` Substitua `<yourResourceGroup>` e com a sua informação.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Para permitir grandes partilhas de ficheiros na sua conta existente, utilize o seguinte comando. `<yourStorageAccountName>` Substitua `<yourResourceGroup>` e com a sua informação.

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>Criar uma grande partilha de ficheiros

Depois de ter ativado grandes ações de ficheiros na sua conta de armazenamento, pode criar ações de ficheiro nessa conta com quotas mais elevadas. 

### <a name="portal"></a>Portal

A criação de uma grande quota de ficheiros é quase idêntica à criação de uma partilha padrão de ficheiros. A principal diferença é que pode definir uma quota até 100 TiB.

1. Na sua conta de armazenamento, selecione **ações do Arquivo**.
1. Selecione **+ Partilha de ficheiros**.
1. Insira um nome para a sua parte do ficheiro. Também pode definir o tamanho da quota que gostaria, até 100 TiB. Em seguida, selecione **Criar**. 

![O portal Azure UI mostrando as caixas de nome e quota](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>CLI

Para criar uma grande partilha de ficheiros, utilize o seguinte comando. Substitua, `<yourStorageAccountName>` `<yourStorageAccountKey>` `<yourFileShareName>` e com a sua informação.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

Para criar uma grande partilha de ficheiros, utilize o seguinte comando. Substitua, `<YourStorageAccountName>` `<YourStorageAccountKey>` `<YourStorageAccountFileShareName>` e com a sua informação.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>Expandir as ações de ficheiros existentes

Depois de ter ativado grandes ações de ficheiros na sua conta de armazenamento, também pode expandir as ações de ficheiros existentes nessa conta para a quota mais elevada. 

### <a name="portal"></a>Portal

1. Na sua conta de armazenamento, selecione **ações do Arquivo**.
1. Clique na sua parte de ficheiro e, em seguida, selecione **Quota**.
1. Introduza o novo tamanho que deseja e, em seguida, selecione **OK**.

![O portal Azure UI com quota de ações de ficheiros existentes](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>CLI

Para definir a quota para o tamanho máximo, utilize o seguinte comando. Substitua, `<yourStorageAccountName>` `<yourStorageAccountKey>` `<yourFileShareName>` e com a sua informação.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

Para definir a quota para o tamanho máximo, utilize o seguinte comando. Substitua, `<YourStorageAccountName>` `<YourStorageAccountKey>` `<YourStorageAccountFileShareName>` e com a sua informação.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>Passos seguintes

* [Conecte e monte uma partilha de ficheiros no Windows](storage-how-to-use-files-windows.md)
* [Conecte e monte uma parte de arquivo no Linux](storage-how-to-use-files-linux.md)
* [Conecte e monte uma partilha de ficheiros no macOS](storage-how-to-use-files-mac.md)
