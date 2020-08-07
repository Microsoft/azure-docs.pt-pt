---
title: Desenvolver para Azure Files com Python Microsoft Docs
description: Saiba como desenvolver aplicações e serviços Python que utilizem ficheiros Azure para armazenar dados de ficheiros.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/14/2018
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-python
ms.openlocfilehash: 46512d61238c45936e7ebed4310993159cb43d34
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87845447"
---
# <a name="develop-for-azure-files-with-python"></a>Programar para os Ficheiros do Azure com Python
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

Este tutorial demonstrará os fundamentos da utilização do Python para desenvolver aplicações ou serviços que utilizem ficheiros Azure para armazenar dados de ficheiros. Neste tutorial, criaremos uma aplicação simples para consolas e mostraremos como realizar ações básicas com Python e Azure Files:

* Criar ações de ficheiros Azure
* Criar diretórios
* Enumerar ficheiros e diretórios numa partilha de ficheiros Azure
* Carregar, transferir e apagar um ficheiro

> [!Note]  
> Como os Ficheiros Azure podem ser acedidos através de SMB, é possível escrever aplicações simples que acedam à partilha de ficheiros Azure utilizando as classes e funções padrão python E/S. Este artigo descreverá como escrever aplicações que utilizem o Azure Storage Python SDK, que utiliza a [API de Ficheiros Azure](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) para falar com ficheiros Azure.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Descarregue e instale O Azure Storage SDK para Python

O [Azure Storage SDK for Python](https://github.com/azure/azure-storage-python) requer Python 2.7, 3.3, 3.4, 3.5 ou 3.6.
 
## <a name="install-via-pypi"></a>Instalar via PyPi

Para instalar através do Python Package Index (PyPI), escreva:

```bash
pip install azure-storage-file
```

> [!NOTE]
> Se estiver a atualizar a partir do Azure Storage SDK para a versão 0.36 ou mais cedo da Python, desinstale o SDK mais antigo utilizando `pip uninstall azure-storage` antes de instalar o pacote mais recente.

Para obter métodos de instalação alternativos, visite o [Azure Storage SDK para Python no GitHub.](https://github.com/Azure/azure-storage-python/)

## <a name="view-the-sample-application"></a>Ver a aplicação da amostra
Para visualizar e executar uma aplicação de amostra que mostre como usar Python com ficheiros Azure, consulte [Azure Storage: Getting Started with Azure Files in Python](https://github.com/Azure-Samples/storage-file-python-getting-started). 

Para executar a aplicação da amostra, certifique-se de que instalou as `azure-storage-file` embalagens e `azure-storage-common` as embalagens.

## <a name="set-up-your-application-to-use-azure-files"></a>Configurar a sua aplicação para utilizar ficheiros Azure
Adicione o seguinte perto do topo de qualquer ficheiro de origem Python no qual deseja aceder programáticamente ao Azure Storage.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Configurar uma ligação com os Ficheiros Azure 
O `FileService` objeto permite-lhe trabalhar com ações, diretórios e ficheiros. O código a seguir cria um `FileService` objeto utilizando o nome da conta de armazenamento e a chave da conta. Substitua `<myaccount>` e `<mykey>` pelo nome e a chave da conta.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure
No seguinte exemplo de código, pode utilizar um `FileService` objeto para criar a partilha se não existir.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Criar um diretório
Também pode organizar o armazenamento colocando ficheiros dentro de sub-directórios em vez de ter todos eles no diretório de raiz. O Azure Files permite-lhe criar o maior número de diretórios que a sua conta permitirá. O código abaixo criará um sub-diretório nomeado **sampledir** sob o diretório de raiz.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerar ficheiros e diretórios numa partilha de ficheiros Azure
Para listar os ficheiros e diretórios numa ação, utilize o método ** \_ de \_ listas e \_ ficheiros.** Este método devolve um gerador. O código seguinte produz o **nome** de cada ficheiro e diretório numa partilha para a consola.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Carregar um ficheiro 
A partilha de ficheiros Azure contém, no mínimo, um diretório de raiz onde os ficheiros podem residir. Nesta secção, você vai aprender a carregar um ficheiro do armazenamento local para o diretório de raiz de uma ação.

Para criar um ficheiro e carregar dados, utilize o `create_file_from_path` `create_file_from_stream` , ou `create_file_from_bytes` `create_file_from_text` métodos. São métodos de alto nível que realizam o chunking necessário quando o tamanho dos dados excede 64 MB.

`create_file_from_path`carrega o conteúdo de um ficheiro a partir do caminho especificado e `create_file_from_stream` envia o conteúdo de um ficheiro/stream já aberto. `create_file_from_bytes`carrega uma matriz de bytes e `create_file_from_text` carrega o valor de texto especificado usando a codificação especificada (predefinições para UTF-8).

O exemplo seguinte envia o conteúdo do ficheiro **sunset.png** para o ficheiro **myfile.**

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None,  # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

## <a name="download-a-file"></a>Transferir um ficheiro
Para descarregar dados de um ficheiro, `get_file_to_path` `get_file_to_stream` `get_file_to_bytes` utilize, ou `get_file_to_text` . São métodos de alto nível que realizam o chunking necessário quando o tamanho dos dados excede 64 MB.

O exemplo que se segue demonstra a `get_file_to_path` utilização para descarregar o conteúdo do ficheiro **myfile** e armazená-lo no ficheiro **out-sunset.png.**

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Eliminar um ficheiro
Finalmente, para apagar um ficheiro, ligue `delete_file` .

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot"></a>Criar instantâneo de partilha
Pode criar uma cópia pontual de toda a sua partilha de ficheiros.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Criar instantâneo de partilha com metadados**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Lista de ações e instantâneos 
Pode listar todas as fotos para uma determinada parte.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Procurar instantâneo de partilha
Pode navegar no conteúdo de cada instantâneo de partilha para recuperar ficheiros e diretórios a partir desse momento.

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Obtenha o arquivo do instantâneo de partilha
Pode descarregar um ficheiro a partir de uma imagem partilhada para o seu cenário de restauro.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Excluir uma única imagem de partilha  
Pode apagar uma única imagem de partilha.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Apague a partilha quando existem instantâneos de partilha
Uma parte que contenha instantâneos não pode ser eliminada a menos que todas as imagens sejam eliminadas primeiro.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu a manipular ficheiros Azure com Python, siga estes links para saber mais.

* [Centro para Programadores do Python](https://azure.microsoft.com/develop/python/)
* [API REST dos Serviços do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355)
* [Microsoft Azure Storage SDK para Python](https://github.com/Azure/azure-storage-python)
