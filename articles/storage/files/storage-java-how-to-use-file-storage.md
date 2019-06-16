---
title: Programar para os ficheiros do Azure com Java | Documentos da Microsoft
description: Saiba como desenvolver aplicações Java e serviços que utilizam ficheiros do Azure para armazenar dados de ficheiros.
services: storage
author: roygara
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 238e5971e79b192e0ef422dcd452859ff7566580
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721661"
---
# <a name="develop-for-azure-files-with-java"></a>Programar para os ficheiros do Azure com Java
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>Acerca deste tutorial
Este tutorial demonstra as noções básicas da utilização de Java para programar aplicações ou serviços que utilizam ficheiros do Azure para armazenar dados de ficheiros. Neste tutorial, iremos criar uma aplicação de consola e Mostrar como realizar ações básicas com Java e ficheiros do Azure:

* Criar e eliminar partilhas de ficheiros do Azure
* Crie e elimine diretórios
* Enumerar arquivos e diretórios numa partilha de ficheiros do Azure
* Carregar, transferir e eliminar um ficheiro

> [!Note]  
> Como ficheiros do Azure pode ser acedidos através de SMB, é possível escrever aplicações que acedam à partilha de ficheiros do Azure com as classes de e/s do Java padrão. Este artigo descreve como escrever aplicações que utilizam o SDK de Java de armazenamento do Azure, que utiliza a [API de REST de ficheiros do Azure](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) para comunicar com o serviço ficheiros do Azure.

## <a name="create-a-java-application"></a>Criar uma aplicação Java
Para criar os exemplos, terá do Kit de desenvolvimento Java (JDK) e o [SDK de armazenamento do Azure para Java](https://github.com/Azure/azure-storage-java). Deve também ter criado uma conta de armazenamento do Azure.

## <a name="set-up-your-application-to-use-azure-files"></a>Configurar a sua aplicação para utilizar ficheiros do Azure
Para utilizar as APIs de armazenamento do Azure, adicione a seguinte instrução na parte superior do ficheiro onde pretende aceder ao serviço de armazenamento de Java.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de ligação de armazenamento do Azure
Para utilizar ficheiros do Azure, terá de ligar à sua conta de armazenamento do Azure. O primeiro passo seria configurar uma cadeia de ligação, o que vamos utilizar para ligar à sua conta de armazenamento. Vamos definir uma variável estática para fazer isso.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> Substitua os valores reais para a sua conta de armazenamento your_storage_account_name e your_storage_account_key.
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Ligar a uma conta de armazenamento do Azure
Para ligar à sua conta de armazenamento, tem de utilizar o **CloudStorageAccount** objeto, passando uma cadeia de ligação para seu **analisar** método.

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** lança uma InvalidKeyException, portanto, precisará colocá-lo dentro de um bloco try/catch.

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure
Todos os ficheiros e diretórios nos ficheiros do Azure residem num contêiner chamado um **partilha**. Sua conta de armazenamento pode ter quantas partilhas como permite que a sua capacidade de conta. Para obter acesso a uma partilha e o respetivo conteúdo, terá de utilizar um cliente de ficheiros do Azure.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Com o cliente de ficheiros do Azure, em seguida, pode obter uma referência a uma partilha.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Para realmente criar a partilha, utilize o **createIfNotExists** método do objeto CloudFileShare.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

Neste momento, **partilhar** mantém uma referência para uma partilha denominada **sampleshare**.

## <a name="delete-an-azure-file-share"></a>Eliminar uma partilha de ficheiros do Azure
A eliminar uma partilha é feita chamando o **deleteIfExists** método num objeto de CloudFileShare. Aqui está o código de exemplo que faz isso.

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
Também pode organizar o armazenamento ao colocar os ficheiros dentro dos subdiretórios em vez de ter todas elas no diretório de raiz. Os ficheiros do Azure permite-lhe criar os diretórios como permitirá que a sua conta. O código abaixo, irá criar um subdiretório nomeado **sampledir** sob o diretório de raiz.

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
Eliminar um diretório é uma tarefa bem simples, embora é importante observar que não é possível eliminar um diretório que ainda contém arquivos ou de outros diretórios.

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

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerar arquivos e diretórios numa partilha de ficheiros do Azure
Obter uma lista de ficheiros e diretórios dentro de uma partilha facilmente é feito chamando **listFilesAndDirectories** numa referência de CloudFileDirectory. O método retorna uma lista de objetos de ListFileItem que pode repetir. Por exemplo, o código a seguir lista ficheiros e diretórios dentro do diretório de raiz.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Carregar um ficheiro
Nesta secção, irá aprender como carregar um ficheiro do armazenamento local para o diretório de raiz de uma partilha.

O primeiro passo para carregar um ficheiro é obter uma referência para o diretório onde devem residir. Fazê-lo ao chamar o **getRootDirectoryReference** método do objeto de partilha.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Agora que tem uma referência para o diretório de raiz da partilha, pode carregar um ficheiro à classe usando o seguinte código.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Transferir um ficheiro
Uma das operações mais frequentes que será executa relativamente aos ficheiros do Azure é transferir ficheiros. No exemplo a seguir, o código downloads SampleFile.txt e apresenta o conteúdo.

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
Outra operação de ficheiros do Azure comum é a eliminação de ficheiros. O código seguinte elimina um ficheiro denominado SampleFile.txt armazenados dentro de um diretório chamado **sampledir**.

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

## <a name="next-steps"></a>Passos Seguintes
Se gostaria de saber mais sobre outras APIs de armazenamento do Azure, siga estas ligações.

* [Azure para programadores Java](/java/azure)/)
* [Armazenamento do Azure SDK para Java](https://github.com/azure/azure-storage-java)
* [Armazenamento do Azure SDK para Android](https://github.com/azure/azure-storage-android)
* [Azure Storage Client SDK Reference](http://dl.windowsazure.com/storage/javadoc/) (Referência do SDK do Cliente do Armazenamento do Azure)
* [API REST dos Serviços do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blogue da Equipa de Armazenamento do Azure](https://blogs.msdn.com/b/windowsazurestorage/)
* [Transferir dados com o Utilitário de Linha de Comandos AzCopy](../common/storage-use-azcopy.md)
* [Resolução de problemas de Ficheiros do Azure - Windows](storage-troubleshoot-windows-file-connection-problems.md)
