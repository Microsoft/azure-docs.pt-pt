---
title: Armazenamento de lago de dados azure Gen2 Python SDK para ficheiros & ACLs
description: Utilize a Python a gerir diretórios e listas de controlo de acesso de ficheiros e diretórios (ACL) em contas de armazenamento que tenham espaço hierárquico (HNS) habilitado.
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: b43bfe5979b8232f1fee2f5c1c6873c9c62695a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061533"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Use python para gerir diretórios, ficheiros e ACLs em Azure Data Lake Storage Gen2

Este artigo mostra-lhe como usar python para criar e gerir diretórios, ficheiros e permissões em contas de armazenamento que tem espaço hierárquico (HNS) habilitado. 

[Pacote (Índice de Pacote Python)](https://pypi.org/project/azure-storage-file-datalake/) | [Amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [De referência](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0/azure.storage.filedatalake.html) | API[Gen1 para gen2 mapeamento](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [Dar feedback](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma subscrição do Azure. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento que tem espaço de nome hierárquico (HNS) ativado. Siga [estas](data-lake-storage-quickstart-create-account.md) instruções para criar uma.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Instale a biblioteca de clientes azure Data Lake Storage para Python utilizando [pip](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake --pre
```

Adicione estas declarações de importação ao topo do seu ficheiro de código.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Ligar à conta

Para utilizar os cortes neste artigo, terá de criar uma instância **DataLakeServiceClient** que represente a conta de armazenamento. 

### <a name="connect-by-using-an-account-key"></a>Conecte-se utilizando uma chave de conta

Esta é a maneira mais fácil de se ligar a uma conta. 

Este exemplo cria uma instância **DataLakeServiceClient** utilizando uma chave de conta.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Substitua `storage_account_name` o valor do espaço reservado pelo nome da sua conta de armazenamento.

- Substitua `storage_account_key` o valor do espaço reservado pela chave de acesso à sua conta de armazenamento.

### <a name="connect-by-using-azure-active-directory-ad"></a>Conecte-se utilizando o Diretório Ativo Azure (AD)

Pode utilizar a biblioteca de [clientes de identidade Azure para](https://pypi.org/project/azure-identity/) python para autenticar a sua aplicação com a Azure AD.

Este exemplo cria uma instância **DataLakeServiceClient** utilizando um ID do cliente, um segredo de cliente e um ID de inquilino.  Para obter estes valores, consulte [Adquirir um símbolo da Azure AD para autorizar pedidos de uma aplicação de cliente](../common/storage-auth-aad-app.md).

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> Para mais exemplos, consulte a biblioteca de [clientes de identidade Azure para](https://pypi.org/project/azure-identity/) documentação python.

## <a name="create-a-file-system"></a>Criar um sistema de ficheiros

Um sistema de ficheiros funciona como um recipiente para os seus ficheiros. Pode criar um, ligando para o método **FileSystemDataLakeServiceClient.create_file_system.**

Este exemplo cria um `my-file-system`sistema de ficheiros chamado .

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório, ligando para o método **FileSystemClient.create_directory.**

Este exemplo adiciona um `my-directory` diretório nomeado para um sistema de ficheiros. 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>Mudar o nome ou mover um diretório

Mude o nome ou mova um diretório, ligando para o método **DataLakeDirectoryClient.rename_directory.** Passe o caminho do diretório desejado um parâmetro. 

Este exemplo renomea um sub-directório para o nome `my-subdirectory-renamed`.

```python
def rename_directory():
    try:
       
       file_system_client = service_client.get_file_system_client(file_system="my-file-system")
       directory_client = file_system_client.get_directory_client("my-directory")
       
       new_dir_name = "my-directory-renamed"
       directory_client.rename_directory(rename_destination=directory_client.file_system_name + '/' + new_dir_name)

    except Exception as e:
     print(e) 
```

## <a name="delete-a-directory"></a>Eliminar um diretório

Elimine um diretório ligando para o método **DataLakeDirectoryClient.delete_directory.**

Este exemplo elimina um `my-directory`diretório chamado .  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```

## <a name="manage-directory-permissions"></a>Gerir permissões de diretório

Obtenha a lista de controlo de acesso (ACL) de um diretório, ligando para o método **DataLakeDirectoryClient.get_access_control** e definir o ACL, ligando para o método **DataLakeDirectoryClient.set_access_control.**

> [!NOTE]
> Se a sua aplicação autorizar o acesso utilizando o Azure Ative Directory (Azure AD), certifique-se de que o responsável pela segurança que a sua aplicação utiliza para autorizar o acesso foi atribuído à função de [Proprietário de Dados blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)de armazenamento . Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de [acesso em Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

Este exemplo recebe e define o ACL `my-directory`de um diretório chamado . A `rwxr-xrw-` corda dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo próprio apenas ler e executar permissões, e dá a todos os outros ler e escrever permissão.

```python
def manage_directory_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_dir_permissions = "rwxr-xrw-"
        
        directory_client.set_access_control(permissions=new_dir_permissions)
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
    
    except Exception as e:
     print(e) 
```

## <a name="upload-a-file-to-a-directory"></a>Faça upload de um ficheiro para um diretório 

Em primeiro lugar, crie uma referência de ficheiro no directório-alvo, criando uma instância da classe **DataLakeFileClient.** Faça upload de um ficheiro através do método **DataLakeFileClient.append_data.** Certifique-se de completar o upload ligando para o método **DataLakeFileClient.flush_data.**

Este exemplo envia um ficheiro de `my-directory`texto para um diretório chamado .   

```python
def upload_file_to_directory():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.create_file("uploaded-file.txt")
        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.append_data(data=file_contents, offset=0, length=len(file_contents))

        file_client.flush_data(len(file_contents))

    except Exception as e:
      print(e) 
```

> [!TIP]
> Se o tamanho do ficheiro for grande, o seu código terá de fazer várias chamadas para o método **DataLakeFileClient.append_data.** Considere utilizar o método **DataLakeFileClient.upload_data** em vez disso. Assim, pode sopor todo o ficheiro numa única chamada. 

## <a name="upload-a-large-file-to-a-directory"></a>Faça upload de um grande ficheiro para um diretório

Utilize o método **DataLakeFileClient.upload_data** para carregar ficheiros grandes sem ter de fazer várias chamadas para o método **DataLakeFileClient.append_data.**

```python
def upload_file_to_directory_bulk():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.upload_data(file_contents, overwrite=True)

    except Exception as e:
      print(e) 
```

## <a name="manage-file-permissions"></a>Gerir permissões de ficheiros

Obtenha a lista de controlo de acesso (ACL) de um ficheiro, ligando para o método **DataLakeFileClient.get_access_control** e definir o ACL, ligando para o método **DataLakeFileClient.set_access_control.**

> [!NOTE]
> Se a sua aplicação autorizar o acesso utilizando o Azure Ative Directory (Azure AD), certifique-se de que o responsável pela segurança que a sua aplicação utiliza para autorizar o acesso foi atribuído à função de [Proprietário de Dados blob](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)de armazenamento . Para saber mais sobre como as permissões da ACL são aplicadas e os efeitos da sua mudança, consulte o controlo de [acesso em Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

Este exemplo recebe e define o `my-file.txt`ACL de um ficheiro chamado . A `rwxr-xrw-` corda dá ao utilizador próprio ler, escrever e executar permissões, dá ao grupo próprio apenas ler e executar permissões, e dá a todos os outros ler e escrever permissão.

```python
def manage_file_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_file_permissions = "rwxr-xrw-"
        
        file_client.set_access_control(permissions=new_file_permissions)
        
        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e) 
```

## <a name="download-from-a-directory"></a>Baixar de um diretório 

Abra um arquivo local para escrever. Em seguida, crie uma instância **DataLakeFileClient** que represente o ficheiro que pretende descarregar. Ligue para **dataLakeFileClient.read_file** para ler bytes do ficheiro e, em seguida, escreva esses bytes para o ficheiro local. 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        downloaded_bytes = file_client.read_file()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Enumerar os conteúdos de diretório, ligando para o método **FileSystemClient.get_paths** e, em seguida, enumerando através dos resultados.

Este exemplo, imprime o caminho de cada subdiretório e `my-directory`arquivo que está localizado num diretório chamado .

```python
def list_directory_contents():
    try:
        
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        paths = file_system_client.get_paths(path="my-directory")

        for path in paths:
            print(path.name + '\n')

    except Exception as e:
     print(e) 
```

## <a name="see-also"></a>Consulte também

* [Documentação de referência da API](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html)
* [Pacote (Índice de Pacote Python)](https://pypi.org/project/azure-storage-file-datalake/)
* [Amostras](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Gen1 para mapeamento Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Dar Feedback](https://github.com/Azure/azure-sdk-for-python/issues)
