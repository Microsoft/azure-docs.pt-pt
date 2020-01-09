---
title: Utilizar a CLI do Azure com o Storage do Azure
description: Saiba como usar a interface de linha de comando do Azure (CLI do Azure) com o armazenamento do Azure para criar e gerenciar contas de armazenamento e trabalhar com BLOBs e arquivos do Azure.
services: storage
author: tamram
ms.service: storage
ms.devlang: azurecli
ms.topic: article
ms.date: 06/02/2017
ms.author: tamram
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: f8e745b214ced865ac41d72bdfd5e44ca36b803a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460468"
---
# <a name="using-the-azure-cli-with-azure-storage"></a>Utilizar a CLI do Azure com o Storage do Azure

A CLI do Azure de software livre e de plataforma cruzada fornece um conjunto de comandos para trabalhar com a plataforma Azure. Ele fornece grande parte das mesmas funcionalidades encontradas no [portal do Azure](https://portal.azure.com), incluindo acesso a dados avançados.

Neste guia, mostraremos como usar o [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) para executar várias tarefas trabalhando com recursos em sua conta de armazenamento do Azure. Recomendamos que você baixe e instale ou atualize para a versão mais recente da CLI antes de usar este guia.

Os exemplos no guia pressupõem o uso do shell bash no Ubuntu, mas outras plataformas devem ser executadas de forma semelhante. 

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="prerequisites"></a>Pré-requisitos
Este guia pressupõe que você compreenda os conceitos básicos do armazenamento do Azure. Ele também pressupõe que você é capaz de atender aos requisitos de criação de conta especificados abaixo para o Azure e o serviço de armazenamento.

### <a name="accounts"></a>Contas
* **Conta do Azure**: se você ainda não tiver uma assinatura do Azure, [crie uma conta gratuita do Azure](https://azure.microsoft.com/free/).
* **Conta de Armazenamento**: veja [Criar uma conta de armazenamento](storage-quickstart-create-account.md) em [Sobre as contas de armazenamento do Azure](storage-create-storage-account.md).

### <a name="install-the-azure-cli"></a>Instalar a CLI do Azure

Baixe e instale o CLI do Azure seguindo as instruções descritas em [instalar o CLI do Azure](/cli/azure/install-az-cli2).

> [!TIP]
> Se você tiver problemas com a instalação, confira a seção [solução de problemas de instalação](/cli/azure/install-az-cli2) do artigo e o guia de solução de problemas de [instalação](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md) no github.
>

## <a name="working-with-the-cli"></a>Trabalhando com a CLI

Depois de instalar a CLI, você pode usar o comando `az` na sua interface de linha de comando (bash, terminal, prompt de comando) para acessar os comandos de CLI do Azure. Digite o comando `az` para ver uma lista completa dos comandos de base (a seguinte saída de exemplo foi truncada):

```
     /\
    /  \    _____   _ _ __ ___
   / /\ \  |_  / | | | \'__/ _ \
  / ____ \  / /| |_| | | |  __/
 /_/    \_\/___|\__,_|_|  \___|


Welcome to the cool new Azure CLI!

Here are the base commands:

    account          : Manage subscriptions.
    acr              : Manage Azure container registries.
    acs              : Manage Azure Container Services.
    ad               : Synchronize on-premises directories and manage Azure Active Directory
                       resources.
    ...
```

Na sua interface de linha de comando, execute o comando `az storage --help` para listar os subgrupos de comandos `storage`. As descrições dos subgrupos fornecem uma visão geral da funcionalidade que o CLI do Azure fornece para trabalhar com seus recursos de armazenamento.

```
Group
    az storage: Durable, highly available, and massively scalable cloud storage.

Subgroups:
    account  : Manage storage accounts.
    blob     : Object storage for unstructured data.
    container: Manage blob storage containers.
    cors     : Manage Storage service Cross-Origin Resource Sharing (CORS).
    directory: Manage file storage directories.
    entity   : Manage table storage entities.
    file     : File shares that use the standard SMB 3.0 protocol.
    logging  : Manage Storage service logging information.
    message  : Manage queue storage messages.
    metrics  : Manage Storage service metrics.
    queue    : Use queues to effectively scale applications according to traffic.
    share    : Manage file shares.
    table    : NoSQL key-value storage using semi-structured datasets.
```

## <a name="connect-the-cli-to-your-azure-subscription"></a>Conectar a CLI à sua assinatura do Azure

Para trabalhar com os recursos em sua assinatura do Azure, você deve primeiro fazer logon em sua conta do Azure com `az login`. Há várias maneiras pelas quais você pode fazer logon:

* **Logon interativo**: `az login`
* **Faça logon com nome de usuário e senha**: `az login -u johndoe@contoso.com -p VerySecret`
  * Isso não funciona com contas da Microsoft ou contas que usam a autenticação multifator.
* **Faça logon com uma entidade de serviço**: `az login --service-principal -u http://azure-cli-2016-08-05-14-31-15 -p VerySecret --tenant contoso.onmicrosoft.com`

## <a name="azure-cli-sample-script"></a>CLI do Azure script de exemplo

Em seguida, trabalharemos com um pequeno script de shell que emite alguns comandos de CLI do Azure básicos para interagir com os recursos de armazenamento do Azure. O script cria primeiro um novo contêiner em sua conta de armazenamento e, em seguida, carrega um arquivo existente (como um blob) para esse contêiner. Em seguida, ele lista todos os BLOBs no contêiner e, por fim, baixa o arquivo para um destino no computador local que você especificar.

```bash
#!/bin/bash
# A simple Azure Storage example script

export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_KEY=<storage_account_key>

export container_name=<container_name>
export blob_name=<blob_name>
export file_to_upload=<file_to_upload>
export destination_file=<destination_file>

echo "Creating the container..."
az storage container create --name $container_name

echo "Uploading the file..."
az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name

echo "Listing the blobs..."
az storage blob list --container-name $container_name --output table

echo "Downloading the file..."
az storage blob download --container-name $container_name --name $blob_name --file $destination_file --output table

echo "Done"
```

**Configurar e executar o script**

1. Abra seu editor de texto favorito e, em seguida, copie e cole o script anterior no editor.

2. Em seguida, atualize as variáveis do script para refletir suas definições de configuração. Substitua os seguintes valores conforme especificado:

   * **\<storage_account_name\>** O nome da sua conta de armazenamento.
   * **\<storage_account_key\>** A chave de acesso primária ou secundária para sua conta de armazenamento.
   * **\<container_name\>** Um nome o novo contêiner a ser criado, como "Azure-CLI-Sample-container".
   * **\<blob_name\>** Um nome para o blob de destino no contêiner.
   * **\<file_to_upload\>** O caminho para o arquivo pequeno no computador local, como "~/images/HelloWorld.png".
   * **\<destination_file\>** O caminho do arquivo de destino, como "~/downloadedImage.png".

3. Depois de atualizar as variáveis necessárias, salve o script e saia do editor. As próximas etapas pressupõem que você tenha nomeado seu script **my_storage_sample. sh**.

4. Marque o script como executável, se necessário: `chmod +x my_storage_sample.sh`

5. Execute o script. Por exemplo, no bash: `./my_storage_sample.sh`

Você deve ver uma saída semelhante à seguinte, e o **\<destination_file\>** especificado no script deve aparecer no computador local.

```
Creating the container...
{
  "created": true
}
Uploading the file...
Percent complete: %100.0
Listing the blobs...
Name       Blob Type      Length  Content Type              Last Modified
---------  -----------  --------  ------------------------  -------------------------
README.md  BlockBlob        6700  application/octet-stream  2017-05-12T20:54:59+00:00
Downloading the file...
Name
---------
README.md
Done
```

> [!TIP]
> A saída anterior está em formato de **tabela** . Você pode especificar qual formato de saída usar especificando o argumento `--output` nos comandos da CLI ou defini-lo globalmente usando `az configure`.
>

## <a name="manage-storage-accounts"></a>Gerir contas de armazenamento

### <a name="create-a-new-storage-account"></a>Criar uma nova conta de armazenamento
Para utilizar o Armazenamento do Azure, é necessária uma conta de armazenamento. Você pode criar uma nova conta de armazenamento do Azure depois de configurar o computador para se conectar à sua assinatura.

```azurecli
az storage account create \
    --location <location> \
    --name <account_name> \
    --resource-group <resource_group> \
    --sku <account_sku>
```

* `--location` [obrigatório]: local. Por exemplo, "oeste dos EUA".
* `--name` [obrigatório]: o nome da conta de armazenamento. O nome deve ter de 3 a 24 caracteres de comprimento e usar apenas caracteres alfanuméricos minúsculos.
* `--resource-group` [obrigatório]: nome do grupo de recursos.
* `--sku` [obrigatório]: a SKU da conta de armazenamento. Valores permitidos:
  * `Premium_LRS`
  * `Standard_GRS`
  * `Standard_LRS`
  * `Standard_RAGRS`
  * `Standard_ZRS`
  * `Standard_GZRS` (versão prévia)
  * `Standard_RAGZRS` (versão prévia)

### <a name="set-default-azure-storage-account-environment-variables"></a>Definir variáveis de ambiente padrão da conta de armazenamento do Azure

Você pode ter várias contas de armazenamento em sua assinatura do Azure. Para selecionar um deles a ser usado para todos os comandos de armazenamento subsequentes, você pode definir essas variáveis de ambiente:

Em primeiro lugar, apresente as chaves da conta de armazenamento com o comando [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive
az storage account keys list \
    --account-name <account_name> \
    --resource-group <resource_group> \
    --output table
```

Agora que você tem a chave, você pode defini-la e o nome da conta como variáveis de ambiente:

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_KEY=<key>
```

Outra maneira de definir uma conta de armazenamento padrão é usando uma cadeia de conexão. Primeiro, obtenha a cadeia de conexão com o comando `show-connection-string`:

```azurecli
az storage account show-connection-string \
    --name <account_name> \
    --resource-group <resource_group>
```

Em seguida, copie a cadeia de conexão de saída e defina a variável de ambiente `AZURE_STORAGE_CONNECTION_STRING` (talvez seja necessário colocar a cadeia de conexão entre aspas):

```azurecli
export AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
```

> [!NOTE]
> Todos os exemplos nas seções a seguir deste artigo pressupõem que você definiu o `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_KEY` variáveis de ambiente.

## <a name="create-and-manage-blobs"></a>Criar e gerir blobs
O armazenamento de BLOBs do Azure é um serviço para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários, que podem ser acessados de qualquer lugar do mundo por meio de HTTP ou HTTPS. Esta seção pressupõe que você já esteja familiarizado com os conceitos do armazenamento de BLOBs do Azure. Para obter informações detalhadas, consulte Introdução [ao armazenamento de BLOBs do Azure usando o .net](../blobs/storage-dotnet-how-to-use-blobs.md) e [conceitos do serviço blob](/rest/api/storageservices/blob-service-concepts).

### <a name="create-a-container"></a>Criar um contentor
Cada blob no armazenamento do Azure deve estar em um contêiner. Você pode criar um contêiner usando o comando `az storage container create`:

```azurecli
az storage container create --name <container_name>
```

Você pode definir um dos três níveis de acesso de leitura para um novo contêiner especificando o argumento de `--public-access` opcional:

* `off` (padrão): os dados do contêiner são privados para o proprietário da conta.
* `blob`: acesso de leitura público para BLOBs.
* `container`: acesso público de leitura e lista para todo o contêiner.

Para obter mais informações, veja [Manage anonymous read access to containers and blobs](../blobs/storage-manage-access-to-resources.md) (Gerir o acesso de leitura anónima a contentores e blobs).

### <a name="upload-a-blob-to-a-container"></a>Carregar um blob para um contentor
O armazenamento de BLOBs do Azure dá suporte a blobs de bloco, acréscimo e página. Carregue BLOBs em um contêiner usando o comando `blob upload`:

```azurecli
az storage blob upload \
    --file <local_file_path> \
    --container-name <container_name> \
    --name <blob_name>
```

Se você quiser carregar diretamente em uma pasta dentro do contêiner em sua conta de armazenamento, substitua `--name <blob_name>` por `--name <folder/blob_name>`.

 Por padrão, o comando `blob upload` carrega arquivos *. VHD para BLOBs de página ou BLOBs de blocos de outra forma. Para especificar outro tipo ao carregar um blob, você pode usar o argumento `--type`-os valores permitidos são `append`, `block`e `page`.

 Para obter mais informações sobre os diferentes tipos de BLOB, consulte [noções básicas sobre blobs de blocos, blobs de acréscimo e blobs de páginas](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).


### <a name="download-a-blob-from-a-container"></a>Transferir um blob a partir de um contentor
Este exemplo demonstra como baixar um blob de um contêiner:

```azurecli
az storage blob download \
    --container-name mycontainer \
    --name myblob.png \
    --file ~/mydownloadedblob.png
```

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Liste os BLOBs em um contêiner com o comando [AZ Storage blob List](/cli/azure/storage/blob) .

```azurecli
az storage blob list \
    --container-name mycontainer \
    --output table
```

### <a name="copy-blobs"></a>Copiar BLOBs
Pode copiar blobs dentro ou entre contas de armazenamento e regiões de forma assíncrona.

O exemplo seguinte demonstra como copiar blobs de uma conta de armazenamento para outra. Primeiro, vamos criar um contentor na conta de armazenamento de origem, especificando o acesso de leitura público para os respetivos blobs. Em seguida, vamos carregar um ficheiro para o contentor e, por último, vamos copiar o blob desse contentor para um contentor na conta de armazenamento de destino.

```azurecli
# Create container in source account
az storage container create \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --name sourcecontainer \
    --public-access blob

# Upload blob to container in source account
az storage blob upload \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --container-name sourcecontainer \
    --file ~/Pictures/sourcefile.png \
    --name sourcefile.png

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

No exemplo acima, o contêiner de destino já deve existir na conta de armazenamento de destino para que a operação de cópia tenha sucesso. Além disso, o blob de origem especificado no argumento `--source-uri` tem de incluir um token de assinatura de acesso partilhado (SAS) ou tem de estar acessível publicamente, tal como acontece neste exemplo.

### <a name="delete-a-blob"></a>Eliminar um blob
Para excluir um blob, use o comando `blob delete`:

```azurecli
az storage blob delete --container-name <container_name> --name <blob_name>
```

### <a name="set-the-content-type"></a>Definir o tipo de conteúdo

O tipo de conteúdo, também denominado tipo de MIME, identifica o formato dos dados no blob. Os browsers e outro software utilizam o tipo de conteúdo para determinar como processar os dados. Por exemplo, o tipo de conteúdo para imagens PNG é `image/png`. Para definir o tipo de conteúdo, use o comando `blob update`:

```azurecli
az storage blob update
    --container-name <container_name> 
    --name <blob_name>
    --content-type <content_type>
```

## <a name="create-and-manage-file-shares"></a>Criar e gerenciar compartilhamentos de arquivos
Os arquivos do Azure oferecem armazenamento compartilhado para aplicativos que usam o protocolo SMB. Microsoft Azure máquinas virtuais e serviços de nuvem, bem como aplicativos locais, podem compartilhar dados de arquivo por meio de compartilhamentos montados. Você pode gerenciar compartilhamentos de arquivos e dados de arquivo por meio do CLI do Azure. Para obter mais informações sobre os arquivos do Azure, consulte [introdução aos arquivos do Azure](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Criar uma partilha de ficheiros
Um compartilhamento de arquivos do Azure é um compartilhamento de arquivos SMB no Azure. Todos os diretórios e arquivos devem ser criados em um compartilhamento de arquivos. Uma conta pode conter um número ilimitado de compartilhamentos e um compartilhamento pode armazenar um número ilimitado de arquivos, até os limites de capacidade da conta de armazenamento. O exemplo a seguir cria um compartilhamento de arquivos chamado **MyShare**.

```azurecli
az storage share create --name myshare
```

### <a name="create-a-directory"></a>Criar um diretório
Um diretório fornece uma estrutura hierárquica em um compartilhamento de arquivos do Azure. O exemplo a seguir cria um diretório chamado **mydir** no compartilhamento de arquivos.

```azurecli
az storage directory create --name myDir --share-name myshare
```

Um caminho de diretório pode incluir vários níveis, por exemplo, **dir1/dir2**. No entanto, você deve garantir que todos os diretórios pai existam antes de criar um subdiretório. Por exemplo, para o caminho **dir1/dir2**, você deve primeiro criar o diretório **dir1**e, em seguida, criar o diretório **dir2**.

### <a name="upload-a-local-file-to-a-share"></a>Carregar um arquivo local em um compartilhamento
O exemplo a seguir carrega um arquivo de **~/Temp/SampleFile.txt** para a raiz do compartilhamento de arquivos **MyShare** . O argumento `--source` especifica o arquivo local existente a ser carregado.

```azurecli
az storage file upload --share-name myshare --source ~/temp/samplefile.txt
```

Assim como acontece com a criação de diretório, você pode especificar um caminho de diretório dentro do compartilhamento para carregar o arquivo em um diretório existente no compartilhamento:

```azurecli
az storage file upload --share-name myshare/myDir --source ~/temp/samplefile.txt
```

Um arquivo no compartilhamento pode ter até 1 TB de tamanho.

### <a name="list-the-files-in-a-share"></a>Listar os arquivos em um compartilhamento
Você pode listar arquivos e diretórios em um compartilhamento usando o comando `az storage file list`:

```azurecli
# List the files in the root of a share
az storage file list --share-name myshare --output table

# List the files in a directory within a share
az storage file list --share-name myshare/myDir --output table

# List the files in a path within a share
az storage file list --share-name myshare --path myDir/mySubDir/MySubDir2 --output table
```

### <a name="copy-files"></a>Copiar ficheiros      
Você pode copiar um arquivo para outro arquivo, um arquivo para um BLOB ou um blob para um arquivo. Por exemplo, para copiar um arquivo para um diretório em um compartilhamento diferente:        
        
```azurecli
az storage file copy start \
--source-share share1 --source-path dir1/file.txt \
--destination-share share2 --destination-path dir2/file.txt     
```

## <a name="create-share-snapshot"></a>Criar instantâneo de compartilhamento
Você pode criar um instantâneo de compartilhamento usando o comando `az storage share snapshot`:

```cli
az storage share snapshot -n <share name>
```

Saída de Exemplo
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="list-share-snapshots"></a>Listar instantâneos de partilha

Você pode listar instantâneos de compartilhamento de um compartilhamento específico usando `az storage share list --include-snapshots`

```cli
az storage share list --include-snapshots
```

**Saída de exemplo**
```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

### <a name="browse-share-snapshots"></a>Procurar instantâneos de partilha
Você também pode procurar um instantâneo de compartilhamento específico para exibir seu conteúdo usando `az storage file list`. É possível especificar o nome do compartilhamento `--share-name <snare name>` e o carimbo de data/hora `--snapshot '2017-10-04T19:45:18.0000000Z'`

```azurecli-interactive
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

**Saída de exemplo**
```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
### <a name="restore-from-share-snapshots"></a>Restaurar de instantâneos de compartilhamento

Você pode restaurar um arquivo copiando ou baixando um arquivo de um instantâneo de compartilhamento usando o comando `az storage file download`

```azurecli-interactive
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```
**Saída de exemplo**
```
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```
## <a name="delete-share-snapshot"></a>Excluir instantâneo de compartilhamento
Você pode excluir um instantâneo de compartilhamento usando o comando `az storage share delete` fornecendo `--snapshot` parâmetro com o carimbo de data/hora do instantâneo de compartilhamento:

```cli
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Saída de Exemplo
```json
{
  "deleted": true
}
```

## <a name="next-steps"></a>Passos seguintes
Aqui estão alguns recursos adicionais para saber mais sobre como trabalhar com o CLI do Azure. 

* [Introdução ao CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Referência de comandos da CLI do Azure](/cli/azure)
* [CLI do Azure no GitHub](https://github.com/Azure/azure-cli)
