---
title: Desenvolva para arquivos Azure com | Python Microsoft Docs
description: Saiba como desenvolver aplicações e serviços Python que utilizem ficheiros Azure para armazenar dados de ficheiros.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/08/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-python
ms.openlocfilehash: d45ce3a782d7ee145f769283b82e34647c78f26e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104799875"
---
# <a name="develop-for-azure-files-with-python"></a>Programar para os Ficheiros do Azure com Python

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Saiba o básico da utilização do Python para desenvolver apps ou serviços que utilizem ficheiros Azure para armazenar dados de ficheiros. Crie uma aplicação simples para consolas e aprenda a realizar ações básicas com Python e Azure Files:

- Criar ações de ficheiros Azure
- Criar diretórios
- Enumerar ficheiros e diretórios numa partilha de ficheiros Azure
- Carregar, transferir e apagar um ficheiro
- Criar backups de partilha de ficheiros utilizando instantâneos

> [!NOTE]
> Como os Ficheiros Azure podem ser acedidos através de SMB, é possível escrever aplicações simples que acedam à partilha de ficheiros Azure utilizando as classes e funções padrão python E/S. Este artigo descreverá como escrever apps que utilizem o Azure Files Storage Python SDK, que utiliza a [API de Ficheiros Azure](/rest/api/storageservices/file-service-rest-api) para falar com ficheiros Azure.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Descarregue e instale O Azure Storage SDK para Python

> [!NOTE]
> Se estiver a atualizar a partir do Azure Storage SDK para a versão 0.36 ou mais cedo da Python, desinstale o SDK mais antigo utilizando `pip uninstall azure-storage` antes de instalar o pacote mais recente.

# <a name="python-v12"></a>[Python v12](#tab/python)

A [biblioteca de clientes Azure File Storage v12.x para Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share) requer Python 2.7 ou 3.6+.

# <a name="python-v2"></a>[Python v2](#tab/python2)

