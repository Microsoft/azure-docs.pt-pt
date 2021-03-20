---
title: 'Quickstart: Azure Blob Storage library v12 - Python'
description: Neste arranque rápido, aprende-se a usar a versão 12 da biblioteca do cliente Azure Blob Storage para a Python para criar um recipiente e uma bolha no armazenamento blob (objeto). Em seguida, vai aprender a transferir o blob para o computador local e a listar todos os blobs num contentor.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/28/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: e315f0f4f7bfff03a659de430e6fe182037f1b8a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99096411"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Quickstart: Gerir bolhas com Python v12 SDK

Neste arranque rápido, aprende-se a gerir as bolhas usando python. As bolhas são objetos que podem conter grandes quantidades de texto ou dados binários, incluindo imagens, documentos, streaming de meios e dados de arquivo. Você vai carregar, baixar e listar bolhas, e você vai criar e apagar recipientes.

Mais recursos:

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

1. De lado o *diretório blob-quickstart-v12,* crie outro diretório chamado *dados*. Este diretório é onde os ficheiros de dados blob serão criados e armazenados.

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

    :::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/app_framework.py":::

1. Guarde o novo ficheiro à medida *que blob-quickstart-v12.py* no *diretório blob-quickstart-v12.*

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Modelo de objeto

O Azure Blob Storage está otimizado para armazenar quantidades massivas de dados não estruturados. Dados não estruturados são dados que não aderem a um determinado modelo ou definição de dados, como texto ou dados binários. O armazenamento blob oferece três tipos de recursos:

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

Estes excertos de código de exemplo mostram-lhe como fazer as seguintes tarefas com a biblioteca de clientes Azure Blob Storage para Python:

* [Obter a cadeia de ligação](#get-the-connection-string)
* [Criar um contentor](#create-a-container)
* [Carregar bolhas para um recipiente](#upload-blobs-to-a-container)
* [Listar os blobs num contentor](#list-the-blobs-in-a-container)
* [Transferir blobs](#download-blobs)
* [Eliminar um contentor](#delete-a-container)

### <a name="get-the-connection-string"></a>Obter a cadeia de ligação

O código abaixo recupera a cadeia de ligação da conta de armazenamento a partir da variável ambiente criada na secção [de cadeia de ligação de armazenamento.](#configure-your-storage-connection-string)

Adicione este código dentro do `try` bloco:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Criar um contentor

Decida o nome do novo recipiente. O código abaixo anexa um valor UUID ao nome do recipiente para garantir que é único.

> [!IMPORTANT]
> Os nomes dos contentores têm de estar em minúscula. Para obter mais informações sobre a atribuição de nomes de contentores e blobs, veja [Nomenclatura e Referenciação de Contentores, Blobs e Metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Crie uma instância da classe [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) chamando o método [from_connection_string.](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) Em seguida, ligue para o método [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) para realmente criar o recipiente na sua conta de armazenamento.

Adicione este código ao fim do `try` bloco:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Carregar bolhas para um recipiente

O seguinte corte de código:

1. Cria um diretório local para guardar ficheiros de dados.
1. Cria um ficheiro de texto no diretório local.
1. Obtém uma referência a um objeto [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) chamando o método [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) no [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) a partir da secção [Criar um recipiente.](#create-a-container)
1. Envia o ficheiro de texto local para a bolha, chamando o método [upload_blob.](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-)

Adicione este código ao fim do `try` bloco:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_UploadBlobs":::

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Enuncie as bolhas no recipiente chamando o método [list_blobs.](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) Neste caso, apenas uma bolha foi adicionada ao recipiente, pelo que a operação de listagem devolve apenas uma bolha.

Adicione este código ao fim do `try` bloco:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Transferir blobs

Descarregue a bolha anteriormente criada chamando o método [download_blob.](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) O código de exemplo adiciona um sufixo de "DOWNLOAD" ao nome do ficheiro para que possa ver ambos os ficheiros no sistema de ficheiros local.

Adicione este código ao fim do `try` bloco:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_DownloadBlobs":::

### <a name="delete-a-container"></a>Eliminar um contentor

O código seguinte limpa os recursos que a app criou removendo todo o recipiente utilizando o método [delete_container.](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) Também pode apagar os ficheiros locais, se quiser.

A aplicação faz uma pausa para a entrada do utilizador, ligando `input()` antes de eliminar a bolha, o recipiente e os ficheiros locais. Verifique se os recursos foram criados corretamente, antes de serem eliminados.

Adicione este código ao fim do `try` bloco:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CleanUp":::

## <a name="run-the-code"></a>Executar o código

Esta aplicação cria um ficheiro de teste na sua pasta local e envia-o para o Azure Blob Storage. O exemplo enumera então as bolhas no recipiente e descarrega o ficheiro com um novo nome. Pode comparar os ficheiros antigos e novos.

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

Antes de iniciar o processo de limpeza, verifique a sua pasta *de dados* para os dois ficheiros. Podes abri-los e observar que são idênticos.

Depois de verificar os ficheiros, prima a tecla **'Entrar'** para eliminar os ficheiros de teste e terminar a demonstração.

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, aprendeu a carregar, descarregar e listar bolhas usando Python.

Para ver as aplicações de amostra de armazenamento Blob, continue a:

> [!div class="nextstepaction"]
> [Amostras de Azure Blob Storage SDK v12 Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Para saber mais, consulte as [bibliotecas de clientes do Azure Storage para Python.](/azure/developer/python/sdk/storage/overview)
* Para tutoriais, amostras, quickstarts e outra documentação, visite [Azure for Python Developers](/azure/python/).
