---
title: 'Início rápido: biblioteca de armazenamento de BLOBs do Azure V12-Java'
description: Neste guia de início rápido, você aprende a usar a biblioteca de cliente de armazenamento de BLOBs do Azure versão 12 para Java para criar um contêiner e um blob no armazenamento de BLOB (objeto). Em seguida, vai aprender a transferir o blob para o computador local e a listar todos os blobs num contentor.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: b5a59e51190fc8309d35445fba7ef4a2dd22b970
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770446"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-java"></a>Início rápido: biblioteca de cliente do armazenamento de BLOBs do Azure V12 para Java

Introdução à biblioteca de cliente do armazenamento de BLOBs do Azure V12 para Java. O Armazenamento de Blobs do Azure é a solução de armazenamento de objetos da Microsoft para a cloud. Siga as etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas. O Armazenamento de blobs está otimizado para armazenar quantidades em grande escala de dados não estruturados.

> [!NOTE]
> Para começar a usar a versão anterior do SDK, consulte [início rápido: biblioteca de cliente do armazenamento de BLOBs do Azure para Java](storage-quickstart-blobs-java-legacy.md).

Use a biblioteca de cliente do armazenamento de BLOBs do Azure V12 para Java para:

* Criar um contentor
* Carregar um blob no armazenamento do Azure
* Listar todos os BLOBs em um contêiner
* Baixar um blob em seu computador local
* Eliminar um contentor

