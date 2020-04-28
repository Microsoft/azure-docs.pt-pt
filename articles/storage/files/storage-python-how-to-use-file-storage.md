---
title: Desenvolver ficheiros Azure com Python Microsoft Docs
description: Saiba como desenvolver aplicações e serviços Python que utilizam ficheiros Azure para armazenar dados de ficheiros.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 139e3009722761172b7bbd57805a7f5b07e55fc0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "68699383"
---
# <a name="develop-for-azure-files-with-python"></a>Programar para os Ficheiros do Azure com Python
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

Este tutorial demonstrará o básico da utilização da Python para desenvolver aplicações ou serviços que utilizam ficheiros Azure para armazenar dados de ficheiros. Neste tutorial, criaremos uma aplicação de consola simples e mostraremos como realizar ações básicas com Python e Azure Files:

* Criar ações de ficheiro sinuosa
* Criar diretórios
* Enumerar ficheiros e diretórios numa partilha de ficheiros Azure
* Faça upload, download e elimine um ficheiro

> [!Note]  
> Uma vez que os Ficheiros Azure podem ser acedidos através de SMB, é possível escrever aplicações simples que acedam à partilha de ficheiros Azure utilizando as classes e funções padrão de Python I/O. Este artigo descreverá como escrever aplicações que utilizam o Azure Storage Python SDK, que utiliza a API REST De [Arquivos Azure](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) para falar com o Azure Files.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Descarregue e instale O Armazenamento Azure SDK para Python

O [SDK](https://github.com/azure/azure-storage-python) de Armazenamento Azure para Python requer Python 2.7, 3.3, 3.4, 3.5 ou 3.6.
 
## <a name="install-via-pypi"></a>Instalar via PyPi

Para instalar através do Índice de Pacotepython (PyPI), escreva:

```bash
pip install azure-storage-file
```

> [!NOTE]
> Se estiver a atualizar a partir do Azure Storage SDK para a versão Python 0.36 ou mais cedo, desinstale o SDK mais antigo utilizando `pip uninstall azure-storage` antes de instalar o pacote mais recente.

Para métodos de instalação alternativos, visite o [Azure Storage SDK para Python no GitHub](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Ver a aplicação da amostra
f Para visualizar e executar uma aplicação de amostra que mostre como usar python com ficheiros Azure, consulte [O Armazenamento Azure: Começar com Ficheiros Azure em Python](https://github.com/Azure-Samples/storage-file-python-getting-started). 

Para executar a aplicação da amostra, `azure-storage-file` certifique-se de que instalou as embalagens e `azure-storage-common` as embalagens.

## <a name="set-up-your-application-to-use-azure-files"></a>Configurar a sua aplicação para utilizar ficheiros Azure
Adicione o seguinte perto do topo de qualquer ficheiro de origem Python no qual deseje aceder programáticamente ao Armazenamento Azure.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Configurar uma ligação aos Ficheiros Azure 
O `FileService` objeto permite-lhe trabalhar com ações, diretórios e ficheiros. O seguinte código `FileService` cria um objeto utilizando o nome da conta de armazenamento e a chave de conta. Substitua `<myaccount>` e `<mykey>` pelo nome e a chave da conta.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure
No seguinte exemplo de código, `FileService` pode utilizar um objeto para criar a parte se não existir.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Criar um diretório
Também pode organizar o armazenamento colocando ficheiros dentro de subdiretórios em vez de ter todos eles no diretório de raiz. O Azure Files permite-lhe criar o maior número de diretórios que a sua conta permitirá. O código abaixo criará um **sampledir** de sub-directório chamado sampledir sob o diretório raiz.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerar ficheiros e diretórios numa partilha de ficheiros Azure
Para listar os ficheiros e diretórios numa ação, utilize os **diretórios\_e\_\_ficheiros** da lista. Este método devolve um gerador. O código seguinte produz o **nome** de cada ficheiro e diretório numa partilha para a consola.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Carregar um ficheiro 
A partilha de ficheiros Azure contém, no mínimo, um diretório de raiz onde os ficheiros podem residir. Nesta secção, você aprenderá a enviar um ficheiro do armazenamento local para o diretório raiz de uma ação.

Para criar um ficheiro e `create_file_from_path`carregar `create_file_from_stream` `create_file_from_bytes` dados, utilize os métodos ou `create_file_from_text` métodos. São métodos de alto nível que executam as fatias necessárias quando o tamanho dos dados excede 64 MB.

`create_file_from_path`envia o conteúdo de um ficheiro a `create_file_from_stream` partir do caminho especificado e envia o conteúdo a partir de um ficheiro/fluxo já aberto. `create_file_from_bytes`carrega uma série de bytes e `create_file_from_text` carrega o valor de texto especificado utilizando a codificação especificada (predefinições para UTF-8).

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
Para descarregar dados de `get_file_to_path`um `get_file_to_stream` `get_file_to_bytes`ficheiro, `get_file_to_text`utilize, ou . São métodos de alto nível que executam as fatias necessárias quando o tamanho dos dados excede 64 MB.

O exemplo que `get_file_to_path` se segue demonstra o uso para descarregar o conteúdo do ficheiro **myfile** e armazená-lo para o ficheiro **out-sunset.png.**

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Eliminar um ficheiro
Finalmente, para apagar um `delete_file`ficheiro, ligue.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot"></a>Criar instantâneo de partilha
Pode criar uma cópia no tempo de toda a sua parte do ficheiro.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Criar instantâneo de partilha com metadados**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Listacções e instantâneos 
Pode listar todas as fotos para uma determinada parte.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Navegue no instantâneo de partilha
Pode navegar no conteúdo de cada fotografia de partilha para recuperar ficheiros e diretórios a partir desse momento.

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Obtenha arquivo a partir de instantâneo de partilha
Pode descarregar um ficheiro a partir de uma fotografia de partilha para o seu cenário de restauro.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Eliminar uma única imagem de instantâneo  
Pode eliminar uma única fotografia de partilha.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Eliminar a partilha quando existirem instantâneos de partilha
Uma parte que contenha instantâneos não pode ser eliminada a menos que todos os instantâneos sejam eliminados primeiro.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Passos seguintes
Agora que aprendeu a manipular ficheiros Azure com python, siga estes links para saber mais.

* [Centro para Programadores do Python](https://azure.microsoft.com/develop/python/)
* [API REST dos Serviços do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355)
* [Microsoft Azure Storage SDK para Python](https://github.com/Azure/azure-storage-python)
