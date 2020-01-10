---
title: Criar uma partilha de ficheiros do Azure
titleSuffix: Azure Files
description: Como criar um compartilhamento de arquivos do Azure usando o portal do Azure, o PowerShell ou o CLI do Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ca4904940c7974e83445892b9597a9d3c36b44ee
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452881"
---
# <a name="create-a-file-share"></a>Criar uma partilha de ficheiros
Você pode criar um compartilhamento em arquivos do Azure usando o [portal do Azure](https://portal.azure.com/), os cmdlets do PowerShell de armazenamento do Azure, as bibliotecas de cliente de armazenamento do Azure ou a API REST do armazenamento do Azure. Neste artigo, você aprenderá a:
- Criar um compartilhamento de arquivos do Azure usando o portal do Azure
- Criar um compartilhamento de arquivos do Azure usando o PowerShell
- Criar um compartilhamento de arquivos do Azure usando o CLI do Azure

## <a name="prerequisites"></a>Pré-requisitos
Para criar um compartilhamento de arquivos do Azure, você pode usar uma conta de armazenamento que já existe ou [criar uma nova conta de armazenamento do Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Para criar um compartilhamento de arquivos do Azure com o PowerShell, você precisará da chave de conta e do nome da sua conta de armazenamento. Você precisará de uma chave de conta de armazenamento se planeja usar o PowerShell ou a CLI.

> [!NOTE]
> Se você quiser criar compartilhamentos de arquivos maiores que 5 TiB, consulte [habilitar compartilhamentos de arquivos grandes](storage-files-how-to-create-large-file-share.md).

## <a name="create-a-file-share-through-the-azure-portal"></a>Criar um compartilhamento de arquivos por meio do portal do Azure

1. Vá para o painel da **conta de armazenamento** na portal do Azure.
    ![painel de conta de armazenamento](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

1. Selecione o botão **+ compartilhamento de arquivos** .
    ![botão Adicionar compartilhamento de arquivos](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

1. Insira suas informações de **nome** e **cota**.
    ![o nome e a cota do novo compartilhamento de arquivos](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

1. Exiba seu novo compartilhamento de arquivos.
    ![o novo compartilhamento de arquivos na interface do usuário](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

1. Carregar um arquivo.
    ![botão carregar na barra de navegação](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

1. Navegue até o compartilhamento de arquivos e gerencie seus diretórios e arquivos.
    ![exibição de pasta de compartilhamentos de arquivos](./media/storage-how-to-create-file-share/create-file-share-portal6.png)

## <a name="create-a-file-share-through-powershell"></a>Criar um compartilhamento de arquivos por meio do PowerShell

Baixe e instale os cmdlets Azure PowerShell. Consulte [como instalar e configurar Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para as instruções de instalação e ponto de instalação.

> [!Note]  
> Recomendamos que você baixe e instale ou atualize para o módulo de Azure PowerShell mais recente.

1. Crie uma nova conta de armazenamento.
    Uma conta de armazenamento é um pool compartilhado de armazenamento no qual você pode implantar compartilhamentos de arquivos do Azure e recursos de armazenamento, como BLOBs ou filas.

    ```PowerShell
    $resourceGroup = "myresourcegroup"
    $storAcctName = "myuniquestorageaccount"
    $region = "westus2"
    $storAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storAcctName -SkuName Standard_LRS -Location $region -Kind StorageV2
    ```

1. Crie um novo compartilhamento de arquivos.        
    ```PowerShell
    $shareName = "myshare"
    $share = New-AzStorageShare -Context $storAcct.Context -Name $shareName
    ```

> [!Note]  
> O nome da partilha de ficheiros tem de ser todo em minúsculas. Para obter detalhes completos sobre como nomear arquivos e compartilhamentos de arquivos, consulte [nomenclatura e referência de compartilhamentos, diretórios, arquivos e metadados](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-a-file-share-through-the-cli"></a>Criar um compartilhamento de arquivos por meio da CLI

1. Baixe e instale o CLI do Azure.
    Consulte [instalar o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e começar [a usar o CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

1. Crie uma cadeia de conexão para a conta de armazenamento na qual você deseja criar o compartilhamento.
    Substitua ```<storage-account>``` e ```<resource_group>``` pelo nome da conta de armazenamento e pelo grupo de recursos no exemplo a seguir:

   ```azurecli
    current_env_conn_string=$(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

1. Crie o compartilhamento de arquivos.
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string > /dev/null
    ```

## <a name="next-steps"></a>Passos seguintes

* [Conectar e montar um compartilhamento de arquivos no Windows](storage-how-to-use-files-windows.md)
* [Conectar e montar um compartilhamento de arquivos no Linux](storage-how-to-use-files-linux.md)
* [Conectar e montar um compartilhamento de arquivos no macOS](storage-how-to-use-files-mac.md)

Veja estas ligações para obter mais informações sobre os Ficheiros do Azure:

* [Perguntas frequentes sobre arquivos de armazenamento](storage-files-faq.md)
* [Solucionar problemas de arquivos do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Solucionar problemas de arquivos do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md)
