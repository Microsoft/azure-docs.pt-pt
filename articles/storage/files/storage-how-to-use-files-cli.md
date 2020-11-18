---
title: Início Rápido para gerir partilhas de ficheiros do Azure com a CLI do Azure
description: Neste arranque rápido, aprenda a usar o Azure CLI para gerir ficheiros Azure. Crie um grupo de recursos e uma conta de armazenamento, em seguida, crie e use uma partilha de ficheiros Azure.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli
ms.openlocfilehash: dd07c09c4ed5be311bf5a485b9bff938c976a2f5
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659238"
---
# <a name="quickstart-create-and-manage-azure-file-shares-using-azure-cli"></a>Início Rápido: criar e gerir partilhas de ficheiros do Azure com a CLI do Azure
Este guia orienta-o pelas noções básicas da utilização de [partilhas de ficheiros do Azure](storage-files-introduction.md) com a CLI do Azure. As partilhas de ficheiros do Azure são como outras partilhas de ficheiros, mas armazenadas na cloud e apoiadas pela plataforma do Azure. As ações do Azure File suportam o protocolo padrão do Bloco de Mensagens do Servidor (SMB) da indústria, o protocolo sistema de ficheiros de rede (NFS) (pré-visualização) e permite a partilha de ficheiros em várias máquinas, aplicações e instâncias. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.4 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

