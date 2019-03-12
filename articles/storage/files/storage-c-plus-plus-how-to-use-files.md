---
title: Programar para os ficheiros do Azure com o C++ | Documentos da Microsoft
description: Saiba como desenvolver aplicativos de C++ e serviços que utilizam ficheiros do Azure para armazenar dados de ficheiros.
services: storage
author: renashahmsft
ms.service: storage
ms.topic: article
ms.date: 09/19/2017
ms.author: renashahmsft
ms.subservice: files
ms.openlocfilehash: 92c77ce8124727d658cc596864f88037f6c34ac0
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569285"
---
# <a name="develop-for-azure-files-with-c"></a>Programar para os ficheiros do Azure com C++
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="about-this-tutorial"></a>Acerca deste tutorial
Neste tutorial, irá aprender como realizar operações básicas em ficheiros do Azure. Os exemplos escritos em C++, aprenderá a criar partilhas e diretórios, carregar, listar e eliminar ficheiros. Se estiver familiarizado com ficheiros do Azure, será útil na compreensão os exemplos de percorrer os conceitos em seções a seguir.

* Criar e eliminar partilhas de ficheiros do Azure
* Crie e elimine diretórios
* Enumerar arquivos e diretórios numa partilha de ficheiros do Azure
* Carregar, transferir e eliminar um ficheiro
* Definir a quota (tamanho máximo) para uma partilha de ficheiros do Azure
* Criar uma assinatura de acesso partilhado (chave SAS) para um ficheiro que utiliza uma política de acesso partilhado definida na partilha.

> [!Note]  
> Como ficheiros do Azure pode ser acedidos através de SMB, é possível escrever aplicações simples que acedam à partilha de ficheiros do Azure com as classes de e/s do C++ padrão e as funções. Este artigo descreve como escrever aplicações que utilizam o SDK de C++ de armazenamento do Azure, que utiliza a [API REST de ficheiros](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) para comunicar com o serviço ficheiros do Azure.

## <a name="create-a-c-application"></a>Criar uma aplicação C++
Para criar os exemplos, terá de instalar a biblioteca de cliente de armazenamento do Azure 2.4.0 para C++. Deve também ter criado uma conta de armazenamento do Azure.

Para instalar o cliente de armazenamento do Azure 2.4.0 para C++, pode usar um dos seguintes métodos:

* **Linux:** Siga as instruções na [biblioteca de cliente de armazenamento do Azure para C++ Leiame](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) página.
* **Windows:** No Visual Studio, clique em **ferramentas &gt; Gestor de pacotes NuGet &gt; Package Manager Console**. Escreva o seguinte comando para o [consola de Gestor de pacotes NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) e prima **ENTER**.
  
```
Install-Package wastorage
```

## <a name="set-up-your-application-to-use-azure-files"></a>Configurar a sua aplicação para utilizar ficheiros do Azure
Adicione que declarações na parte superior do ficheiro de origem C++ para manipular os ficheiros do Azure onde pretende de incluir o seguinte:

```cpp
#include <was/storage_account.h>
#include <was/file.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de ligação de armazenamento do Azure
Para utilizar o armazenamento de ficheiros, terá de ligar à sua conta de armazenamento do Azure. O primeiro passo seria configurar uma cadeia de ligação, o que vamos utilizar para ligar à sua conta de armazenamento. Vamos definir uma variável estática para fazer isso.

```cpp
// Define the connection-string with your values.
const utility::string_t
storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="connecting-to-an-azure-storage-account"></a>Ligar a uma conta de armazenamento do Azure
Pode utilizar o **cloud_storage_account** classe para representar as informações da conta de armazenamento. Para obter as informações da conta de armazenamento da cadeia de ligação de armazenamento, pode utilizar o método **analisar**.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure
Todos os ficheiros e diretórios numa partilha de ficheiros do Azure residem num contêiner chamado um **partilhar**. Sua conta de armazenamento pode ter quantas partilhas como permite que a sua capacidade de conta. Para obter acesso a uma partilha e o respetivo conteúdo, tem de utilizar um cliente de ficheiros do Azure.

```cpp
// Create the Azure Files client.
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();
```

Com o cliente de ficheiros do Azure, em seguida, pode obter uma referência a uma partilha.

```cpp
// Get a reference to the file share
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
```

Para criar a partilha, utilize o **create_if_not_exists** método o **cloud_file_share** objeto.

```cpp
if (share.create_if_not_exists()) {
    std::wcout << U("New share created") << std::endl;
}
```

Neste momento, **partilhar** mantém uma referência para uma partilha denominada **partilha de amostra my**.

