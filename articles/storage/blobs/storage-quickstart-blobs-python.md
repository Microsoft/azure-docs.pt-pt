---
title: 'Início rápido: biblioteca de armazenamento de BLOBs do Azure V12-Python'
description: Neste guia de início rápido, você aprende a usar a biblioteca de cliente de armazenamento de BLOBs do Azure versão 12 para Python para criar um contêiner e um blob no armazenamento de BLOB (objeto). Em seguida, vai aprender a transferir o blob para o computador local e a listar todos os blobs num contentor.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 03f298b49e6a1eba84e8adf5ca6039df0bfe1abd
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906430"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Quickstart: Gerir bolhas com Python v12 SDK

Neste arranque rápido, aprende-se a gerir bolhas usando python. As bolhas são objetos que podem conter grandes quantidades de texto ou dados binários, incluindo imagens, documentos, meios de streaming e dados de arquivo. Você vai carregar, descarregar e listar bolhas, e você vai criar e apagar recipientes.

[Documentação de referência de API](/python/api/azure-storage-blob) | [código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob) | [pacote (índice de pacote do Python)](https://pypi.org/project/azure-storage-blob/) | [amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Uma conta do Armazenamento do Azure. [Criar uma conta de armazenamento](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/) 2.7, 3.5, ou acima.

> [!NOTE]
> Para começar com a versão SDK anterior, consulte [Quickstart: Gerencie as bolhas com Python v2.1 SDK](storage-quickstart-blobs-python-legacy.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Configurando

Esta seção orienta você pela preparação de um projeto para trabalhar com a biblioteca de cliente do armazenamento de BLOBs do Azure V12 para Python.

### <a name="create-the-project"></a>Criar o projeto

Crie um aplicativo Python chamado *blob-QuickStart-V12*.

1. Em uma janela de console (como cmd, PowerShell ou bash), crie um novo diretório para o projeto.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Alterne para o diretório *blob-QuickStart-V12* recém-criado.

    ```console
    cd blob-quickstart-v12
    ```

1. No lado do diretório *blob-QuickStart-V12* , crie outro diretório chamado *Data*. É aqui que os arquivos de dados de blob serão criados e armazenados.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Instalar o pacote

Ainda no diretório do aplicativo, instale a biblioteca de cliente do armazenamento de BLOBs do Azure para o pacote do Python usando o comando `pip install`.

```console
pip install azure-storage-blob
```

Esse comando instala a biblioteca de cliente do armazenamento de BLOBs do Azure para o pacote do Python e todas as bibliotecas das quais ela depende. Nesse caso, essa é apenas a biblioteca principal do Azure para Python.

### <a name="set-up-the-app-framework"></a>Configurar a estrutura do aplicativo

No diretório do projeto:

1. Abrir um novo arquivo de texto em seu editor de códigos
1. Adicionar instruções de `import`
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

1. Salve o novo arquivo como *blob-QuickStart-V12.py* no diretório *blob-QuickStart-V12* .

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O armazenamento de BLOBs do Azure é otimizado para armazenar grandes quantidades de dados não estruturados. Dados não estruturados são dados que não aderem a um determinado modelo ou definição de dados, tais como texto ou dados binários. O armazenamento de BLOBs oferece três tipos de recursos:

* A conta de armazenamento
* Um contêiner na conta de armazenamento
* Um blob no contêiner

O diagrama seguinte mostra a relação entre estes recursos.

![Diagrama da arquitetura de armazenamento de BLOBs](./media/storage-blob-introduction/blob1.png)

Use as seguintes classes python para interagir com estes recursos:

* [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient): a classe `BlobServiceClient` permite que você manipule os recursos de armazenamento do Azure e contêineres de BLOB.
* [ContainerClient](/python/api/azure-storage-blob/azure.storage.blob.containerclient): a classe `ContainerClient` permite que você manipule contêineres de armazenamento do Azure e seus BLOBs.
* [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient): a classe `BlobClient` permite que você manipule os blobs de armazenamento do Azure.

## <a name="code-examples"></a>Exemplos de código

Esses trechos de código de exemplo mostram como executar o seguinte com a biblioteca de cliente de armazenamento de BLOBs do Azure para Python:

* [Obter a cadeia de conexão](#get-the-connection-string)
* [Criar um contêiner](#create-a-container)
* [Carregar BLOBs em um contêiner](#upload-blobs-to-a-container)
* [Listar os BLOBs em um contêiner](#list-the-blobs-in-a-container)
* [Baixar BLOBs](#download-blobs)
* [Eliminar um contentor](#delete-a-container)

### <a name="get-the-connection-string"></a>Obter a cadeia de ligação

O código a seguir recupera a cadeia de conexão para a conta de armazenamento da variável de ambiente criada na seção [configurar sua cadeia de conexão de armazenamento](#configure-your-storage-connection-string) .

Adicione este código dentro do bloco de `try`:

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

Escolha um nome para o novo contêiner. O código a seguir acrescenta um valor UUID ao nome do contêiner para garantir que ele seja exclusivo.

> [!IMPORTANT]
> Os nomes dos contentores têm de estar em minúscula. Para obter mais informações sobre a atribuição de nomes de contentores e blobs, veja [Nomenclatura e Referenciação de Contentores, Blobs e Metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Crie uma instância da classe [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) chamando o método [from_connection_string.](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) Em seguida, ligue para o [método create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) para realmente criar o recipiente na sua conta de armazenamento.

Adicione este código ao final do bloco de `try`:

```python
# Create the BlobServiceClient object which will be used to create a container client
blob_service_client = BlobServiceClient.from_connection_string(connect_str)

# Create a unique name for the container
container_name = "quickstart" + str(uuid.uuid4())

# Create the container
container_client = blob_service_client.create_container(container_name)
```

### <a name="upload-blobs-to-a-container"></a>Carregar BLOBs em um contêiner

O seguinte trecho de código:

1. Cria um arquivo de texto no diretório local.
1. Obtém uma referência a um objeto [BlobClient,](/python/api/azure-storage-blob/azure.storage.blob.blobclient) chamando o método [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) no [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) da secção [Criar um contentor.](#create-a-container)
1. Envia o ficheiro de texto local para a bolha, ligando para o método [upload_blob.](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-)

Adicione este código ao final do bloco de `try`:

```python
# Create a file in local Documents directory to upload and download
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

Enumera ritas no recipiente, chamando o método [list_blobs.](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) Nesse caso, apenas um blob foi adicionado ao contêiner, portanto, a operação de listagem retorna apenas um blob.

Adicione este código ao final do bloco de `try`:

```python
print("\nListing blobs...")

# List the blobs in the container
blob_list = container_client.list_blobs()
for blob in blob_list:
    print("\t" + blob.name)
```

### <a name="download-blobs"></a>Transferir blobs

Descarregue a bolha previamente criada, chamando o método [download_blob.](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) O código de exemplo adiciona um sufixo de "DOWNLOAD" ao nome do arquivo para que você possa ver os dois arquivos no sistema de arquivos local.

Adicione este código ao final do bloco de `try`:

```python
# Download the blob to a local file
# Add 'DOWNLOAD' before the .txt extension so you can see both files in Documents
download_file_path = os.path.join(local_path, str.replace(local_file_name ,'.txt', 'DOWNLOAD.txt'))
print("\nDownloading blob to \n\t" + download_file_path)

with open(download_file_path, "wb") as download_file:
    download_file.write(blob_client.download_blob().readall())
```

### <a name="delete-a-container"></a>Eliminar um contentor

O código que se segue limpa os recursos que a app criou removendo todo o recipiente utilizando o método [delete_container.](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) Você também pode excluir os arquivos locais, se desejar.

O aplicativo pausa a entrada do usuário chamando `input()` antes de excluir o blob, o contêiner e os arquivos locais. Esta é uma boa oportunidade para verificar se os recursos foram corretamente criados, antes de serem eliminados.

Adicione este código ao final do bloco de `try`:

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

Este aplicativo cria um arquivo de teste em sua pasta local e o carrega no armazenamento de BLOBs. Em seguida, o exemplo lista os BLOBs no contêiner e baixa o arquivo com um novo nome para que você possa comparar os arquivos novos e antigos.

Navegue até o diretório que contém o arquivo *blob-QuickStart-V12.py* e execute o comando `python` a seguir para executar o aplicativo.

```console
python blob-quickstart-v12.py
```

A saída do aplicativo é semelhante ao exemplo a seguir:

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

Antes de começar o processo de limpeza, verifique os dois arquivos na pasta *documentos* . Pode abri-los e constatar que são idênticos.

Depois de verificar os arquivos, pressione a tecla **Enter** para excluir os arquivos de teste e concluir a demonstração.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você aprendeu a carregar, baixar e listar BLOBs usando o Python.

Para ver os aplicativos de exemplo de armazenamento de BLOBs, continue em:

> [!div class="nextstepaction"]
> [Exemplos de Python do SDK do armazenamento de BLOBs do Azure V12](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Para saber mais, consulte o [SDK do Azure para Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/README.md).
* Para tutoriais, amostras, quickstarts e outra documentação, visite [o Azure para desenvolvedores de Python.](/azure/python/)
