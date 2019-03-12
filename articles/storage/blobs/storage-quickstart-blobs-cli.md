---
title: Início Rápido do Azure – Criar um blob no armazenamento de objetos com a CLI do Azure | Microsoft Docs
description: Neste início rápido, irá utilizar a CLI do Azure no armazenamento de objetos (Blobs). Em seguida, utilize a CLI para carregar um blob para o Armazenamento do Microsoft Azure, transferir um blob e listar os blobs num contentor.
services: storage
author: roygara
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 11/14/2018
ms.author: rogarana
ms.openlocfilehash: 11bd639d86c6ad9a9f373ac26dc271817bc46b08
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57773091"
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-azure-cli"></a>Início rápido: Carregar, transferir e listar os blobs com a CLI do Azure

A CLI do Azure é a experiência da linha de comandos do Azure para a gestão de recursos do Azure. Pode utilizá-la no seu browser com o Azure Cloud Shell. Também pode instalá-la no macOS, Linux ou Windows e executá-la a partir da linha de comandos. Neste início rápido, vai aprender a utilizar a CLI do Azure para carregar e transferir dados de e para o armazenamento de Blobs do Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Execute `az --version` para determinar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Criar um contentor

Os Blobs são sempre carregados para um contentor. Pode organizar grupos de blobs, à semelhança de como organiza os ficheiros em pastas no seu computador.

Crie um contentor para armazenar blobs com o comando [az storage container create](/cli/azure/storage/container).

```azurecli-interactive
az storage container create --name mystoragecontainer
```

## <a name="upload-a-blob"></a>Carregar um blob

O armazenamento de blobs suporta blobs de blocos, blobs de acréscimo e blobs de páginas. A maioria dos ficheiros guardados no armazenamento de Blobs é armazenada como blobs de blocos. Os blobs de acréscimo são utilizados quando é preciso adicionar dados a um blob existente sem modificar o conteúdo existente, como é o caso das operações de registo. Os blobs de páginas dão suporte aos ficheiros VHD de máquinas virtuais IaaS.

Primeiro, crie um ficheiro para carregar para um blob.
Se estiver a utilizar o Azure Cloud Shell, utilize o seguinte para criar um ficheiro: `vi helloworld` quando o ficheiro abrir, prima **insert**, escreva "Hello world" e, em seguida, prima **Esc** e introduza `:x` e prima **Enter**.

Neste exemplo, carregue um blob para o contentor criado no último passo com o comando [az storage blob upload](/cli/azure/storage/blob).

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/path/to/local/file
```

Se utilizou o método descrito anteriormente para criar um ficheiro no seu Azure Cloud Shell, pode utilizar antes este comando CLI (note que não precisa de especificar um caminho, uma vez que o ficheiro foi criado no diretório base; normalmente, teria de especificar um caminho):

```azurecli-interactive
az storage blob upload \
    --container-name mystoragecontainer \
    --name helloworld \
    --file helloworld
```

Esta operação cria o blob, caso este ainda não exista, ou substitui-o se o mesmo já existir. Carregue os ficheiros que quiser antes de continuar.

Para carregar múltiplos ficheiros em simultâneo, pode utilizar o comando [az storage blob upload-batch](/cli/azure/storage/blob).

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Liste os blobs no contentor com o comando [az storage blob list](/cli/azure/storage/blob).

```azurecli-interactive
az storage blob list \
    --container-name mystoragecontainer \
    --output table
```

## <a name="download-a-blob"></a>Transferir um blob

Utilize o comando [az storage blob download](/cli/azure/storage/blob) para transferir o blob carregado anteriormente.

```azurecli-interactive
az storage blob download \
    --container-name mystoragecontainer \
    --name blobName \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>Transferência de dados com o AzCopy

O utilitário [AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) é outra opção para a transferência de dados de scripts de elevado desempenho para o Armazenamento do Azure. Pode utilizar o AzCopy para transferir dados de e para o armazenamento de Blobs, Ficheiros e Tabelas.

Como exemplo rápido, eis o comando do AzCopy para carregar um ficheiro denominado *myfile.txt* para o contentor *mystoragecontainer*.

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://mystorageaccount.blob.core.windows.net/mystoragecontainer \
    --dest-key <storage-account-access-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>Limpar recursos

Se já não precisa de nenhum dos recursos presentes no seu grupo de recursos, incluindo a conta de armazenamento que criou neste Início Rápido, elimine o grupo de recursos com o comando [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos Seguintes

Neste Início Rápido, aprendeu a transferir ficheiros entre o disco local e um contentor no armazenamento de Blobs do Azure. Para saber mais sobre a utilização de blobs no Armazenamento do Azure, avance para o tutorial para utilizar o armazenamento de Blobs do Azure.

> [!div class="nextstepaction"]
> [How to: Operações de armazenamento de Blobs com a CLI do Azure](storage-how-to-use-blobs-cli.md)