[Documentação de referência de API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/index.html) | [exemplos](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob) de | do Maven ( [código-fonte](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob) | [do pacote)](https://mvnrepository.com/artifact/com.azure/azure-storage-blob?repo=jcenter) da biblioteca

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) versão 8 ou superior
* [Apache Maven](https://maven.apache.org/download.cgi)
* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* Conta de armazenamento do Azure – [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="setting-up"></a>Configurando

Esta seção orienta você pela preparação de um projeto para trabalhar com a biblioteca de cliente do armazenamento de BLOBs do Azure V12 para Java.

### <a name="create-the-project"></a>Criar o projeto

Crie um aplicativo Java chamado *blob-QuickStart-V12*.

1. Em uma janela de console (como cmd, PowerShell ou bash), use o Maven para criar um novo aplicativo de console com o nome *blob-QuickStart-V12*. Digite o seguinte comando **MVN** em uma única linha para criar um "Olá mundo!" simples Projeto Java. O comando é exibido aqui em várias linhas para facilitar a leitura.

   ```console
   mvn archetype:generate -DgroupId=com.blobs.quickstart
                          -DartifactId=blob-quickstart-v12
                          -DarchetypeArtifactId=maven-archetype-quickstart
                          -DarchetypeVersion=1.4
                          -DinteractiveMode=false
   ```

1. A saída da geração do projeto deve ser semelhante a esta:

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

1. No lado do diretório *blob-QuickStart-V12* , crie outro diretório chamado *Data*. É aqui que os arquivos de dados de blob serão criados e armazenados.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Instalar o pacote

Abra o arquivo *pom. xml* em seu editor de texto. Adicione o elemento de dependência a seguir ao grupo de dependências.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-storage-blob</artifactId>
    <version>12.0.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Configurar a estrutura do aplicativo

No diretório do projeto:

1. Navegue até o diretório */src/main/java/com/BLOBs/QuickStart*
1. Abra o arquivo *app. java* em seu editor
1. Excluir a instrução `System.out.println("Hello world!");`
1. Adicionar `import` diretivas

Este é o código:

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

[!INCLUDE [storage-quickstart-connection-string-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O armazenamento de BLOBs do Azure é otimizado para armazenar grandes quantidades de dados não estruturados. Dados não estruturados são dados que não aderem a um modelo ou definição de dados específico, como texto ou dados binários. O armazenamento de BLOBs oferece três tipos de recursos:

* A conta de armazenamento
* Um contêiner na conta de armazenamento
* Um blob no contêiner

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama da arquitetura de armazenamento de BLOBs](./media/storage-blob-introduction/blob1.png)

Use as classes Java a seguir para interagir com estes recursos:

* [BlobServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobServiceClient.html): a classe `BlobServiceClient` permite que você manipule os recursos de armazenamento do Azure e contêineres de BLOB. A conta de armazenamento fornece o namespace de nível superior para o serviço BLOB.
* [BlobServiceClientBuilder](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobServiceClientBuilder.html): a classe `BlobServiceClientBuilder` fornece uma API do Fluent Builder para ajudar a configuração e a instanciação de objetos `BlobServiceClient`.
* [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html): a classe `BlobContainerClient` permite que você manipule contêineres de armazenamento do Azure e seus BLOBs.
* [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html): a classe `BlobClient` permite que você manipule os blobs de armazenamento do Azure.
* [BlobItem](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/models/BlobItem.html): a classe `BlobItem` representa BLOBs individuais retornados de uma chamada para `listBlobsFlat`.

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código de exemplo mostram como executar o seguinte com a biblioteca de cliente de armazenamento de BLOBs do Azure para Java:

* [Obter a cadeia de conexão](#get-the-connection-string)
* [Criar um contêiner](#create-a-container)
* [Carregar BLOBs em um contêiner](#upload-blobs-to-a-container)
* [Listar os BLOBs em um contêiner](#list-the-blobs-in-a-container)
* [Baixar BLOBs](#download-blobs)
* [Eliminar um contentor](#delete-a-container)

### <a name="get-the-connection-string"></a>Obter a cadeia de ligação

O código a seguir recupera a cadeia de conexão para a conta de armazenamento da variável de ambiente criada na seção [configurar sua cadeia de conexão de armazenamento](#configure-your-storage-connection-string) .

Adicione este código dentro do método `Main`:

```java
System.out.println("Azure Blob storage v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called CONNECT_STR. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("CONNECT_STR");
```

### <a name="create-a-container"></a>Criar um contentor

Escolha um nome para o novo contêiner. O código a seguir acrescenta um valor UUID ao nome do contêiner para garantir que ele seja exclusivo.

> [!IMPORTANT]
> Os nomes dos contentores têm de estar em minúscula. Para obter mais informações sobre a atribuição de nomes de contentores e blobs, veja [Nomenclatura e Referenciação de Contentores, Blobs e Metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Em seguida, crie uma instância da classe [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html) e chame o método [Create](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#create--) para realmente criar o contêiner em sua conta de armazenamento.

Adicione este código ao final do método de `Main`:

```java
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClientBuilder().connectionString(connectStr).buildClient();

//Create a unique name for the container
String containerName = "quickstartblobs" + java.util.UUID.randomUUID();

// Create the container and return a container client object
BlobContainerClient containerClient = blobServiceClient.createBlobContainer(containerName);
```

### <a name="upload-blobs-to-a-container"></a>Carregar BLOBs em um contêiner

O seguinte trecho de código:

1. Cria um arquivo de texto no diretório de *dados* local.
1. Obtém uma referência a um objeto [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html) chamando o método [getBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#getBlobClient-java.lang.String-) no contêiner a partir da seção [criar um contêiner](#create-a-container) .
1. Carrega o arquivo de texto local para o blob chamando o método [Uploadfromfile](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobClient.html#uploadFromFile-java.lang.String-) . Esse método criará o blob se ele ainda não existir, mas não o substituirá, se tiver.

Adicione este código ao final do método de `Main`:

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

Liste os BLOBs no contêiner chamando o método [listBlobs](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#listBlobs--) . Nesse caso, apenas um blob foi adicionado ao contêiner, portanto, a operação de listagem retorna apenas um blob.

Adicione este código ao final do método de `Main`:

```java
System.out.println("\nListing blobs...");

// List the blob(s) in the container.
for (BlobItem blobItem : containerClient.listBlobs()) {
    System.out.println("\t" + blobItem.getName());
}
```

### <a name="download-blobs"></a>Transferir blobs

Baixe o blob criado anteriormente chamando o método [downloadToFile](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/specialized/BlobClientBase.html#downloadToFile-java.lang.String-) . O código de exemplo adiciona um sufixo de "DOWNLOAD" ao nome do arquivo para que você possa ver os dois arquivos no sistema de arquivos local.

Adicione este código ao final do método de `Main`:

```java
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension so that you can see both files.
String downloadFileName = fileName.replace(".txt", "DOWNLOAD.txt");
File downloadedFile = new File(localPath + downloadFileName);

System.out.println("\nDownloading blob to\n\t " + localPath + downloadFileName);

blobClient.downloadToFile(localPath + downloadFileName);
```

### <a name="delete-a-container"></a>Eliminar um contentor

O código a seguir limpa os recursos que o aplicativo criou removendo todo o contêiner usando o método [delete](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-blob/12.0.0/com/azure/storage/blob/BlobContainerClient.html#delete--) . Ele também exclui os arquivos locais criados pelo aplicativo.

O aplicativo pausa a entrada do usuário chamando `System.console().readLine()` antes de excluir o blob, o contêiner e os arquivos locais. Essa é uma boa chance de verificar se os recursos foram realmente criados corretamente, antes de serem excluídos.

Adicione este código ao final do método de `Main`:

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

Este aplicativo cria um arquivo de teste em sua pasta local e o carrega no armazenamento de BLOBs. Em seguida, o exemplo lista os BLOBs no contêiner e baixa o arquivo com um novo nome para que você possa comparar os arquivos novos e antigos.

Navegue até o diretório que contém o arquivo *pom. xml* e compile o projeto usando o comando `mvn` a seguir.

```console
mvn compile
```

Em seguida, compile o pacote.

```console
mvn package
```

Execute o comando `mvn` a seguir para executar o aplicativo.

```console
mvn exec:java -Dexec.mainClass="com.blobs.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

A saída do aplicativo é semelhante ao exemplo a seguir:

```output
Azure Blob storage v12 - Java quickstart sample

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

Antes de começar o processo de limpeza, verifique os dois arquivos na pasta meus *documentos* . Pode abri-los e constatar que são idênticos.

Depois de verificar os arquivos, pressione a tecla **Enter** para excluir os arquivos de teste e concluir a demonstração.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a carregar, baixar e listar BLOBs usando o Java.

Para ver os aplicativos de exemplo de armazenamento de BLOBs, continue em:

> [!div class="nextstepaction"]
> [Exemplos de Java do SDK do armazenamento de BLOBs do Azure V12](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-blob/src/samples/java/com/azure/storage/blob)

* Para saber mais, consulte o [SDK do Azure para Java](https://github.com/Azure/azure-sdk-for-java/blob/master/README.md).
* Para obter tutoriais, exemplos, inícios rápidos e outras documentações, visite [Azure para desenvolvedores de nuvem Java](/azure/java/).
