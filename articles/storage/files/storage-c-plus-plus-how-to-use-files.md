---
title: Desenvolver para Ficheiros Azure com | C++ Microsoft Docs
description: Saiba como desenvolver aplicações e serviços C++ que utilizem ficheiros Azure para armazenar dados de ficheiros.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bb74ab16e51fbb3a157757353d5743e889f993dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629704"
---
# <a name="develop-for-azure-files-with-c"></a>Desenvolver para Ficheiros Azure com C++

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="about-this-tutorial"></a>Acerca deste tutorial

Neste tutorial, você vai aprender a executar operações básicas em Arquivos Azure. Através de amostras escritas em C++, você aprenderá a criar partilhas e diretórios, carregar, listar e apagar ficheiros. Se é novo no Azure Files, analisar os conceitos nas secções que se seguem será útil para entender as amostras.

* Criar e eliminar ações de ficheiros Azure
* Criar e apagar diretórios
* Enumerar ficheiros e diretórios numa partilha de ficheiros Azure
* Carregar, transferir e apagar um ficheiro
* Definir a quota (tamanho máximo) para uma partilha de ficheiros Azure
* Criar uma assinatura de acesso partilhado (chave SAS) para um ficheiro que utiliza uma política de acesso partilhado definida na partilha.

> [!Note]  
> Como os Ficheiros Azure podem ser acedidos através de SMB, é possível escrever aplicações simples que acedam à partilha de ficheiros Azure utilizando as classes e funções padrão C++ I/O. Este artigo descreverá como escrever aplicações que utilizem o Azure Storage C++ SDK, que utiliza a [API de REST de Ficheiros](/rest/api/storageservices/file-service-rest-api) para falar com ficheiros Azure.

## <a name="create-a-c-application"></a>Criar uma aplicação C++

Para construir as amostras, terá de instalar a Biblioteca do Cliente de Armazenamento Azure 2.4.0 para C++. Também deveria ter criado uma conta de armazenamento Azure.

Para instalar o Cliente de Armazenamento Azure 2.4.0 para C++, pode utilizar um dos seguintes métodos:

* **Linux:** Siga as instruções dadas na Biblioteca do Cliente de Armazenamento Azure para a página [C++ README.](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)
* **Janelas:** No Estúdio Visual, clique em **Tools &gt; NuGet Package Manager &gt; Package Manager Consola**. Digite o seguinte comando na [consola NuGet Package Manager](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) e prima **ENTER**.
  

```powershell
Install-Package wastorage
```

## <a name="set-up-your-application-to-use-azure-files"></a>Configurar a sua aplicação para utilizar ficheiros Azure

Adicione as seguintes declarações ao topo do ficheiro de origem C++ onde pretende manipular ficheiros Azure:

```cpp
#include <was/storage_account.h>
#include <was/file.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de ligação de armazenamento Azure

Para utilizar o armazenamento de ficheiros, tem de se ligar à sua conta de armazenamento Azure. O primeiro passo seria configurar uma cadeia de ligação, que usaremos para ligar à sua conta de armazenamento. Vamos definir uma variável estática para fazer isso.

```cpp
// Define the connection-string with your values.
const utility::string_t
storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="connecting-to-an-azure-storage-account"></a>Ligação a uma conta de armazenamento Azure

Pode utilizar a classe **cloud_storage_account** para representar as informações da sua Conta de Armazenamento. Para obter as informações da conta de armazenamento da cadeia de ligação de armazenamento, pode utilizar o método **analisar**.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure

Todos os ficheiros e diretórios de uma partilha de ficheiros Azure residem num contentor chamado **Share**. A sua conta de armazenamento pode ter o máximo de ações que a sua capacidade de conta permitir. Para obter acesso a uma partilha e ao seu conteúdo, é necessário utilizar um cliente Azure Files.

```cpp
// Create the Azure Files client.
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();
```

Utilizando o cliente Azure Files, pode então obter uma referência a uma partilha.

```cpp
// Get a reference to the file share
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
```