## <a name="delete-an-azure-file-share"></a>Eliminar uma partilha de ficheiros do Azure
A eliminar uma partilha é feita chamando o **delete_if_exists** método num objeto de cloud_file_share. Aqui está o código de exemplo que faz isso.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// delete the share if exists
share.delete_share_if_exists();
```

## <a name="create-a-directory"></a>Criar um diretório
Pode organizar o armazenamento ao colocar os ficheiros dentro dos subdiretórios em vez de ter todas elas no diretório de raiz. Os ficheiros do Azure permite-lhe criar os diretórios como permitirá que a sua conta. O código abaixo cria um diretório chamado **directory meu exemplo** sob o diretório de raiz, bem como um subdiretório nomeado **subdiretório meu exemplo**.

```cpp
// Retrieve a reference to a directory
azure::storage::cloud_file_directory directory = share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Return value is true if the share did not exist and was successfully created.
directory.create_if_not_exists();

// Create a subdirectory.
azure::storage::cloud_file_directory subdirectory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
subdirectory.create_if_not_exists();
```

## <a name="delete-a-directory"></a>Eliminar um diretório
Eliminar um diretório é uma tarefa simple, embora é importante observar que não é possível eliminar um diretório que ainda contém arquivos ou de outros diretórios.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// Get a reference to the directory.
azure::storage::cloud_file_directory directory =
  share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Get a reference to the subdirectory you want to delete.
azure::storage::cloud_file_directory sub_directory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));

// Delete the subdirectory and the sample directory.
sub_directory.delete_directory_if_exists();

directory.delete_directory_if_exists();
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerar arquivos e diretórios numa partilha de ficheiros do Azure
Obter uma lista de ficheiros e diretórios dentro de uma partilha facilmente é feito chamando **list_files_and_directories** num **cloud_file_directory** referência. Para aceder ao conjunto avançado de propriedades e métodos de um **list_file_and_directory_item**, tem de chamar os **list_file_and_directory_item.as_file** método para obter um **cloud_file**  objeto, ou o **list_file_and_directory_item.as_directory** método para obter uma **cloud_file_directory** objeto.

O código a seguir demonstra como obter e apresentar o URI de cada item no diretório de raiz da partilha.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir =
  share.get_root_directory_reference();

// Output URI of each item.
azure::storage::list_file_and_directory_result_iterator end_of_results;

for (auto it = directory.list_files_and_directories(); it != end_of_results; ++it)
{
    if(it->is_directory())
    {
        ucout << "Directory: " << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
    else if (it->is_file())
    {
        ucout << "File: " << it->as_file().uri().primary_uri().to_string() << std::endl;
    }
}
```

## <a name="upload-a-file"></a>Carregar um ficheiro
Na pior das hipóteses, uma partilha de ficheiros do Azure contém um diretório de raiz em que podem residir. Nesta secção, irá aprender como carregar um ficheiro do armazenamento local para o diretório de raiz de uma partilha.

O primeiro passo para carregar um ficheiro é obter uma referência para o diretório onde devem residir. Fazê-lo ao chamar o **get_root_directory_reference** método do objeto de partilha.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();
```

Agora que tem uma referência para o diretório de raiz da partilha, pode carregar um ficheiro à classe. Este exemplo carrega a partir de um ficheiro de texto e de um fluxo.

```cpp
// Upload a file from a stream.
concurrency::streams::istream input_stream =
  concurrency::streams::file_stream<uint8_t>::open_istream(_XPLATSTR("DataFile.txt")).get();

azure::storage::cloud_file file1 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));
file1.upload_from_stream(input_stream);

// Upload some files from text.
azure::storage::cloud_file file2 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
file2.upload_text(_XPLATSTR("more text"));

// Upload a file from a file.
azure::storage::cloud_file file4 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
file4.upload_from_file(_XPLATSTR("DataFile.txt"));
```

## <a name="download-a-file"></a>Transferir um ficheiro
Para transferir ficheiros, primeiro obter uma referência de ficheiro e, em seguida, chamar o **download_to_stream** método para transferir o conteúdo do ficheiro para um objeto de fluxo, que, em seguida, pode manter num ficheiro local. Em alternativa, pode utilizar o **download_to_file** método para transferir o conteúdo de um ficheiro para um ficheiro local. Pode utilizar o **download_text** método para transferir o conteúdo de um ficheiro como uma cadeia de texto.

O exemplo seguinte utiliza a **download_to_stream** e **download_text** métodos para demonstrar a transferir os ficheiros que foram criados nas secções anteriores.

```cpp
// Download as text
azure::storage::cloud_file text_file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
utility::string_t text = text_file.download_text();
ucout << "File Text: " << text << std::endl;

