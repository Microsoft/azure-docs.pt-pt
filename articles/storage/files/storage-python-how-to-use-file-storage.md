---
title: Programar para os ficheiros do Azure com Python | Documentos da Microsoft
description: Saiba como desenvolver aplicações Python e serviços que utilizam ficheiros do Azure para armazenar dados de ficheiros.
services: storage
author: roygara
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 12/14/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ecb3ef82196c3b6febd44850b47f467ba37facc2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64701604"
---
# <a name="develop-for-azure-files-with-python"></a>Programar para os ficheiros do Azure com Python
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

Este tutorial demonstra as noções básicas da utilização do Python para programar aplicações ou serviços que utilizam ficheiros do Azure para armazenar dados de ficheiros. Neste tutorial, iremos criar uma aplicação de consola simples e Mostrar como realizar ações básicas com Python e ficheiros do Azure:

* Criar partilhas de ficheiros do Azure
* Criar diretórios
* Enumerar arquivos e diretórios numa partilha de ficheiros do Azure
* Carregar, transferir e eliminar um ficheiro

> [!Note]  
> Como ficheiros do Azure pode ser acedidos através de SMB, é possível escrever aplicações simples que acedam à partilha de ficheiros do Azure com as classes de e/s de Python padrão e as funções. Este artigo descreve como escrever aplicações que utilizam o SDK de Python de armazenamento do Azure, que utiliza a [API de REST de ficheiros do Azure](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) para comunicar com o serviço ficheiros do Azure.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Transfira e instale o SDK de armazenamento do Azure para Python

O [SDK de armazenamento do Azure para Python](https://github.com/azure/azure-storage-python) requer o Python 2.7, 3.3, 3.4, 3.5 ou 3.6.
 
## <a name="install-via-pypi"></a>Instalar através de PyPi

Para instalar via o índice de pacote do Python (PyPI), escreva:

```bash
pip install azure-storage-file
```

> [!NOTE]
> Se estiver a atualizar o SDK de armazenamento do Azure para Python versão 0,36 ou anterior, desinstale o SDK mais antigo usando `pip uninstall azure-storage` antes de instalar o pacote mais recente.

Para métodos de instalação alternativa, visite o [SDK de armazenamento do Azure para Python no GitHub](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Ver a aplicação de exemplo
f para ver e executar uma aplicação de exemplo que mostra como utilizar Python com o serviço ficheiros do Azure, consulte [armazenamento do Azure: Introdução ao ficheiros do Azure em Python](https://github.com/Azure-Samples/storage-file-python-getting-started). 

Para executar o aplicativo de exemplo, certifique-se de que instalou o ambos os `azure-storage-file` e `azure-storage-common` pacotes.

## <a name="set-up-your-application-to-use-azure-files"></a>Configurar a sua aplicação para utilizar ficheiros do Azure
Adicione o seguinte perto da parte superior de qualquer ficheiro de origem do Python no qual pretende aceder programaticamente ao armazenamento do Azure.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Configurar uma ligação para ficheiros do Azure 
O `FileService` objeto permite-lhe trabalhar com partilhas, diretórios e ficheiros. O código seguinte cria um `FileService` objeto usando a chave de conta e o nome da conta de armazenamento. Substitua `<myaccount>` e `<mykey>` pelo nome e a chave da conta.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure
No exemplo de código a seguir, pode usar um `FileService` objeto para criar a partilha se não existir.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Criar um diretório
Também pode organizar o armazenamento ao colocar os ficheiros dentro dos subdiretórios, em vez de ter todas elas no diretório de raiz. Os ficheiros do Azure permite-lhe criar os diretórios como permitirá que a sua conta. O código abaixo, irá criar um subdiretório com o nome **sampledir** sob o diretório de raiz.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerar arquivos e diretórios numa partilha de ficheiros do Azure
Para listar os ficheiros e diretórios num compartilhamento, utilize o **lista\_diretórios\_e\_ficheiros** método. Este método devolve um gerador. O seguinte código saídas a **nome** de cada ficheiro e de diretório numa partilha no Console.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Carregar um ficheiro 
Partilha de ficheiros do Azure contém na pior das hipóteses, um diretório de raiz em que podem residir. Nesta secção, irá aprender como carregar um ficheiro do armazenamento local para o diretório de raiz de uma partilha.

Para criar um ficheiro e carregar dados, utilize o `create_file_from_path`, `create_file_from_stream`, `create_file_from_bytes` ou `create_file_from_text` métodos. Eles são métodos de alto nível que executam a segmentação necessárias quando o tamanho dos dados exceder 64 MB.

`create_file_from_path` carrega o conteúdo de um ficheiro a partir do caminho especificado, e `create_file_from_stream` carrega o conteúdo do ficheiro/fluxo já aberto. `create_file_from_bytes` carrega uma matriz de bytes, e `create_file_from_text` carrega o valor de texto especificado usando a codificação especificada (a predefinição é UTF-8).

O exemplo seguinte carrega o conteúdo do **sunset.png** de ficheiros para o **myfile** ficheiro.

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None, # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

## <a name="download-a-file"></a>Transferir um ficheiro
Para transferir dados de um ficheiro, utilize `get_file_to_path`, `get_file_to_stream`, `get_file_to_bytes`, ou `get_file_to_text`. Eles são métodos de alto nível que executam a segmentação necessárias quando o tamanho dos dados exceder 64 MB.

O exemplo seguinte demonstra como utilizar `get_file_to_path` para transferir o conteúdo do **myfile** de ficheiros e armazená-lo para o **fora sunset.png** ficheiro.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Eliminar um ficheiro
Por fim, para eliminar um ficheiro, chame `delete_file`.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot"></a>Criar instantâneo de partilha
Pode criar um ponto na cópia de tempo de seu compartilhamento de arquivo inteiro.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Criar instantâneo de partilha com metadados**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Listar partilhas e instantâneos 
Pode listar todos os instantâneos para uma partilha específico.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Procurar o instantâneo de partilha
Pode procurar conteúdo de cada instantâneo de partilha para obter ficheiros e diretórios a partir desse ponto no tempo.

```python
directories_and_files = list(file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Obter o ficheiro de instantâneo de partilha
Pode transferir um ficheiro a partir de um instantâneo de partilha para o seu cenário de restauro.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Eliminar um instantâneo de partilha única  
Pode eliminar um instantâneo de partilha única.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Eliminar partilha quando existem instantâneos de partilha
Não é possível eliminar uma partilha que contém instantâneos, a menos que todos os instantâneos são eliminados primeiro.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Passos Seguintes
Agora que aprendeu como manipular ficheiros do Azure com Python, siga estas ligações para saber mais.

* [Centro para Programadores do Python](https://azure.microsoft.com/develop/python/)
* [API REST dos Serviços do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355)
* [Armazenamento do Microsoft Azure SDK para Python](https://github.com/Azure/azure-storage-python)
