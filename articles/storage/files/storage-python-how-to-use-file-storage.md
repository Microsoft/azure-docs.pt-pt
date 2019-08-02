---
title: Desenvolver para arquivos do Azure com Python | Microsoft Docs
description: Saiba como desenvolver aplicativos e serviços Python que usam os arquivos do Azure para armazenar dados de arquivo.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 139e3009722761172b7bbd57805a7f5b07e55fc0
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699383"
---
# <a name="develop-for-azure-files-with-python"></a>Desenvolver para arquivos do Azure com Python
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

Este tutorial demonstrará as noções básicas do uso do Python para desenvolver aplicativos ou serviços que usam os arquivos do Azure para armazenar dados de arquivo. Neste tutorial, criaremos um aplicativo de console simples e mostraremos como executar ações básicas com o Python e os arquivos do Azure:

* Criar compartilhamentos de arquivos do Azure
* Criar diretórios
* Enumerar arquivos e diretórios em um compartilhamento de arquivos do Azure
* Carregar, baixar e excluir um arquivo

> [!Note]  
> Como os arquivos do Azure podem ser acessados via SMB, é possível escrever aplicativos simples que acessam o compartilhamento de arquivos do Azure usando as classes e funções padrão de e/s do Python. Este artigo descreverá como escrever aplicativos que usam o SDK do Python do armazenamento do Azure, que usa a [API REST dos arquivos do Azure](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) para se comunicar com os arquivos do Azure.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Baixe e instale o SDK do armazenamento do Azure para Python

O [SDK do armazenamento do Azure para Python](https://github.com/azure/azure-storage-python) requer Python 2,7, 3,3, 3,4, 3,5 ou 3,6.
 
## <a name="install-via-pypi"></a>Instalar via PyPi

Para instalar por meio do índice de pacote do Python (PyPI), digite:

```bash
pip install azure-storage-file
```

> [!NOTE]
> Se você estiver atualizando do SDK do armazenamento do Azure para o Python versão 0,36 ou anterior, desinstale `pip uninstall azure-storage` o SDK mais antigo usando antes de instalar o pacote mais recente.

Para métodos de instalação alternativos, visite o [SDK do armazenamento do Azure para Python no GitHub](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Exibir o aplicativo de exemplo
f para exibir e executar um aplicativo de exemplo que mostra como usar o Python com os arquivos do [Azure, consulte armazenamento do Azure: Introdução com os arquivos do Azure](https://github.com/Azure-Samples/storage-file-python-getting-started)no Python. 

Para executar o aplicativo de exemplo, verifique se você instalou os `azure-storage-file` pacotes e. `azure-storage-common`

## <a name="set-up-your-application-to-use-azure-files"></a>Configurar seu aplicativo para usar os arquivos do Azure
Adicione o seguinte próximo à parte superior de qualquer arquivo de origem Python no qual você deseja acessar o armazenamento do Azure programaticamente.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Configurar uma conexão com os arquivos do Azure 
O `FileService` objeto permite que você trabalhe com compartilhamentos, diretórios e arquivos. O código a seguir cria `FileService` um objeto usando o nome da conta de armazenamento e a chave de conta. Substitua `<myaccount>` e `<mykey>` pelo nome e a chave da conta.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Criar uma partilha de ficheiros do Azure
No exemplo de código a seguir, você pode usar `FileService` um objeto para criar o compartilhamento, se ele não existir.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>Criar um diretório
Você também pode organizar o armazenamento colocando arquivos dentro de subdiretórios, em vez de ter todos eles no diretório raiz. Os arquivos do Azure permitem que você crie quantos diretórios forem permitidos na sua conta. O código a seguir criará um subdiretório chamado **SampleDir** no diretório raiz.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerar arquivos e diretórios em um compartilhamento de arquivos do Azure
Para listar os arquivos e diretórios em um compartilhamento, use o método **listar\_diretórios\_e\_arquivos** . Este método devolve um gerador. O código a seguir gera o **nome** de cada arquivo e diretório em um compartilhamento para o console.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>Carregar um ficheiro 
O compartilhamento de arquivos do Azure contém, no mínimo, um diretório raiz onde os arquivos podem residir. Nesta seção, você aprenderá a carregar um arquivo do armazenamento local no diretório raiz de um compartilhamento.

Para criar um arquivo e carregar dados, use os `create_file_from_path`métodos `create_file_from_stream` `create_file_from_bytes` , ou `create_file_from_text` . Eles são métodos de alto nível que executam o agrupamento necessário quando o tamanho dos dados exceder 64 MB.

`create_file_from_path`carrega o conteúdo de um arquivo do caminho especificado e `create_file_from_stream` carrega o conteúdo de um arquivo/fluxo já aberto. `create_file_from_bytes`carrega uma matriz de bytes e `create_file_from_text` carrega o valor de texto especificado usando a codificação especificada (o padrão é UTF-8).

O exemplo a seguir carrega o conteúdo do arquivo **pôr-sol. png** no arquivo **MyFile** .

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
Para baixar dados de um arquivo, use `get_file_to_path` `get_file_to_bytes`, `get_file_to_stream`, ou `get_file_to_text`. Eles são métodos de alto nível que executam o agrupamento necessário quando o tamanho dos dados exceder 64 MB.

O exemplo a seguir demonstra `get_file_to_path` como usar o para baixar o conteúdo do arquivo **MyFile** e armazená-lo no arquivo **out-Sunset. png** .

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>Eliminar um ficheiro
Por fim, para excluir um arquivo, `delete_file`chame.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot"></a>Criar instantâneo de compartilhamento
Você pode criar uma cópia pontual de todo o seu compartilhamento de arquivos.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Criar instantâneo de compartilhamento com metadados**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>Listar compartilhamentos e instantâneos 
Você pode listar todos os instantâneos para um determinado compartilhamento.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>Procurar instantâneo de compartilhamento
Você pode procurar o conteúdo de cada instantâneo de compartilhamento para recuperar arquivos e diretórios desse ponto no tempo.

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>Obter arquivo do instantâneo de compartilhamento
Você pode baixar um arquivo de um instantâneo de compartilhamento para o cenário de restauração.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>Excluir um único instantâneo de compartilhamento  
Você pode excluir um único instantâneo de compartilhamento.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>Excluir compartilhamento quando existirem instantâneos de compartilhamento
Um compartilhamento que contém instantâneos não pode ser excluído, a menos que todos os instantâneos sejam excluídos primeiro.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>Passos Seguintes
Agora que você aprendeu a manipular os arquivos do Azure com o Python, siga estes links para saber mais.

* [Centro para Programadores do Python](https://azure.microsoft.com/develop/python/)
* [API REST dos Serviços do Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355)
* [SDK do Armazenamento do Microsoft Azure para Python](https://github.com/Azure/azure-storage-python)
