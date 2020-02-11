---
title: Utilização do clássico CLI azure com armazenamento Azure  Microsoft Docs
description: Saiba como utilizar a interface clássica de linha de comando (CLI) azure com armazenamento Azure para criar e gerir contas de armazenamento e trabalhar com blobs e ficheiros Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/30/2017
ms.author: tamram
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 6554385a879b054153dcb808c3dff4b60c136458
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120855"
---
# <a name="using-the-azure-classic-cli-with-azure-storage"></a>Usando o CLI clássico azure com armazenamento azure

## <a name="overview"></a>Descrição geral

O clássico CLI azure fornece um conjunto de comandos de código aberto, plataforma cruzada para trabalhar com a Plataforma Azure. Fornece grande parte da mesma funcionalidade encontrada no [portal Azure,](https://portal.azure.com) bem como uma rica funcionalidade de acesso a dados.

Neste guia, vamos explorar como usar o [CLI clássico azure](../../cli-install-nodejs.md) para realizar uma variedade de tarefas de desenvolvimento e administração com o Armazenamento Azure. Recomendamos que descarregue e instale ou atualize para o mais recente CLI clássico antes de utilizar este guia.

Este guia assume que compreende os conceitos básicos do Armazenamento Azure. O guia fornece uma série de scripts para demonstrar o uso do CLÁSSICO CLI com Armazenamento Azure. Certifique-se de atualizar as variáveis do script com base na sua configuração antes de executar cada script.

> [!NOTE]
> O guia fornece o comando cli clássico azure e exemplos de scripts para contas de armazenamento clássicas. Consulte [a utilização do Azure CLI para Mac, Linux e Windows com Azure Resource Management](../../virtual-machines/azure-cli-arm-commands.md#storage-objects) para comandos CLI clássicos do Azure para contas de armazenamento do Gestor de Recursos.
>
>

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="get-started-with-azure-storage-and-the-azure-classic-cli-in-5-minutes"></a>Começa com o Azure Storage e o clássico CLI azure em 5 minutos
Este guia utiliza Ubuntu por exemplo, mas outras plataformas de SO devem funcionar da mesma forma.

**Novo para Azure:** Obtenha uma subscrição do Microsoft Azure e uma conta Microsoft associada a essa subscrição. Para obter informações sobre as opções de compra do Azure, consulte [O Teste Gratuito,](https://azure.microsoft.com/pricing/free-trial/) [Opções de Compra](https://azure.microsoft.com/pricing/purchase-options/)e [Ofertas de Membros](https://azure.microsoft.com/pricing/member-offers/) (para membros da MSDN, Microsoft Partner Network e BizSpark, e outros programas da Microsoft).

Consulte as funções de [administrador de atribuição no Azure Ative Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) para obter mais informações sobre subscrições do Azure.

**Depois de criar uma subscrição e conta microsoft Azure:**

1. Descarregue e instale o CLI clássico azure seguindo as instruções descritas no [Clássico CLI azure](../../cli-install-nodejs.md).
2. Uma vez instalado o CLÁSSICO CLI, poderá utilizar o comando azul a partir da sua interface de linha de comando (Bash, Terminal, Comando) para aceder aos comandos CLÁSSICOs do CLI. Digite o comando _azul_ e deverá ver a seguinte saída.

    ![Saída de comando Azure](./media/storage-azure-cli/azure_command.png)   
3. Na interface da linha de comando, o tipo `azure storage` para listar todos os comandos de armazenamento azul e obter uma primeira impressão das funcionalidades que o CLI clássico fornece. Pode digitar o nome de comando com um parâmetro **de -h** (por exemplo, `azure storage share create -h`) para ver detalhes da sintaxe de comando.
4. Agora, vamos dar-lhe um simples script que mostra comandos cli clássicos básicos para aceder ao Armazenamento Azure. O script irá primeiro pedir-lhe para definir duas variáveis para a sua conta de armazenamento e chave. Em seguida, o script criará um novo recipiente nesta nova conta de armazenamento e enviará um ficheiro de imagem existente (blob) para esse recipiente. Depois de o script listar todas as bolhas nesse recipiente, irá descarregar o ficheiro de imagem para o diretório de destino que existe no computador local.

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

5. No seu computador local, abra o seu editor de texto preferido (vim, por exemplo). Digite o guião acima no seu editor de texto.
6. Agora, precisa atualizar as variáveis do script com base nas definições de configuração.

   * **<storage_account_name>** Utilize o nome dado no script ou introduza um novo nome para a sua conta de armazenamento. **Importante:** O nome da conta de armazenamento deve ser único em Azure. Deve ser minúsculo, também!
   * **<storage_account_key>** A chave de acesso da sua conta de armazenamento.
   * **<container_name>** Utilize o nome dado no guião ou introduza um novo nome para o seu recipiente.
   * **<image_to_upload>** Insira um caminho para uma imagem no seu computador local, tais como: "~/images/HelloWorld.png".
   * **<destination_folder>** Insira um caminho para um diretório local para armazenar ficheiros descarregados do Azure Storage, tais como: "~/downloadImages".
7. Depois de atualizar as variáveis necessárias no vim, prima as combinações de teclas `ESC`, `:`, `wq!` para salvar o script.
8. Para executar este script, basta escrever o nome do ficheiro script na consola bash. Depois deste script ser executado, deve ter uma pasta de destino local que inclua o ficheiro de imagem descarregado. A imagem que se segue mostra uma saída de exemplo:

Após a eção do script, deverá ter uma pasta de destino local que inclua o ficheiro de imagem descarregado.

## <a name="manage-storage-accounts-with-the-azure-classic-cli"></a>Gerir contas de armazenamento com o CLI clássico do Azure
### <a name="connect-to-your-azure-subscription"></a>Ligar à sua subscrição do Azure
Enquanto a maioria dos comandos de armazenamento funcionará sem uma subscrição Azure, recomendamos que se ligue à sua subscrição a partir do CLÁSSICO CLI.

### <a name="create-a-new-storage-account"></a>Criar uma nova conta de armazenamento
Para utilizar o armazenamento Azure, necessitará de uma conta de armazenamento. Pode criar uma nova conta de armazenamento Azure depois de configurar o seu computador para se ligar à sua subscrição.

```azurecli
azure storage account create <account_name>
```

O nome da sua conta de armazenamento deve ter entre 3 e 24 caracteres de comprimento e utilizar apenas números e letras minúsculas.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>Definir uma conta de armazenamento de Azure padrão em variáveis ambientais
Pode ter várias contas de armazenamento na sua subscrição. Pode escolher um deles e defini-lo nas variáveis ambientais para todos os comandos de armazenamento na mesma sessão. Isto permite-lhe executar os comandos de armazenamento CLÁSSICOs CLI sem especificar explicitamente a conta de armazenamento e a chave.

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Outra forma de definir uma conta de armazenamento predefinida é usar a cadeia de ligação. Em primeiro lugar, obtenha a cadeia de ligação por comando:

```azurecli
azure storage account connectionstring show <account_name>
```

Em seguida, copie a cadeia de ligação de saída e coloque-a na variável ambiente:

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

## <a name="create-and-manage-blobs"></a>Criar e gerir blobs
O armazenamento da Azure Blob é um serviço para armazenar grandes quantidades de dados não estruturados, como texto ou dados binários, que podem ser acedidos a partir de qualquer parte do mundo através de HTTP ou HTTPS. Esta secção assume que já está familiarizado com os conceitos de armazenamento do Azure Blob. Para obter informações detalhadas, consulte Iniciar com [o armazenamento Azure Blob utilizando .NET](../blobs/storage-dotnet-how-to-use-blobs.md) e [Blob Service Concepts](https://msdn.microsoft.com/library/azure/dd179376.aspx).

### <a name="create-a-container"></a>Criar um contentor
Todas as bolhas no armazém do Azure devem estar num contentor. Pode criar um recipiente privado utilizando o comando `azure storage container create`:

```azurecli
azure storage container create mycontainer
```

> [!NOTE]
> Existem três níveis de acesso de leitura anónimo: **Off,** **Blob,** e **Container**. Para evitar o acesso anónimo a bolhas, defina o parâmetro de permissão para **desligar**. Por predefinição, o novo contentor é privado e só pode ser acedido pelo proprietário da conta. Para permitir o acesso anónimo ao público aos recursos blob, mas não para contentor metadados ou para a lista de bolhas no recipiente, defina o parâmetro de permissão para **blob**. Para permitir o acesso completo ao público aos recursos blob, metadados de contentores e a lista de bolhas no recipiente, defina o parâmetro de permissão para **contentor**. Para obter mais informações, veja [Manage anonymous read access to containers and blobs](../blobs/storage-manage-access-to-resources.md) (Gerir o acesso de leitura anónima a contentores e blobs).
>
>

### <a name="upload-a-blob-into-a-container"></a>Carregar um blob para um contentor
O Blob Storage do Azure suporta blobs de blocos e blobs de páginas. Para mais informações, consulte [Understanding Block Blobs, Append Blobs e Page Blobs](https://msdn.microsoft.com/library/azure/ee691964.aspx).

Para fazer o upload de bolhas num recipiente, pode utilizar o `azure storage blob upload`. Por padrão, este comando envia os ficheiros locais para uma bolha de bloco. Para especificar o tipo para a bolha, pode utilizar o parâmetro `--blobtype`.

```azurecli
azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob
```

### <a name="download-blobs-from-a-container"></a>Descarregue bolhas de um recipiente
O exemplo que se segue demonstra como descarregar bolhas de um contentor.

```azurecli
azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'
```

### <a name="copy-blobs"></a>Copiar bolhas
Pode copiar blobs dentro ou entre contas de armazenamento e regiões de forma assíncrona.

O exemplo seguinte demonstra como copiar blobs de uma conta de armazenamento para outra. Nesta amostra criamos um recipiente onde as bolhas são publicamente, anonimamente acessíveis.

```azurecli
azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer
```

Esta amostra executa uma cópia assíncrona. Pode monitorizar o estado de cada operação de cópia executando a operação `azure storage blob copy show`.

Note que o URL de origem fornecido para a operação de cópia deve ser acessível ao público ou incluir um símbolo SAS (assinatura de acesso partilhado).

### <a name="delete-a-blob"></a>Eliminar um blob
Para eliminar uma bolha, utilize o comando abaixo:

```azurecli
azure storage blob delete mycontainer myBlockBlob2
```

## <a name="create-and-manage-file-shares"></a>Criar e gerir ações de ficheiros
O Azure Files oferece armazenamento partilhado para aplicações utilizando o protocolo Padrão SMB. As máquinas virtuais e os serviços de nuvem do Microsoft Azure, bem como as aplicações no local, podem partilhar dados de ficheiros através de ações montadas. Pode gerir as partilhas de ficheiros e arquivar dados através do CLI clássico. Para obter mais informações sobre ficheiros Azure, consulte [Introdução aos Ficheiros Azure](../files/storage-files-introduction.md).

### <a name="create-a-file-share"></a>Criar uma partilha de ficheiros
Uma participação de ficheiro SMB é uma participação de ficheiro SMB no Azure. Todos os diretórios e ficheiros devem ser criados numa partilha de ficheiros. Uma conta pode conter um número ilimitado de ações, e uma ação pode armazenar um número ilimitado de ficheiros, até aos limites de capacidade da conta de armazenamento. O exemplo seguinte cria uma parte de ficheiro chamada **myshare**.

```azurecli
azure storage share create myshare
```

### <a name="create-a-directory"></a>Criar um diretório
Um diretório fornece uma estrutura hierárquica opcional para uma partilha de ficheiros Azure. O exemplo seguinte cria um diretório chamado **myDir** na partilha de ficheiros.

```azurecli
azure storage directory create myshare myDir
```

Note que o percurso do diretório pode incluir vários níveis, *por exemplo,* **a/b**. No entanto, deve garantir que todos os diretórios parentais existam. Por exemplo, para o caminho **a/b,** deve criar um diretório **primeiro,** em seguida, criar o diretório **b**.

### <a name="upload-a-local-file-to-directory"></a>Faça upload de um ficheiro local para o diretório
O exemplo seguinte envia um ficheiro de **~/temp/samplefile.txt** para o diretório **myDir.** Editar o caminho do ficheiro de modo a apontar para um ficheiro válido na sua máquina local:

```azurecli
azure storage file upload '~/temp/samplefile.txt' myshare myDir
```

Note que um ficheiro na parte pode ter até 1 TB de tamanho.

### <a name="list-the-files-in-the-share-root-or-directory"></a>Enumerar os ficheiros na raiz de partilha ou no diretório
Pode listar os ficheiros e subdiretórios numa raiz de partilha ou num diretório utilizando o seguinte comando:

```azurecli
azure storage file list myshare myDir
```

Note que o nome do diretório é opcional para a operação de listagem. Se omitir, o comando lista o conteúdo do diretório raiz da parte.

### <a name="copy-files"></a>Copiar ficheiros
Começando pela versão 0.9.8 do CLI clássico, pode copiar um ficheiro para outro ficheiro, um ficheiro para uma bolha ou uma bolha num ficheiro. Abaixo demonstramos como executar estas operações de cópia utilizando comandos CLI. Para copiar um ficheiro para o novo diretório:

```azurecli
azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare
    --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

Para copiar uma bolha para um diretório de ficheiros:

```azurecli
azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello
    --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

## <a name="next-steps"></a>Passos Seguintes

Pode encontrar aqui referência de comando CLI clássico do Azure para trabalhar com recursos de Armazenamento:

* [Comandos CLI clássicos do Azure no modo Gestor de Recursos](../../virtual-machines/azure-cli-arm-commands.md#storage-objects)
* [Comandos CLI clássicos do Azure em modo de Gestão de Serviços Azure](../../cli-install-nodejs.md)

Também pode gostar de experimentar a versão mais recente do [Azure CLI,](../storage-azure-cli.md)para utilização com o modelo de implementação do Gestor de Recursos.
