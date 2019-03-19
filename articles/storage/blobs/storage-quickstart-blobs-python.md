---
title: Início Rápido do Azure – Criar um blob no armazenamento de objetos com Python | Microsoft Docs
description: Neste início rápido, crie uma conta de armazenamento e um contentor no armazenamento de objetos (Blobs). Em seguida, utilize a biblioteca de clientes de armazenamento para Python, para carregar um blob para o Armazenamento do Microsoft Azure, transferir um blob e listar os blobs num contentor.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 12/14/2018
ms.author: tamram
ms.openlocfilehash: 95b960245e00a353bb165e1f1d38db56bce62a8f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57996720"
---
# <a name="quickstart-upload-download-and-list-blobs-with-python"></a>Início rápido: Carregar, transferir e listar os blobs com o Python

Neste início rápido, vai aprender a utilizar o Python para carregar, transferir e listar blobs de blocos num contentor no armazenamento de Blobs do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Certifique-se de que tem os seguintes pré-requisitos adicionais instalados:

* [Python](https://www.python.org/downloads/)
* [Armazenamento do Azure SDK para Python](https://github.com/Azure/azure-sdk-for-python)

## <a name="download-the-sample-application"></a>Transferir a aplicação de exemplo
A [aplicação de exemplo](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) utilizada neste guia de introdução é uma aplicação Python básica.  

Utilize o [git](https://git-scm.com/) para transferir uma cópia da aplicação para o seu ambiente de desenvolvimento. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Este comando clona o repositório *Azure-Samples/storage-blobs-python-quickstart* para a sua pasta git local. Para executar o programa de Python, abra o ficheiro *example.py* na raiz do repositório.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de ligação de armazenamento
Na aplicação, indique o nome da conta de armazenamento e a chave de conta para criar um objeto `BlockBlobService`. Abra o ficheiro *example.py* no Explorador de Soluções no seu IDE. Substitua os valores de `accountname` e `accountkey` pelo nome e a chave da conta. 

```python 
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
```

## <a name="run-the-sample"></a>Executar o exemplo
Este exemplo cria um ficheiro de teste na pasta *Documents*. O programa de exemplo carrega o ficheiro de teste para o armazenamento de Blobs, lista os blobs no contentor e transfere o ficheiro com um novo nome. 

Primeiro, instale as dependências ao executar `pip install`:

    pip install azure-storage-blob

Em seguida, execute o exemplo. Verá mensagens semelhantes à saída seguinte:
  
```
Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

List blobs in the container
         Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

Downloading blob to C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
```
Antes de continuar, procure os dois ficheiros na pasta *Documents*. Pode abri-los e ver que são idênticos.

Também pode utilizar uma ferramenta como o [Explorador de Armazenamento do Azure](https://storageexplorer.com) para ver os ficheiros no armazenamento de Blobs. O Explorador de Armazenamento do Azure é uma ferramenta multiplataformas gratuita que lhe permite aceder às informações da sua conta de armazenamento. 

Depois de verificar os ficheiros, prima qualquer tecla para concluir a demonstração e eliminar os ficheiros de teste. Agora que sabe o que o exemplo faz, abra o ficheiro *example.py* para ver o código. 

## <a name="understand-the-sample-code"></a>Compreender o código de exemplo

Vamos analisar o código de exemplo, para compreender como funciona.

### <a name="get-references-to-the-storage-objects"></a>Obter referências para os objetos de armazenamento
Em primeiro lugar, cria as referências para os objetos que são utilizados para aceder ao armazenamento de Blobs e geri-lo. Estes objetos dependem uns dos outros e cada um é utilizado pelo que vem a seguir na lista.

* Instancie o objeto **BlockBlobService**, que aponta para o serviço Blob na sua conta de armazenamento. 

* Instancie o objeto **CloudBlobContainer**, que representa o contentor a que está aceder. Os contentores servem para organizar os blobs, da mesma forma como utiliza pastas para organizar os ficheiros.

Quando tiver o contentor Cloud Blob, instancie o objeto **CloudBlockBlob** que aponta para o blob específico em que está interessado. Depois, pode carregar, transferir e copiar o blob, conforme o que precisar.

> [!IMPORTANT]
> Os nomes dos contentores têm de estar em minúscula. Para obter mais informações sobre os nomes dos contentores e dos blobs, veja [Naming and referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Nomenclatura e referência a Contentores, Blobs e Metadados).

Nesta secção, vai instanciar os objetos, criar um contentor novo e, em seguida, definir as permissões no contentor, para que os blobs sejam públicos. O contentor é designado **quickstartblobs**. 

```python 
# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='accountname', account_key='accountkey') 
 
# Create a container called 'quickstartblobs'.
container_name ='quickstartblobs'
block_blob_service.create_container(container_name) 

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(container_name, public_access=PublicAccess.Container)
```
### <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contentor

O armazenamento de blobs suporta blobs de blocos, blobs de acréscimo e blobs de páginas. Os blobs de blocos são os mais utilizados e vamos utilizá-los neste início rápido.  

Para carregar um ficheiro para um blob, obtenha o caminho completo do ficheiro ao associar o nome de diretório ao nome de ficheiro no disco local. Em seguida, pode utilizar o método `create\_blob\_from\_path` para carregar o ficheiro para o caminho especificado. 

O código de exemplo cria um ficheiro local que vai ser utilizado para o carregamento e a transferência, armazenando o ficheiro a carregar como `file\_path\_to\_file` e o nome do blob como `local\_file\_name`. O exemplo seguinte carrega o ficheiro para o seu contentor com o nome **quickstartblobs**.

```python
# Create a file in Documents to test the upload and download.
local_path=os.path.expanduser("~\Documents")
local_file_name ="QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file =os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file,  'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name
block_blob_service.create_blob_from_path(container_name, local_file_name, full_path_to_file)
```

Pode utilizar diversos métodos de carregamento com o armazenamento de Blobs. Por exemplo, se tiver um fluxo de memória, pode utilizar o método `create\_blob\_from\_stream` em vez de `create\_blob\_from\_path`. 

Os blobs de blocos podem ter no máximo 4,7 TB e podem ser qualquer coisa, desde folhas de cálculo do Excel a ficheiros grandes de vídeo. Os blobs de páginas utilizam-se principalmente para os ficheiros VHD utilizados que suportam VMs de IaaS. Os blobs de acréscimo servem para registo, como quando quer escrever num ficheiro e continuar a adicionar mais informações. A maioria dos objetos guardados no armazenamento de Blobs são blobs de blocos.

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs num contentor

Obtenha uma lista de ficheiros no contentor com o método `list_blobs`. Este método devolve um gerador. O código seguinte obtém a lista de blobs &mdash; e percorre-os &mdash;, mostrando os nomes dos blobs encontrados num contentor.  

```python
# List the blobs in the container
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Transferir os blobs

Transfira os blobs para o seu disco local com o método `the get\_blob\_to\_path`. O código seguinte transfere o blob atualizado numa secção anterior. *_DOWNLOADED* é adicionado como sufixo ao nome do blob, para que possa ver ambos os ficheiros no disco local. 

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, string.replace(local_file_name ,'.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Limpar recursos
Se já não precisar dos blobs carregados neste início rápido, pode eliminar o contentor inteiro com o método `delete\_container`. Se pretender eliminar ficheiros individuais, utilize o método `delete\_blob`.

```python
# Clean up resources. This includes the container and the temp files
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```
## <a name="resources-for-developing-python-applications-with-blobs"></a>Recursos para desenvolver aplicações Python com blobs

Para obter mais informações sobre o desenvolvimento em Python com o armazenamento de Blobs, veja estes recursos adicionais:

### <a name="binaries-and-source-code"></a>Binários e código fonte

- Veja, transfira e instale o [código fonte da biblioteca cliente Python](https://github.com/Azure/azure-storage-python) do Armazenamento do Azure no GitHub.

### <a name="client-library-reference-and-samples"></a>Referência e exemplos da biblioteca de cliente

- Para obter mais informações sobre a biblioteca de cliente Python, veja a [Referência da API de Python](https://docs.microsoft.com/python/api/overview/azure/storage).
- Explore os [exemplos de armazenamento de blobs](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob) escritos com a biblioteca de cliente Python.

## <a name="next-steps"></a>Passos Seguintes
 
Neste início rápido, aprendeu a transferir ficheiros entre um disco local e o armazenamento de Blobs do Azure com Python. Para saber mais sobre a utilização do armazenamento de Blobs, avance para os procedimentos do armazenamento de blobs.

> [!div class="nextstepaction"]
> [Procedimentos de Operações de Armazenamento de Blobs](./storage-python-how-to-use-blob-storage.md)
 
Para obter mais informações sobre o Explorador de armazenamento e os Blobs, veja [Manage Azure Blob storage resources with Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Gerir recursos do armazenamento de Blobs do Azure com o Explorador de Armazenamento).
