---
title: Quickstart - Crie uma bolha com Azure CLI
titleSuffix: Azure Storage
description: Neste arranque rápido, você aprende a usar o Azure CLI carregar uma bolha para Azure Storage, baixar uma bolha e listar as bolhas num recipiente.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: c9797024315a8fdc744d08863a42fba0b0b16420
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660343"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>Quickstart: Criar, transferir e listar bolhas com Azure CLI

A CLI do Azure é a experiência da linha de comandos do Azure para a gestão de recursos do Azure. Pode utilizá-la no seu browser com o Azure Cloud Shell. Também pode instalá-la no macOS, Linux ou Windows e executá-la a partir da linha de comandos. Neste início rápido, vai aprender a utilizar a CLI do Azure para carregar e transferir dados de e para o armazenamento de Blobs do Azure.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment-h3.md)]

- Este artigo requer a versão 2.0.46 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="authorize-access-to-blob-storage"></a>Autorizar o acesso ao armazenamento blob

Pode autorizar o acesso ao armazenamento blob a partir do CLI Azure, quer com credenciais AZure AD, quer utilizando a chave de acesso à conta de armazenamento. Recomenda-se a utilização de credenciais AZure AD. Este artigo mostra como autorizar operações de armazenamento Blob usando Azure AD.

O Azure CLI ordena operações de dados contra o suporte de armazenamento `--auth-mode` Blob, o parâmetro, que lhe permite especificar como autorizar uma determinada operação. Desa esta medida para `--auth-mode` `login` autorizar com credenciais AZure AD. Para obter mais informações, consulte [Autorizar o acesso aos dados de blob ou fila com o Azure CLI](../common/authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Apenas as operações de armazenamento blob suportam o `--auth-mode` parâmetro. As operações de gestão, tais como a criação de um grupo de recursos ou uma conta de armazenamento, utilizam automaticamente credenciais Azure AD para autorização.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com o comando [az group create](/cli/azure/group). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Lembre-se de substituir os valores do espaço reservado nos suportes angulares com os seus próprios valores:

```azurecli
az group create \
    --name <resource-group> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma conta de armazenamento para fins gerais com o comando [az storage account create](/cli/azure/storage/account). A conta de armazenamento para fins gerais pode ser utilizada para os quatro serviços: blobs, ficheiros, tabelas e filas.

Lembre-se de substituir os valores do espaço reservado nos suportes angulares com os seus próprios valores:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob
```

## <a name="create-a-container"></a>Criar um contentor

Os Blobs são sempre carregados para um contentor. Pode organizar grupos de bolhas em recipientes semelhantes à forma como organiza os seus ficheiros no seu computador em pastas. Crie um contentor para armazenar blobs com o comando [az storage container create](/cli/azure/storage/container).

O exemplo a seguir utiliza a sua conta Azure AD para autorizar a operação de criação do recipiente. Antes de criar o recipiente, atribua a [função de Contribuinte de Dados de Armazenamento blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) a si mesmo. Mesmo que você é o proprietário da conta, você precisa de permissões explícitas para realizar operações de dados contra a conta de armazenamento. Para obter mais informações sobre a atribuição de funções Azure, consulte [Use Azure CLI para atribuir uma função Azure para acesso](../common/storage-auth-aad-rbac-cli.md?toc=/azure/storage/blobs/toc.json).  

Lembre-se de substituir os valores do espaço reservado nos suportes angulares com os seus próprios valores:

```azurecli
az ad signed-in-user show --query objectId -o tsv | az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee @- \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"

az storage container create \
    --account-name <storage-account> \
    --name <container> \
    --auth-mode login
```

> [!IMPORTANT]
> As atribuições de funções azure podem demorar alguns minutos a propagar-se.

Também pode utilizar a chave da conta de armazenamento para autorizar a operação para criar o recipiente. Para obter mais informações sobre a autorização de operações de dados com o Azure CLI, consulte [Autorizar o acesso aos dados de blob ou fila com o Azure CLI](../common/authorize-data-operations-cli.md?toc=/azure/storage/blobs/toc.json).

## <a name="upload-a-blob"></a>Carregar um blob

O armazenamento de blobs suporta blobs de blocos, blobs de acréscimo e blobs de páginas. Os exemplos neste arranque rápido mostram como trabalhar com bolhas de bloco.

Primeiro, crie um ficheiro para carregar para uma bolha de bloco. Se estiver a utilizar o Azure Cloud Shell, utilize o seguinte comando para criar um ficheiro:

```bash
vi helloworld
```

Quando o ficheiro abrir, prima **a inserção**. Tipo *Olá mundo,* em seguida, prima **Esc**. Em seguida, *escreva :x,* em seguida, prima **Enter**.

Neste exemplo, carregue um blob para o contentor criado no último passo com o comando [az storage blob upload](/cli/azure/storage/blob). Não é necessário especificar um caminho de arquivo desde que o ficheiro foi criado no diretório de raiz. Lembre-se de substituir os valores do espaço reservado nos suportes angulares com os seus próprios valores:

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file helloworld \
    --auth-mode login
```

Esta operação cria o blob, caso este ainda não exista, ou substitui-o se o mesmo já existir. Carregue os ficheiros que quiser antes de continuar.

Para carregar múltiplos ficheiros em simultâneo, pode utilizar o comando [az storage blob upload-batch](/cli/azure/storage/blob).

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Liste os blobs no contentor com o comando [az storage blob list](/cli/azure/storage/blob). Lembre-se de substituir os valores do espaço reservado nos suportes angulares com os seus próprios valores:

```azurecli
az storage blob list \
    --account-name <storage-account> \
    --container-name <container> \
    --output table \
    --auth-mode login
```

## <a name="download-a-blob"></a>Transferir um blob

Utilize o comando [az storage blob download](/cli/azure/storage/blob) para transferir o blob carregado anteriormente. Lembre-se de substituir os valores do espaço reservado nos suportes angulares com os seus próprios valores:

```azurecli
az storage blob download \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file ~/destination/path/for/file \
    --auth-mode login
```

## <a name="data-transfer-with-azcopy"></a>Transferência de dados com o AzCopy

O utilitário da linha de comando AzCopy oferece transferência de dados scriptable de alto desempenho para armazenamento Azure. Pode utilizar o AzCopy para transferir dados de e para o armazenamento blob e ficheiros Azure. Para mais informações sobre a AzCopy v10, a versão mais recente do AzCopy, consulte [Começar com a AzCopy](../common/storage-use-azcopy-v10.md). Para saber sobre a utilização do AzCopy v10 com armazenamento Blob, consulte [os dados de Transferência com armazenamento AzCopy e Blob](../common/storage-use-azcopy-blobs.md).

O exemplo a seguir utiliza o AzCopy para enviar um ficheiro local para uma bolha. Lembre-se de substituir os valores da amostra pelos seus próprios valores:

```bash
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se pretender eliminar os recursos que criou como parte deste arranque rápido, incluindo a conta de armazenamento, elimine o grupo de recursos utilizando o comando de eliminação do [grupo az.](/cli/azure/group) Lembre-se de substituir os valores do espaço reservado nos suportes angulares com os seus próprios valores:

```azurecli
az group delete \
    --name <resource-group> \
    --no-wait
```

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, aprendeu a transferir ficheiros entre um sistema de ficheiros local e um contentor no armazenamento Azure Blob. Para saber mais sobre como trabalhar com o armazenamento blob utilizando o Azure CLI, explore as amostras do Azure CLI para armazenamento blob.

> [!div class="nextstepaction"]
> [Amostras de CLI Azure para armazenamento blob](/azure/storage/blobs/storage-samples-blobs-cli?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
