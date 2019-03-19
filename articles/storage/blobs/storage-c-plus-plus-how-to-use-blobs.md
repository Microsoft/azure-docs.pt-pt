---
title: Como utilizar o armazenamento de (Blob objeto) do C++ - Azure | Documentos da Microsoft
description: Store dados não estruturados na cloud com o armazenamento de Blobs do Azure (objeto).
services: storage
author: MichaelHauss
ms.service: storage
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: michaelhauss
ms.subservice: blobs
ms.openlocfilehash: d86b2c71515900405b0e7714d2c36cd8e4cbc7fc
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993214"
---
# <a name="how-to-use-blob-storage-from-c"></a>Como utilizar o armazenamento de Blobs do C++

Este guia demonstra como realizar cenários comuns utilizando o armazenamento de Blobs do Azure. Os exemplos mostram como carregar, listar, transferir e eliminar blobs. Os exemplos são escritos no C++ e utilizam a [Biblioteca de Cliente de Armazenamento do Microsoft Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).   

Para saber mais sobre o armazenamento de BLOBs, veja [introdução ao armazenamento de Blobs do Azure](storage-blobs-introduction.md).

> [!NOTE]
> Este guia destina-se à Biblioteca de Clientes de Armazenamento do Microsoft Azure para C++ versão 1.0.0 e acima. A Microsoft recomenda utilizar a versão mais recente da biblioteca de clientes de armazenamento para C++, disponível através de [NuGet](https://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Criar uma aplicação C++
Neste guia, irá utilizar recursos de armazenamento que podem ser executados dentro de um aplicativo C++.  

Para tal, terá de instalar a biblioteca de clientes de Armazenamento do Microsoft Azure para C++ e criar uma conta de armazenamento do Azure na sua subscrição do Azure.   

Para instalar a biblioteca de clientes de Armazenamento do Microsoft Azure para C++, pode utilizar os seguintes métodos:

* **Linux:** Siga as instruções na [biblioteca de cliente de armazenamento do Azure para C++ Leiame](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) página.  
* **Windows:** No Visual Studio, clique em **Ferramentas > Gestor de Pacotes NuGet >Consola do Gestor de Pacotes**. Escreva o seguinte comando para o [consola de Gestor de pacotes NuGet](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) e prima **ENTER**.  
  
     Pacote de instalação wastorage

## <a name="configure-your-application-to-access-blob-storage"></a>Configurar a sua aplicação para aceder ao armazenamento de BLOBs
Adicione que declarações na parte superior do ficheiro de C++ em que pretende utilizar as APIs de armazenamento do Azure para aceder a blobs de incluir o seguinte:  

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Configurar uma cadeia de ligação de armazenamento do Azure
Os clientes do Armazenamento do Azure utilizam uma cadeia de ligação de armazenamento para armazenar pontos finais e credenciais para aceder a serviços de gestão de dados. Quando em execução num aplicativo cliente, tem de fornecer a cadeia de ligação de armazenamento no seguinte formato, com o nome da sua conta de armazenamento e a chave de acesso de armazenamento para a conta de armazenamento listada no [Portal do Azure](https://portal.azure.com) para o *AccountName* e *AccountKey* valores. Para obter informações sobre contas de armazenamento e as chaves de acesso, consulte [sobre contas de armazenamento do Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Este exemplo mostra como pode declarar um campo estático para conter a cadeia de ligação:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Para testar a sua aplicação no seu computador Windows local, pode utilizar o Microsoft Azure [emulador de armazenamento](../storage-use-emulator.md) que é instalado com o [SDK do Azure](https://azure.microsoft.com/downloads/). O emulador de armazenamento é um utilitário que simula os serviços de BLOBs, filas e tabelas disponíveis no Azure no seu computador de desenvolvimento local. O seguinte exemplo mostra como pode declarar um campo estático para conter a cadeia de ligação para o seu emulador local de armazenamento:

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Para iniciar o emulador de armazenamento do Azure, selecione o **começar** botão ou prima o **Windows** chave. Comece a escrever **emulador de armazenamento do Azure**e selecione **emulador de armazenamento do Microsoft Azure** na lista de aplicações.  

Os exemplos seguintes partem do princípio de que utiliza um destes dois métodos para obter a cadeia de ligação de armazenamento.  

## <a name="retrieve-your-connection-string"></a>Obter a sua cadeia de ligação
Pode utilizar o **cloud_storage_account** classe para representar as informações da conta de armazenamento. Para obter as informações da conta de armazenamento da cadeia de ligação de armazenamento, pode utilizar o método **analisar**.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Em seguida, obtém uma referência a um **cloud_blob_client** classe, pois permite-lhe obter os objetos que representam a contentores e blobs armazenados no armazenamento de Blobs. O código seguinte cria um **cloud_blob_client** objeto usando o objeto de conta de armazenamento recuperamos acima:  

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Como: Criar um contentor
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Este exemplo mostra como criar um contentor se ainda não existir:  

```cpp
try
{
    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Create the container if it doesn't already exist.
    container.create_if_not_exists();
}
catch (const std::exception& e)
{
    std::wcout << U("Error: ") << e.what() << std::endl;
}  
```

Por predefinição, o novo contentor é privado e tem de especificar a chave de acesso de armazenamento para transferir blobs deste contentor. Se pretender disponibilizar os ficheiros (blobs) dentro do contentor para todos os utilizadores, pode definir o contentor como público utilizando o seguinte código:  

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Qualquer pessoa na Internet pode ver os blobs num contentor público, mas pode modificar ou eliminá-los apenas se tiver a chave de acesso adequada.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Como: Carregar um blob para um contentor
O Azure suporta de armazenamento de BLOBs bloquear blobs e blobs de páginas. Na maioria dos casos, o blob de blocos é o tipo recomendado a utilizar.  

Para carregar um ficheiro para um blob de blocos, obtenha uma referência de contentor e utilize-a para obter uma referência de blob de blocos. Assim que tiver uma referência de blob, pode carregar qualquer fluxo de dados para o mesmo ao chamar o **upload_from_stream** método. Esta operação irá criar o blob caso não exista, ou substituí-lo se existir. O exemplo seguinte mostra como carregar um blob para um contentor e parte do princípio de que o contentor já foi criado.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Create or overwrite the "my-blob-1" blob with contents from a local file.
concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
blockBlob.upload_from_stream(input_stream);
input_stream.close().wait();

// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
// Retrieve a reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
blob2.upload_text(U("more text"));

// Retrieve a reference to a blob named "my-blob-3".
azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
blob3.upload_text(U("other text"));  
```

Em alternativa, pode utilizar o **upload_from_file** método para carregar um ficheiro para um blob de blocos.

## <a name="how-to-list-the-blobs-in-a-container"></a>Como: Listar os blobs num contentor
Para listar os blobs num contentor, obtenha primeiro uma referência de contentor. Em seguida, pode utilizar o contentor **list_blobs** método para recuperar os blobs e/ou diretórios dentro do mesmo. Para aceder ao conjunto avançado de propriedades e métodos de um **list_blob_item**, tem de chamar os **list_blob_item.as_blob** método para obter uma **cloud_blob** objeto, ou o **list_blob.as_directory** método para obter um objeto de cloud_blob_directory. O código a seguir demonstra como obter e apresentar o URI de cada item na **contentor meu exemplo** contentor:

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Output URI of each item.
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
    }
    else
    {
        std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
}
```

Para obter mais detalhes sobre operações de listagem, consulte [lista de recursos de armazenamento do Azure em C++](../storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Como: Transferir blobs
Para transferir blobs, primeiro obter uma referência de blob e, em seguida, chamar o **download_to_stream** método. O exemplo seguinte utiliza a **download_to_stream** método para transferir os conteúdos do blob para um objeto de fluxo que, em seguida, pode manter num ficheiro local.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Save blob contents to a file.
concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
blockBlob.download_to_stream(output_stream);

std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();

outfile.write((char *)&data[0], buffer.size());
outfile.close();  
```

Em alternativa, pode utilizar o **download_to_file** método para transferir o conteúdo de um blob para um ficheiro.
Além disso, também pode utilizar o **download_text** método para transferir o conteúdo de um blob como uma cadeia de texto.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

// Download the contents of a blog as a text string.
utility::string_t text = text_blob.download_text();
```

## <a name="how-to-delete-blobs"></a>Como: Eliminar blobs
Para eliminar um blob, obtenha primeiro uma referência de blob e, em seguida, chame o **delete_blob** método nele.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Delete the blob.
blockBlob.delete_blob();
```

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu as noções básicas do armazenamento de BLOBs, siga estas ligações para saber mais sobre o armazenamento do Azure.  

* [Como utilizar o armazenamento de filas do C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Como utilizar o armazenamento de tabelas do C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Listar os recursos de armazenamento do Azure em C++](../storage-c-plus-plus-enumeration.md)
* [Biblioteca de clientes de armazenamento para referência do C++](https://azure.github.io/azure-storage-cpp)
* [Documentação do Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Transferir dados com o utilitário de linha de comandos AzCopy](../storage-use-azcopy.md)

