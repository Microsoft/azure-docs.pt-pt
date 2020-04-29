---
title: 'Python: Operações do sistema de arquivos no Azure Data Lake Storage Gen1 [ Microsoft Docs'
description: Aprenda a usar python SDK para trabalhar com o sistema de ficheiros Data Lake Storage Gen1.
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 17b53c508f0c4c5ba8fa257fcdc692cdaa45d470
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76294223"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-python"></a>Operações do sistema de ficheiros no Azure Data Lake Storage Gen1 usando Python
> [!div class="op_single_selector"]
> * [SDK .NET](data-lake-store-data-operations-net-sdk.md)
> * [SDK Java](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Neste artigo, aprende-se a usar o Python SDK para realizar operações de sistema de ficheiros no Azure Data Lake Storage Gen1. Para obter instruções sobre como realizar operações de gestão de conta em Data Lake Storage Gen1 utilizando Python, consulte operações de gestão de [conta no Data Lake Storage Gen1 utilizando python](data-lake-store-get-started-python.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Python.** Pode transferir o Python [aqui](https://www.python.org/downloads/). Este artigo utiliza a versão Python 3.6.2.

* **Uma subscrição Azure.** Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Conta Azure Data Lake Storage Gen1.** Siga as instruções no [Get started com Azure Data Lake Storage Gen1 utilizando o portal Azure](data-lake-store-get-started-portal.md).

## <a name="install-the-modules"></a>Instalar os módulos

Para trabalhar com data Lake Storage Gen1 usando Python, você precisa instalar três módulos.

* O módulo `azure-mgmt-resource`, que inclui módulos do Azure para o Active Directory, etc.
* O `azure-mgmt-datalake-store` módulo, que inclui as operações de gestão de conta azure Data Lake Gen1. Para obter mais informações sobre este módulo, consulte a referência do [módulo azure-mgmt-datalake store](/python/api/azure-mgmt-datalake-store/).
* O `azure-datalake-store` módulo, que inclui as operações do sistema de ficheiros Azure Data Lake Gen1. Para obter mais informações sobre este módulo, consulte a referência do módulo de módulo de [ficheiros azure-datalake-store](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core/).

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

* Para autenticação de utilizador final para a sua aplicação, consulte a [autenticação do utilizador final com data Lake Storage Gen1 utilizando Python](data-lake-store-end-user-authenticate-python.md).
* Para autenticação de serviço a serviço para a sua aplicação, consulte [a autenticação serviço-a-serviço com data Lake Storage Gen1 utilizando Python](data-lake-store-service-to-service-authenticate-python.md).

## <a name="create-filesystem-client"></a>Criar cliente do sistema de ficheiros

O seguinte corte cria primeiro o cliente da conta Data Lake Storage Gen1. Usa o objeto cliente para criar uma conta Gen1 de Armazenamento de Data Lake. Por fim, o fragmento cria um objeto de cliente do sistema de ficheiros.

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
* [Operações de gestão de conta em Data Lake Storage Gen1 usando Python](data-lake-store-get-started-python.md).

## <a name="see-also"></a>Consulte também

* [Referência de armazenamento de lagos azure Data Gen1 Python (Sistema de Ficheiros)](https://docs.microsoft.com/python/api/azure-datalake-store/azure.datalake.store.core)
* [Aplicações open Source Big Data compatíveis com Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)
