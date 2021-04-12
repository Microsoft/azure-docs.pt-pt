---
title: 'Quickstart: Azure Blob Storage library v12 - Java'
description: Neste arranque rápido, aprende-se a usar a versão 12 da biblioteca do cliente Azure Blob Storage para a Java criar um recipiente e uma bolha no armazenamento blob (objeto). Em seguida, vai aprender a transferir o blob para o computador local e a listar todos os blobs num contentor.
author: twooley
ms.custom: devx-track-java
ms.author: twooley
ms.date: 12/01/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: b6d42cf26bc128257aa5d0cd7ef01e8fe8749a51
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280637"
---
# <a name="quickstart-manage-blobs-with-java-v12-sdk"></a>Quickstart: Gerir bolhas com Java v12 SDK

Neste arranque rápido, aprende-se a gerir as bolhas usando o Java. As bolhas são objetos que podem conter grandes quantidades de texto ou dados binários, incluindo imagens, documentos, streaming de meios e dados de arquivo. Você vai carregar, baixar e listar bolhas, e você vai criar e apagar recipientes.

Recursos adicionais:

* [Documentação de referência da API](/java/api/overview/azure/storage-blob-readme)
* [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob)
* [Pacote (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-blob)
* [Amostras](../common/storage-samples-java.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Uma conta do Armazenamento do Azure. [Criar uma conta de armazenamento](../common/storage-account-create.md).
- [Java Development Kit (JDK)](/java/azure/jdk/) versão 8 ou superior.
- [Apache Maven](https://maven.apache.org/download.cgi).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Configuração

Esta secção acompanha-o através da preparação de um projeto para trabalhar com a biblioteca de clientes Azure Blob Storage v12 para Java.

### <a name="create-the-project"></a>Criar o projeto

Crie uma aplicação Java chamada *blob-quickstart-v12*.

1. Numa janela de consola (como cmd, PowerShell ou Bash), utilize a Maven para criar uma nova aplicação de consola com o nome *blob-quickstart-v12*. Digite o seguinte comando **mvn** para criar um "Olá mundo!" Projeto Java.

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    mvn archetype:generate `
        --define interactiveMode=n `
        --define groupId=com.blobs.quickstart `
        --define artifactId=blob-quickstart-v12 `
        --define archetypeArtifactId=maven-archetype-quickstart `
        --define archetypeVersion=1.4
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.blobs.quickstart \
        --define artifactId=blob-quickstart-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
    ```

    ---

1. A produção da geração do projeto deve ser algo assim:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/blobs/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.blobs.quickstart
    [INFO] Parameter: groupId, Value: com.blobs.quickstart
    [INFO] Parameter: artifactId, Value: blob-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\QuickStarts\blob-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  7.056 s
    [INFO] Finished at: 2019-10-23T11:09:21-07:00
    [INFO] ------------------------------------------------------------------------
        ```

1. Switch to the newly created *blob-quickstart-v12* folder.

   ```console
   cd blob-quickstart-v12
   ```

1. De lado o *diretório blob-quickstart-v12,* crie outro diretório chamado *dados*. É aqui que os ficheiros de dados blob serão criados e armazenados.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Instale o pacote

Abra o ficheiro *pom.xml* no seu editor de texto. Adicione o seguinte elemento de dependência ao grupo de dependências.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-storage-blob</artifactId>
    <version>12.6.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

Do diretório do projeto:

1. Navegue para o */src/main/java/com/blobs/quickstart* diretório
1. Abra o ficheiro *.java App* no seu editor
1. Apagar a `System.out.println("Hello world!");` declaração
1. Adicionar `import` diretivas

Aqui está o código:

```java
package com.blobs.quickstart;

/**
 * Azure blob storage v12 SDK quickstart
 */
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O Azure Blob Storage está otimizado para armazenar quantidades massivas de dados não estruturados. Os dados não estruturados são dados que não seguem uma definição ou um modelo de dados em particular, como por exemplo, texto ou dados binários. O armazenamento blob oferece três tipos de recursos:

* A conta de armazenamento
* Um recipiente na conta de armazenamento
* Uma bolha no recipiente

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama da arquitetura de armazenamento blob](./media/storage-blobs-introduction/blob1.png)

Utilize as seguintes classes java para interagir com estes recursos:

* [BlobServiceClient](/java/api/com.azure.storage.blob.blobserviceclient): A `BlobServiceClient` classe permite-lhe manipular os recursos de armazenamento do Azure e os recipientes blob. A conta de armazenamento fornece o espaço de nome de nível superior para o serviço Blob.
* [BlobServiceClientBuilder](/java/api/com.azure.storage.blob.blobserviceclientbuilder): A `BlobServiceClientBuilder` classe fornece um construtor fluente API para ajudar na configuração e instantiação de `BlobServiceClient` objetos.
* [BlobContainerClient](/java/api/com.azure.storage.blob.blobcontainerclient): A `BlobContainerClient` classe permite-lhe manipular os recipientes de armazenamento Azure e as suas bolhas.
* [BlobClient](/java/api/com.azure.storage.blob.blobclient): A `BlobClient` classe permite-lhe manipular as bolhas de armazenamento Azure.
* [BlobItem](/java/api/com.azure.storage.blob.models.blobitem): A `BlobItem` classe representa bolhas individuais devolvidas de uma chamada para [listBlobs](/java/api/com.azure.storage.blob.blobcontainerclient.listblobs).

## <a name="code-examples"></a>Exemplos de código

Estes excertos de código de exemplo mostram-lhe como executar o seguinte com a biblioteca de clientes Azure Blob Storage para Java:

* [Obter a cadeia de ligação](#get-the-connection-string)
* [Criar um contentor](#create-a-container)
* [Carregar bolhas para um recipiente](#upload-blobs-to-a-container)
* [Listar os blobs num contentor](#list-the-blobs-in-a-container)
* [Transferir blobs](#download-blobs)
* [Eliminar um contentor](#delete-a-container)

### <a name="get-the-connection-string"></a>Obter a cadeia de ligação

O código abaixo recupera a cadeia de ligação para a conta de armazenamento a partir da variável ambiente criada na secção de cadeia de [ligação de armazenamento.](#configure-your-storage-connection-string)

Adicione este código dentro do `Main` método:

```java
System.out.println("Azure Blob Storage v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-container"></a>Criar um contentor

Decida o nome do novo recipiente. O código abaixo anexa um valor UUID ao nome do recipiente para garantir que é único.

> [!IMPORTANT]
> Os nomes dos contentores têm de estar em minúscula. Para obter mais informações sobre a atribuição de nomes de contentores e blobs, veja [Nomenclatura e Referenciação de Contentores, Blobs e Metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Em seguida, crie uma instância da classe [BlobContainerClient](/java/api/com.azure.storage.blob.blobcontainerclient) e, em seguida, chame o método [de criação](/java/api/com.azure.storage.blob.blobcontainerclient.create) para realmente criar o recipiente na sua conta de armazenamento.

Adicione este código ao fim do `Main` método:

```java
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClientBuilder().connectionString(connectStr).buildClient();

//Create a unique name for the container
String containerName = "quickstartblobs" + java.util.UUID.randomUUID();

// Create the container and return a container client object
BlobContainerClient containerClient = blobServiceClient.createBlobContainer(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Carregar bolhas para um recipiente

O seguinte corte de código:

1. Cria um ficheiro de texto no diretório de *dados* local.
1. Obtém uma referência a um objeto [BlobClient](/java/api/com.azure.storage.blob.blobclient) chamando o método [getBlobClient](/java/api/com.azure.storage.blob.blobcontainerclient.getblobclient) no recipiente a partir da secção [criar um recipiente.](#create-a-container)
1. Envia o ficheiro de texto local para a bolha, chamando o método [uploadFromFile.](/java/api/com.azure.storage.blob.blobclient.uploadfromfile) Este método cria a bolha se já não existir, mas não a substituirá se existir.

Adicione este código ao fim do `Main` método:

```java
// Create a local file in the ./data/ directory for uploading and downloading
String localPath = "./data/";
String fileName = "quickstart" + java.util.UUID.randomUUID() + ".txt";
File localFile = new File(localPath + fileName);

// Write text to the file
FileWriter writer = new FileWriter(localPath + fileName, true);
writer.write("Hello, World!");
writer.close();

// Get a reference to a blob
BlobClient blobClient = containerClient.getBlobClient(fileName);

System.out.println("\nUploading to Blob storage as blob:\n\t" + blobClient.getBlobUrl());

// Upload the blob
blobClient.uploadFromFile(localPath + fileName);
```

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Enuncie as bolhas no recipiente chamando o método [listBlobs.](/java/api/com.azure.storage.blob.blobcontainerclient.listblobs) Neste caso, apenas uma bolha foi adicionada ao recipiente, pelo que a operação de listagem devolve apenas uma bolha.

Adicione este código ao fim do `Main` método:

```java
System.out.println("\nListing blobs...");

// List the blob(s) in the container.
for (BlobItem blobItem : containerClient.listBlobs()) {
    System.out.println("\t" + blobItem.getName());
}
```

### <a name="download-blobs"></a>Transferir blobs

Descarregue a bolha anteriormente criada chamando o método [downloadToFile.](/java/api/com.azure.storage.blob.specialized.blobclientbase.downloadtofile) O código de exemplo adiciona um sufixo de "DOWNLOAD" ao nome do ficheiro para que possa ver ambos os ficheiros no sistema de ficheiros local.

Adicione este código ao fim do `Main` método:

```java
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension so that you can see both files.
String downloadFileName = fileName.replace(".txt", "DOWNLOAD.txt");
File downloadedFile = new File(localPath + downloadFileName);

System.out.println("\nDownloading blob to\n\t " + localPath + downloadFileName);

blobClient.downloadToFile(localPath + downloadFileName);
```

### <a name="delete-a-container"></a>Eliminar um contentor

O código seguinte limpa os recursos que a app criou removendo todo o recipiente utilizando o método [de eliminação.](/java/api/com.azure.storage.blob.blobcontainerclient.delete) Também elimina os ficheiros locais criados pela app.

A aplicação faz uma pausa para a entrada do utilizador, ligando `System.console().readLine()` antes de eliminar a bolha, o recipiente e os ficheiros locais. Esta é uma boa oportunidade para verificar se os recursos foram criados corretamente, antes de serem eliminados.

Adicione este código ao fim do `Main` método:

```java
// Clean up
System.out.println("\nPress the Enter key to begin clean up");
System.console().readLine();

System.out.println("Deleting blob container...");
containerClient.delete();

System.out.println("Deleting the local source and downloaded files...");
localFile.delete();
downloadedFile.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>Executar o código

Esta aplicação cria um ficheiro de teste na sua pasta local e envia-o para o armazenamento blob. O exemplo enumera então as bolhas no recipiente e descarrega o ficheiro com um novo nome para que possa comparar os ficheiros antigos e novos.

Navegue para o diretório que contém o ficheiro *pom.xml* e compile o projeto utilizando o seguinte `mvn` comando.

```console
mvn compile
```

Então, construa o pacote.

```console
mvn package
```

Execute o seguinte `mvn` comando para executar a aplicação.

```console
mvn exec:java -Dexec.mainClass="com.blobs.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

A saída da app é semelhante ao seguinte exemplo:

```output
Azure Blob Storage v12 - Java quickstart sample

Uploading to Blob storage as blob:
        https://mystorageacct.blob.core.windows.net/quickstartblobsf9aa68a5-260e-47e6-bea2-2dcfcfa1fd9a/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Listing blobs...
        quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65.txt

Downloading blob to
        ./data/quickstarta9c3a53e-ae9d-4863-8b34-f3d807992d65DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Antes de iniciar o processo de limpeza, verifique se a pasta *de dados* é composta pelos dois ficheiros. Pode abri-los e constatar que são idênticos.

Depois de verificar os ficheiros, prima a tecla **'Entrar'** para eliminar os ficheiros de teste e terminar a demonstração.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a carregar, descarregar e listar bolhas usando Java.

Para ver as aplicações de amostra de armazenamento Blob, continue a:

> [!div class="nextstepaction"]
> [Amostras de Azure Blob Storage SDK v12 Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob)

* Para saber mais, consulte o [Azure SDK para Java.](https://github.com/Azure/azure-sdk-for-java/blob/master/README.md)
* Para tutoriais, amostras, quickstarts e outra documentação, visite [Azure para desenvolvedores de nuvem java.](/azure/developer/java/)
