---
title: Habilitar e criar compartilhamentos de arquivos grandes-arquivos do Azure
description: Neste artigo, você aprenderá a habilitar e a criar grandes compartilhamentos de arquivos.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: a9b545d71f21138c0374cf199ce10dc2dc246afb
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732149"
---
# <a name="enable-and-create-large-file-shares"></a>Habilitar e criar compartilhamentos de arquivos grandes

Originalmente, os compartilhamentos de arquivos padrão só poderiam ser dimensionados para até 5 TiB. Agora, com grandes compartilhamentos de arquivos, eles podem ser escalados verticalmente até 100 TiB. Você pode habilitar esse dimensionamento em suas contas de armazenamento existentes para seus compartilhamentos de arquivos existentes. Os compartilhamentos de arquivos Premium são escalados verticalmente até 100 TiB por padrão.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Se você pretende usar o CLI do Azure, [Instale a versão mais recente](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- Se você pretende usar Azure PowerShell, [Instale a versão mais recente](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0).

## <a name="restrictions"></a>Restrições

Por enquanto, você só pode usar LRS ou ZRS em contas com compartilhamento de arquivos grandes – habilitados. Você não pode usar GZRS, GRS ou RA-GRS.
A habilitação de grandes compartilhamentos de arquivos em uma conta é um processo irreversível. Depois de habilitá-lo, você não conseguirá converter sua conta em GZRS, GRS ou RA-GRS.

## <a name="create-a-new-storage-account"></a>Criar uma nova conta de armazenamento

### <a name="portal"></a>Portal

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. No portal do Azure, selecione **Todos os serviços**. 
1. Na lista de recursos, insira **contas de armazenamento**. Conforme você digita, a lista filtra com base em sua entrada. Selecione **Contas do Storage**.
1. Na janela **contas de armazenamento** que aparece, selecione **Adicionar**.
1. Selecione a assinatura que você usará para criar a conta de armazenamento.
1. No campo **Grupo de recursos**, selecione **Criar novo**. Insira um nome para o novo grupo de recursos.

    ![Captura de ecrã que mostra como criar um grupo de recursos no portal](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. A seguir, introduza um nome para a sua conta de armazenamento. O nome deve ser exclusivo no Azure. O nome também deve ter de 3 a 24 caracteres de comprimento e só pode ter números e letras minúsculas.
1. Selecione um local para sua conta de armazenamento e verifique se ele é [uma das regiões com suporte para grandes compartilhamentos de arquivos](storage-files-planning.md#regional-availability).
1. Defina a replicação para armazenamento com redundância **local** ou **armazenamento com redundância de zona**.
1. Deixe esses campos com seus valores padrão:

   |Campo  |Valor  |
   |---------|---------|
   |Modelo de implementação     |Resource Manager         |
   |Desempenho     |Padrão         |
   |Tipo de conta     |StorageV2 (general-purpose v2)         |
   |Escalão de acesso     |Acesso Frequente         |

1. Selecione **avançado**e, em seguida, selecione o botão de opção **habilitado** à direita de **grandes compartilhamentos de arquivos**.
1. Selecione **Rever + Criar** para rever as definições de conta de armazenamento e criar a conta.

    ![Captura de tela com o botão de opção "habilitado" em uma nova conta de armazenamento no portal do Azure](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Selecione **Criar**.

### <a name="cli"></a>CLI

Primeiro, [Instale a versão mais recente do CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para que você possa habilitar grandes compartilhamentos de arquivos.

Para criar uma conta de armazenamento com grandes compartilhamentos de arquivos habilitados, use o comando a seguir. Substitua `<yourStorageAccountName>`, `<yourResourceGroup>`e `<yourDesiredRegion>` pelas suas informações.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Primeiro, [Instale a versão mais recente do PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) para que você possa habilitar compartilhamentos de arquivos grandes.

Para criar uma conta de armazenamento com grandes compartilhamentos de arquivos habilitados, use o comando a seguir. Substitua `<yourStorageAccountName>`, `<yourResourceGroup>`e `<yourDesiredRegion>` pelas suas informações.

```PowerShell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-large-files-shares-on-an-existing-account"></a>Habilitar compartilhamentos de arquivos grandes em uma conta existente

Você também pode habilitar compartilhamentos de arquivos grandes em suas contas existentes. Se você habilitar grandes compartilhamentos de arquivos, não será possível converter em GZRS, GRS ou RA-GRS. A habilitação de grandes compartilhamentos de arquivos é irreversível nessa conta de armazenamento.

### <a name="portal"></a>Portal

1. Abra o [portal do Azure](https://portal.azure.com)e vá para a conta de armazenamento onde você deseja habilitar grandes compartilhamentos de arquivos.
1. Abra a conta de armazenamento e selecione **configuração**.
1. Selecione **habilitado** em **compartilhamentos de arquivos grandes**e, em seguida, selecione **salvar**.
1. Selecione **visão geral** e selecione **Atualizar**.

![Selecionando o botão de opção habilitado em uma conta de armazenamento existente no portal do Azure](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Agora você habilitou grandes compartilhamentos de arquivos em sua conta de armazenamento. Em seguida, você deve atualizar a cota do compartilhamento existente para tirar proveito da maior capacidade e escala.

Se você receber a mensagem de erro "grandes compartilhamentos de arquivos não estão disponíveis para a conta ainda", sua região poderá estar no meio da conclusão de sua distribuição. Entre em contato com o suporte se você tiver uma necessidade urgente de grandes compartilhamentos de arquivos.

### <a name="cli"></a>CLI

Para habilitar grandes compartilhamentos de arquivos em sua conta existente, use o comando a seguir. Substitua `<yourStorageAccountName>` e `<yourResourceGroup>` pelas suas informações.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Para habilitar grandes compartilhamentos de arquivos em sua conta existente, use o comando a seguir. Substitua `<yourStorageAccountName>` e `<yourResourceGroup>` pelas suas informações.

```PowerShell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>Criar um compartilhamento de arquivos grande

Depois de habilitar grandes compartilhamentos de arquivos em sua conta de armazenamento, você poderá criar compartilhamentos de arquivos nessa conta com cotas maiores. 

### <a name="portal"></a>Portal

A criação de um compartilhamento de arquivos grande é quase idêntica à criação de um compartilhamento de arquivos padrão. A principal diferença é que você pode definir uma cota de até 100 TiB.

1. Em sua conta de armazenamento, selecione **compartilhamentos de arquivos**.
1. Selecione **+ Partilha de ficheiros**.
1. Insira um nome para o compartilhamento de arquivos. Você também pode definir o tamanho da cota que deseja, até 100 TiB. Em seguida, selecione **Criar**. 

![A interface do usuário do portal do Azure mostrando as caixas nome e cota](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>CLI

Para criar um compartilhamento de arquivos grande, use o comando a seguir. Substitua `<yourStorageAccountName>`, `<yourStorageAccountKey>`e `<yourFileShareName>` pelas suas informações.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

Para criar um compartilhamento de arquivos grande, use o comando a seguir. Substitua `<YourStorageAccountName>`, `<YourStorageAccountKey>`e `<YourStorageAccountFileShareName>` pelas suas informações.

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>Expandir compartilhamentos de arquivos existentes

Depois de habilitar grandes compartilhamentos de arquivos em sua conta de armazenamento, você também pode expandir os compartilhamentos de arquivos existentes nessa conta para a cota mais alta. 

### <a name="portal"></a>Portal

1. Em sua conta de armazenamento, selecione **compartilhamentos de arquivos**.
1. Clique com o botão direito do mouse no compartilhamento de arquivos e selecione **cota**.
1. Insira o novo tamanho desejado e selecione **OK**.

![A interface do usuário do portal do Azure com cota de compartilhamentos de arquivos existentes](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>CLI

Para definir a cota para o tamanho máximo, use o comando a seguir. Substitua `<yourStorageAccountName>`, `<yourStorageAccountKey>`e `<yourFileShareName>` pelas suas informações.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

Para definir a cota para o tamanho máximo, use o comando a seguir. Substitua `<YourStorageAccountName>`, `<YourStorageAccountKey>`e `<YourStorageAccountFileShareName>` pelas suas informações.

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>Passos seguintes

* [Conectar e montar um compartilhamento de arquivos no Windows](storage-how-to-use-files-windows.md)
* [Conectar e montar um compartilhamento de arquivos no Linux](storage-how-to-use-files-linux.md)
* [Conectar e montar um compartilhamento de arquivos no macOS](storage-how-to-use-files-mac.md)
