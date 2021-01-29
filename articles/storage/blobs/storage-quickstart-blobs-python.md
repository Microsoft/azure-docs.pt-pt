---
title: 'Quickstart: Azure Blob Storage library v12 - Python'
description: Neste arranque rápido, aprende-se a usar a versão 12 da biblioteca do cliente Azure Blob Storage para a Python para criar um recipiente e uma bolha no armazenamento blob (objeto). Em seguida, vai aprender a transferir o blob para o computador local e a listar todos os blobs num contentor.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: b35144c1ff4de9324086629bc764caea4bef98b6
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052741"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Quickstart: Gerir bolhas com Python v12 SDK

Neste arranque rápido, aprende-se a gerir as bolhas usando python. As bolhas são objetos que podem conter grandes quantidades de texto ou dados binários, incluindo imagens, documentos, streaming de meios e dados de arquivo. Você vai carregar, baixar e listar bolhas, e você vai criar e apagar recipientes.

Recursos adicionais:

* [Documentação de referência da API](/python/api/azure-storage-blob)
* [Código fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob)
* [Pacote (Índice de Pacote Python)](https://pypi.org/project/azure-storage-blob/)
* [Amostras](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Uma conta do Armazenamento do Azure. [Criar uma conta de armazenamento](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/) 2.7, 3.5, ou acima.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Configuração

Esta secção acompanha-o através da preparação de um projeto para trabalhar com a biblioteca de clientes Azure Blob Storage v12 para Python.

### <a name="create-the-project"></a>Criar o projeto

Crie uma aplicação Python chamada *blob-quickstart-v12*.

1. Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para o projeto.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Mude para o *recém-criado diretório blob-quickstart-v12.*

    ```console
    cd blob-quickstart-v12
    ```

1. De lado o *diretório blob-quickstart-v12,* crie outro diretório chamado *dados*. É aqui que os ficheiros de dados blob serão criados e armazenados.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Instale o pacote

Enquanto ainda está no diretório de aplicações, instale a biblioteca de clientes Azure Blob Storage para pacote Python utilizando o `pip install` comando.

```console
pip install azure-storage-blob
```

Este comando instala a biblioteca cliente Azure Blob Storage para pacote Python e todas as bibliotecas de que depende. Neste caso, esta é apenas a biblioteca central de Azure para Python.

### <a name="set-up-the-app-framework"></a>Configurar o quadro de aplicações

Do diretório do projeto:

1. Abra um novo ficheiro de texto no seu editor de código
1. Adicionar `import` declarações
1. Criar a estrutura para o programa, incluindo o manuseamento básico de exceções

    Aqui está o código:

    ```python
    import os, uuid
    from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient, __version__

    try:
        print("Azure Blob Storage v" + __version__ + " - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

1. Guarde o novo ficheiro à medida *que blob-quickstart-v12.py* no *diretório blob-quickstart-v12.*

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O Azure Blob Storage está otimizado para armazenar quantidades massivas de dados não estruturados. Os dados não estruturados são dados que não seguem uma definição ou um modelo de dados em particular, como por exemplo, texto ou dados binários. O armazenamento blob oferece três tipos de recursos:

* A conta de armazenamento
* Um recipiente na conta de armazenamento
* Uma bolha no recipiente

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama da arquitetura de armazenamento blob](./media/storage-blobs-introduction/blob1.png)

Utilize as seguintes classes Python para interagir com estes recursos:

* [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient): A `BlobServiceClient` classe permite-lhe manipular os recursos de armazenamento do Azure e os recipientes blob.
* [ContainerClient](/python/api/azure-storage-blob/azure.storage.blob.containerclient): A `ContainerClient` classe permite-lhe manipular recipientes de armazenamento Azure e suas bolhas.
* [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient): A `BlobClient` classe permite-lhe manipular as bolhas de armazenamento Azure.

## <a name="code-examples"></a>Exemplos de código

Estes excertos de código de exemplo mostram-lhe como executar o seguinte com a biblioteca de clientes Azure Blob Storage para Python:

* [Obter a cadeia de ligação](#get-the-connection-string)
* [Criar um contentor](#create-a-container)
* [Carregar bolhas para um recipiente](#upload-blobs-to-a-container)
* [Listar os blobs num contentor](#list-the-blobs-in-a-container)
* [Transferir blobs](#download-blobs)
* [Eliminar um contentor](#delete-a-container)

### <a name="get-the-connection-string"></a>Obter a cadeia de ligação

O código abaixo recupera a cadeia de ligação para a conta de armazenamento a partir da variável ambiente criada na secção de cadeia de [ligação de armazenamento.](#configure-your-storage-connection-string)

Adicione este código dentro do `try` bloco:

```python
# Retrieve the connection string for use with the application. The storage
# connection string is stored in an environment variable on the machine
# running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable is
# created after the application is launched in a console or with Visual Studio,
# the shell or application needs to be closed and reloaded to take the
# environment variable into account.
connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-container"></a>Criar um contentor

Decida o nome do novo recipiente. O código abaixo anexa um valor UUID ao nome do recipiente para garantir que é único.

> [!IMPORTANT]
> Os nomes dos contentores têm de estar em minúscula. Para obter mais informações sobre a atribuição de nomes de contentores e blobs, veja [Nomenclatura e Referenciação de Contentores, Blobs e Metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Crie uma instância da classe [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) chamando o método [from_connection_string.](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) Em seguida, ligue para o método [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) para realmente criar o recipiente na sua conta de armazenamento.

Adicione este código ao fim do `try` bloco:

```python
# Create the BlobServiceClient object which will be used to create a container client
blob_service_client = BlobServiceClient.from_connection_string(connect_str)

# Create a unique name for the container
container_name = "quickstart" + str(uuid.uuid4())

# Create the container
container_client = blob_service_client.create_container(container_name)
```

### <a name="upload-blobs-to-a-container"></a>Carregar bolhas para um recipiente

O seguinte corte de código:

1. Cria um ficheiro de texto no diretório local.
1. Obtém uma referência a um objeto [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) chamando o método [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) no [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) a partir da secção [Criar um recipiente.](#create-a-container)
1. Envia o ficheiro de texto local para a bolha, chamando o método [upload_blob.](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-)

Adicione este código ao fim do `try` bloco:

```python
# Create a file in local data directory to upload and download
local_path = "./data"
local_file_name = "quickstart" + str(uuid.uuid4()) + ".txt"
upload_file_path = os.path.join(local_path, local_file_name)

# Write text to the file
file = open(upload_file_path, 'w')
file.write("Hello, World!")
file.close()

# Create a blob client using the local file name as the name for the blob
blob_client = blob_service_client.get_blob_client(container=container_name, blob=local_file_name)

print("\nUploading to Azure Storage as blob:\n\t" + local_file_name)

# Upload the created file
with open(upload_file_path, "rb") as data:
    blob_client.upload_blob(data)
```

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Enuncie as bolhas no recipiente chamando o método [list_blobs.](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) Neste caso, apenas uma bolha foi adicionada ao recipiente, pelo que a operação de listagem devolve apenas uma bolha.

Adicione este código ao fim do `try` bloco:

```python
print("\nListing blobs...")

# List the blobs in the container
blob_list = container_client.list_blobs()
for blob in blob_list:
    print("\t" + blob.name)
```

### <a name="download-blobs"></a>Transferir blobs

Descarregue a bolha anteriormente criada chamando o método [download_blob.](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) O código de exemplo adiciona um sufixo de "DOWNLOAD" ao nome do ficheiro para que possa ver ambos os ficheiros no sistema de ficheiros local.

Adicione este código ao fim do `try` bloco:

```python
# Download the blob to a local file
# Add 'DOWNLOAD' before the .txt extension so you can see both files in the data directory
download_file_path = os.path.join(local_path, str.replace(local_file_name ,'.txt', 'DOWNLOAD.txt'))
print("\nDownloading blob to \n\t" + download_file_path)

with open(download_file_path, "wb") as download_file:
    download_file.write(blob_client.download_blob().readall())
```

### <a name="delete-a-container"></a>Eliminar um contentor

O código seguinte limpa os recursos que a app criou removendo todo o recipiente utilizando o método [delete_container.](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) Também pode apagar os ficheiros locais, se quiser.

A aplicação faz uma pausa para a entrada do utilizador, ligando `input()` antes de eliminar a bolha, o recipiente e os ficheiros locais. Esta é uma boa oportunidade para verificar se os recursos foram criados corretamente, antes de serem eliminados.

Adicione este código ao fim do `try` bloco:

```python
# Clean up
print("\nPress the Enter key to begin clean up")
input()

print("Deleting blob container...")
container_client.delete_container()

print("Deleting the local source and downloaded files...")
os.remove(upload_file_path)
os.remove(download_file_path)

print("Done")
```

## <a name="run-the-code"></a>Executar o código

Esta aplicação cria um ficheiro de teste na sua pasta local e envia-o para o armazenamento blob. O exemplo enumera então as bolhas no recipiente e descarrega o ficheiro com um novo nome para que possa comparar os ficheiros antigos e novos.

Navegue para o diretório que contém o ficheiro *blob-quickstart-v12.py* e, em seguida, execute o seguinte `python` comando para executar a aplicação.

```console
python blob-quickstart-v12.py
```

A saída da app é semelhante ao seguinte exemplo:

```output
Azure Blob Storage v12 - Python quickstart sample

Uploading to Azure Storage as blob:
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Listing blobs...
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Downloading blob to
        ./data/quickstartcf275796-2188-4057-b6fb-038352e35038DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Antes de iniciar o processo de limpeza, verifique se a pasta *de dados* é composta pelos dois ficheiros. Pode abri-los e constatar que são idênticos.

Depois de verificar os ficheiros, prima a tecla **'Entrar'** para eliminar os ficheiros de teste e terminar a demonstração.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a carregar, descarregar e listar bolhas usando Python.

Para ver as aplicações de amostra de armazenamento Blob, continue a:

> [!div class="nextstepaction"]
> [Amostras de Azure Blob Storage SDK v12 Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Para saber mais, consulte as [bibliotecas de clientes do Azure Storage para Python.](/azure/developer/python/sdk/storage/overview)
* Para tutoriais, amostras, quickstarts e outra documentação, visite [Azure for Python Developers](/azure/python/).
