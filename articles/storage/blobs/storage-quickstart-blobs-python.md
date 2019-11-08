---
title: 'Início rápido: biblioteca de armazenamento de BLOBs do Azure V12-Python'
description: Neste guia de início rápido, você aprende a usar a biblioteca de cliente de armazenamento de BLOBs do Azure versão 12 para Python para criar um contêiner e um blob no armazenamento de BLOB (objeto). Em seguida, vai aprender a transferir o blob para o computador local e a listar todos os blobs num contentor.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 397464a70164ef06642a991fb8470ddfa2922647
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73809930"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-python"></a>Início rápido: biblioteca de cliente do armazenamento de BLOBs do Azure V12 para Python

Introdução à biblioteca de cliente do armazenamento de BLOBs do Azure V12 para Python. O Armazenamento de Blobs do Azure é a solução de armazenamento de objetos da Microsoft para a cloud. Siga as etapas para instalar o pacote e experimentar o código de exemplo para tarefas básicas. O Armazenamento de blobs está otimizado para armazenar quantidades em grande escala de dados não estruturados.

> [!NOTE]
> Para começar a usar a versão anterior do SDK, consulte [início rápido: biblioteca de cliente do armazenamento de BLOBs do Azure para Python](storage-quickstart-blobs-python-legacy.md).

Use a biblioteca de cliente do armazenamento de BLOBs do Azure para:

* Criar um contentor
* Carregar um blob no armazenamento do Azure
* Listar todos os BLOBs em um contêiner
* Baixe o blob em seu computador local
* Eliminar um contentor

[Documentação de referência de API](/python/api/azure-storage-blob) | [código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob) | [pacote (índice de pacote do Python)](https://pypi.org/project/azure-storage-blob/) | [amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/)
* Conta de armazenamento do Azure – [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [Python](https://www.python.org/downloads/) para seu sistema operacional-2,7, 3,5 ou superior

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
1. Crie a estrutura para o programa, incluindo manipulação de exceção muito básica

    Este é o código:

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

### <a name="copy-your-credentials-from-the-azure-portal"></a>Copiar as credenciais do Portal do Azure

Quando o aplicativo de exemplo faz uma solicitação para o armazenamento do Azure, ele deve ser autorizado. Para autorizar uma solicitação, adicione as credenciais da conta de armazenamento ao aplicativo como uma cadeia de conexão. Veja as credenciais da conta de armazenamento através dos seguintes passos:

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).
2. Localize a sua conta de armazenamento.
3. Na secção **Definições** da descrição geral da conta de armazenamento, selecione **Chaves de acesso**. Aqui, pode ver as chaves de acesso da conta e a cadeia de ligação completa para cada chave.
4. Encontre o valor da **Cadeia de ligação** em **key1** e selecione o botão **Copiar** para copiar a cadeia de ligação. Irá adicionar o valor da cadeia de ligação para uma variável de ambiente no próximo passo.

    ![Captura de ecrã que mostra como copiar uma cadeia de ligação do portal do Azure](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento

Após ter copiado a cadeia de ligação, escreva-a numa nova variável de ambiente no computador local que está a executar a aplicação. Para definir a variável de ambiente, abra uma janela da consola e siga as instruções relevantes para o seu sistema operativo. Substitua `<yourconnectionstring>` pela cadeia de conexão real.

#### <a name="windows"></a>Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

Depois de adicionar a variável de ambiente no Windows, você deve iniciar uma nova instância da janela de comando.

#### <a name="linux"></a>Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>Reiniciar programas

Depois de adicionar a variável de ambiente, reinicie os programas em execução que precisarão ler a variável de ambiente. Por exemplo, reinicie o seu ambiente ou editor de desenvolvimento antes de continuar.

## <a name="object-model"></a>Modelo de objeto

O armazenamento de BLOBs do Azure é otimizado para armazenar grandes quantidades de dados não estruturados. Dados não estruturados são dados que não aderem a um modelo ou definição de dados específico, como texto ou dados binários. O armazenamento de BLOBs oferece três tipos de recursos:

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
# running the application called CONNECT_STR. If the environment variable is
# created after the application is launched in a console or with Visual Studio,
# the shell or application needs to be closed and reloaded to take the
# environment variable into account.
connect_str = os.getenv('CONNECT_STR')
```

### <a name="create-a-container"></a>Criar um contentor

Escolha um nome para o novo contêiner. O código a seguir acrescenta um valor UUID ao nome do contêiner para garantir que ele seja exclusivo.

> [!IMPORTANT]
> Os nomes dos contentores têm de estar em minúscula. Para obter mais informações sobre a atribuição de nomes de contentores e blobs, veja [Nomenclatura e Referenciação de Contentores, Blobs e Metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Crie uma instância da classe [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) chamando o método [from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) . Em seguida, chame o método [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) para realmente criar o contêiner em sua conta de armazenamento.

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
1. Obtém uma referência a um objeto [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) chamando o método [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) no [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) da seção [criar um contêiner](#create-a-container) .
1. Carrega o arquivo de texto local para o blob chamando o método [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) .

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

Liste os BLOBs no contêiner chamando o método [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) . Nesse caso, apenas um blob foi adicionado ao contêiner, portanto, a operação de listagem retorna apenas um blob.

Adicione este código ao final do bloco de `try`:

```python
print("\nListing blobs...")

# List the blobs in the container
blob_list = container_client.list_blobs()
for blob in blob_list:
    print("\t" + blob.name)
```

### <a name="download-blobs"></a>Transferir blobs

Baixe o blob criado anteriormente chamando o método [DOWNLOAD_BLOB](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) . O código de exemplo adiciona um sufixo de "DOWNLOAD" ao nome do arquivo para que você possa ver os dois arquivos no sistema de arquivos local.

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

O código a seguir limpa os recursos que o aplicativo criou removendo todo o contêiner usando o método [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) . Você também pode excluir os arquivos locais, se desejar.

O aplicativo pausa a entrada do usuário chamando `input()` antes de excluir o blob, o contêiner e os arquivos locais. Essa é uma boa chance de verificar se os recursos foram realmente criados corretamente, antes de serem excluídos.

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
* Para obter tutoriais, exemplos, inícios rápidos e outras documentações, visite [Azure para desenvolvedores de Python](/azure/python/).
