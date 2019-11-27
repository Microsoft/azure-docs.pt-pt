---
title: Como usar o armazenamento de objeto (BLOB) C++ do-Azure | Microsoft Docs
description: Armazene dados não estruturados na nuvem com o armazenamento BLOB (objeto) do Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/21/2018
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: be3dc27823c09823133d5b9a3a3f34afe52ec57d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227912"
---
# <a name="how-to-use-blob-storage-from-c"></a>Como usar o armazenamento de blobs deC++

Este guia demonstra como executar cenários comuns usando o armazenamento de BLOBs do Azure. Os exemplos mostram como carregar, listar, baixar e excluir BLOBs. Os exemplos são escritos no C++ e utilizam a [Biblioteca de Cliente de Armazenamento do Microsoft Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).   

Para saber mais sobre o armazenamento de BLOBs, consulte [introdução ao armazenamento de BLOBs do Azure](storage-blobs-introduction.md).

> [!NOTE]
> Este guia destina-se à Biblioteca de Clientes de Armazenamento do Microsoft Azure para C++ versão 1.0.0 e acima. A Microsoft recomenda usar a versão mais recente da biblioteca de cliente de C++armazenamento para, disponível via [NuGet](https://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Criar uma aplicação C++
Neste guia, você usará os recursos de armazenamento que podem ser executados em C++ um aplicativo.  

Para tal, terá de instalar a biblioteca de clientes de Armazenamento do Microsoft Azure para C++ e criar uma conta de armazenamento do Azure na sua subscrição do Azure.   

Para instalar a biblioteca de clientes de Armazenamento do Microsoft Azure para C++, pode utilizar os seguintes métodos:

* **Linux:** Siga as instruções fornecidas na página [biblioteca de cliente do armazenamento C++ do Azure para leiame: introdução no Linux](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) .
* **Windows:** No Windows, use [vcpkg](https://github.com/microsoft/vcpkg) como o Gerenciador de dependência. Siga o [início rápido](https://github.com/microsoft/vcpkg#quick-start) para inicializar o vcpkg. Em seguida, use o seguinte comando para instalar a biblioteca:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Você pode encontrar um guia sobre como criar o código-fonte e exportar para o NuGet no arquivo [Leiame](https://github.com/Azure/azure-storage-cpp#download--install) .

## <a name="configure-your-application-to-access-blob-storage"></a>Configurar seu aplicativo para acessar o armazenamento de BLOBs
Adicione as seguintes instruções include à parte superior do C++ arquivo em que você deseja usar as APIs de armazenamento do Azure para acessar os BLOBs:  

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Configurar uma cadeia de conexão de armazenamento do Azure
Os clientes do Armazenamento do Azure utilizam uma cadeia de ligação de armazenamento para armazenar pontos finais e credenciais para aceder a serviços de gestão de dados. Ao executar em um aplicativo cliente, você deve fornecer a cadeia de conexão de armazenamento no formato a seguir, usando o nome da sua conta de armazenamento e a chave de acesso de armazenamento para a conta de armazenamento listada no [portal do Azure](https://portal.azure.com) para os valores *AccountName* e *AccountKey* . Para obter informações sobre contas de armazenamento e chaves de acesso, consulte [sobre contas de armazenamento do Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Este exemplo mostra como pode declarar um campo estático para conter a cadeia de ligação:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Para testar seu aplicativo no computador local do Windows, você pode usar o [emulador de armazenamento](../storage-use-emulator.md) Microsoft Azure que é instalado com o [SDK do Azure](https://azure.microsoft.com/downloads/). O emulador de armazenamento é um utilitário que simula os serviços BLOB, fila e tabela disponíveis no Azure em seu computador de desenvolvimento local. O seguinte exemplo mostra como pode declarar um campo estático para conter a cadeia de ligação para o seu emulador local de armazenamento:

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Para iniciar o emulador de armazenamento do Azure, selecione o botão **Iniciar** ou pressione a tecla **Windows** . Comece digitando **emulador de armazenamento do Azure**e selecione **emulador de armazenamento do Microsoft Azure** na lista de aplicativos.  

Os exemplos seguintes partem do princípio de que utiliza um destes dois métodos para obter a cadeia de ligação de armazenamento.  

## <a name="retrieve-your-storage-account"></a>Recuperar sua conta de armazenamento
Você pode usar a classe **cloud_storage_account** para representar as informações da conta de armazenamento. Para obter as informações da conta de armazenamento da cadeia de ligação de armazenamento, pode utilizar o método **analisar**.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Em seguida, obtenha uma referência a uma classe **cloud_blob_client** , pois ela permite que você recupere objetos que representam contêineres e blobs armazenados no armazenamento de BLOBs. O código a seguir cria um objeto **cloud_blob_client** usando o objeto de conta de armazenamento que recuperamos acima:  

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Como: criar um contêiner
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

Por padrão, o novo contêiner é privado e você deve especificar sua chave de acesso de armazenamento para baixar BLOBs desse contêiner. Se você quiser disponibilizar os arquivos (BLOBs) dentro do contêiner para todos, poderá definir o contêiner como público usando o seguinte código:  

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Qualquer pessoa na Internet pode ver BLOBs em um contêiner público, mas você pode modificá-los ou excluí-los somente se tiver a chave de acesso apropriada.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Como carregar um blob em um contêiner
O armazenamento de BLOBs do Azure dá suporte a blobs de blocos e blobs de páginas. Na maioria dos casos, o blob de blocos é o tipo recomendado a utilizar.  

Para carregar um ficheiro para um blob de blocos, obtenha uma referência de contentor e utilize-a para obter uma referência de blob de blocos. Depois de ter uma referência de BLOB, você pode carregar qualquer fluxo de dados para ele chamando o método **upload_from_stream** . Esta operação irá criar o blob caso não exista, ou substituí-lo se existir. O exemplo seguinte mostra como carregar um blob para um contentor e parte do princípio de que o contentor já foi criado.  

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

Como alternativa, você pode usar o método **upload_from_file** para carregar um arquivo em um blob de blocos.

## <a name="how-to-list-the-blobs-in-a-container"></a>Como: listar os BLOBs em um contêiner
Para listar os blobs num contentor, obtenha primeiro uma referência de contentor. Em seguida, você pode usar o método **list_blobs** do contêiner para recuperar os BLOBs e/ou diretórios dentro dele. Para acessar o rico conjunto de propriedades e métodos para um **list_blob_item**retornado, você deve chamar o método **list_blob_item. as_blob** para obter um objeto **cloud_blob** ou o método **list_blob. as_directory** para obter um objeto cloud_blob_directory. O código a seguir demonstra como recuperar e gerar a saída do URI de cada item no contêiner **meu contêiner de exemplo** :

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

Para obter mais detalhes sobre as operações de listagem, consulte [listar C++recursos de armazenamento do Azure no ](../storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Como: baixar BLOBs
Para baixar BLOBs, primeiro recupere uma referência de BLOB e, em seguida, chame o método **download_to_stream** . O exemplo a seguir usa o método **download_to_stream** para transferir o conteúdo do blob para um objeto de fluxo que você pode persistir em um arquivo local.  

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

Como alternativa, você pode usar o método **download_to_file** para baixar o conteúdo de um blob em um arquivo.
Além disso, você também pode usar o método **download_text** para baixar o conteúdo de um blob como uma cadeia de texto.  

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

## <a name="how-to-delete-blobs"></a>Como excluir BLOBs
Para excluir um blob, primeiro obtenha uma referência de BLOB e, em seguida, chame o método **delete_blob** nela.  

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

## <a name="next-steps"></a>Passos seguintes
Agora que você aprendeu os conceitos básicos do armazenamento de BLOBs, siga estes links para saber mais sobre o armazenamento do Azure.  

* [Como usar o armazenamento de filas doC++](../storage-c-plus-plus-how-to-use-queues.md)
* [Como usar o armazenamento de tabela doC++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Listar recursos de armazenamento do Azure noC++](../storage-c-plus-plus-enumeration.md)
* [Biblioteca de cliente de C++ armazenamento para referência](https://azure.github.io/azure-storage-cpp)
* [Documentação do Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Transferir dados com o utilitário de linha de comandos AzCopy](../storage-use-azcopy.md)

