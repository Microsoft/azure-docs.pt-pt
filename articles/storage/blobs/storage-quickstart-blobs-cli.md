---
title: Quickstart - Crie uma bolha com o Azure CLI
titleSuffix: Azure Storage
description: Neste arranque rápido, aprende-se a usar o Azure CLI para carregar uma bolha para o Azure Storage, descarregar uma bolha e listar as bolhas num recipiente.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 04/23/2020
ms.author: tamram
ms.openlocfilehash: 237982c2135430891022ab7a823f8374844e8d90
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82117561"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>Quickstart: Criar, descarregar e listar bolhas com o Azure CLI

A CLI do Azure é a experiência da linha de comandos do Azure para a gestão de recursos do Azure. Pode utilizá-la no seu browser com o Azure Cloud Shell. Também pode instalá-la no macOS, Linux ou Windows e executá-la a partir da linha de comandos. Neste início rápido, vai aprender a utilizar a CLI do Azure para carregar e transferir dados de e para o armazenamento de Blobs do Azure.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="install-the-azure-cli-locally"></a>Instale o Azure CLI localmente

Se optar por instalar e utilizar o Azure CLI localmente, este quickstart requer que esteja a executar a versão Azure CLI 2.0.46 ou posterior. Execute `az --version` para determinar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Se estiver a executar o Azure CLI localmente, tem de fazer login e autenticar. Este passo não é necessário se estiver a utilizar a Azure Cloud Shell. Para iniciar sessão no Azure CLI, executar `az login` e autenticar na janela do navegador:

```azurecli
az login
```

Para mais informações sobre a autenticação' com o Azure CLI, consulte [O Signin em com o Azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="authorize-access-to-blob-storage"></a>Autorizar o acesso ao armazenamento blob

Pode autorizar o acesso ao armazenamento Blob a partir do Azure CLI, quer com credenciais De AD Azure, quer utilizando a chave de acesso à conta de armazenamento. Recomenda-se a utilização de credenciais AD Azure. Este artigo mostra como autorizar operações de armazenamento blob usando a AD Azure.

O Azure CLI ordena operações de `--auth-mode` dados contra o suporte de armazenamento Blob, o parâmetro, que lhe permite especificar como autorizar uma determinada operação. Defina `--auth-mode` o `login` parâmetro para autorizar com credenciais Azure AD. Para mais informações, consulte [Autorizar o acesso a dados blob ou fila com o Azure CLI](../common/authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Apenas as operações `--auth-mode` de dados de armazenamento blob suportam o parâmetro. As operações de gestão, tais como a criação de um grupo de recursos ou uma conta de armazenamento, utilizam automaticamente credenciais de AD Azure para autorização.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com o comando [az group create](/cli/azure/group). Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Lembre-se de substituir os valores do espaço reservado em suportes angulares por valores próprios:

```azurecli
az group create \
    --name <resource-group> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Crie uma conta de armazenamento para fins gerais com o comando [az storage account create](/cli/azure/storage/account). A conta de armazenamento para fins gerais pode ser utilizada para os quatro serviços: blobs, ficheiros, tabelas e filas.

Lembre-se de substituir os valores do espaço reservado em suportes angulares por valores próprios:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob
```

## <a name="create-a-container"></a>Criar um contentor

Os Blobs são sempre carregados para um contentor. Pode organizar grupos de bolhas em contentores semelhantes à forma como organiza os seus ficheiros no computador em pastas.

Crie um contentor para armazenar blobs com o comando [az storage container create](/cli/azure/storage/container). Lembre-se de substituir os valores do espaço reservado em suportes angulares por valores próprios:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name <container> \
    --auth-mode login
```

## <a name="upload-a-blob"></a>Carregar um blob

O armazenamento de blobs suporta blobs de blocos, blobs de acréscimo e blobs de páginas. Os exemplos neste arranque rápido mostram como trabalhar com bolhas de blocos.

Primeiro, crie um ficheiro para fazer o upload para uma bolha de bloco. Se estiver a utilizar a Azure Cloud Shell, utilize o seguinte comando para criar um ficheiro:

```bash
vi helloworld
```

Quando o ficheiro abrir, prima **a inserção**. Tipo *Olá mundo,* em seguida, prima **Esc**. Em seguida, *escreva :x,* em seguida, prima **Enter**.

Neste exemplo, carregue um blob para o contentor criado no último passo com o comando [az storage blob upload](/cli/azure/storage/blob). Não é necessário especificar um caminho de ficheiro, uma vez que o ficheiro foi criado no diretório raiz. Lembre-se de substituir os valores do espaço reservado em suportes angulares por valores próprios:

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

Liste os blobs no contentor com o comando [az storage blob list](/cli/azure/storage/blob). Lembre-se de substituir os valores do espaço reservado em suportes angulares por valores próprios:

```azurecli
az storage blob list \
    --account-name <storage-account> \
    --container-name <container> \
    --output table \
    --auth-mode login
```

## <a name="download-a-blob"></a>Transferir um blob

Utilize o comando [az storage blob download](/cli/azure/storage/blob) para transferir o blob carregado anteriormente. Lembre-se de substituir os valores do espaço reservado em suportes angulares por valores próprios:

```azurecli
az storage blob download \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file ~/destination/path/for/file \
    --auth-mode login
```

## <a name="data-transfer-with-azcopy"></a>Transferência de dados com o AzCopy

O utilitário de linha de comando AzCopy oferece transferência de dados de alto desempenho e scriptable para o Armazenamento Azure. Pode utilizar o AzCopy para transferir dados de e para o armazenamento blob e ficheiros Azure. Para mais informações sobre o AzCopy v10, a versão mais recente do AzCopy, consulte [Get started with AzCopy](../common/storage-use-azcopy-v10.md). Para aprender sobre a utilização do AzCopy v10 com armazenamento Blob, consulte [os dados da Transferência com armazenamento AzCopy e Blob](../common/storage-use-azcopy-blobs.md).

O exemplo que se segue utiliza o AzCopy para fazer o upload de um ficheiro local para uma bolha. Lembre-se de substituir os valores da amostra por valores próprios:

```bash
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Limpar recursos

Se pretender eliminar os recursos que criou como parte deste quickstart, incluindo a conta de armazenamento, elimine o grupo de recursos utilizando o comando de eliminação do [grupo AZ.](/cli/azure/group) Lembre-se de substituir os valores do espaço reservado em suportes angulares por valores próprios:

```azurecli
az group delete \
    --name <resource-group> \
    --no-wait
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a transferir ficheiros entre um sistema de ficheiros local e um contentor no armazenamento do Azure Blob. Para saber mais sobre a utilização de blobs no Armazenamento do Azure, avance para o tutorial para utilizar o armazenamento de Blobs do Azure.

> [!div class="nextstepaction"]
> [Procedimentos de operações de armazenamento de Blobs com a CLI do Azure](storage-how-to-use-blobs-cli.md)
