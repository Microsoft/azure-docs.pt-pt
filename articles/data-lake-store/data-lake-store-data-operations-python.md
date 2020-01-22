---
title: 'Python: operações de FileSystem em Azure Data Lake Storage Gen1 | Microsoft Docs'
description: Saiba como usar o SDK do Python para trabalhar com o sistema de arquivos Data Lake Storage Gen1.
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 17b53c508f0c4c5ba8fa257fcdc692cdaa45d470
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294223"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-python"></a>Operações do sistema de arquivos em Azure Data Lake Storage Gen1 usando Python
> [!div class="op_single_selector"]
> * [SDK do .NET](data-lake-store-data-operations-net-sdk.md)
> * [SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Neste artigo, você aprenderá a usar o SDK do Python para executar operações de sistema de arquivos em Azure Data Lake Storage Gen1. Para obter instruções sobre como executar operações de gerenciamento de conta em Data Lake Storage Gen1 usando o Python, consulte [operações de gerenciamento de conta em data Lake Storage Gen1 usando o Python](data-lake-store-get-started-python.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Python**. Pode transferir o Python [aqui](https://www.python.org/downloads/). Este artigo utiliza a versão Python 3.6.2.

* **Uma subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure data Lake Storage Gen1 conta**. Siga as instruções em Introdução ao [Azure data Lake Storage Gen1 usando o portal do Azure](data-lake-store-get-started-portal.md).

## <a name="install-the-modules"></a>Instalar os módulos

Para trabalhar com Data Lake Storage Gen1 usando Python, você precisa instalar três módulos.

* O módulo `azure-mgmt-resource`, que inclui módulos do Azure para o Active Directory, etc.
* O módulo `azure-mgmt-datalake-store`, que inclui as operações de gerenciamento de conta de Azure Data Lake Storage Gen1. Para obter mais informações sobre esse módulo, consulte a [referência do módulo Azure-MGMT-datalake-Store](/python/api/azure-mgmt-datalake-store/).
* O módulo `azure-datalake-store`, que inclui as operações de Azure Data Lake Storage Gen1 FileSystem. Para obter mais informações sobre esse módulo, consulte a [referência do módulo arquivo do Azure-datalake-Store](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/).

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

   ## Common Azure imports
   from azure.mgmt.resource.resources import ResourceManagementClient
   from azure.mgmt.resource.resources.models import ResourceGroup

   ## Use these as needed for your application
   import logging, getpass, pprint, uuid, time
   ```

3. Guarde as alterações a mysample.py.

## <a name="authentication"></a>Autenticação

Nesta secção, vamos falar sobre as diferentes formas de autenticar com o Azure AD. As opções disponíveis são:

* Para autenticação do usuário final para seu aplicativo, consulte [autenticação do usuário final com data Lake Storage Gen1 usando Python](data-lake-store-end-user-authenticate-python.md).
* Para autenticação serviço a serviço para seu aplicativo, consulte [Autenticação serviço a serviço com data Lake Storage Gen1 usando Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-filesystem-client"></a>Criar cliente do sistema de ficheiros

O trecho a seguir cria primeiro o cliente da conta Data Lake Storage Gen1. Ele usa o objeto de cliente para criar uma conta de Data Lake Storage Gen1. Por fim, o fragmento cria um objeto de cliente do sistema de ficheiros.

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

## <a name="next-steps"></a>Passos seguintes
* [Operações de gerenciamento de conta em data Lake Storage Gen1 usando Python](data-lake-store-get-started-python.md).

## <a name="see-also"></a>Ver também

* [Referência de Azure Data Lake Storage Gen1 Python (Filesystem)](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core)
* [Aplicativos de Big data de software livre compatíveis com o Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)