- Por predefinição, os comandos da CLI do Azure devolvem JavaScript Object Notation (JSON). JSON é a forma padrão de enviar e receber mensagens a partir de APIs REST. Para facilitar a utilização das respostas JSON, alguns dos exemplos neste artigo utilizam o parâmetro *query* nos comandos da CLI do Azure. Este parâmetro utiliza a [linguagem de consulta JMESPath](http://jmespath.org/) para analisar o JSON. Para saber mais sobre como utilizar os resultados dos comandos da CLI do Azure ao seguir a linguagem de consulta do JMESPath, veja o [tutorial JMESPath](http://jmespath.org/tutorial.html).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. Se ainda não tiver um grupo de recursos do Azure, pode utilizar o comando [az group create](/cli/azure/group) para criar um. 

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na *localização West US 2:*

```azurecli-interactive 
export resourceGroupName="myResourceGroup"
region="westus2"

az group create \
    --name $resourceGroupName \
    --location $region \
    --output none
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
Uma conta de armazenamento é um conjunto partilhado de armazenamento no qual pode implementar partilhas de ficheiros do Azure ou outros recursos de armazenamento, como blobs ou filas. Uma conta de armazenamento pode conter um número ilimitado de partilhas de ficheiros. Uma partilha pode armazenar um número ilimitado de ficheiros, até aos limites de capacidade da conta de armazenamento.

O exemplo a seguir cria uma conta de armazenamento utilizando a [conta de armazenamento az criar](/cli/azure/storage/account) comando. Os nomes de contas de armazenamento têm de ser exclusivos, por isso, utilize `$RANDOM` para anexar um número ao nome e torná-lo exclusivo.

```azurecli-interactive 
export storageAccountName="mystorageacct$RANDOM"

az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --location $region \
    --kind StorageV2 \
    --sku Standard_LRS \
    --enable-large-file-share \
    --output none
```

> [!Note]  
> As ações superiores a 5 TiB (até um máximo de 100 TiB por ação) só estão disponíveis em contas de armazenamento redundantes locais (LRS) e zonas redundantes (ZRS). Para criar uma conta de armazenamento geo-redundante (GRS) ou geo-zona redundante (GZRS), remova o `--enable-large-file-share` parâmetro.

### <a name="get-the-storage-account-key"></a>Obter a chave da conta de armazenamento
As chaves de contas de armazenamento controlam o acesso aos recursos numa conta de armazenamento. As chaves são criadas automaticamente quando cria uma conta de armazenamento. Pode obter as chaves da conta de armazenamento para a mesma com o comando [az storage account keys list](/cli/azure/storage/account/keys): 

```azurecli-interactive 
export storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure
Agora, pode criar a sua primeira partilha de ficheiros do Azure. Crie partilhas de ficheiros com o comando [az storage share create](/cli/azure/storage/share). Este exemplo cria uma partilha de ficheiros do Azure com o nome *myshare*: 

```azurecli-interactive
shareName="myshare"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --quota 1024 \
    --enabled-protocols SMB \
    --output none
```

Os nomes das partilhas só podem ter letras minúsculas, números e hífenes (mas não podem começar com um hífen). Para obter detalhes completos sobre ações e ficheiros de ficheiros de [nomeação, consulte naming e referências de ações, diretórios, ficheiros e metadados](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Utilizar a partilha de ficheiros do Azure
O serviço Ficheiros do Azure fornece dois métodos para utilizar ficheiros e pastas na sua partilha de ficheiros do Azure : o [protocolo SMB (Server Message Block)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) padrão do setor e o [protocolo REST de Ficheiros](/rest/api/storageservices/file-service-rest-api). 

Para montar uma partilha de ficheiros com SMB, veja o documento seguinte com base no seu SO:
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)
- [Windows](storage-how-to-use-files-windows.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Utilizar uma partilha de ficheiros do Azure com o protocolo REST de Ficheiros 
É possível trabalhar diretamente com o protocolo File REST diretamente (manualMENTE REST HTTP chama-se), mas a forma mais comum de usar o protocolo File REST é utilizar o Azure CLI, o [módulo Azure PowerShell,](storage-how-to-use-files-powershell.md)ou um Azure Storage SDK, todos os quais fornecem um bom invólucro em torno do protocolo File REST na linguagem de script/programação à sua escolha.  

Contamos que a maioria das utilizações dos Ficheiros do Azure irão querer trabalhar com a partilha de ficheiros do Azure através do protocolo SMB, pois permite-lhes utilizar as aplicações e ferramentas existentes, mas existem vários motivos pelos quais é vantajoso utilizar a API REST de Ficheiros em vez do SMB, tais como:

- Se estiver a navegar na partilha de ficheiros a partir do Azure Bash Cloud Shell (que não pode montar partilhas de ficheiros através de SMB).
- Se estiver a tirar partido de recursos sem servidor, como o serviço [Funções do Azure](../../azure-functions/functions-overview.md). 
- Está a criar um serviço de valor-add que irá interagir com muitas ações de ficheiros Azure, tais como a realização de verificações de backup ou antivírus.

Os exemplos a seguir mostram como utilizar o CLI Azure para manipular a sua partilha de ficheiros Azure com o protocolo File REST. 

### <a name="create-a-directory"></a>Criar um diretório
Para criar um novo diretório chamado *myDirect* na raiz da sua partilha de ficheiros Azure, utilize o [`az storage directory create`](/cli/azure/storage/directory) comando:

```azurecli-interactive
az storage directory create \
   --account-name $storageAccountName \
   --account-key $storageAccountKey \
   --share-name $shareName \
   --name "myDirectory" \
   --output none
```

### <a name="upload-a-file"></a>Carregar um ficheiro
Para demonstrar como carregar um ficheiro utilizando o [`az storage file upload`](/cli/azure/storage/file) comando, crie primeiro um ficheiro para carregar na unidade de risco Cloud Shell. No exemplo seguinte, irá criar e carregar o ficheiro:

```azurecli-interactive
cd ~/clouddrive/
date > SampleUpload.txt

az storage file upload \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --source "SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Se estiver a executar a CLI do Azure localmente, substitua `~/clouddrive` por um caminho existente no seu computador.

Depois de fazer o upload do ficheiro, pode utilizar o [`az storage file list`](/cli/azure/storage/file) comando para se certificar de que o ficheiro foi enviado para a sua partilha de ficheiros Azure:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>Transferir um ficheiro
Pode utilizar o [`az storage file download`](/cli/azure/storage/file) comando para descarregar uma cópia do ficheiro que fez o upload para a unidade de risco Cloud Shell:

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -f SampleDownload.txt

az storage file download \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --dest "SampleDownload.txt" \
    --output none
```

### <a name="copy-files"></a>Copiar ficheiros
Uma tarefa comum é copiar ficheiros de uma partilha de ficheiros para outra partilha de ficheiros. Para demonstrar esta funcionalidade, crie uma nova partilha. Copie o ficheiro que carregou para esta nova partilha através do comando [az storage file copy](/cli/azure/storage/file/copy): 

```azurecli-interactive
otherShareName="myshare2"

az storage share create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $otherShareName \
    --quota 1024 \
    --enabled-protocols SMB \
    --output none

az storage directory create \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --name "myDirectory2" \
    --output none

az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-share $shareName \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share $otherShareName \
    --destination-path "myDirectory2/SampleCopy.txt"
```

Agora, se listar os ficheiros na nova partilha, deverá ver o ficheiro copiado:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $otherShareName \
    --path "myDirectory2" \
    --output table
```

Embora o `az storage file copy start` comando seja conveniente para movimentos de ficheiros entre ações de ficheiros Azure, para migrações e movimentos de dados maiores, recomendamos no `rsync` macOS e Linux e `robocopy` no Windows. `rsync` e `robocopy` usar o SMB para executar os movimentos de dados em vez da API FileREST.

## <a name="create-and-manage-share-snapshots"></a>Criar e gerir instantâneos de partilha
Outra tarefa útil que pode realizar com uma partilha de ficheiros do Azure é criar instantâneos de partilha. Os instantâneos preservam uma cópia de ponto no tempo das partilhas de ficheiros do Azure. Os instantâneos de partilha são semelhantes a algumas tecnologias de sistema operativo com as quais pode já estar familiarizado:

- [Snapshots lógicos do Gestor de Volume (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) para sistemas Linux.
- Instantâneos do [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) para macOS.
- [Serviço de Cópia Sombra de Volume (VSS)](/windows/desktop/VSS/volume-shadow-copy-service-portal) para sistemas de ficheiros Windows, tais como NTFS e ReFS.
 
Pode criar uma imagem partilhada utilizando o [`az storage share snapshot`](/cli/azure/storage/share) comando:

```azurecli-interactive
snapshot=$(az storage share snapshot \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>Procurar conteúdo de instantâneo de partilha
Pode procurar o conteúdo de um instantâneo de partilha ao transmitir o carimbo de data/hora do instantâneo de partilha capturado na variável `$snapshot` ao comando `az storage file list`:

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --snapshot $snapshot \
    --output table
```

### <a name="list-share-snapshots"></a>Listar instantâneos de partilha
Para ver a lista de instantâneos que criou para a sua partilha, utilize o seguinte comando:

```azurecli-interactive
az storage share list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --include-snapshot \
    --query "[? name== '$shareName' && snapshot!=null].snapshot" \
    --output tsv
```

### <a name="restore-from-a-share-snapshot"></a>Restaurar a partir de um instantâneo de partilha
Pode restaurar um ficheiro com o comando `az storage file copy start` utilizado anteriormente. Em primeiro lugar, elimine o ficheiro SampleUpload.txt que carregou, para que possa restaurá-lo a partir do instantâneo:

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --output none

# Build the source URI for a snapshot restore
URI=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')

URI=$URI$shareName"/myDirectory/SampleUpload.txt?sharesnapshot="$snapshot

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --source-uri $URI \
    --destination-share $shareName \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Eliminar um instantâneo de partilha
Pode eliminar uma imagem partilhada utilizando o [`az storage share delete`](/cli/azure/storage/share) comando. Utilize a variável que contém a referência `$SNAPSHOT` para o parâmetro `--snapshot`:

```azurecli-interactive
az storage share delete \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --name $shareName \
    --snapshot $snapshot \
    --output none
```

## <a name="clean-up-resources"></a>Limpar os recursos
Quando terminar, pode usar o [`az group delete`](/cli/azure/group) comando para remover o grupo de recursos e todos os recursos relacionados: 

```azurecli-interactive 
az group delete --name $resourceGroupName
```

Em alternativa, pode remover recursos individualmente.
- Para remover as partilhas de ficheiros do Azure criadas para este artigo:

    ```azurecli-interactive
    az storage share list \
            --account-name $storageAccountName \
            --account-key $storageAccountKey \
            --query "[].name" \
            --output tsv | \
        xargs -L1 bash -ec '\
            az storage share delete \
                --account-name "$storageAccountName" \
                --account-key "$storageAccountKey" \
                --name $0 \
                --delete-snapshots include \
                --output none'
    ```

- Para remover a própria conta de armazenamento. (Isto remove implicitamente as partilhas de ficheiros do Azure que criou e quaisquer outros recursos de armazenamento que possa ter criado, como um contentor de armazenamento de Blobs do Azure.)

    ```azurecli-interactive
    az storage account delete \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --yes
    ```

## <a name="next-steps"></a>Próximos passos
> [!div class="nextstepaction"]
> [O que são os Ficheiros do Azure?](storage-files-introduction.md)