O [Azure Storage SDK for Python](https://github.com/azure/azure-storage-python) requer Python 2.7 ou 3.6+.

---

## <a name="install-via-pypi"></a>Instalar via PyPI

Para instalar através do Python Package Index (PyPI), escreva:

# <a name="python-v12"></a>[Python v12](#tab/python)

```console
pip install azure-storage-file-share
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```console
pip install azure-storage-file
```

### <a name="view-the-sample-application"></a>Ver a aplicação da amostra

Para visualizar e executar uma aplicação de amostra que mostre como usar Python com ficheiros Azure, consulte [Azure Storage: Getting Started with Azure Files in Python](https://github.com/Azure-Samples/storage-file-python-getting-started).

Para executar a aplicação da amostra, certifique-se de que instalou os `azure-storage-file` pacotes e `azure-storage-common` os pacotes.

---

## <a name="set-up-your-application-to-use-azure-files"></a>Configurar a sua aplicação para utilizar ficheiros Azure

Adicione o seguinte perto do topo de um ficheiro de origem Python para utilizar os cortes de código neste artigo.

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_Imports":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
from azure.storage.file import FileService
```

---

## <a name="set-up-a-connection-to-azure-files"></a>Configurar uma ligação com os Ficheiros Azure

# <a name="python-v12"></a>[Python v12](#tab/python)

[O ShareServiceClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareserviceclient) permite-lhe trabalhar com ações, diretórios e ficheiros. O seguinte código cria um `ShareServiceClient` objeto utilizando o fio de ligação da conta de armazenamento.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateShareServiceClient":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

O objeto [FileService](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) permite-lhe trabalhar com ações, diretórios e ficheiros. O código a seguir cria um `FileService` objeto utilizando o nome da conta de armazenamento e a chave da conta. Substitua `<myaccount>` e `<mykey>` pelo nome e a chave da conta.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

---

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure

# <a name="python-v12"></a>[Python v12](#tab/python)

O exemplo de código a seguir utiliza um objeto [ShareClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient) para criar a partilha se não existir.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateFileShare":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

O exemplo de código a seguir utiliza um objeto [FileService](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) para criar a partilha se esta não existir.

```python
file_service.create_share('myshare')
```

---

## <a name="create-a-directory"></a>Criar um diretório

Pode organizar o armazenamento colocando ficheiros dentro de subdireções em vez de ter todos eles no diretório de raiz.

# <a name="python-v12"></a>[Python v12](#tab/python)

O método a seguir cria um diretório na raiz da partilha de ficheiros especificada utilizando um objeto [ShareDirectoryClient.](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharedirectoryclient)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateDirectory":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

O código abaixo criará um subdiretório nomeado *sampledir* sob o diretório de raiz.

```python
file_service.create_directory('myshare', 'sampledir')
```

---

## <a name="upload-a-file"></a>Carregar um ficheiro

Nesta secção, você vai aprender a enviar um ficheiro do armazenamento local para o Azure File Storage.

# <a name="python-v12"></a>[Python v12](#tab/python)

O método seguinte envia o conteúdo do ficheiro especificado para o diretório especificado na partilha de ficheiros Azure especificada.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_UploadFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Uma partilha de ficheiros Azure contém, no mínimo, um diretório de raiz onde os ficheiros podem residir. Para criar um ficheiro e carregar dados, utilize os [create_file_from_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-path-share-name--directory-name--file-name--local-file-path--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object---timeout-none-), [create_file_from_stream,](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-stream-share-name--directory-name--file-name--stream--count--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--) [create_file_from_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-bytes-share-name--directory-name--file-name--file--index-0--count-none--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--)ou [métodos create_file_from_text.](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-text-share-name--directory-name--file-name--text--encoding--utf-8---content-settings-none--metadata-none--validate-content-false--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--) São métodos de alto nível que realizam o chunking necessário quando o tamanho dos dados excede 64 MB.

`create_file_from_path` carrega o conteúdo de um ficheiro a partir do caminho especificado e `create_file_from_stream` envia o conteúdo de um ficheiro/stream já aberto. `create_file_from_bytes` carrega uma matriz de bytes e `create_file_from_text` carrega o valor de texto especificado usando a codificação especificada (predefinições para UTF-8).

O exemplo seguinte envia o conteúdo do ficheiro *sunset.png* para o ficheiro **myfile.**

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None,  # We want to create this file in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerar ficheiros e diretórios numa partilha de ficheiros Azure

# <a name="python-v12"></a>[Python v12](#tab/python)

Para listar os ficheiros e diretórios numa subdiretória, utilize o método [list_directories_and_files.](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#list-directories-and-files-directory-name-none--name-starts-with-none--marker-none----kwargs-) Este método devolve uma iterável de paging automático. O código seguinte produz o **nome** de cada ficheiro e subdireorisor no diretório especificado para a consola.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListFilesAndDirs":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Para listar os ficheiros e diretórios numa ação, utilize o método [list_directories_and_files.](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#list-directories-and-files-share-name--directory-name-none--num-results-none--marker-none--timeout-none--prefix-none--snapshot-none-) Este método devolve um gerador. O código seguinte produz o **nome** de cada ficheiro e diretório numa partilha para a consola.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

---

## <a name="download-a-file"></a>Transferir um ficheiro

# <a name="python-v12"></a>[Python v12](#tab/python)

Para descarregar dados de um ficheiro, utilize [download_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#download-file-offset-none--length-none----kwargs-).

O exemplo a seguir demonstra a utilização `download_file` para obter o conteúdo do ficheiro especificado e armazená-lo localmente com download-premeded para o nome de ficheiro. 

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Para descarregar dados de um ficheiro, utilize [get_file_to_path,](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-path-share-name--directory-name--file-name--file-path--open-mode--wb---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-) [get_file_to_stream,](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-stream-share-name--directory-name--file-name--stream--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-) [get_file_to_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-bytes-share-name--directory-name--file-name--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-)ou [get_file_to_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-text-share-name--directory-name--file-name--encoding--utf-8---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-). São métodos de alto nível que realizam o chunking necessário quando o tamanho dos dados excede 64 MB.

O exemplo que se segue demonstra a `get_file_to_path` utilização para descarregar o conteúdo do ficheiro **myfile** e armazená-lo no ficheiro *out-sunset.png.*

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

---

## <a name="create-a-share-snapshot"></a>Criar um instantâneo de partilha

Pode criar uma cópia pontual de toda a sua partilha de ficheiros.

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateSnapshot":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Criar instantâneo de partilha com metadados**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

---

## <a name="list-shares-and-snapshots"></a>Lista de ações e instantâneos

Pode listar todas as fotos para uma determinada parte.

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListSharesAndSnapshots":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

---

## <a name="browse-share-snapshot"></a>Procurar instantâneo de partilha

Pode navegar em cada instantâneo de partilha para recuperar ficheiros e diretórios a partir desse momento.

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_BrowseSnapshotDir":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

---

## <a name="get-file-from-share-snapshot"></a>Obtenha o arquivo do instantâneo de partilha

Pode descarregar um ficheiro a partir de uma fotografia de partilha. Isto permite-lhe restaurar uma versão anterior de um ficheiro.

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadSnapshotFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

---

## <a name="delete-a-single-share-snapshot"></a>Excluir uma única imagem de partilha
Pode apagar uma única imagem de partilha.

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteSnapshot":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

---

## <a name="delete-a-file"></a>Eliminar um ficheiro

# <a name="python-v12"></a>[Python v12](#tab/python)

Para eliminar um ficheiro, ligue [para delete_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#delete-file---kwargs-).

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Para eliminar um ficheiro, ligue [para delete_file](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#delete-file-share-name--directory-name--file-name--timeout-none-).

```python
file_service.delete_file('myshare', None, 'myfile')
```

---

## <a name="delete-share-when-share-snapshots-exist"></a>Apague a partilha quando existem instantâneos de partilha

# <a name="python-v12"></a>[Python v12](#tab/python)

Para eliminar uma partilha que contenha instantâneos, ligue [para delete_share](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#delete-share-delete-snapshots-false----kwargs-) com `delete_snapshots=True` .

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteShare":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Uma parte que contenha instantâneos não pode ser eliminada a menos que todas as imagens sejam eliminadas primeiro.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

---

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a manipular ficheiros Azure com Python, siga estes links para saber mais.

- [Centro para Programadores do Python](/azure/developer/python/)
- [API REST dos Serviços do Armazenamento do Azure](/rest/api/azure/)
- [Microsoft Azure Storage SDK para Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)
