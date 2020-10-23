---
title: 'Quickstart: Azure Blob Storage library v12 - C++'
description: Neste arranque rápido, aprende-se a usar a versão 12 da biblioteca do cliente Azure Blob Storage para criar um recipiente e uma bolha no armazenamento blob (objeto). Em seguida, vai aprender a transferir o blob para o computador local e a listar todos os blobs num contentor.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/21/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: ba5dfbaba49be0521e07b2460c9920664790bf1e
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92379307"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-c"></a>Quickstart: Azure Blob Storage biblioteca de clientes v12 para C++

Começa com a biblioteca de clientes Azure Blob Storage v12 para C++. Azure Blob Storage é a solução de armazenamento de objetos da Microsoft para a nuvem. Siga as medidas para instalar a embalagem e experimente o código de exemplo para tarefas básicas. O Blob Storage está otimizado para armazenar quantidades massivas de dados não estruturados.

Utilize a biblioteca de clientes Azure Blob Storage v12 para C++ para:

- Criar um contentor
- Carregar um blob para o Armazenamento do Microsoft Azure
- Listar todas as bolhas em um recipiente
- Descarregue a bolha para o seu computador local
- Eliminar um contentor

Recursos:

- [Documentação de referência da API](https://azure.github.io/azure-sdk-for-cpp/storage.html)
- [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage)
- [Amostras](/azure/storage/common/storage-samples-c-plus-plus?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Subscrição do Azure](https://azure.microsoft.com/free/)
- [Conta de armazenamento Azure](/azure/storage/common/storage-quickstart-create-account)
- [Compilador C++](https://azure.github.io/azure-sdk/cpp_implementation.html#supported-platforms)
- [CMake](https://cmake.org/)
- [Vcpkg - C e C++ gestor de pacotes](https://github.com/microsoft/vcpkg/blob/master/docs/index.md)
- [LibCurl](https://curl.haxx.se/libcurl/)
- [LibXML2](http://www.xmlsoft.org/)

## <a name="setting-up"></a>Configuração

Esta secção acompanha-o através da preparação de um projeto para trabalhar com a biblioteca de clientes Azure Blob Storage v12 para C++.

### <a name="install-the-packages"></a>Instalar as embalagens

Se ainda não o fez, instale os pacotes LibCurl e LibXML2 utilizando o `vcpkg install` comando.

```console
vcpkg.exe install libxml2:x64-windows curl:x64-windows
```

Siga as instruções no GitHub para adquirir e construir o [Azure SDK para C++](https://github.com/Azure/azure-sdk-for-cpp/).

### <a name="create-the-project"></a>Criar o projeto

No Visual Studio, crie uma nova aplicação de consola C++ para Windows chamada *BlobQuickstartV12*.

:::image type="content" source="./media/quickstart-blobs-c-plus-plus/vs-create-project.jpg" alt-text="Diálogo visual Studio para configurar uma nova aplicação de consola C++ Windows":::

Adicione as seguintes bibliotecas ao projeto:

- libcurl.lib
- libxml2.lib
- bcrypt.lib
- Crypt32.Lib
- WS2_32.Lib
- azure-core.lib
- azure-storage-common.lib
- azure-storage-blobs.lib

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O Azure Blob Storage está otimizado para armazenar quantidades massivas de dados não estruturados. Dados não estruturados são dados que não aderem a um determinado modelo ou definição de dados, como texto ou dados binários. Blob Storage oferece três tipos de recursos:

- A conta de armazenamento
- Um recipiente na conta de armazenamento
- Uma bolha no recipiente

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama da arquitetura de armazenamento de bolhas](./media/storage-blobs-introduction/blob1.png)

Utilize as seguintes classes C++ para interagir com estes recursos:

- [BlobServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_service_client.html): A `BlobServiceClient` classe permite-lhe manipular os recursos de armazenamento do Azure e os recipientes blob.
- [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html): A `BlobContainerClient` classe permite-lhe manipular os recipientes de armazenamento Azure e as suas bolhas.
- [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html): A `BlobClient` classe permite-lhe manipular as bolhas de armazenamento Azure. É a classe base para todas as aulas especializadas de blob.
- [BlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html): A `BlockBlobClient` classe permite-lhe manipular as bolhas do bloco de armazenamento Azure.

## <a name="code-examples"></a>Exemplos de código

Estes excertos de código de exemplo mostram-lhe como fazer as seguintes tarefas com a biblioteca do cliente Azure Blob Storage para C++:

- [Adicionar incluir ficheiros](#add-include-files)
- [Obter a cadeia de ligação](#get-the-connection-string)
- [Criar um contentor](#create-a-container)
- [Carregar bolhas para um recipiente](#upload-blobs-to-a-container)
- [Listar os blobs num contentor](#list-the-blobs-in-a-container)
- [Transferir blobs](#download-blobs)
- [Eliminar um contentor](#delete-a-container)

### <a name="add-include-files"></a>Adicionar incluir ficheiros

Do diretório do projeto:

1. Abra o ficheiro de solução *BlobQuickstartV12.sln* no Visual Studio
1. Inside Visual Studio, abra o ficheiro de origem *BlobQuickstartV12.cpp*
1. Remova qualquer código dentro `main` que tenha sido autogerado
1. Adicionar `#include` declarações

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_Includes":::

### <a name="get-the-connection-string"></a>Obter a cadeia de ligação

O código abaixo recupera a cadeia de ligação para a sua conta de armazenamento a partir da variável ambiente criada na [Configuração da sua cadeia de ligação de armazenamento](#configure-your-storage-connection-string).

Adicione este código `main()` dentro:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Criar um contentor

Crie uma instância da classe [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html) chamando a função [CreateFromConnectionString.](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ad103d1e3a7ce7c53a82da887d12ce6fe) Em seguida, ligue para [criar](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#a22a1e001068a4ec52bb1b6bd8b52c047) o recipiente real na sua conta de armazenamento.

> [!IMPORTANT]
> Os nomes dos contentores têm de estar em minúscula. Para obter mais informações sobre a atribuição de nomes de contentores e blobs, veja [Nomenclatura e Referenciação de Contentores, Blobs e Metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Adicione este código ao fim `main()` de:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Carregar bolhas para um recipiente

O seguinte corte de código:

1. Declara uma corda contendo "Olá Azure!".
1. Obtém uma referência a um objeto [BlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html) chamando [GetBlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#acd8c68e3f37268fde0010dd478ff048f) no recipiente a partir da secção [criar um recipiente.](#create-a-container)
1. Carrega o fio para a bolha chamando a função [UploadFrom.](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html#af93af7e37f8806e39481596ef253f93d) Esta função cria a bolha se já não existir, ou atualiza-a se existir.

Adicione este código ao fim `main()` de:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_UploadBlob":::

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Enuncie as bolhas no recipiente chamando a função [ListBlobsFlatSegment.](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ac7712bc46909dc061d6bf554b496550c) Apenas uma bolha foi adicionada ao recipiente, por isso a operação devolve apenas aquela bolha.

Adicione este código ao fim `main()` de:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Transferir blobs

Obtenha as propriedades da bolha carregada. Em seguida, declare e redimensione um novo `std::string` objeto utilizando as propriedades da bolha carregada. Descarregue a bolha criada anteriormente para o novo `std::string` objeto, chamando a função [DownloadTo](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#aa844f37a8c216f3cb0f27912b114c4d2) na classe base [BlobClient.](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html) Finalmente, exiba os dados da bolha descarregado.

Adicione este código ao fim `main()` de:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DownloadBlob":::

### <a name="delete-a-blob"></a>Apagar uma Bolha

O seguinte código elimina a bolha do recipiente de armazenamento Azure Blob, chamando a função [BlobClient.Delete.](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#a621eabcc8d23893ca1eb106494198615)

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DeleteBlob":::

### <a name="delete-a-container"></a>Eliminar um contentor

O código seguinte limpa os recursos que a aplicação criou através da eliminação de todo o recipiente utilizando [BlobContainerClient.Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#aa6b1db52697ae92e9a1227e2e02a5178).

Adicione este código ao fim `main()` de:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DeleteContainer":::

## <a name="run-the-code"></a>Executar o código

Esta aplicação cria um contentor e envia um ficheiro de texto para o Azure Blob Storage. O exemplo enumera então as bolhas no recipiente, descarrega o ficheiro e exibe o conteúdo do ficheiro. Finalmente, a aplicação elimina a bolha e o recipiente.

A saída da app é semelhante ao seguinte exemplo:

```output
Azure Blob Storage v12 - C++ quickstart sample
Creating container: myblobcontainer
Uploading blob: blob.txt
Listing blobs...
Blob name: blob.txt
Downloaded blob contents: Hello Azure!
Deleting blob: blob.txt
Deleting container: myblobcontainer
```

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a carregar, descarregar e listar bolhas usando C++. Também aprendeu a criar e apagar um recipiente de armazenamento Azure Blob.

Para ver uma amostra de armazenamento de bolhas C++, continue a:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK v12 para a amostra C++](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-blobs/sample)
