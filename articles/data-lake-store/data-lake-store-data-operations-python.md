---
title: 'Python: Operações de sistema de ficheiros em Gen1 de armazenamento do Azure Data Lake | Documentos da Microsoft'
description: Saiba como utilizar o Python SDK para trabalhar com o sistema de ficheiros de geração 1 de armazenamento do Data Lake.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: ed28c502bb407c65b388704c177fca13cecc38d6
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57548631"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-python"></a>Operações do sistema de ficheiros em Gen1 de armazenamento do Azure Data Lake com o Python
> [!div class="op_single_selector"]
> * [SDK do .NET](data-lake-store-data-operations-net-sdk.md)
> * [SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Neste artigo, irá aprender a utilizar o Python SDK para executar operações de sistema de ficheiros no Gen1 de armazenamento do Azure Data Lake. Para obter instruções sobre como efetuar operações de gestão de conta na geração 1 de armazenamento do Data Lake com o Python, veja [operações de gestão na geração 1 de armazenamento do Data Lake com o Python contas](data-lake-store-get-started-python.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Python**. Pode transferir o Python [aqui](https://www.python.org/downloads/). Este artigo utiliza a versão Python 3.6.2.

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Conta do Azure de geração 1 de armazenamento do Data Lake**. Siga as instruções em [introdução à geração 1 de armazenamento do Azure Data Lake com o portal do Azure](data-lake-store-get-started-portal.md).

## <a name="install-the-modules"></a>Instalar os módulos

Para trabalhar com a geração 1 de armazenamento do Data Lake com o Python, tem de instalar três módulos.

* O módulo `azure-mgmt-resource`, que inclui módulos do Azure para o Active Directory, etc.
* O `azure-mgmt-datalake-store` módulo, que inclui as operações de gestão de conta de geração 1 de armazenamento do Azure Data Lake. Para obter mais informações sobre este módulo, consulte a [referência de módulo do azure-mgmt-datalake-store](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python).
* O `azure-datalake-store` módulo, que inclui as operações de sistema de ficheiros de geração 1 de armazenamento do Azure Data Lake. Para obter mais informações sobre este módulo, consulte a [referência de módulo do sistema de ficheiros do azure-datalake-store](https://azure-datalake-store.readthedocs.io/en/latest/).

Utilize os comandos seguintes para instalar os módulos.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>Criar uma aplicação Python nova

1. No IDE à sua escolha, crie uma aplicação Python nova, como, por exemplo, **mysample.py**.

2. Adicione as linhas seguintes para importar os módulos necessários.

    ```
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Storage Gen1 account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Storage Gen1 filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. Guarde as alterações a mysample.py.

## <a name="authentication"></a>Authentication

Nesta secção, vamos falar sobre as diferentes formas de autenticar com o Azure AD. As opções disponíveis são:

* Para a autenticação de utilizador final para a sua aplicação, veja [autenticação de utilizador final com geração 1 de armazenamento do Data Lake com o Python](data-lake-store-end-user-authenticate-python.md).
* Para a autenticação de serviço a serviço para a sua aplicação, veja [autenticação de serviço para serviço com geração 1 de armazenamento do Data Lake com o Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-filesystem-client"></a>Criar cliente do sistema de ficheiros

O fragmento seguinte cria primeiro o cliente de conta de geração 1 de armazenamento do Data Lake. Ele usa o objeto de cliente para criar uma conta de geração 1 de armazenamento do Data Lake. Por fim, o fragmento cria um objeto de cliente do sistema de ficheiros.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create a filesystem client object
    adlsFileSystemClient = core.AzureDLFileSystem(adlCreds, store_name=adlsAccountName)

## <a name="create-a-directory"></a>Criar um diretório

    ## Create a directory
    adlsFileSystemClient.mkdir('/mysampledirectory')

## <a name="upload-a-file"></a>Carregar um ficheiro


    ## Upload a file
    multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)


## <a name="download-a-file"></a>Transferir um ficheiro

    ## Download a file
    multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)

## <a name="delete-a-directory"></a>Eliminar um diretório

    ## Delete a directory
    adlsFileSystemClient.rm('/mysampledirectory', recursive=True)

## <a name="next-steps"></a>Passos Seguintes
* [Operações de gestão na geração 1 de armazenamento do Data Lake com o Python contas](data-lake-store-get-started-python.md).

## <a name="see-also"></a>Consulte também

* [Referência de Python (sistema de ficheiros) do Azure Data Lake Storage Gen1](https://azure-datalake-store.readthedocs.io/en/latest)
* [Abrir aplicações de macrodados de código compatíveis com a geração 1 de armazenamento do Azure Data Lake](data-lake-store-compatible-oss-other-applications.md)
