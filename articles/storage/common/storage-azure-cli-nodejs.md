---
title: Utilizar a CLI clássica do Azure com o armazenamento do Azure | Documentos da Microsoft
description: Saiba como utilizar a Interface de linha de comandos clássico do Azure (CLI) com o armazenamento do Azure para criar e gerir contas de armazenamento e trabalhar com ficheiros e de blobs do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/30/2017
ms.author: tamram
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 88f713c5695e2453edc58d072899aa417f0514af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65147047"
---
# <a name="using-the-azure-classic-cli-with-azure-storage"></a>Utilizar a CLI clássica do Azure com o armazenamento do Azure

## <a name="overview"></a>Descrição geral

A CLI clássica do Azure fornece um conjunto de código-fonte aberto, comandos de várias plataformas para trabalhar com a plataforma do Azure. Ele fornece grande parte a mesma funcionalidade encontrada no [portal do Azure](https://portal.azure.com) dados avançados, bem como acessam a funcionalidade.

Neste guia, vamos explorar como usar [CLI clássica do Azure](../../cli-install-nodejs.md) para realizar uma variedade de tarefas de desenvolvimento e de administração com o armazenamento do Azure. Recomendamos que baixa e instalar ou atualizar para a mais recente CLI clássica antes de utilizar este guia.

Este guia assume que compreende os conceitos básicos do armazenamento do Azure. O guia fornece uma série de scripts para demonstrar a utilização da CLI clássica do armazenamento do Azure. Certifique-se de que Atualize as variáveis de script com base na sua configuração antes de executar cada script.

> [!NOTE]
> Este guia fornece os Azure CLI comando e script exemplos clássicos para contas de armazenamento clássicas. Ver [com a CLI do Azure para Mac, Linux e Windows com o Azure Resource Management](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects) para comandos da CLI clássicos do Azure para contas de armazenamento do Resource Manager.
>
>

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="get-started-with-azure-storage-and-the-azure-classic-cli-in-5-minutes"></a>Introdução ao armazenamento do Azure e a CLI clássica do Azure em 5 minutos
Este guia utiliza Ubuntu para obter exemplos, mas outras plataformas de SO devem efetuar da mesma forma.

**Novo para o Azure:** Obtenha uma subscrição do Microsoft Azure e uma conta Microsoft associada a essa subscrição. Para obter informações sobre as opções de compra do Azure, consulte [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/), [opções de compra](https://azure.microsoft.com/pricing/purchase-options/), e [ofertas para membros](https://azure.microsoft.com/pricing/member-offers/) (para os membros do MSDN e BizSpark, Microsoft Partner Network e outros programas da Microsoft).

Ver [atribuir funções de administrador no Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) para obter mais informações sobre as subscrições do Azure.

**Depois de criar uma subscrição do Microsoft Azure e uma conta:**

1. Transferir e instalar a CLI do Azure clássica, seguindo as instruções descritas [instalar a CLI do Azure clássica](../../cli-install-nodejs.md).
2. Depois de ter sido instalada a CLI clássica, será capaz de usar o comando do azure a partir da sua interface de linha de comandos (Bash, Terminal, linha de comandos) para acessar os comandos da CLI clássicos. Tipo de _azure_ comando e deverá ver o resultado seguinte.

    ![Saída do comando do Azure](./media/storage-azure-cli/azure_command.png)   
3. Na interface de linha de comandos, escreva `azure storage` para listar todos os comandos de armazenamento do azure e obtenha sua primeira impressão das funcionalidades da CLI clássica fornece. Pode digitar o nome do comando com **-h** parâmetro (por exemplo, `azure storage share create -h`) para ver os detalhes da sintaxe de comando.
4. Agora, mostraremos um script simples que mostra os comandos da CLI clássicos básicos para aceder ao armazenamento do Azure. O script primeiro solicitará que definir duas variáveis para a conta de armazenamento e a chave. Em seguida, o script irá criar um novo contentor nesta nova conta de armazenamento e carregar um ficheiro de imagem existente (blob) para esse contentor. Depois do script apresenta uma lista de todos os blobs existentes nesse contentor, ele irá transferir o ficheiro de imagem para o diretório de destino que existe no computador local.

    ```azurecli
    #!/bin/bash
    # A simple Azure storage example

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export image_to_upload=<image_to_upload>
    export destination_folder=<destination_folder>

    echo "Creating the container..."
    azure storage container create $container_name

    echo "Uploading the image..."
    azure storage blob upload $image_to_upload $container_name $blob_name

    echo "Listing the blobs..."
    azure storage blob list $container_name

    echo "Downloading the image..."
    azure storage blob download $container_name $blob_name $destination_folder

    echo "Done"
    ```

5. No seu computador local, abra o seu editor de texto preferido (vim por exemplo). Escreva o script acima no editor de texto.
6. Agora, tem de atualizar as variáveis de script com base nas suas definições de configuração.

   * **< Storage_account_name >** utilizar o nome fornecido no script ou introduza um novo nome para a sua conta de armazenamento. **Importante:** O nome da conta de armazenamento tem de ser exclusivo no Azure. Tem de ser minúscula, demais!
   * **< Storage_account_key >** a chave de acesso da conta de armazenamento.
   * **< Container_name >** utilizar o nome fornecido no script ou introduza um novo nome para o contentor.
   * **< Image_to_upload >** introduza um caminho para uma imagem no seu computador local, como: "~ / images/HelloWorld.png".
   * **< Destination_folder >** introduza um caminho para um diretório local para armazenar os ficheiros transferidos a partir do armazenamento do Azure, tal como: "~/downloadImages".
7. Depois de atualizou as variáveis necessárias no vim, pressione combinações de teclas `ESC`, `:`, `wq!` para guardar o script.
8. Para executar este script, basta digite o nome de ficheiro de script na consola do bash. Depois deste script é executado, deve ter uma pasta de destino local que inclui o ficheiro de imagem transferido. Captura de ecrã seguinte mostra um exemplo de saída:

Depois do script é executado, deve ter uma pasta de destino local que inclui o ficheiro de imagem transferido.

## <a name="manage-storage-accounts-with-the-azure-classic-cli"></a>Gerir contas de armazenamento com a CLI do Azure clássica
### <a name="connect-to-your-azure-subscription"></a>Ligar à sua subscrição do Azure
Embora a maioria dos comandos armazenamento irá funcionar sem uma subscrição do Azure, recomendamos-lhe ligar à sua subscrição a partir da CLI clássica.

### <a name="create-a-new-storage-account"></a>Criar uma nova conta de armazenamento
Para utilizar o armazenamento do Azure, terá uma conta de armazenamento. Pode criar uma nova conta de armazenamento do Azure após ter configurado o computador para ligar à sua subscrição.

```azurecli
azure storage account create <account_name>
```

O nome da conta de armazenamento tem de ter entre 3 e 24 carateres de comprimento e utilizar números e letras minúsculas apenas.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Definir uma conta de armazenamento do Azure predefinida em variáveis de ambiente
Pode ter várias contas de armazenamento na sua subscrição. Pode escolher um deles e defini-lo nas variáveis de ambiente para todos os comandos de armazenamento na mesma sessão. Isto permite-lhe executar os comandos de armazenamento da CLI clássicos sem especificar a conta de armazenamento e a chave explicitamente.

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Outra forma de definir uma conta de armazenamento predefinida está a utilizar cadeia de ligação. Em primeiro lugar para obter a cadeia de ligação comando:

```azurecli
azure storage account connectionstring show <account_name>
```

Em seguida, copie a cadeia de ligação de saída e defina-o à variável de ambiente:

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

## <a name="create-and-manage-blobs"></a>Criar e gerir blobs
Armazenamento de Blobs do Azure é um serviço para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários, que podem ser acedidos em qualquer local no mundo através de HTTP ou HTTPS. Esta secção assume que já está familiarizado com os conceitos de armazenamento de Blobs do Azure. Para obter informações detalhadas, consulte [introdução ao armazenamento de Blobs do Azure com .NET](../blobs/storage-dotnet-how-to-use-blobs.md) e [conceitos do serviço Blob](https://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="create-a-container"></a>Criar um contentor
Todos os BLOBs no armazenamento do Azure tem de ser num contentor. Pode criar um contentor privada com o `azure storage container create` comando:

```azurecli
azure storage container create mycontainer
```

> [!NOTE]
> Existem três níveis de acesso de leitura anónimo: **Desativar**, **Blob**, e **contentor**. Para impedir o acesso anónimo para blobs, defina o parâmetro de permissão para **desativar**. Por predefinição, o novo contentor é privado e pode ser acedido apenas pelo proprietário da conta. Para permitir anónimo o acesso de leitura público aos recursos de blob, mas não para os metadados do contentor ou à lista de blobs no contentor, defina o parâmetro de permissão como **BLOBs**. Para permitir o acesso de leitura público completa para recursos, metadados de contentor e a lista de blobs no contentor de BLOBs, defina o parâmetro de permissão para **contentor**. Para obter mais informações, veja [Manage anonymous read access to containers and blobs](../blobs/storage-manage-access-to-resources.md) (Gerir o acesso de leitura anónima a contentores e blobs).
>
>

### <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor
O Blob Storage do Azure suporta blobs de blocos e blobs de páginas. Para obter mais informações, consulte [Noções básicas sobre Blobs de blocos, Blobs de acréscimo e Blobs de páginas](https://msdn.microsoft.com/library/azure/ee691964.aspx).

Para carregar blobs para um contentor, pode utilizar o `azure storage blob upload`. Por predefinição, este comando carrega os ficheiros locais para um blob de blocos. Para especificar o tipo para o blob, pode utilizar o `--blobtype` parâmetro.

```azurecli
azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob
```

### <a name="download-blobs-from-a-container"></a>Transferir blobs a partir de um contentor
O exemplo seguinte demonstra como transferir blobs a partir de um contentor.

```azurecli
azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'
```

### <a name="copy-blobs"></a>Copiar blobs
Pode copiar blobs dentro ou entre contas de armazenamento e regiões de forma assíncrona.

O exemplo seguinte demonstra como copiar blobs de uma conta de armazenamento para outra. Neste exemplo, vamos criar um contentor no qual blobs publicamente, estão acessíveis anonimamente.

```azurecli
azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer
```

Este exemplo efetua uma cópia assíncrona. Pode monitorizar o estado de cada operação de cópia, executando o `azure storage blob copy show` operação.

Tenha em atenção que o URL de origem fornecido para a operação de cópia tem de estar acessível publicamente, ou incluir um token SAS (assinatura de acesso partilhado).

### <a name="delete-a-blob"></a>Eliminar um blob
Para eliminar um blob, utilize o comando abaixo:

```azurecli
azure storage blob delete mycontainer myBlockBlob2
```

## <a name="create-and-manage-file-shares"></a>Criar e gerir partilhas de ficheiros
Os ficheiros do Azure oferece armazenamento partilhado para aplicações utilizando o protocolo SMB padrão. As máquinas virtuais do Microsoft Azure e serviços cloud, bem como aplicações no local, podem partilhar dados de ficheiros através de partilhas montadas. Pode gerir partilhas de ficheiros e dados de ficheiros através da CLI clássica. Para obter mais informações sobre ficheiros do Azure, consulte [Introduction to Azure Files](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Criar uma partilha de ficheiros
Uma partilha de ficheiros do Azure é uma partilha de ficheiros SMB no Azure. Todos os diretórios e arquivos devem ser criados numa partilha de ficheiros. Uma conta pode conter um número ilimitado de partilhas e uma partilha pode armazenar um número ilimitado de ficheiros, até aos limites de capacidade da conta de armazenamento. O exemplo seguinte cria uma partilha de ficheiros com o nome **myshare**.

```azurecli
azure storage share create myshare
```

### <a name="create-a-directory"></a>Criar um diretório
Um diretório fornece uma estrutura hierárquica opcional para uma partilha de ficheiros do Azure. O exemplo seguinte cria um diretório chamado **myDir** na partilha de ficheiros.

```azurecli
azure storage directory create myshare myDir
```

Tenha em atenção que o caminho de diretório pode incluir vários níveis *por exemplo,* , **um / b**. No entanto, tem de se certificar de que todos os diretórios existem. Por exemplo, para o caminho **um / b**, tem de criar o diretório **uma** em primeiro lugar, em seguida, criar o diretório **b**.

### <a name="upload-a-local-file-to-directory"></a>Carregar um ficheiro local para o diretório
O exemplo seguinte carrega um ficheiro a partir **~/temp/samplefile.txt** para o **myDir** diretório. Edite o caminho do ficheiro para que ele aponta para um ficheiro válido no seu computador local:

```azurecli
azure storage file upload '~/temp/samplefile.txt' myshare myDir
```

Tenha em atenção que um ficheiro na partilha pode ser até 1 TB de tamanho.

### <a name="list-the-files-in-the-share-root-or-directory"></a>Listar os ficheiros na partilha de raiz ou diretório
Pode listar os ficheiros e subdiretórios na raiz de uma partilha ou um diretório com o seguinte comando:

```azurecli
azure storage file list myshare myDir
```

Observe que o nome do diretório é opcional para a operação de listagem. Se for omitido, o comando lista o conteúdo do diretório de raiz da partilha.

### <a name="copy-files"></a>Copiar ficheiros
A partir da versão 0.9.8 da CLI clássica, pode copiar um ficheiro para outro ficheiro, um ficheiro para um blob ou um blob para um ficheiro. Abaixo, demonstramos como executar estas operações de cópia através de comandos da CLI. Para copiar um ficheiro para o novo diretório:

```azurecli
azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare
    --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

Para copiar um blob para um diretório do ficheiro:

```azurecli
azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello
    --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

## <a name="next-steps"></a>Próximos Passos

Encontrará a referência de comando da CLI clássica do Azure para trabalhar com recursos de armazenamento aqui:

* [Comandos da CLI clássicos do Azure no modo Resource Manager](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects)
* [Comandos da CLI clássicos do Azure no modo de gestão de serviço do Azure](../../cli-install-nodejs.md)

Também queira experimentar a versão mais recente do [CLI do Azure](../storage-azure-cli.md), para utilização com o modelo de implementação do Resource Manager.
