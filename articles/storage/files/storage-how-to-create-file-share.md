---
title: Como criar uma partilha de ficheiros do Azure | Microsoft Docs
description: Como criar uma partilha de ficheiros do Azure nos Ficheiros do Azure com o portal do Azure, o PowerShell e a CLI do Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e56be394bc7667dfca9a0b417593e8e587073712
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699652"
---
# <a name="create-a-file-share-in-azure-files"></a>Criar uma partilha de ficheiros nos Ficheiros do Azure
Você pode criar compartilhamentos de arquivos do Azure usando o [portal do Azure](https://portal.azure.com/), os cmdlets do PowerShell de armazenamento do Azure, as bibliotecas de cliente de armazenamento do Azure ou a API REST do armazenamento do Azure. Neste tutorial, irá aprender:
* Como criar um compartilhamento de arquivos do Azure usando o portal do Azure
* [Como criar uma partilha de ficheiros do Azure com o PowerShell](#create-file-share-through-powershell)
* [Como criar um compartilhamento de arquivos do Azure usando a CLI](#create-file-share-through-command-line-interface-cli)

## <a name="prerequisites"></a>Pré-requisitos
Para criar uma partilha de ficheiros do Azure, pode utilizar uma Conta de Armazenamento já existente ou [criar uma Conta de Armazenamento do Azure nova](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Para criar uma partilha de ficheiros do Azure com o PowerShell, precisa da chave da conta e do nome da conta de armazenamento. Você precisará de uma chave de conta de armazenamento se planeja usar o PowerShell ou a CLI.

## <a name="create-a-file-share-through-the-azure-portal"></a>Criar um compartilhamento de arquivos por meio do portal do Azure
1. **Acesse a folha da conta de armazenamento no portal do Azure**:    
    ![Painel da Conta de Armazenamento](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **Clique no botão Adicionar Partilha de Ficheiros**:    
    ![Clique no botão adicionar partilha de ficheiros](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **Indique o Nome e a Quota. O valor máximo atual da cota é 5 TiB**:    
    ![Indique um nome e uma quota pretendida para a partilha de ficheiros nova](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **Exiba seu novo compartilhamento de arquivos**:  ![Exibir seu novo compartilhamento de arquivos](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **Carregar um arquivo**:  ![Carregar um ficheiro](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **Navegue até o compartilhamento de arquivos e gerencie seus diretórios e arquivos**:  ![Procurar compartilhamento de arquivos](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Criar a partilha de Ficheiros através do PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para preparar para utilizar o PowerShell, transfira e instale os cmdlets do Azure PowerShell. Consulte [como instalar e configurar Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) para o ponto de instalação e instruções de instalação.

> [!Note]  
> É recomendado que transfira e instale ou atualize para o módulo do Azure PowerShell mais recente.

1. **Crie um contexto para a conta de armazenamento e a chave** O contexto contém o nome da conta de armazenamento e a chave da conta. Para obter instruções sobre como copiar sua chave de conta do [portal do Azure](https://portal.azure.com/), consulte [chaves de acesso da conta de armazenamento](../common/storage-account-manage.md#access-keys).

    ```powershell
    $storageContext = New-AzStorageContext <storage-account-name> <storage-account-key>
    ```
    
2. **Criar uma partilha de ficheiros nova**:    
    
    ```powershell
    $share = New-AzStorageShare logs -Context $storageContext
    ```

> [!Note]  
> O nome da partilha de ficheiros tem de ser todo em minúsculas. Para obter detalhes completos sobre como nomear arquivos e compartilhamentos de arquivos, consulte [nomenclatura e referência de compartilhamentos, diretórios, arquivos e metadados](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-file-share-through-command-line-interface-cli"></a>Criar a partilha de Ficheiros através da Interface de Linha de Comandos (CLI)
1. **Para se preparar para usar a CLI (interface de linha de comando), baixe e instale o CLI do Azure.**  
    Consulte [instalar o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e [introdução ao CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

2. **Crie uma cadeia de ligação para a conta de armazenamento na qual quer criar a partilha.**  
    Substitua ```<storage-account>``` epelo ```<resource_group>```nomeda conta de armazenamento e pelo grupo de recursos no exemplo a seguir: 

   ```azurecli
    current_env_conn_string=$(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. **Criar o compartilhamento de arquivos**
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string > /dev/null
    ```

## <a name="next-steps"></a>Passos Seguintes
* [Connect and Mount File Share - Windows](storage-how-to-use-files-windows.md) (Ligar e Montar Partilha de Ficheiros - Windows)
* [Connect and Mount File Share - Linux](../storage-how-to-use-files-linux.md) (Ligar e Montar Partilha de Ficheiros - Linux)
* [Connect and Mount File Share - macOS](storage-how-to-use-files-mac.md) (Ligar e Montar Partilha de Ficheiros - macOS)

Veja estas ligações para obter mais informações sobre os Ficheiros do Azure.

* [FAQ](../storage-files-faq.md)
* [Resolução de Problemas no Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Resolução de Problemas no Linux](storage-troubleshoot-linux-file-connection-problems.md)   
