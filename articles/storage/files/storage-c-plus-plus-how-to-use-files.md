---
title: Desenvolver para arquivos do Azure C++ com | Microsoft Docs
description: Saiba como desenvolver C++ aplicativos e serviços que usam os arquivos do Azure para armazenar dados de arquivo.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 97af40bd1f57acb5b26d3b6216984dfb8e3a5181
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699806"
---
# <a name="develop-for-azure-files-with-c"></a>Desenvolver para arquivos do Azure comC++

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="about-this-tutorial"></a>Acerca deste tutorial

Neste tutorial, você aprenderá a executar operações básicas em arquivos do Azure. Por meio de exemplos C++escritos em, você aprenderá a criar compartilhamentos e diretórios, carregar, listar e excluir arquivos. Se você for novo nos arquivos do Azure, passar pelos conceitos nas seções a seguir será útil para entender os exemplos.

* Criar e excluir compartilhamentos de arquivos do Azure
* Criar e excluir diretórios
* Enumerar arquivos e diretórios em um compartilhamento de arquivos do Azure
* Carregar, baixar e excluir um arquivo
* Definir a cota (tamanho máximo) para um compartilhamento de arquivos do Azure
* Criar uma assinatura de acesso partilhado (chave SAS) para um ficheiro que utiliza uma política de acesso partilhado definida na partilha.

> [!Note]  
> Como os arquivos do Azure podem ser acessados via SMB, é possível escrever aplicativos simples que acessam o compartilhamento de C++ arquivos do Azure usando as classes e funções padrão de e/s. Este artigo descreverá como escrever aplicativos que usam o SDK de armazenamento C++ do Azure, que usa a [API REST de arquivo](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) para se comunicar com os arquivos do Azure.

## <a name="create-a-c-application"></a>Criar uma aplicação C++

Para criar os exemplos, você precisará instalar a biblioteca de cliente do armazenamento do Azure C++2.4.0 para. Você também deve ter criado uma conta de armazenamento do Azure.

Para instalar o 2.4.0 do cliente de armazenamento C++do Azure para o, você pode usar um dos seguintes métodos:

* **Linux:** Siga as instruções fornecidas na página [biblioteca de cliente do armazenamento C++ do Azure para Leiame](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) .
* **Windows:** No Visual Studio, clique **em &gt; ferramentas Gerenciador &gt; de pacotes NuGet console do Gerenciador de pacotes**. Digite o seguinte comando no [console do Gerenciador de pacotes NuGet](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) e pressione **Enter**.
  

```powershell
Install-Package wastorage
```

## <a name="set-up-your-application-to-use-azure-files"></a>Configurar seu aplicativo para usar os arquivos do Azure

Adicione as seguintes instruções include à parte superior do arquivo C++ de origem no qual você deseja manipular os arquivos do Azure:

```cpp
#include <was/storage_account.h>
#include <was/file.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Configurar uma cadeia de conexão de armazenamento do Azure

Para usar o armazenamento de arquivos, você precisa se conectar à sua conta de armazenamento do Azure. A primeira etapa seria configurar uma cadeia de conexão, que usaremos para se conectar à sua conta de armazenamento. Vamos definir uma variável estática para fazer isso.

```cpp
// Define the connection-string with your values.
const utility::string_t
storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="connecting-to-an-azure-storage-account"></a>Conectando-se a uma conta de armazenamento do Azure

Você pode usar a classe **cloud_storage_account** para representar as informações da sua conta de armazenamento. Para obter as informações da conta de armazenamento da cadeia de ligação de armazenamento, pode utilizar o método **analisar**.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure

Todos os arquivos e diretórios em um compartilhamento de arquivos do Azure residem em um contêiner chamado **compartilhamento**. Sua conta de armazenamento pode ter o máximo de compartilhamentos que a capacidade da sua conta permite. Para obter acesso a um compartilhamento e seu conteúdo, você precisa usar um cliente de arquivos do Azure.

```cpp
// Create the Azure Files client.
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();
```

Usando o cliente de arquivos do Azure, você pode obter uma referência a um compartilhamento.

```cpp
// Get a reference to the file share
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
```

Para criar o compartilhamento, use o método **create_if_not_exists** do objeto **cloud_file_share** .

