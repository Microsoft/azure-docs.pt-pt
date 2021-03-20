---
title: Desenvolver para arquivos Azure com Java | Microsoft Docs
description: Saiba como desenvolver aplicações e serviços Java que usam ficheiros Azure para armazenar dados de ficheiros.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-java
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 25baa278961b93b04e60f2e997b98753cb6cf3ab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95024114"
---
# <a name="develop-for-azure-files-with-java"></a>Programar para os Ficheiros do Azure com Java

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Saiba os fundamentos que desenvolvem aplicações Java que usam ficheiros Azure para armazenar dados. Crie uma aplicação de consola e aprenda ações básicas usando APIs de Ficheiros Azure:

- Criar e eliminar ações de ficheiros Azure
- Criar e apagar diretórios
- Enumerar ficheiros e diretórios numa partilha de ficheiros Azure
- Carregar, transferir e apagar um ficheiro

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="create-a-java-application"></a>Criar uma aplicação Java

Para construir as amostras, você precisará do Kit de Desenvolvimento de Java (JDK) e do [Azure Storage SDK para Java.](https://github.com/azure/azure-sdk-for-java) Também deveria ter criado uma conta de armazenamento Azure.

## <a name="set-up-your-application-to-use-azure-files"></a>Configurar a sua aplicação para utilizar ficheiros Azure

Para utilizar as APIs dos Ficheiros Azure, adicione o seguinte código ao topo do ficheiro Java de onde pretende aceder aos Ficheiros Azure.

# <a name="java-v12"></a>[Java v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

```java
// Include the following imports to use Azure Files APIs v11
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de ligação de armazenamento Azure

Para utilizar ficheiros Azure, tem de se ligar à sua conta de armazenamento Azure. Configure uma cadeia de ligação e use-a para ligar à sua conta de armazenamento. Defina uma variável estática para segurar a cadeia de ligação.

# <a name="java-v12"></a>[Java v12](#tab/java)

Substitua *\<storage_account_name\>* e *\<storage_account_key\>* pelos valores reais da sua conta de armazenamento.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Substitua *your_storage_account_name* e *your_storage_account_key* pelos valores reais da sua conta de armazenamento.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

---

## <a name="access-azure-files-storage"></a>Aceder ao armazenamento de ficheiros Azure

# <a name="java-v12"></a>[Java v12](#tab/java)

Para aceder aos Ficheiros Azure, crie um objeto [ShareClient.](/java/api/com.azure.storage.file.share.shareclient) Utilize a classe [ShareClientBuilder](/java/api/com.azure.storage.file.share.shareclientbuilder) para construir um novo objeto **ShareClient.**

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createClient":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Para aceder à sua conta de armazenamento, utilize o objeto **CloudStorageAccount,** passando a cadeia de ligação para o seu método **de parse.**

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** lança um InvalidKeyException, por isso terá de colocá-lo dentro de um bloco de tentativa/captura.

---

## <a name="create-a-file-share"></a>Criar uma partilha de ficheiros

Todos os ficheiros e diretórios em Ficheiros Azure são armazenados num contentor chamado share.

# <a name="java-v12"></a>[Java v12](#tab/java)

O [método ShareClient.create](/java/api/com.azure.storage.file.share.shareclient.create) lança uma exceção se a partilha já existir. Coloque a chamada para **criar** num `try/catch` bloco e lidar com a exceção.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createFileShare":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Para obter acesso a uma partilha e ao seu conteúdo, crie um cliente Azure Files.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Utilizando o cliente Azure Files, pode então obter uma referência a uma partilha.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Para realmente criar a partilha, utilize o método **createIfNotExists** do objeto **CloudFileShare.**

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

Neste momento, **a share** detém uma referência a uma parte da **amostra** nomeada.

---

## <a name="delete-a-file-share"></a>Eliminar partilhas de ficheiros

O seguinte código de amostra elimina uma partilha de ficheiros.

# <a name="java-v12"></a>[Java v12](#tab/java)

Elimine uma ação chamando o método [ShareClient.delete.](/java/api/com.azure.storage.file.share.shareclient.delete)

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFileShare":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Elimine uma ação chamando o método **deleteIfExists** num objeto **CloudFileShare.**

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

---

## <a name="create-a-directory"></a>Criar um diretório

Organize o armazenamento colocando ficheiros dentro de subdiretórios em vez de ter todos eles no diretório de raiz.

# <a name="java-v12"></a>[Java v12](#tab/java)

O seguinte código cria um diretório chamando [ShareDirectoryClient.create](/java/api/com.azure.storage.file.share.sharedirectoryclient.create). O método exemplo devolve um `Boolean` valor que indica se criou com sucesso o diretório.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createDirectory":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

O código a seguir cria um **sampledir** nomeado subdireor sob o diretório de raiz.

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

---

## <a name="delete-a-directory"></a>Eliminar um diretório

Apagar um diretório é uma tarefa simples. Não é possível apagar um diretório que ainda contenha ficheiros ou subdireções.

# <a name="java-v12"></a>[Java v12](#tab/java)

O método [ShareDirectoryClient.delete](/java/api/com.azure.storage.file.share.sharedirectoryclient.delete) lança uma exceção se o diretório não existir ou não estiver vazio. Coloque a chamada para **apagar** num `try/catch` bloco e lidar com a exceção.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteDirectory":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

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

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerar ficheiros e diretórios numa partilha de ficheiros Azure

# <a name="java-v12"></a>[Java v12](#tab/java)

Obtenha uma lista de ficheiros e diretórios ligando para [ShareDirectoryClient.listFilesAndDirectories](/java/api/com.azure.storage.file.share.sharedirectoryclient.listfilesanddirectories). O método devolve uma lista de objetos [ShareFileItem](/java/api/com.azure.storage.file.share.models.sharefileitem) nos quais pode iterar. O código que se segue lista ficheiros e diretórios dentro do diretório especificado pelo parâmetro *dirName.*

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_enumerateFilesAndDirs":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Obtenha uma lista de ficheiros e diretórios através da **listaFilesAndDirectories** numa referência **CloudFileDirectory.** O método devolve uma lista de objetos **ListFileItem** nos quais pode iterar. O código que se segue lista ficheiros e diretórios dentro do diretório de raiz.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

---

## <a name="upload-a-file"></a>Carregar um ficheiro

Saiba como carregar um ficheiro a partir do armazenamento local.

# <a name="java-v12"></a>[Java v12](#tab/java)

O código seguinte envia um ficheiro local para o armazenamento do Ficheiro Azure, ligando para o método [ShareFileClient.uploadFromFile.](/java/api/com.azure.storage.file.share.sharefileclient.uploadfromfile) O método de exemplo a seguir devolve um `Boolean` valor que indica se fez o upload com sucesso do ficheiro especificado.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_uploadFile":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

Obtenha uma referência ao diretório onde o ficheiro será carregado chamando o método **getRootDirectoryReference** no objeto de partilha.

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

---

## <a name="download-a-file"></a>Transferir um ficheiro

Uma das operações mais frequentes é descarregar ficheiros do armazenamento do Azure Files.

# <a name="java-v12"></a>[Java v12](#tab/java)

O exemplo seguinte descarrega o ficheiro especificado para o diretório local especificado no parâmetro *destDir.* O método exemplo torna o nome de ficheiro descarregado único, pré-enquanto preencha a data e a hora.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_downloadFile":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

O exemplo a seguir descarrega SampleFile.txt e exibe o seu conteúdo.

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

---

## <a name="delete-a-file"></a>Eliminar um ficheiro

Outra operação comum dos Ficheiros Azure é a eliminação de ficheiros.

# <a name="java-v12"></a>[Java v12](#tab/java)

O código seguinte elimina o ficheiro especificado. Em primeiro lugar, o exemplo cria um [ShareDirectoryClient](/java/api/com.azure.storage.file.share.sharedirectoryclient) baseado no parâmetro *dirName.* Em seguida, o código obtém um [ShareFileClient](/java/api/com.azure.storage.file.share.sharefileclient) do cliente do diretório, com base no parâmetro *de nome de ficheiro.* Finalmente, o método exemplo chama [ShareFileClient.delete](/java/api/com.azure.storage.file.share.sharefileclient.delete) para eliminar o ficheiro.

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFile":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

O código seguinte elimina um ficheiro denominado SampleFile.txt armazenado dentro de um diretório denominado **sampledir**.

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

---

## <a name="next-steps"></a>Passos seguintes

Se você gostaria de saber mais sobre outras APIs de armazenamento Azure, siga estes links.

- [Azure para programadores de Java](/azure/developer/java)
- [SDK do Azure para Java](https://github.com/azure/azure-sdk-for-java)
- [Azure SDK para Android](https://github.com/azure/azure-sdk-for-android)
- [Biblioteca de clientes Azure File Share para Referência SDK java](/java/api/overview/azure/storage-file-share-readme)
- [API REST dos Serviços do Armazenamento do Azure](/rest/api/storageservices/)
- [Blog da equipa de armazenamento Azure](https://azure.microsoft.com/blog/topics/storage-backup-and-recovery/)
- [Transferir dados com o Utilitário de Linha de Comandos AzCopy](../common/storage-use-azcopy-v10.md)
- [Resolução de problemas de Ficheiros do Azure - Windows](storage-troubleshoot-windows-file-connection-problems.md)
