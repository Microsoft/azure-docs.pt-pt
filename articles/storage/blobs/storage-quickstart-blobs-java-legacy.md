---
title: 'Início rápido: biblioteca de cliente do armazenamento de BLOBs do Azure V8 para Java'
description: Crie uma conta de armazenamento e um contentor no armazenamento de objetos (blobs). Em seguida, use a biblioteca de cliente de armazenamento do Azure V8 para Java para carregar um blob no armazenamento do Azure, baixar um blob e listar os BLOBs em um contêiner.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: a6c4380e44d705e551bc96746a809c57aa02ac5b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825377"
---
# <a name="quickstart-azure-blob-storage-client-library-v8-for-java"></a>Início rápido: biblioteca de cliente do armazenamento de BLOBs do Azure V8 para Java

Introdução à biblioteca de cliente do armazenamento de BLOBs do Azure V8 para Java. O armazenamento de BLOBs do Azure é a solução de armazenamento de objetos da Microsoft para a nuvem. Siga as etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas. O Armazenamento de blobs está otimizado para armazenar quantidades em grande escala de dados não estruturados.

Use a biblioteca de cliente de armazenamento de BLOBs do Azure para Java para:

* Criar um contentor
* Definir permissões em um contêiner
* Criar um blob no armazenamento do Azure
* Baixe o blob em seu computador local
* Listar todos os BLOBs em um contêiner
* Eliminar um contentor

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* Conta de armazenamento do Azure – [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Um IDE com integração Maven.
* Em alternativa, instale e configure o Maven para funcionar a partir da linha de comandos.

Este guia usa o [Eclipse](https://www.eclipse.org/downloads/) com a configuração "Eclipse IDE para desenvolvedores de Java".

## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo

O [aplicativo de exemplo](https://github.com/Azure-Samples/storage-blobs-java-quickstart) é um aplicativo de console básico.

Utilize o [git](https://git-scm.com/) para transferir uma cópia da aplicação para o seu ambiente de desenvolvimento.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

Este comando clona o repositório para a sua pasta local do git. Para abrir o projeto, inicie o Eclipse e feche o ecrã de Boas-vindas. Selecione **Ficheiro** e **Abrir Projetos a Partir do Sistema de Ficheiros**. Confirme que **Detect and configure project natures** (Detetar e configurar tipos de projeto) está assinalado. Selecione **Diretório** e navegue para o local onde armazenou o repositório clonado. Dentro do repositório clonado, selecione a pasta **blobAzureApp**. Certifique-se de que o projeto **blobAzureApp** aparece como projeto do Eclipse e selecione **Concluir**.

Após a importação do projeto, abra **AzureApp.java** (localizado em **blobQuickstart.blobAzureApp**, dentro de **src/main/java**) e substitua `accountname` e `accountkey` dentro da cadeia`storageConnectionString`. Em seguida, execute a aplicação. As instruções específicas para concluir estas tarefas estão descritas nas secções a seguir.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento

Na aplicação, tem de indicar a cadeia de ligação da sua conta de armazenamento. Abra o ficheiro **AzureApp.Java**. Localize a variável `storageConnectionString` e colar o valor da cadeia de ligação que copiou na secção anterior. A variável `storageConnectionString` deve ter um aspeto semelhante ao seguinte código de exemplo:

```java
public static final String storageConnectionString =
"DefaultEndpointsProtocol=https;" +
"AccountName=<account-name>;" +
"AccountKey=<account-key>";
```

## <a name="run-the-sample"></a>Executar o exemplo

Este exemplo de aplicação cria um ficheiro de teste no seu diretório predefinido (*C:\Users\<user>\AppData\Local\Temp*, para utilizadores do Windows), carrega-o para o armazenamento de Blobs, lista os blobs no contentor e, depois, transfere o ficheiro com um nome novo, para que possa comparar o ficheiro novo e o antigo.

Execute o exemplo com o Maven na linha de comandos. Abra uma shell e navegue para **blobAzureApp** dentro do diretório clonado. Em seguida, introduza `mvn compile exec:java`.

O exemplo seguinte mostra a saída se executasse a aplicação no Windows.

```
Azure Blob storage quick start sample
Creating container: quickstartcontainer
Creating a sample file at: C:\Users\<user>\AppData\Local\Temp\sampleFile514658495642546986.txt
Uploading the sample file
URI of blob is: https://myexamplesacct.blob.core.windows.net/quickstartcontainer/sampleFile514658495642546986.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.

Deleting the container
Deleting the source, and downloaded files
```

Antes de continuar, verifique o ficheiro de exemplo no seu diretório predefinido (*C:\Users\<user>\AppData\Local\Temp*, para utilizadores do Windows). Copie o URL para o blob fora da janela da consola e cole-o num browser para ver os conteúdos do ficheiro no armazenamento de Blobs. Se comparar o ficheiro de exemplo no seu diretório com os conteúdos armazenados no armazenamento de Blobs, verá que eles são os mesmos.

  >[!NOTE]
  >Também pode utilizar uma ferramenta como o [Explorador de Armazenamento do Azure](https://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para ver os ficheiros no armazenamento de Blobs. O Explorador de Armazenamento do Azure é uma ferramenta multiplataformas gratuita que lhe permite aceder às informações da sua conta de armazenamento.

Depois de verificar os ficheiros, prima a tecla **Enter** para concluir a demonstração e eliminar os ficheiros de teste. Agora que sabe o que o exemplo faz, abra o ficheiro **AzureApp.java** para ver o código.

## <a name="understand-the-sample-code"></a>Compreender o código de exemplo

Em seguida, vamos analisar o código de exemplo, para que saiba como funciona.

### <a name="get-references-to-the-storage-objects"></a>Obter referências para os objetos de armazenamento

A primeira coisa a fazer é criar as referências para os objetos utilizados para aceder e gerir ao armazenamento de Blobs. Estes objetos dependem uns dos outros; cada um é utilizado pelo que vem a seguir na lista.

* Crie uma instância do objeto [CloudStorageAccount](/java/api/com.microsoft.azure.management.storage.storageaccount) que aponte para a conta de armazenamento.

    O objeto **CloudStorageAccount** é uma representação da sua conta de armazenamento e permite-lhe definir e aceder programaticamente às propriedades da conta de armazenamento. Ao utilizar o objeto **CloudStorageAccount**, pode criar uma instância de **CloudBlobClient**, que é necessário para aceder ao serviço de blobs.

* Crie uma instância do objeto **CloudBlobClient** que aponte para o [serviço de Blobs](/java/api/com.microsoft.azure.storage.blob._cloud_blob_client) na sua conta de armazenamento.

    O **CloudBlobClient** disponibiliza-lhe um ponto de acesso ao serviço de blobs, permitindo-lhe definir e aceder às propriedades do armazenamento de Blobs programaticamente. Ao utilizar **CloudBlobClient**, pode criar uma instância do objeto **CloudBlobContainer**, que é necessário para criar contentores.

* Crie uma instância do objeto [CloudBlobContainer](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container), que represente o contentor a que está aceder. Utilize contentores para organizar os blobs, da mesma forma como utiliza pastas para organizar os ficheiros.

    Quando tiver **CloudBlobContainer**, pode criar uma instância do objeto [CloudBlockBlob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob), que aponte para o blob específico em que está interessado e realizar uma operação de carregamento, transferência, cópia, ou outra.

> [!IMPORTANT]
> Os nomes dos contentores têm de estar em minúscula. Para obter mais informações sobre os contentores, veja [Nomenclatura e Referência para Contentores, Blobs e Metadados](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="create-a-container"></a>Criar um contentor

Nesta secção, vai criar uma instância dos objetos, criar um contentor novo e, em seguida, definir as permissões no contentor, para que os blobs sejam públicos e possam ser acedidos com apenas um URL. O contentor é designado **quickstartcontainer**.

Este exemplo utiliza [CreateIfNotExists](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.createifnotexists), uma vez que queremos criar um contentor novo sempre que o exemplo é executado. Num ambiente de produção onde for utilizado o mesmo contentor em toda a aplicação, é melhor prática chamar **CreateIfNotExists** apenas uma vez. Em alternativa, pode criar o contentor com antecedência, para que não tenha de criar o código.

```java
// Parse the connection string and create a blob client to interact with Blob storage
storageAccount = CloudStorageAccount.parse(storageConnectionString);
blobClient = storageAccount.createCloudBlobClient();
container = blobClient.getContainerReference("quickstartcontainer");

// Create the container if it does not exist with public access.
System.out.println("Creating container: " + container.getName());
container.createIfNotExists(BlobContainerPublicAccessType.CONTAINER, new BlobRequestOptions(), new OperationContext());
```

### <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contentor

Para carregar um arquivo em um blob de blocos, obtenha uma referência para o blob no contêiner de destino. Assim que tiver a referência do blob, pode carregar dados para o mesmo com [CloudBlockBlob.Upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload). Esta operação cria o blob, caso este ainda não exista, ou substitui o mesmo se já existir.

O código de exemplo cria um ficheiro local que vai ser utilizado para o carregamento e a transferência, armazenando o ficheiro a carregar como **origem** e o nome do blob em **blob**. O exemplo seguinte carrega o ficheiro para o seu contentor com o nome **quickstartcontainer**.

```java
//Creating a sample file
sourceFile = File.createTempFile("sampleFile", ".txt");
System.out.println("Creating a sample file at: " + sourceFile.toString());
Writer output = new BufferedWriter(new FileWriter(sourceFile));
output.write("Hello Azure!");
output.close();

//Getting a blob reference
CloudBlockBlob blob = container.getBlockBlobReference(sourceFile.getName());

//Creating blob and uploading file to it
System.out.println("Uploading the sample file ");
blob.uploadFromFile(sourceFile.getAbsolutePath());
```

Existem vários métodos `upload`, incluindo [upload](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload), [uploadBlock](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadblock), [uploadFullBlob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadfullblob), [uploadStandardBlobTier](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadstandardblobtier) e [uploadText](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadtext), que pode utilizar com o Armazenamento de blobs. Por exemplo, se tiver uma cadeia, pode utilizar o método `UploadText` em vez de `Upload`.

Os blobs de blocos podem ser qualquer tipo de ficheiro binário ou de texto. Os blobs de páginas utilizam-se principalmente para os ficheiros VHD utilizados que suportam VMs de IaaS. Utilize blobs de acréscimo para registo, como quando quer escrever num ficheiro e continue a adicionar mais informações. A maioria dos objetos guardados no armazenamento de Blobs são blobs de blocos.

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Pode obter uma lista de ficheiros no contentor com [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobs). O código seguinte obtém a lista de blobs, depois percorre-a e mostra os URIs dos blobs encontrados. Pode copiar o URI a partir da janela de comandos e colá-lo num browser para ver o ficheiro.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

### <a name="download-blobs"></a>Transferir blobs

Utilize [CloudBlob.DownloadToFile](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.downloadtofile) para transferir blobs para o seu disco local.

O código seguinte transfere o blob carregado numa secção anterior, adicionando o sufixo "_DOWNLOADED", ao nome do mesmo, para que possa ver ambos os ficheiros no disco local.

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and
// haven't changed the blob we're interested in, so we can reuse it.
// Here we are creating a new file to download to. Alternatively you can also pass in the path as a string into downloadToFile method: blob.downloadToFile("/path/to/new/file").
downloadedFile = new File(sourceFile.getParentFile(), "downloadedFile.txt");
blob.downloadToFile(downloadedFile.getAbsolutePath());
```

### <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais dos BLOBs que carregou, poderá excluir o contêiner inteiro usando [CloudBlobContainer. DeleteIfExists](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.deleteifexists). Este método também elimina os ficheiros no contentor.

```java
try {
if(container != null)
    container.deleteIfExists();
} catch (StorageException ex) {
System.out.println(String.format("Service error. Http code: %d and error code: %s", ex.getHttpStatusCode(), ex.getErrorCode()));
}

System.out.println("Deleting the source, and downloaded files");

if(downloadedFile != null)
downloadedFile.deleteOnExit();

if(sourceFile != null)
sourceFile.deleteOnExit();
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a transferir arquivos entre um disco local e o armazenamento de BLOBs do Azure usando o Java. Para saber mais sobre como trabalhar com o Java, avance para o nosso repositório de código de origem do GitHub.

> [!div class="nextstepaction"]
> [Exemplos de código
> de referência de](../common/storage-samples-java.md) [API Java](https://docs.microsoft.com/java/api/overview/azure/storage?view=azure-java-legacy) para Java
