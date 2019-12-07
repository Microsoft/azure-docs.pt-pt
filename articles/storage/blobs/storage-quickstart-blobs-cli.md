---
title: Início rápido-criar um blob com CLI do Azure
titleSuffix: Azure Storage
description: Neste guia de início rápido, você aprende a usar o CLI do Azure carregar um blob no armazenamento do Azure, baixar um blob e listar os BLOBs em um contêiner.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: c913cb978796abeed5766ffa030aaeb6142320ec
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892928"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>Início rápido: criar, baixar e listar BLOBs com CLI do Azure

A CLI do Azure é a experiência da linha de comandos do Azure para a gestão de recursos do Azure. Pode utilizá-la no seu browser com o Azure Cloud Shell. Também pode instalá-la no macOS, Linux ou Windows e executá-la a partir da linha de comandos. Neste início rápido, vai aprender a utilizar a CLI do Azure para carregar e transferir dados de e para o armazenamento de Blobs do Azure.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tópico requer a execução da versão 2.0.4 ou posterior da CLI do Azure. Execute `az --version` para determinar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

[!INCLUDE [storage-quickstart-tutorial-intro-include-cli](../../../includes/storage-quickstart-tutorial-intro-include-cli.md)]

## <a name="create-a-container"></a>Criar um contentor

Os Blobs são sempre carregados para um contentor. Pode organizar grupos de blobs, à semelhança de como organiza os ficheiros em pastas no seu computador.

Crie um contentor para armazenar blobs com o comando [az storage container create](/cli/azure/storage/container).

```azurecli-interactive
az storage container create --name sample-container
```

## <a name="upload-a-blob"></a>Carregar um blob

O armazenamento de blobs suporta blobs de blocos, blobs de acréscimo e blobs de páginas. Os exemplos neste guia de início rápido mostram como trabalhar com blobs de bloco.

Primeiro, crie um arquivo para carregar em um blob de blocos. Se você estiver usando Azure Cloud Shell, use o seguinte comando para criar um arquivo:

```bash
vi helloworld
```

Quando o arquivo for aberto, pressione **Insert**. Digite *Olá, mundo*e pressione **ESC**. Em seguida, digite *: x*e pressione **Enter**.

Neste exemplo, carregue um blob para o contentor criado no último passo com o comando [az storage blob upload](/cli/azure/storage/blob). Não é necessário especificar um caminho de arquivo, pois o arquivo foi criado no diretório raiz:

```azurecli-interactive
az storage blob upload \
    --container-name sample-container \
    --name helloworld \
    --file helloworld
```

Esta operação cria o blob, caso este ainda não exista, ou substitui-o se o mesmo já existir. Carregue os ficheiros que quiser antes de continuar.

Para carregar múltiplos ficheiros em simultâneo, pode utilizar o comando [az storage blob upload-batch](/cli/azure/storage/blob).

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Liste os blobs no contentor com o comando [az storage blob list](/cli/azure/storage/blob).

```azurecli-interactive
az storage blob list \
    --container-name sample-container \
    --output table
```

## <a name="download-a-blob"></a>Transferir um blob

Utilize o comando [az storage blob download](/cli/azure/storage/blob) para transferir o blob carregado anteriormente.

```azurecli-interactive
az storage blob download \
    --container-name sample-container \
    --name helloworld \
    --file ~/destination/path/for/file
```

## <a name="data-transfer-with-azcopy"></a>Transferência de dados com o AzCopy

O utilitário [AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) é outra opção para a transferência de dados de scripts de elevado desempenho para o Armazenamento do Azure. Pode utilizar o AzCopy para transferir dados de e para o armazenamento de Blobs, Ficheiros e Tabelas.

O exemplo a seguir usa AzCopy para carregar um arquivo chamado *MyFile. txt* para o contêiner de *contêiner de exemplo* . Lembre-se de substituir valores de espaço reservado entre colchetes angulares por seus próprios valores:

```bash
azcopy \
    --source /mnt/myfiles \
    --destination https://<account-name>.blob.core.windows.net/sample-container \
    --dest-key <account-key> \
    --include "myfile.txt"
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais de nenhum dos recursos em seu grupo de recursos, incluindo a conta de armazenamento criada neste guia de início rápido, exclua o grupo de recursos com o comando [AZ Group Delete](/cli/azure/group) . Lembre-se de substituir valores de espaço reservado entre colchetes angulares por seus próprios valores:

```azurecli-interactive
az group delete --name <resource-group-name>
```

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a transferir arquivos entre um sistema de arquivos local e um contêiner no armazenamento de BLOBs do Azure. Para saber mais sobre a utilização de blobs no Armazenamento do Azure, avance para o tutorial para utilizar o armazenamento de Blobs do Azure.

> [!div class="nextstepaction"]
> [Procedimentos de operações de armazenamento de Blobs com a CLI do Azure](storage-how-to-use-blobs-cli.md)
