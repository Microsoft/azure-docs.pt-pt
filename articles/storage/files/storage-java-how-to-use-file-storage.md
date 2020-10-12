---
title: Desenvolver para Azure Files com Java Microsoft Docs
description: Saiba como desenvolver aplicações e serviços Java que usam ficheiros Azure para armazenar dados de ficheiros.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/19/2017
ms.custom: devx-track-java
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2c80d741ca1cf0b4d66bc18488af8ef1da44fd14
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89462603"
---
# <a name="develop-for-azure-files-with-java"></a>Programar para os Ficheiros do Azure com Java
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>Acerca deste tutorial
Este tutorial demonstrará os fundamentos da utilização da Java para desenvolver aplicações ou serviços que utilizem ficheiros Azure para armazenar dados de ficheiros. Neste tutorial, criaremos uma aplicação de consola e mostraremos como realizar ações básicas com Java e Azure Files:

* Criar e eliminar ações de ficheiros Azure
* Criar e apagar diretórios
* Enumerar ficheiros e diretórios numa partilha de ficheiros Azure
* Carregar, transferir e apagar um ficheiro

> [!Note]  
> Como os Ficheiros Azure podem ser acedidos através de SMB, é possível escrever aplicações que acedam à partilha de ficheiros Azure utilizando as classes padrão java I/O. Este artigo descreverá como escrever aplicações que utilizem o Azure Storage Java SDK, que utiliza a [API de Ficheiros Azure](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) para falar com ficheiros Azure.

## <a name="create-a-java-application"></a>Criar uma aplicação Java
Para construir as amostras, você precisará do Kit de Desenvolvimento de Java (JDK) e do [Azure Storage SDK para Java.](https://github.com/Azure/azure-storage-java) Também deveria ter criado uma conta de armazenamento Azure.

## <a name="set-up-your-application-to-use-azure-files"></a>Configurar a sua aplicação para utilizar ficheiros Azure
Para utilizar as APIs de armazenamento Azure, adicione a seguinte declaração ao topo do ficheiro Java onde pretende aceder ao serviço de armazenamento a partir.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de ligação de armazenamento Azure
Para utilizar ficheiros Azure, tem de se ligar à sua conta de armazenamento Azure. O primeiro passo seria configurar uma cadeia de ligação, que usaremos para ligar à sua conta de armazenamento. Vamos definir uma variável estática para fazer isso.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> Substitua your_storage_account_name e your_storage_account_key pelos valores reais da sua conta de armazenamento.
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Ligação a uma conta de armazenamento Azure
Para se ligar à sua conta de armazenamento, tem de utilizar o objeto **CloudStorageAccount,** passando uma cadeia de ligação ao seu método **de análise.**

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** lança um InvalidKeyException, por isso terá de colocá-lo dentro de um bloco de tentativa/captura.

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure
Todos os ficheiros e diretórios em Ficheiros Azure residem num contentor chamado **Share**. A sua conta de armazenamento pode ter o máximo de ações que a sua capacidade de conta permitir. Para obter acesso a uma partilha e ao seu conteúdo, é necessário utilizar um cliente Azure Files.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Utilizando o cliente Azure Files, pode então obter uma referência a uma partilha.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Para realmente criar a partilha, utilize o método **createIfNotExists** do objeto CloudFileShare.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

Neste momento, **a share** detém uma referência a uma partilha denominada **sampleshare**.

## <a name="delete-an-azure-file-share"></a>Excluir uma partilha de ficheiros Azure
A eliminação de uma ação é feita chamando o método **deleteIfExists** num objeto CloudFileShare. Aqui está o código de amostra que faz isso.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the file client.
   CloudFileClient fileClient = storageAccount.createCloudFileClient();

   // Get a reference to the file share
   CloudFileShare share = fileClient.getShareReference("sampleshare");

   if (share.deleteIfExists()) {
       System.out.println("sampleshare deleted");
   }
} catch (Exception e) {
    e.printStackTrace();
}
```

## <a name="create-a-directory"></a>Criar um diretório
Também pode organizar o armazenamento colocando ficheiros dentro de subdireções em vez de ter todos eles no diretório de raiz. O Azure Files permite-lhe criar o maior número de diretórios que a sua conta permitirá. O código abaixo criará um subdiretório nomeado **sampledir** sob o diretório de raiz.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

## <a name="delete-a-directory"></a>Eliminar um diretório
A eliminação de um diretório é uma tarefa simples, embora deva notar que não é possível apagar um diretório que ainda contenha ficheiros ou outros diretórios.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerar ficheiros e diretórios numa partilha de ficheiros Azure
A obtenção de uma lista de ficheiros e diretórios dentro de uma ação é facilmente feita através da **chamada ListFilesAndDirectories** numa referência CloudFileDirectory. O método devolve uma lista de objetos ListFileItem em que pode iterar. Como exemplo, o seguinte código listará ficheiros e diretórios dentro do diretório de raiz.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Carregar um ficheiro
Nesta secção, você vai aprender a carregar um ficheiro do armazenamento local para o diretório de raiz de uma ação.

O primeiro passo para o upload de um ficheiro é obter uma referência ao diretório onde deve residir. Fá-lo chamando o método **getRootDirectoryReference** do objeto de partilha.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Agora que tem uma referência ao diretório de raiz da partilha, pode enviar um ficheiro para ele utilizando o seguinte código.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Transferir um ficheiro
Uma das operações mais frequentes que irá realizar contra o Azure Files é descarregar ficheiros. No exemplo seguinte, o código descarrega SampleFile.txt e exibe o seu conteúdo.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

## <a name="delete-a-file"></a>Eliminar um ficheiro
Outra operação comum dos Ficheiros Azure é a eliminação de ficheiros. O código seguinte elimina um ficheiro denominado SampleFile.txt armazenado dentro de um diretório denominado **sampledir**.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory where the file to be deleted is in
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

String filename = "SampleFile.txt"
CloudFile file;

file = containerDir.getFileReference(filename)
if ( file.deleteIfExists() ) {
    System.out.println(filename + " was deleted");
}
```

## <a name="next-steps"></a>Passos seguintes
Se você gostaria de saber mais sobre outras APIs de armazenamento Azure, siga estes links.

* [Azure para desenvolvedores java](/java/azure)/)
* [Azure Storage SDK for Java](https://github.com/azure/azure-storage-java) (SDK do Armazenamento do Azure para Java)
* [Azure Storage SDK para Android](https://github.com/azure/azure-storage-android)
* [Azure Storage Client SDK Reference](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html) (Referência do SDK do Cliente do Armazenamento do Azure)
* [API REST dos Serviços do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog da equipa de armazenamento Azure](https://docs.microsoft.com/archive/blogs/windowsazurestorage/)
* [Transferir dados com o Utilitário de Linha de Comandos AzCopy](../common/storage-use-azcopy.md)
* [Resolução de problemas de Ficheiros do Azure - Windows](storage-troubleshoot-windows-file-connection-problems.md)
