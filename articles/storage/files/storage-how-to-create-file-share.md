---
title: Como criar uma partilha de ficheiros do Azure | Microsoft Docs
description: Como criar uma partilha de ficheiros do Azure nos Ficheiros do Azure com o portal do Azure, o PowerShell e a CLI do Azure.
services: storage
author: RenaShahMSFT
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: renash
ms.subservice: files
ms.openlocfilehash: 1620683f4bc02d607afd8e0371e8d4a5461612b3
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772335"
---
# <a name="create-a-file-share-in-azure-files"></a>Criar uma partilha de ficheiros nos Ficheiros do Azure
Pode criar partilhas de ficheiros do Azure com o [portal do Azure](https://portal.azure.com/), os cmdlets do PowerShell do armazenamento do Azure, as bibliotecas de cliente de armazenamento do Azure ou a API de REST de armazenamento do Azure. Neste tutorial, irá aprender:
* Como criar uma partilha de ficheiros do Azure com o portal do Azure
* [Como criar uma partilha de ficheiros do Azure com o PowerShell](#create-file-share-through-powershell)
* [Como criar uma partilha de ficheiros do Azure com a CLI](#create-file-share-through-command-line-interface-cli)

## <a name="prerequisites"></a>Pré-requisitos
Para criar uma partilha de ficheiros do Azure, pode utilizar uma Conta de Armazenamento já existente ou [criar uma Conta de Armazenamento do Azure nova](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Para criar uma partilha de ficheiros do Azure com o PowerShell, precisa da chave da conta e do nome da conta de armazenamento. Terá de uma chave de conta de armazenamento se planeia utilizar o Powershell ou a CLI.

## <a name="create-a-file-share-through-the-azure-portal"></a>Criar uma partilha de ficheiros através do portal do Azure
1. **Aceda ao painel de conta de armazenamento no portal do Azure**:    
    ![Painel da Conta de Armazenamento](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **Clique no botão Adicionar Partilha de Ficheiros**:    
    ![Clique no botão adicionar partilha de ficheiros](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **Indique o Nome e a Quota. O valor máximo atual a Quota é de 5 TiB**:    
    ![Indique um nome e uma quota pretendida para a partilha de ficheiros nova](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **Ver a nova partilha de ficheiros**:  ![Ver a nova partilha de ficheiros](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **Carregar um ficheiro**:  ![Carregar um ficheiro](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **Navegue para a partilha de ficheiros e gerir os seus diretórios e arquivos**:  ![Procurar a partilha de ficheiros](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Criar a partilha de Ficheiros através do PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para preparar para utilizar o PowerShell, transfira e instale os cmdlets do Azure PowerShell. Ver [como instalar e configurar o Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) para as instruções de instalação e do ponto de instalação.

> [!Note]  
> É recomendado que transfira e instale ou atualize para o módulo do Azure PowerShell mais recente.

1. **Crie um contexto para a conta de armazenamento e a chave** O contexto contém o nome da conta de armazenamento e a chave da conta. Para obter instruções sobre a cópia da chave de conta a partir da [portal do Azure](https://portal.azure.com/), consulte [chaves de acesso da conta de armazenamento](../common/storage-account-manage.md#access-keys).

    ```powershell
    $storageContext = New-AzStorageContext <storage-account-name> <storage-account-key>
    ```
    
2. **Criar uma partilha de ficheiros nova**:    
    
    ```powershell
    $share = New-AzStorageShare logs -Context $storageContext
    ```

> [!Note]  
> O nome da partilha de ficheiros tem de ser todo em minúsculas. Para obter detalhes completos sobre a nomenclatura de partilhas de ficheiros e ficheiros, consulte [nomenclatura e referência de partilhas, diretórios, ficheiros e metadados](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-file-share-through-command-line-interface-cli"></a>Criar a partilha de Ficheiros através da Interface de Linha de Comandos (CLI)
1. **Para se preparar para utilizar a Interface de linha de comandos (CLI), transfira e instale a CLI do Azure.**  
    Ver [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e [introdução à CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

2. **Crie uma cadeia de ligação para a conta de armazenamento na qual quer criar a partilha.**  
    Substitua ```<storage-account>``` e ```<resource_group>``` com o seu grupo de recursos e o nome da conta de armazenamento no exemplo a seguir:

   ```azurecli
    current_env_conn_string = $(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. **Criar a partilha de ficheiros**
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string 1 > /dev/null
    ```

## <a name="next-steps"></a>Passos Seguintes
* [Connect and Mount File Share - Windows](storage-how-to-use-files-windows.md) (Ligar e Montar Partilha de Ficheiros - Windows)
* [Connect and Mount File Share - Linux](../storage-how-to-use-files-linux.md) (Ligar e Montar Partilha de Ficheiros - Linux)
* [Connect and Mount File Share - macOS](storage-how-to-use-files-mac.md) (Ligar e Montar Partilha de Ficheiros - macOS)

Veja estas ligações para obter mais informações sobre os Ficheiros do Azure.

* [FAQ](../storage-files-faq.md)
* [Resolução de Problemas no Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Resolução de Problemas no Linux](storage-troubleshoot-linux-file-connection-problems.md)   
