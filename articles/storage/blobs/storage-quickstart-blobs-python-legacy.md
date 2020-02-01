---
title: 'Início rápido: biblioteca de cliente do armazenamento de BLOBs do Azure v 2.1 para Python'
description: Neste início rápido, crie uma conta de armazenamento e um contentor no armazenamento de objetos (Blobs). Em seguida, você usa a biblioteca de cliente de armazenamento v 2.1 para Python para carregar um blob no armazenamento do Azure, baixar um blob e listar os BLOBs em um contêiner.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: seo-python-october2019
ms.openlocfilehash: 4b0248604b6e9189d5275177a4960e4c352e8215
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906435"
---
# <a name="quickstart-manage-blobs-with-python-v21-sdk"></a>Quickstart: Gerir bolhas com Python v2.1 SDK

Neste arranque rápido, aprende-se a gerir bolhas usando python. As bolhas são objetos que podem conter grandes quantidades de texto ou dados binários, incluindo imagens, documentos, meios de streaming e dados de arquivo. Você vai carregar, descarregar e listar bolhas, e você vai criar e apagar recipientes.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Uma conta do Armazenamento do Azure. [Criar uma conta de armazenamento](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/).
- [Azure Storage SDK para Python](https://github.com/Azure/azure-sdk-for-python).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo

A [aplicação de exemplo](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) utilizada neste guia de introdução é uma aplicação Python básica.  

Use o comando [git](https://git-scm.com/) a seguir para baixar o aplicativo em seu ambiente de desenvolvimento. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Para examinar o programa Python, abra o arquivo *example.py* na raiz do repositório.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento

Na aplicação, indique o nome da conta de armazenamento e a chave de conta para criar um objeto `BlockBlobService`.

1. Abra o ficheiro *example.py* no Explorador de Soluções no seu IDE.

1. Substitua os valores de `accountname` e `accountkey` pelo nome e chave da sua conta de armazenamento:

    ```python
    block_blob_service = BlockBlobService(
        account_name='accountname', account_key='accountkey')
    ```

1. Guarde e feche o ficheiro.

## <a name="run-the-sample"></a>Executar o exemplo

O programa de exemplo cria um arquivo de teste na pasta *documentos* , carrega o arquivo no armazenamento de BLOBs, lista os BLOBs no arquivo e baixa o arquivo com um novo nome.

1. Instale as dependências:

    ```console
    pip install azure-storage-blob==2.1.0
    ```

1. Vá para o aplicativo de exemplo:

    ```console
    cd storage-blobs-python-quickstart
    ```

1. Execute o exemplo:

    ```console
    python example.py
    ```

    Verá mensagens semelhantes à saída seguinte:
  
    ```output
    Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    List blobs in the container
             Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Downloading blob to     C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
    ```

1. Antes de continuar, vá para a pasta *documentos* e verifique os dois arquivos.

    * *QuickStart_\<\> de identificação universalmente única*
    * *QuickStart_\<_DOWNLOADED\>de identificação universalmente única*

1. Pode abri-los e ver que são idênticos.

    Você também pode usar uma ferramenta como a [Gerenciador de armazenamento do Azure](https://storageexplorer.com). É bom para exibir os arquivos no armazenamento de BLOBs. Gerenciador de Armazenamento do Azure é uma ferramenta gratuita de plataforma cruzada que permite acessar suas informações de conta de armazenamento. 

1. Depois de examinar os arquivos, pressione qualquer tecla para concluir a amostra e excluir os arquivos de teste.

## <a name="learn-about-the-sample-code"></a>Saiba mais sobre o código de exemplo

Agora que sabe o que o exemplo faz, abra o ficheiro *example.py* para ver o código.

### <a name="get-references-to-the-storage-objects"></a>Obter referências para os objetos de armazenamento

Nesta secção, vai instanciar os objetos, criar um contentor novo e, em seguida, definir as permissões no contentor, para que os blobs sejam públicos. Você chamará o contêiner `quickstartblobs`. 

```python
# Create the BlockBlockService that the system uses to call the Blob service for the storage account.
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')

# Create a container called 'quickstartblobs'.
container_name = 'quickstartblobs'
block_blob_service.create_container(container_name)

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(
    container_name, public_access=PublicAccess.Container)
```

Em primeiro lugar, cria as referências para os objetos que são utilizados para aceder ao armazenamento de Blobs e geri-lo. Estes objetos dependem uns dos outros e cada um é utilizado pelo que vem a seguir na lista.

* Instancie o objeto **BlockBlobService**, que aponta para o serviço Blob na sua conta de armazenamento. 

* Instancie o objeto **CloudBlobContainer**, que representa o contentor a que está aceder. O sistema usa contêineres para organizar seus BLOBs, como você usa pastas em seu computador para organizar os arquivos.

Quando tiver o contentor Cloud Blob, instancie o objeto **CloudBlockBlob** que aponta para o blob específico em que está interessado. Depois, pode carregar, transferir e copiar o blob, conforme o que precisar.

> [!IMPORTANT]
> Os nomes dos contentores têm de estar em minúscula. Para obter mais informações sobre os nomes dos contentores e dos blobs, veja [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Nomenclatura e Referência para Contentores, Blobs e Metadados).

### <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contentor

O armazenamento de blobs suporta blobs de blocos, blobs de acréscimo e blobs de páginas. Os blobs de blocos podem ter no máximo 4,7 TB e podem ser qualquer coisa, desde folhas de cálculo do Excel a ficheiros grandes de vídeo. Você pode usar blobs de acréscimo para registro em log quando desejar gravar em um arquivo e, em seguida, continuar adicionando mais informações. Os blobs de páginas são usados principalmente para os arquivos de disco rígido virtual (VHD) que retornem as máquinas virtuais de infraestrutura como serviço (VMs de IaaS). Os blobs de blocos são os mais frequentemente utilizados. Este início rápido usa blobs de blocos.

Para carregar um ficheiro para um blob, obtenha o caminho completo do ficheiro ao associar o nome de diretório ao nome de ficheiro no disco local. Em seguida, pode utilizar o método `create_blob_from_path` para carregar o ficheiro para o caminho especificado. 

O código da amostra cria um ficheiro local que o sistema utiliza para o upload e download, armazenando o ficheiro que o sistema envia como *full_path_to_file* e o nome da bolha como *local_file_name*. Este exemplo carrega o arquivo em seu contêiner chamado `quickstartblobs`:

```python
# Create a file in Documents to test the upload and download.
local_path = os.path.expanduser("~\Documents")
local_file_name = "QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file = os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file, 'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name.
block_blob_service.create_blob_from_path(
    container_name, local_file_name, full_path_to_file)
```

Pode utilizar diversos métodos de carregamento com o armazenamento de Blobs. Por exemplo, se tiver um fluxo de memória, pode utilizar o método `create_blob_from_stream` em vez de `create_blob_from_path`. 

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

O código seguinte cria uma `generator` para o método `list_blobs`. O código percorre a lista de BLOBs no contêiner e imprime seus nomes no console.

```python
# List the blobs in the container.
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Transferir os blobs


Descarregue as bolhas para o seu disco local utilizando o método `get_blob_to_path`.
O código a seguir baixa o blob que você carregou anteriormente. O sistema adere *_DOWNLOADED* ao nome blob para que possa ver ambos os ficheiros no seu disco local.

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, local_file_name.replace(
   '.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(
    container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Limpar recursos
Se já não precisar dos blobs carregados neste início rápido, pode eliminar o contentor inteiro com o método `delete_container`. Se pretender eliminar ficheiros individuais, utilize o método `delete_blob`.

```python
# Clean up resources. This includes the container and the temp files.
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```

## <a name="resources-for-developing-python-applications-with-blobs"></a>Recursos para desenvolver aplicações Python com blobs

Para obter mais informações sobre o desenvolvimento em Python com o armazenamento de BLOBs, consulte estes recursos adicionais:

### <a name="binaries-and-source-code"></a>Binários e código fonte

- Veja, transfira e instale o [código fonte da biblioteca cliente Python](https://github.com/Azure/azure-storage-python) do Armazenamento do Azure no GitHub.

### <a name="client-library-reference-and-samples"></a>Referência e exemplos da biblioteca de cliente

- Para obter mais informações sobre a biblioteca de cliente do Python, consulte as [bibliotecas de armazenamento do Azure para Python](https://docs.microsoft.com/python/api/overview/azure/storage).
- Explore os [exemplos de armazenamento de blobs](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob) escritos com a biblioteca de cliente Python.

## <a name="next-steps"></a>Passos seguintes
 
Neste início rápido, aprendeu a transferir ficheiros entre um disco local e o armazenamento de Blobs do Azure com Python. 

Para obter mais informações sobre a Gerenciador de Armazenamento e os BLOBs, consulte [gerenciar recursos de armazenamento de BLOBs do Azure com Gerenciador de armazenamento](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
