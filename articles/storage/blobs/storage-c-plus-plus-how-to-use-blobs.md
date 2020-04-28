---
title: Como utilizar o armazenamento de objetos (Blob) de C++ - Azure [ Microsoft Docs
description: Guarde dados não estruturados na nuvem com armazenamento azure Blob (objeto).
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/21/2018
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 0a9015e33f5456efeac7f7c887995ac4a69f0259
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75941815"
---
# <a name="how-to-use-blob-storage-from-c"></a>Como utilizar o armazenamento Blob a partir de C++

Este guia demonstra como executar cenários comuns utilizando o armazenamento azure blob. Os exemplos mostram como carregar, listar, descarregar e eliminar bolhas. Os exemplos são escritos no C++ e utilizam a [Biblioteca de Cliente de Armazenamento do Microsoft Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).   

Para saber mais sobre o armazenamento blob, consulte [Introdução ao armazenamento De Blob Azure](storage-blobs-introduction.md).

> [!NOTE]
> Este guia destina-se à Biblioteca de Clientes de Armazenamento do Microsoft Azure para C++ versão 1.0.0 e acima. A Microsoft recomenda a utilização da versão mais recente da Biblioteca de Clientes de Armazenamento para C++, disponível via [NuGet](https://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Criar uma aplicação C++
Neste guia, utilizará funcionalidades de armazenamento que podem ser executadas dentro de uma aplicação C++.  

Para tal, terá de instalar a biblioteca de clientes de Armazenamento do Microsoft Azure para C++ e criar uma conta de armazenamento do Azure na sua subscrição do Azure.   

Para instalar a biblioteca de clientes de Armazenamento do Microsoft Azure para C++, pode utilizar os seguintes métodos:

* **Linux:** Siga as instruções dadas na Biblioteca de Clientes de [Armazenamento Azure para C++ README: Começar na página do Linux.](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux)
* **Janelas:** No Windows, use [vcpkg](https://github.com/microsoft/vcpkg) como gerente de dependência. Siga o [início rápido](https://github.com/microsoft/vcpkg#quick-start) para inicializar os vcpkg. Em seguida, utilize o seguinte comando para instalar a biblioteca:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Pode encontrar um guia para como construir o código fonte e exportar para nuGet no ficheiro [README.](https://github.com/Azure/azure-storage-cpp#download--install)

## <a name="configure-your-application-to-access-blob-storage"></a>Configure a sua aplicação para aceder ao armazenamento blob
Adicione as seguintes declarações no topo do ficheiro C++ onde pretende utilizar as APIs de armazenamento Azure para aceder a bolhas:  

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Configurar uma cadeia de ligação de armazenamento Azure
Os clientes do Armazenamento do Azure utilizam uma cadeia de ligação de armazenamento para armazenar pontos finais e credenciais para aceder a serviços de gestão de dados. Ao executar uma aplicação cliente, deve fornecer a cadeia de ligação de armazenamento no seguinte formato, utilizando o nome da sua conta de armazenamento e a chave de acesso ao armazenamento para a conta de armazenamento listada no [Portal Azure](https://portal.azure.com) para os valores *De Nome* de Conta e Chave *de Conta.* Para obter informações sobre contas de armazenamento e chaves de acesso, consulte sobre as Contas de [Armazenamento Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Este exemplo mostra como pode declarar um campo estático para conter a cadeia de ligação:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Para testar a sua aplicação no seu computador Windows local, pode utilizar o [emulador](../storage-use-emulator.md) de armazenamento Microsoft Azure que está instalado com o [Azure SDK](https://azure.microsoft.com/downloads/). O emulador de armazenamento é um utilitário que simula os serviços Blob, Queue e Table disponíveis em Azure na sua máquina de desenvolvimento local. O seguinte exemplo mostra como pode declarar um campo estático para conter a cadeia de ligação para o seu emulador local de armazenamento:

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Para iniciar o emulador de armazenamento Azure, selecione o botão **Iniciar** ou prima a tecla **Windows.** Comece a digitar **o Emulador**de Armazenamento Azure e selecione **o Emulador** de Armazenamento Do Microsoft Azure da lista de aplicações.  

Os exemplos seguintes partem do princípio de que utiliza um destes dois métodos para obter a cadeia de ligação de armazenamento.  

## <a name="retrieve-your-storage-account"></a>Recupere a sua conta de armazenamento
Pode utilizar a **classe cloud_storage_account** para representar as informações da sua Conta de Armazenamento. Para obter as informações da conta de armazenamento da cadeia de ligação de armazenamento, pode utilizar o método **analisar**.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Em seguida, obtenha uma referência a uma aula **de cloud_blob_client** pois permite-lhe recuperar objetos que representam contentores e bolhas armazenados no armazenamento blob. O seguinte código cria um objeto **cloud_blob_client** utilizando o objeto da conta de armazenamento que recuperámos acima:  

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Como: Criar um recipiente
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

Por predefinição, o novo recipiente é privado e deve especificar a sua chave de acesso ao armazenamento para descarregar bolhas deste contentor. Se pretender disponibilizar os ficheiros (bolhas) no recipiente a todos, pode configurar o recipiente para ser público utilizando o seguinte código:  

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Qualquer pessoa na Internet pode ver bolhas num contentor público, mas só pode modificá-las ou eliminá-las se tiver a chave de acesso adequada.  

## <a name="how-to-upload-a-blob-into-a-container"></a>Como: Enviar uma bolha num recipiente
O armazenamento Azure Blob suporta bolhas de blocoe bolhas de página. Na maioria dos casos, o blob de blocos é o tipo recomendado a utilizar.  

Para carregar um ficheiro para um blob de blocos, obtenha uma referência de contentor e utilize-a para obter uma referência de blob de blocos. Uma vez que tenha uma referência blob, pode enviar qualquer fluxo de dados para ele, chamando o método **upload_from_stream.** Esta operação irá criar o blob caso não exista, ou substituí-lo se existir. O exemplo seguinte mostra como carregar um blob para um contentor e parte do princípio de que o contentor já foi criado.  

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

Em alternativa, pode utilizar o método **upload_from_file** para fazer o upload de um ficheiro para uma bolha de bloco.

## <a name="how-to-list-the-blobs-in-a-container"></a>Como: Listar as bolhas num recipiente
Para listar os blobs num contentor, obtenha primeiro uma referência de contentor. Em seguida, pode utilizar o método **list_blobs** do recipiente para recuperar as bolhas e/ou diretórios no seu interior. Para aceder ao rico conjunto de propriedades e métodos para uma **list_blob_item**devolvida, deve chamar o método **list_blob_item.as_blob** para obter um objeto **cloud_blob,** ou o método **list_blob.as_directory** para obter um objeto cloud_blob_directory. O seguinte código demonstra como recuperar e obter o URI de cada item no recipiente **de meu recipiente de amostra:**

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

Para mais detalhes sobre as operações de listagem, consulte [list Azure Storage Resources em C++](../storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Como: Baixar bolhas
Para baixar bolhas, primeiro recupere uma referência blob e, em seguida, ligue para o método **download_to_stream.** O exemplo que se segue utiliza o método **download_to_stream** para transferir o conteúdo da bolha para um objeto de fluxo que pode então persistir num ficheiro local.  

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

Em alternativa, pode utilizar o método **download_to_file** para descarregar o conteúdo de uma bolha para um ficheiro.
Além disso, também pode usar o método **download_text** para descarregar o conteúdo de uma bolha como uma cadeia de texto.  

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

## <a name="how-to-delete-blobs"></a>Como: Eliminar bolhas
Para apagar uma bolha, primeiro obtenha uma referência blob e, em seguida, ligue para o **método delete_blob** nele.  

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
Agora que aprendeu o básico do armazenamento de blob, siga estes links para saber mais sobre o Armazenamento Azure.  

* [Como utilizar o Armazenamento de Filas a partir do C++](../storage-c-plus-plus-how-to-use-queues.md)
* [Como utilizar o Armazenamento de Mesa a partir de C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Lista De recursos de armazenamento Azure em C++](../storage-c-plus-plus-enumeration.md)
* [Biblioteca do Cliente de Armazenamento para Referência C++](https://azure.github.io/azure-storage-cpp)
* [Documentação de Armazenamento Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Transferir dados com o utilitário da linha de comando AzCopy](../storage-use-azcopy.md)

