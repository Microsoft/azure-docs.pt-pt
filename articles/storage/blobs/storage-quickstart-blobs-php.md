---
title: Início Rápido do Azure – Criar um blob no armazenamento de objetos com PHP | Microsoft Docs
description: Aprenda rapidamente a transferir objetos de/para o armazenamento de Blobs do Azure com PHP
services: storage
author: roygara
ms.service: storage
ms.devlang: php
ms.topic: quickstart
ms.date: 11/14/2018
ms.author: rogarana
ms.openlocfilehash: 3e1738c3e5acbe63faf1d614e2435088efd8c4d6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58014783"
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-php"></a>Transferir objetos de/para o armazenamento de Blobs do Azure com PHP
Neste início rápido, vai aprender a utilizar PHP para carregar, transferir e listar blobs de blocos num contentor no armazenamento de Blobs do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Certifique-se de que tem os seguintes pré-requisitos adicionais instalados:

* [PHP](https://php.net/downloads.php)
* [Armazenamento do Azure SDK para PHP](https://github.com/Azure/azure-storage-php)

## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo
O [exemplo de aplicação](https://github.com/Azure-Samples/storage-blobs-php-quickstart.git) utilizado neste início rápido é uma aplicação PHP básica.  

Utilize o [git](https://git-scm.com/) para transferir uma cópia da aplicação para o seu ambiente de desenvolvimento. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-php-quickstart.git
```

Este comando clona o repositório para a sua pasta local do git. Para abrir a aplicação de exemplo PHP, procure a pasta storage-blobs-php-quickstart e abra o ficheiro phpqs.php.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento
Na aplicação, tem de indicar o nome da conta de armazenamento e a chave de conta para criar a instância **BlobRestProxy** para a aplicação. É recomendado armazenar estes identificadores numa variável de ambiente no computador local que está a executar a aplicação. Utilize um dos exemplos seguintes, consoante o Sistema Operativo para criar a variável de ambiente. Substitua os valores **youraccountname** e **youraccountkey** pelo nome da conta e a chave.

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export ACCOUNT_NAME=<youraccountname>
export ACCOUNT_KEY=<youraccountkey>
```

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```cmd
setx ACCOUNT_NAME=<youraccountname>
setx ACCOUNT_KEY=<youraccountkey>
```
---

## <a name="configure-your-environment"></a>Configurar o ambiente
Retire a pasta da pasta git local e coloque-a num diretório servido pelo servidor PHP. Em seguida, abra uma linha de comandos no âmbito desse mesmo diretório e introduza: `php composer.phar install`

## <a name="run-the-sample"></a>Executar o exemplo
Este exemplo cria um ficheiro de teste na pasta ".". O programa de exemplo carrega o ficheiro de teste para o armazenamento de Blobs, lista os blobs no contentor e transfere o ficheiro com um novo nome. 

Execute o exemplo. O resultado seguinte é um exemplo do resultado devolvido ao executar a aplicação:
  
```
Uploading BlockBlob: HelloWorld.txt
These are the blobs present in the container: HelloWorld.txt: https://myexamplesacct.blob.core.windows.net/blockblobsleqvxd/HelloWorld.txt

This is the content of the blob uploaded: Hello Azure!
```
Quando prime o botão apresentado, o programa de exemplo elimina o contentor de armazenamento e os ficheiros. Antes de continuar, procure os dois ficheiros na pasta do servidor. Pode abrir e ver que são idênticos.

Também pode utilizar uma ferramenta como o [Explorador de Armazenamento do Azure](https://storageexplorer.com) para ver os ficheiros no armazenamento de Blobs. O Explorador de Armazenamento do Azure é uma ferramenta multiplataformas gratuita que lhe permite aceder às informações da sua conta de armazenamento. 

Depois de verificar os ficheiros, prima qualquer tecla para concluir a demonstração e eliminar os ficheiros de teste. Agora que sabe o que o exemplo faz, abra o ficheiro example.rb para ver o código. 

## <a name="understand-the-sample-code"></a>Compreender o código de exemplo

Em seguida, vamos analisar o código de exemplo, para que saiba como funciona.

### <a name="get-references-to-the-storage-objects"></a>Obter referências para os objetos de armazenamento
A primeira coisa a fazer é criar as referências para os objetos utilizados para aceder e gerir ao armazenamento de Blobs. Estes objetos dependem uns dos outros e cada um é utilizado pelo que vem a seguir na lista.

* Crie uma instância do objeto do armazenamento do Azure **BlobRestProxy** para configurar as credenciais de ligação. 
* Crie o objeto **BlobService**, que aponta para o serviço Blob na sua conta de armazenamento. 
* Crie o objeto **Container**, que represente o contentor a que está aceder. Os contentores servem para organizar os blobs, da mesma forma como utiliza pastas para organizar os ficheiros.

Quando tiver o objeto de contentor **blobClient**, pode criar o objeto de blob **Block** que aponta para o blob específico no qual está interessado. Em seguida, pode efetuar operações como carregar, transferir e copiar.

> [!IMPORTANT]
> Os nomes dos contentores têm de estar em minúscula. Para obter informações sobre os nomes dos contentores e dos blobs, veja [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Dar Nomes e Referenciar Contentores, Blobs e Metadados).

Nesta secção, vai configurar uma instância do cliente de armazenamento do Azure, instanciar o objeto do serviço Blob, criar um contentor novo e definir as permissões no contentor, para que os blobs sejam públicos. O contentor é designado **quickstartblobs**. 

```PHP
    # Setup a specific instance of an Azure::Storage::Client
    $connectionString = "DefaultEndpointsProtocol=https;AccountName=".getenv('account_name').";AccountKey=".getenv('account_key');
    
    // Create blob client.
    $blobClient = BlobRestProxy::createBlobService($connectionString);
    
    # Create the BlobService that represents the Blob service for the storage account
    $createContainerOptions = new CreateContainerOptions();
    
    $createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);
    
    // Set container metadata.
    $createContainerOptions->addMetaData("key1", "value1");
    $createContainerOptions->addMetaData("key2", "value2");

    $containerName = "blockblobs".generateRandomString();

    try    {
        // Create container.
        $blobClient->createContainer($containerName, $createContainerOptions);
```

### <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contentor

O armazenamento de blobs suporta blobs de blocos, blobs de acréscimo e blobs de páginas. Os blobs de blocos são os mais utilizados e vamos utilizá-los neste guia de introdução.  

Para carregar um ficheiro para um blob, obtenha o caminho completo do ficheiro ao associar o nome de diretório e o nome de ficheiro no disco local. Em seguida, pode carregar o ficheiro para o caminho especificado, com o método **createBlockBlob()**. 

O código de exemplo utiliza um ficheiro local e carrega-o para o Azure. O ficheiro é armazenado como **myfile** e o nome do blob como **fileToUpload** no código. O exemplo seguinte carrega o ficheiro para o seu contentor com o nome **quickstartblobs**.

```PHP
    $myfile = fopen("HelloWorld.txt", "w") or die("Unable to open file!");
    fclose($myfile);

    # Upload file as a block blob
    echo "Uploading BlockBlob: ".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    
    $content = fopen($fileToUpload, "r");

    //Upload blob
    $blobClient->createBlockBlob($containerName, $fileToUpload, $content);
```

Para efetuar uma atualização parcial do conteúdo de um blob de blocos, utilize o método **createblocklist()**. Os blobs de blocos podem ter no máximo 4,7 TB e podem ser qualquer coisa, desde folhas de cálculo do Excel a ficheiros grandes de vídeo. Os blobs de páginas utilizam-se principalmente para os ficheiros VHD utilizados para fazer cópias de VMs de IaaS. Os blobs de acréscimo servem para registo, como quando quer escrever num ficheiro e continuar a adicionar mais informações. Deve anexar o blob num único modelo de escritor. A maioria dos objetos guardados no armazenamento de Blobs são blobs de blocos.

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Pode obter uma lista de ficheiros no contentor com o método **listBlobs()**. O código seguinte obtém a lista de blobs, depois percorre-os e mostra os nomes dos blobs encontrados num contentor.  

```PHP
    $listBlobsOptions = new ListBlobsOptions();
    $listBlobsOptions->setPrefix("HelloWorld");
    
    echo "These are the blobs present in the container: ";
    
    do{
        $result = $blobClient->listBlobs($containerName, $listBlobsOptions);
        foreach ($result->getBlobs() as $blob)
        {
            echo $blob->getName().": ".$blob->getUrl()."<br />";
        }
        
        $listBlobsOptions->setContinuationToken($result->getContinuationToken());
    } while($result->getContinuationToken());
```

### <a name="get-the-content-of-your-blobs"></a>Obter o conteúdo dos blobs

Obtenha o conteúdo dos blobs com o método **getBlob()**. O código seguinte apresenta o conteúdo do blob atualizado numa secção anterior.

```PHP
    $blob = $blobClient->getBlob($containerName, $fileToUpload);
    fpassthru($blob->getContentStream());
```

### <a name="clean-up-resources"></a>Limpar recursos
Se já não precisar dos blobs carregados neste início rápido, pode eliminar o contentor inteiro com o método **deleteContainer()**. Se os ficheiros criados já não forem necessários, utilize o método **deleteBlob()** para eliminar os ficheiros.

```PHP
    // Delete blob.
    echo "Deleting Blob".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    $blobClient->deleteBlob($_GET["containerName"], $fileToUpload);

    // Delete container.
    echo "Deleting Container".PHP_EOL;
    echo $_GET["containerName"].PHP_EOL;
    echo "<br />";
    $blobClient->deleteContainer($_GET["containerName"]);

    //Deleting local file
    echo "Deleting file".PHP_EOL;
    echo "<br />";
    unlink($fileToUpload);   
```

## <a name="resources-for-developing-php-applications-with-blobs"></a>Recursos para desenvolver aplicações PHP com blobs

Consulte estes recursos adicionais para o desenvolvimento de PHP com armazenamento de blobs:

- Veja, transfira e instale o [código fonte em PHP da biblioteca cliente](https://github.com/Azure/azure-storage-php) do Armazenamento do Azure no GitHub.
- Explore os [exemplos de armazenamento de blobs](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=php&term=blob) escritos com a biblioteca de cliente PHP.

## <a name="next-steps"></a>Passos Seguintes
 
Neste início rápido, aprendeu a transferir ficheiros entre um disco local e o armazenamento de Blobs do Azure com PHP. Para obter mais informações sobre como trabalhar com o PHP, avance para o nosso Centro de programadores de PHP.

> [!div class="nextstepaction"]
> [Centro de Programadores de PHP](https://azure.microsoft.com/develop/php/)


Para obter mais informações sobre o Explorador de armazenamento e os Blobs, veja [Manage Azure Blob storage resources with Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Gerir recursos do armazenamento de Blobs do Azure com o Explorador de Armazenamento).