```cpp
if (share.create_if_not_exists()) {
    std::wcout << U("New share created") << std::endl;
}
```

Neste ponto, o **compartilhamento** mantém uma referência a um compartilhamento chamado **My-Sample-share**.

## <a name="delete-an-azure-file-share"></a>Excluir um compartilhamento de arquivos do Azure

A exclusão de um compartilhamento é feita chamando o método **delete_if_exists** em um objeto cloud_file_share. Aqui está o código de exemplo que faz isso.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// delete the share if exists
share.delete_share_if_exists();
```

## <a name="create-a-directory"></a>Criar um diretório

Você pode organizar o armazenamento colocando arquivos dentro de subdiretórios em vez de ter todos eles no diretório raiz. Os arquivos do Azure permitem que você crie quantos diretórios forem permitidos na sua conta. O código a seguir criará um diretório chamado **My-Sample-Directory** no diretório raiz, bem como um subdiretório chamado **My-Sample-** subdirectory.

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

A exclusão de um diretório é uma tarefa simples, embora deva-se observar que não é possível excluir um diretório que ainda contenha arquivos ou outros diretórios.

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

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerar arquivos e diretórios em um compartilhamento de arquivos do Azure

A obtenção de uma lista de arquivos e diretórios em um compartilhamento é facilmente feita chamando **list_files_and_directories** em uma referência **cloud_file_directory** . Para acessar o rico conjunto de propriedades e métodos para um **list_file_and_directory_item**retornado, você deve chamar o método **list_file_and_directory_item. as _file** para obter um objeto **cloud_file** ou o **list_file_and_directory_ item. como método _directory** para obter um objeto **cloud_file_directory** .

O código a seguir demonstra como recuperar e gerar a saída do URI de cada item no diretório raiz do compartilhamento.

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

Na pior das hipóteses, um compartilhamento de arquivos do Azure contém um diretório raiz onde os arquivos podem residir. Nesta seção, você aprenderá a carregar um arquivo do armazenamento local no diretório raiz de um compartilhamento.

A primeira etapa no carregamento de um arquivo é obter uma referência para o diretório onde ele deve residir. Faça isso chamando o método **get_root_directory_reference** do objeto de compartilhamento.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();
```

Agora que você tem uma referência ao diretório raiz do compartilhamento, é possível carregar um arquivo nele. Este exemplo carrega de um arquivo, de texto e de um fluxo.

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

Para baixar arquivos, primeiro recupere uma referência de arquivo e, em seguida, chame o método **download_to_stream** para transferir o conteúdo do arquivo para um objeto de fluxo, que você pode persistir em um arquivo local. Como alternativa, você pode usar o método **download_to_file** para baixar o conteúdo de um arquivo para um arquivo local. Você pode usar o método **download_text** para baixar o conteúdo de um arquivo como uma cadeia de texto.

O exemplo a seguir usa os métodos **download_to_stream** e **download_text** para demonstrar o download dos arquivos, que foram criados nas seções anteriores.

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

Outra operação comum dos arquivos do Azure é a exclusão de arquivos. O código a seguir exclui um arquivo chamado My-Sample-File-3 armazenado no diretório raiz.

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

## <a name="set-the-quota-maximum-size-for-an-azure-file-share"></a>Definir a cota (tamanho máximo) para um compartilhamento de arquivos do Azure

Você pode definir a cota (ou o tamanho máximo) para um compartilhamento de arquivos, em gigabytes. De igual modo, pode verificar a quantidade de dados atualmente armazenada na partilha.

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

Você pode gerar uma assinatura de acesso compartilhado (SAS) para um compartilhamento de arquivos ou para um arquivo individual. De igual modo, pode criar uma política de acesso partilhado numa partilha de ficheiros para gerir assinaturas de acesso partilhado. Recomenda-se a criação de uma política de acesso partilhado, uma vez que fornece um meio de revogação do SAS se este estiver comprometido.

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
* [Exemplos de serviço de arquivo de armazenamento do Azure emC++](https://github.com/Azure-Samples/storage-file-cpp-getting-started)
* [Explorador do Armazenamento do Azure](https://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
* [Documentação do Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/)