Para criar a partilha, utilize o **método create_if_not_exists** do objeto **cloud_file_share.**

```cpp
if (share.create_if_not_exists()) {
    std::wcout << U("New share created") << std::endl;
}
```

Neste momento, **a share** detém uma referência a uma ação chamada **my-sample-share**.

## <a name="delete-an-azure-file-share"></a>Excluir uma partilha de ficheiros Azure

A eliminação de uma parte é feita chamando o método **delete_if_exists** num objeto cloud_file_share. Aqui está o código de amostra que faz isso.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// delete the share if exists
share.delete_share_if_exists();
```

## <a name="create-a-directory"></a>Criar um diretório

Pode organizar o armazenamento colocando ficheiros dentro de subdireções em vez de ter todos eles no diretório de raiz. O Azure Files permite-lhe criar o maior número de diretórios que a sua conta permitirá. O código abaixo criará um diretório denominado **meu diretório de amostras** sob o diretório de raiz, bem como um subdiretório chamado **my-sample-subdirectory**.

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

A eliminação de um diretório é uma tarefa simples, embora deva notar que não é possível apagar um diretório que ainda contenha ficheiros ou outros diretórios.

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

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerar ficheiros e diretórios numa partilha de ficheiros Azure

A obtenção de uma lista de ficheiros e diretórios dentro de uma ação é facilmente feita ligando **list_files_and_directories** numa referência **cloud_file_directory.** Para aceder ao rico conjunto de propriedades e métodos para um **list_file_and_directory_item** devolvido, deve ligar para o método **list_file_and_directory_item.as_file** para obter um objeto **cloud_file** ou o método **list_file_and_directory_item.as_directy** para obter um objeto **cloud_file_directory.**

O código que se segue demonstra como recuperar e desaudam o URI de cada item no diretório de raiz da ação.

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

No mínimo, uma partilha de ficheiros Azure contém um diretório de raiz onde os ficheiros podem residir. Nesta secção, você vai aprender a carregar um ficheiro do armazenamento local para o diretório de raiz de uma ação.

O primeiro passo para o upload de um ficheiro é obter uma referência ao diretório onde deve residir. Fá-lo chamando o **método get_root_directory_reference** do objeto de partilha.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();
```

Agora que tem uma referência ao diretório de raiz da ação, pode enviar um ficheiro para o mesmo. Este exemplo é enviado a partir de um ficheiro, de texto e de um stream.

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

Para descarregar ficheiros, primeiro recupere uma referência de ficheiro e, em seguida, ligue para o método **download_to_stream** para transferir o conteúdo do ficheiro para um objeto de fluxo, que pode então persistir num ficheiro local. Em alternativa, pode utilizar o método **download_to_file** para transferir o conteúdo de um ficheiro para um ficheiro local. Pode utilizar o método **download_text** para descarregar o conteúdo de um ficheiro como uma cadeia de texto.

O exemplo a seguir utiliza os métodos **de download_to_stream** e **download_text** para demonstrar o descarregamento dos ficheiros, que foram criados em secções anteriores.

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

Outra operação comum dos Ficheiros Azure é a eliminação de ficheiros. O código seguinte elimina um ficheiro chamado my-sample-file-3 armazenado no diretório de raiz.

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

## <a name="set-the-quota-maximum-size-for-an-azure-file-share"></a>Definir a quota (tamanho máximo) para uma partilha de ficheiros Azure

Pode definir a quota (ou tamanho máximo) para uma partilha de ficheiros, em gigabytes. De igual modo, pode verificar a quantidade de dados atualmente armazenada na partilha.

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

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Storage do Azure, explore estes recursos:

* [Biblioteca de Clientes do Storage para C++](https://github.com/Azure/azure-storage-cpp)
* [Amostras de serviço de ficheiros de armazenamento Azure em C++](https://github.com/Azure-Samples/storage-file-cpp-getting-started)
* [Explorador do Storage do Azure](https://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
* [Documentação de armazenamento Azure](https://azure.microsoft.com/documentation/services/storage/)