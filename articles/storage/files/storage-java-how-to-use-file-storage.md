---
title: Desenvolver para arquivos do Azure com Java | Microsoft Docs
description: Saiba como desenvolver aplicativos e serviços Java que usam os arquivos do Azure para armazenar dados de arquivo.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 00130114b793b5f4d4459eee3404fc4adaf6a8b4
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699401"
---
# <a name="develop-for-azure-files-with-java"></a>Desenvolver para arquivos do Azure com Java
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>Acerca deste tutorial
Este tutorial demonstrará as noções básicas do uso do Java para desenvolver aplicativos ou serviços que usam os arquivos do Azure para armazenar dados de arquivo. Neste tutorial, criaremos um aplicativo de console e mostraremos como executar ações básicas com o Java e os arquivos do Azure:

* Criar e excluir compartilhamentos de arquivos do Azure
* Criar e excluir diretórios
* Enumerar arquivos e diretórios em um compartilhamento de arquivos do Azure
* Carregar, baixar e excluir um arquivo

> [!Note]  
> Como os arquivos do Azure podem ser acessados via SMB, é possível escrever aplicativos que acessam o compartilhamento de arquivos do Azure usando as classes padrão de e/s do Java. Este artigo descreverá como escrever aplicativos que usam o SDK do Java do armazenamento do Azure, que usa a [API REST dos arquivos do Azure](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) para se comunicar com os arquivos do Azure.

## <a name="create-a-java-application"></a>Criar uma aplicação Java
Para criar os exemplos, você precisará do JDK (Java Development Kit) e do [SDK do armazenamento do Azure para Java](https://github.com/Azure/azure-storage-java). Você também deve ter criado uma conta de armazenamento do Azure.

## <a name="set-up-your-application-to-use-azure-files"></a>Configurar seu aplicativo para usar os arquivos do Azure
Para usar as APIs de armazenamento do Azure, adicione a seguinte instrução à parte superior do arquivo Java do qual você pretende acessar o serviço de armazenamento.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de conexão de armazenamento do Azure
Para usar os arquivos do Azure, você precisa se conectar à sua conta de armazenamento do Azure. A primeira etapa seria configurar uma cadeia de conexão, que usaremos para se conectar à sua conta de armazenamento. Vamos definir uma variável estática para fazer isso.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> Substitua your_storage_account_name e your_storage_account_key pelos valores reais de sua conta de armazenamento.
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Conectando-se a uma conta de armazenamento do Azure
Para se conectar à sua conta de armazenamento, você precisa usar o objeto **CloudStorageAccount** , passando uma cadeia de conexão para o método **Parse** .

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount. Parse** gera um InvalidKeyException, portanto, você precisará colocá-lo dentro de um bloco try/catch.

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure
Todos os arquivos e diretórios nos arquivos do Azure residem em um contêiner chamado **compartilhamento**. Sua conta de armazenamento pode ter o máximo de compartilhamentos que a capacidade da sua conta permite. Para obter acesso a um compartilhamento e seu conteúdo, você precisa usar um cliente de arquivos do Azure.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Usando o cliente de arquivos do Azure, você pode obter uma referência a um compartilhamento.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Para realmente criar o compartilhamento, use o método **createIfNotExists** do objeto CloudFileShare.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

Neste ponto, o **compartilhamento** mantém uma referência a um compartilhamento chamado **sampleshare**.

## <a name="delete-an-azure-file-share"></a>Excluir um compartilhamento de arquivos do Azure
A exclusão de um compartilhamento é feita chamando o método **deleteIfExists** em um objeto CloudFileShare. Aqui está o código de exemplo que faz isso.

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
Você também pode organizar o armazenamento colocando arquivos dentro de subdiretórios em vez de ter todos eles no diretório raiz. Os arquivos do Azure permitem que você crie quantos diretórios forem permitidos na sua conta. O código a seguir criará um subdiretório chamado **SampleDir** no diretório raiz.

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
A exclusão de um diretório é uma tarefa simples, embora deva-se observar que você não pode excluir um diretório que ainda contém arquivos ou outros diretórios.

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

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerar arquivos e diretórios em um compartilhamento de arquivos do Azure
A obtenção de uma lista de arquivos e diretórios em um compartilhamento é facilmente feita chamando **listFilesAndDirectories** em uma referência CloudFileDirectory. O método retorna uma lista de objetos ListFileItem nos quais você pode iterar. Por exemplo, o código a seguir listará arquivos e diretórios dentro do diretório raiz.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Carregar um ficheiro
Nesta seção, você aprenderá a carregar um arquivo do armazenamento local no diretório raiz de um compartilhamento.

A primeira etapa no carregamento de um arquivo é obter uma referência para o diretório onde ele deve residir. Faça isso chamando o método **getRootDirectoryReference** do objeto de compartilhamento.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Agora que você tem uma referência ao diretório raiz do compartilhamento, você pode carregar um arquivo para ele usando o código a seguir.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Transferir um ficheiro
Uma das operações mais frequentes que você executará em relação aos arquivos do Azure é baixar arquivos. No exemplo a seguir, o código baixa SampleFile. txt e exibe seu conteúdo.

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
Outra operação comum dos arquivos do Azure é a exclusão de arquivos. O código a seguir exclui um arquivo chamado SampleFile. txt armazenado dentro de um diretório chamado **SampleDir**.

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
Se você quiser saber mais sobre outras APIs de armazenamento do Azure, siga estes links.

* [Azure para desenvolvedores Java](/java/azure)/)
* [SDK do armazenamento do Azure para Java](https://github.com/azure/azure-storage-java)
* [SDK do armazenamento do Azure para Android](https://github.com/azure/azure-storage-android)
* [Azure Storage Client SDK Reference](http://dl.windowsazure.com/storage/javadoc/) (Referência do SDK do Cliente do Armazenamento do Azure)
* [API REST dos Serviços do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blogue da Equipa de Armazenamento do Azure](https://blogs.msdn.com/b/windowsazurestorage/)
* [Transferir dados com o Utilitário de Linha de Comandos AzCopy](../common/storage-use-azcopy.md)
* [Resolução de problemas de Ficheiros do Azure - Windows](storage-troubleshoot-windows-file-connection-problems.md)