// Download as a stream.
azure::storage::cloud_file stream_file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
stream_file.download_to_stream(output_stream);
std::ofstream outfile("DownloadFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();
outfile.write((char *)&data[0], buffer.size());
outfile.close();
```

## <a name="delete-a-file"></a>Eliminar um ficheiro
Outra operação de ficheiros do Azure comum é a eliminação de ficheiros. O código seguinte elimina um ficheiro denominado my-exemplo-ficheiro-3 armazenados no diretório de raiz.

```cpp
// Get a reference to the root directory for the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

azure::storage::cloud_file_directory root_dir =
  share.get_root_directory_reference();

azure::storage::cloud_file file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

file.delete_file_if_exists();
```

## <a name="set-the-quota-maximum-size-for-an-azure-file-share"></a>Definir a quota (tamanho máximo) para uma partilha de ficheiros do Azure
Pode definir a quota (ou o tamanho máximo) para uma partilha de ficheiros, em gigabytes. De igual modo, pode verificar a quantidade de dados atualmente armazenada na partilha.

Ao definir a quota para uma partilha, pode limitar o tamanho total dos ficheiros armazenados na partilha. Se o tamanho total dos ficheiros na partilha exceder a quota definida na partilha, os clientes não poderão aumentar o tamanho dos ficheiros existentes ou criar novos ficheiros, a menos que esses ficheiros estejam vazios.

O exemplo abaixo mostra como verificar a utilização atual para uma partilha e como configurar a quota para a partilha.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client.
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();

// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
if (share.exists())
{
    std::cout << "Current share usage: " << share.download_share_usage() << "/" << share.properties().quota();

    //This line sets the quota to 2560GB
    share.resize(2560);

    std::cout << "Quota increased: " << share.download_share_usage() << "/" << share.properties().quota();

}
```

## <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Gerar uma assinatura de acesso partilhado para um ficheiro ou partilha de ficheiros
Pode gerar uma assinatura de acesso partilhado (SAS) para uma partilha de ficheiros ou para um ficheiro individual. De igual modo, pode criar uma política de acesso partilhado numa partilha de ficheiros para gerir assinaturas de acesso partilhado. Recomenda-se a criação de uma política de acesso partilhado, uma vez que fornece um meio de revogação do SAS se este estiver comprometido.

O exemplo seguinte cria uma política de acesso partilhado numa partilha e, em seguida, utiliza essa política para fornecer as restrições para um SAS num ficheiro dentro da partilha.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client and get a reference to the share
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();

azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

if (share.exists())
{
    // Create and assign a policy
    utility::string_t policy_name = _XPLATSTR("sampleSharePolicy");

    azure::storage::file_shared_access_policy sharedPolicy =
      azure::storage::file_shared_access_policy();

    //set permissions to expire in 90 minutes
    sharedPolicy.set_expiry(utility::datetime::utc_now() +
       utility::datetime::from_minutes(90));

    //give read and write permissions
    sharedPolicy.set_permissions(azure::storage::file_shared_access_policy::permissions::write | azure::storage::file_shared_access_policy::permissions::read);

    //set permissions for the share
    azure::storage::file_share_permissions permissions;

    //retrieve the current list of shared access policies
    azure::storage::shared_access_policies<azure::storage::file_shared_access_policy> policies;

    //add the new shared policy
    policies.insert(std::make_pair(policy_name, sharedPolicy));

    //save the updated policy list
    permissions.set_policies(policies);
    share.upload_permissions(permissions);

    //Retrieve the root directory and file references
    azure::storage::cloud_file_directory root_dir =
        share.get_root_directory_reference();
    azure::storage::cloud_file file =
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));

    // Generate a SAS for a file in the share
    //  and associate this access policy with it.
    utility::string_t sas_token = file.get_shared_access_signature(sharedPolicy);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    azure::storage::cloud_file file_with_sas(azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()));
    utility::string_t text = _XPLATSTR("My sample content");
    file_with_sas.upload_text(text);

    //Download and print URL with SAS.
    utility::string_t downloaded_text = file_with_sas.download_text();
    ucout << downloaded_text << std::endl;
    ucout << azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()).to_string() << std::endl;

}
```
## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o Storage do Azure, explore estes recursos:

* [Biblioteca de Clientes de Armazenamento para C++](https://github.com/Azure/azure-storage-cpp)
* [Exemplos de serviço de ficheiros de armazenamento do Azure em C++](https://github.com/Azure-Samples/storage-file-cpp-getting-started)
* [Explorador do Armazenamento do Azure](https://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
* [Documentação do Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)
