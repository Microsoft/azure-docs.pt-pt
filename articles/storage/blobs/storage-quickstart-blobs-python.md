---
title: 'Quickstart: Biblioteca de armazenamento Azure Blob v12 - Python'
description: Neste arranque rápido, aprende-se a usar a versão 12 da biblioteca de clientes de armazenamento Azure Blob para python para criar um recipiente e uma bolha no armazenamento blob (objeto). Em seguida, vai aprender a transferir o blob para o computador local e a listar todos os blobs num contentor.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 8daf7380e859cd2f9b5890c716f7b7d95e6c3fe4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061344"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Quickstart: Gerir bolhas com Python v12 SDK

Neste arranque rápido, aprende-se a gerir bolhas usando python. As bolhas são objetos que podem conter grandes quantidades de texto ou dados binários, incluindo imagens, documentos, meios de streaming e dados de arquivo. Você vai carregar, descarregar e listar bolhas, e você vai criar e apagar recipientes.

[Documentação de](/python/api/azure-storage-blob) | referência API Pacote[de código fonte](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob) | [(Índice de Pacote Python)](https://pypi.org/project/azure-storage-blob/) | [Samples](https://docs.microsoft.com/azure/storage/common/storage-samples-python?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Uma conta do Armazenamento do Azure. [Criar uma conta de armazenamento.](../common/storage-account-create.md)
- [Python](https://www.python.org/downloads/) 2.7, 3.5, ou acima.

> [!NOTE]
> Para começar com a versão SDK anterior, consulte [Quickstart: Gerencie as bolhas com Python v2.1 SDK](storage-quickstart-blobs-python-legacy.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Configuração

Esta secção acompanha-o através da preparação de um projeto para trabalhar com a biblioteca de clientes de armazenamento Azure Blob v12 para Python.

### <a name="create-the-project"></a>Criar o projeto

Crie uma aplicação Python chamada *blob-quickstart-v12*.

1. Numa janela de consola (como cmd, PowerShell ou Bash), crie um novo diretório para o projeto.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Mude para o recém-criado diretório *blob-quickstart-v12.*

    ```console
    cd blob-quickstart-v12
    ```

1. Paralelamente ao diretório *blob-quickstart-v12,* crie outro diretório chamado *dados*. É aqui que os ficheiros de dados blob serão criados e armazenados.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Instale o pacote

Ainda no diretório de aplicações, instale a biblioteca de clientes `pip install` de armazenamento Azure Blob para pacote Python utilizando o comando.

```console
pip install azure-storage-blob
```

Este comando instala a biblioteca de clientes de armazenamento Azure Blob para o pacote Python e todas as bibliotecas de que depende. Neste caso, esta é apenas a biblioteca central azure para Python.

### <a name="set-up-the-app-framework"></a>Configurar o quadro da aplicação

Do diretório do projeto:

1. Abra um novo ficheiro de texto no seu editor de código
1. Adicionar `import` declarações
1. Criar a estrutura para o programa, incluindo o tratamento básico de exceções

    Aqui está o código:

    ```python
    import os, uuid
    from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient

    try:
        print("Azure Blob storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

1. Guarde o novo ficheiro como *blob-quickstart-v12.py* no diretório *blob-quickstart-v12.*

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O armazenamento da Blob Azure está otimizado para armazenar quantidades massivas de dados não estruturados. Os dados não estruturados são dados que não seguem uma definição ou um modelo de dados em particular, como por exemplo, texto ou dados binários. O armazenamento blob oferece três tipos de recursos:

* A conta de armazenamento
* Um recipiente na conta de armazenamento
* Uma bolha no recipiente

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama da arquitetura de armazenamento blob](./media/storage-blobs-introduction/blob1.png)

Utilize as seguintes aulas de Python para interagir com estes recursos:

* [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient): `BlobServiceClient` A classe permite-lhe manipular os recursos de Armazenamento Azure e os recipientes blob.
* [ContainerClient](/python/api/azure-storage-blob/azure.storage.blob.containerclient): `ContainerClient` A classe permite-lhe manipular os recipientes de armazenamento Azure e as suas bolhas.
* [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient): `BlobClient` A classe permite-lhe manipular bolhas de Armazenamento Azure.

## <a name="code-examples"></a>Exemplos de código

Estes snippets de código de exemplo mostram-lhe como executar o seguinte com a biblioteca de clientes de armazenamento Azure Blob para Python:

* [Obter a cadeia de ligação](#get-the-connection-string)
* [Criar um contentor](#create-a-container)
* [Faça upload de bolhas para um recipiente](#upload-blobs-to-a-container)
* [Listar os blobs num contentor](#list-the-blobs-in-a-container)
* [Transferir blobs](#download-blobs)
* [Eliminar um contentor](#delete-a-container)

### <a name="get-the-connection-string"></a>Obter a cadeia de ligação

O código abaixo recupera a cadeia de ligação para a conta de armazenamento a partir da variável ambiental criada na secção de cadeias de ligação de [armazenamento Configure.](#configure-your-storage-connection-string)

Adicione este código `try` dentro do bloco:

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

Decida um nome para o novo contentor. O código abaixo anexa um valor UUID para o nome do recipiente para garantir que é único.

> [!IMPORTANT]
> Os nomes dos contentores têm de estar em minúscula. Para obter mais informações sobre a atribuição de nomes de contentores e blobs, veja [Nomenclatura e Referenciação de Contentores, Blobs e Metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Crie uma instância da classe [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) chamando o método [from_connection_string.](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) Em seguida, ligue para o [método create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) para realmente criar o recipiente na sua conta de armazenamento.

Adicione este código ao `try` fim do bloco:

```python
# Create the BlobServiceClient object which will be used to create a container client
blob_service_client = BlobServiceClient.from_connection_string(connect_str)

# Create a unique name for the container
container_name = "quickstart" + str(uuid.uuid4())

# Create the container
container_client = blob_service_client.create_container(container_name)
```

### <a name="upload-blobs-to-a-container"></a>Faça upload de bolhas para um recipiente

O seguinte código snippet:

1. Cria um ficheiro de texto no diretório local.
1. Obtém uma referência a um objeto [BlobClient,](/python/api/azure-storage-blob/azure.storage.blob.blobclient) chamando o método [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) no [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) da secção [Criar um contentor.](#create-a-container)
1. Envia o ficheiro de texto local para a bolha, ligando para o método [upload_blob.](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-)

Adicione este código ao `try` fim do bloco:

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

Enumera ritas no recipiente, chamando o método [list_blobs.](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) Neste caso, apenas uma bolha foi adicionada ao recipiente, pelo que a operação de listagem devolve apenas uma bolha.

Adicione este código ao `try` fim do bloco:

```python
print("\nListing blobs...")

# List the blobs in the container
blob_list = container_client.list_blobs()
for blob in blob_list:
    print("\t" + blob.name)
```

### <a name="download-blobs"></a>Transferir blobs

Descarregue a bolha previamente criada, chamando o método [download_blob.](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) O código de exemplo adiciona um sufixo de "DOWNLOAD" ao nome do ficheiro para que possa ver ambos os ficheiros no sistema de ficheiros local.

Adicione este código ao `try` fim do bloco:

```python
# Download the blob to a local file
# Add 'DOWNLOAD' before the .txt extension so you can see both files in the data directory
download_file_path = os.path.join(local_path, str.replace(local_file_name ,'.txt', 'DOWNLOAD.txt'))
print("\nDownloading blob to \n\t" + download_file_path)

with open(download_file_path, "wb") as download_file:
    download_file.write(blob_client.download_blob().readall())
```

### <a name="delete-a-container"></a>Eliminar um contentor

O código que se segue limpa os recursos que a app criou removendo todo o recipiente utilizando o método [delete_container.](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) Também pode eliminar os ficheiros locais, se quiser.

A aplicação faz uma pausa `input()` para a entrada do utilizador, ligando antes de eliminar a bolha, o contentor e os ficheiros locais. Esta é uma boa oportunidade para verificar se os recursos foram corretamente criados, antes de serem eliminados.

Adicione este código ao `try` fim do bloco:

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

Esta aplicação cria um ficheiro de teste na sua pasta local e envia-o para o armazenamento blob. O exemplo lista as bolhas no recipiente e descarrega o ficheiro com um novo nome para que possa comparar os ficheiros antigos e novos.

Navegue para o *blob-quickstart-v12.py* diretório que contém `python` o ficheiro blob-quickstart-v12.py e execute o seguinte comando para executar a aplicação.

```console
python blob-quickstart-v12.py
```

A saída da aplicação é semelhante ao seguinte exemplo:

```output
Azure Blob storage v12 - Python quickstart sample

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

Antes de iniciar o processo de limpeza, verifique a pasta de *dados* dos dois ficheiros. Pode abri-los e constatar que são idênticos.

Depois de verificar os ficheiros, prima a tecla **Enter** para eliminar os ficheiros de teste e terminar a demonstração.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, aprendeu a carregar, descarregar e listar bolhas usando Python.

Para ver aplicações de amostrade armazenamento Blob, continue a:

> [!div class="nextstepaction"]
> [Amostras de armazenamento Azure Blob SDK v12 Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Para saber mais, consulte o [Azure SDK para Python.](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/README.md)
* Para tutoriais, amostras, quickstarts e outra documentação, visite [o Azure para desenvolvedores de Python.](/azure/python/)
